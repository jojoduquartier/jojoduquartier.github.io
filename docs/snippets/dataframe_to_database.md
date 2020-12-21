---
disqus: jojoduquartier
---

...

---
From Dataframe to Database Table
---
Published: December 2020

There are different database connectors but this one is mainly about using [SQLAlchemy](https://www.sqlalchemy.org/). You already know that [pandas](https://pandas.pydata.org/pandas-docs/stable/index.html) offers the [.sql](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_sql.html) on dataframes and it is extremely user friendly (especially when you add some database configurations and define column datatypes, this method can be very fast).

This post will provide a (potentially) quicker route to add data to your database with sqlalchemy.

Disclaimer: For most databases, you need the classic `insert into {} ...` statement to push data; SQLAlchemy is not a replacement for SQL.

There are two common problems when someone tries to create the `insert` statement via [`f-strings`](https://www.python.org/dev/peps/pep-0498/).

1. Formatting dates to the specification of the database
2. Handling nulls. If your dataframe has `NaN` or `NaT` values, definitely change them to `None`!

#### The Idea

1. [Reflect](https://www.pythonsheets.com/notes/python-sqlalchemy.html#reflection-loading-table-from-existing-database) your table
2. Make sure your dataframe has `None` for every null variable
3. Turn your dataframe into a list of dictionaries (this is used to bind variables/parameter)
4. Execute the insert statement

If you have too large a dataframe (like say 100K rows or more), I suggest [chunking](https://toolz.readthedocs.io/en/latest/api.html#toolz.itertoolz.partition_all) the list.


#### The Snippet

```python
import time
import pandas
import sqlalchemy

# assume you have a sqlalchemy engine and we want to push data to table `some_table`
engine: sqlalchemy.engine.base.Engine # define your engine - connector
dataframe: pandas.DataFrame # your data you want to push

# 1. Reflect - just one way (some db won't allow you to reflect properly)
table = sqlalchemy.Table(
    'some_table',
    sqlalchemy.MetaData(engine), # you can also provide a schema argument to MetaData
    autoload=True
)

# 2. Handle Nulls
dataframe_ = dataframe.where(pd.notnull(dataframe), None)

# 3. Turn dataframe int a list of variables to bind
records = dataframe_.to_dict(orient='records'))

# 4. Push - execute statement
result = engine.execute(table.insert(), records)

# how many were inserted
print(f"Successfully inserted {result.rowcount} records")
result.close()

"""
if you partitioned, you could use a for-loop
for group in toolz.partition_all(30000, records):
    try:
        _ = engine.execute(table.insert(), group)
    except sqlalchemy.exc.OperationalError as e:
        # usually this happens when minor connection problems occur
        # or you're adding too many records too quick
        # something that works is to provide some cooldown time
        
        time.sleep(5) # maybe 5 seconds

        # then try to insert again
        try:
            ...
        except ... as e_:
            # at this point might as well just raise the error
            # but somethings would have been inserted already :(
            raise e_
"""

```

Make sure your dataframe has the same columns as your table (make sure that they are the same case, lower or upper or camelcase etc.)

Drawbacks:

1. This probably only works for some databases.
2. When you execute insert statements like this, you cannot really undo them. SQLAlchemy offers `Session` and `Transaction` objects that allow you to revert actions. This post is for when you are absolutely sure you want to push the data and would have done a bulk insert anyways.
https://www.pythonsheets.com/notes/python-sqlalchemy.html#object-relational-add-data

My personal preference (if possible) is to use [Objects to declare your database metadata](https://www.pythonsheets.com/notes/python-sqlalchemy.html#object-relational-add-data).