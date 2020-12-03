---
disqus: jojoduquartier
---

...


---
Organize DB Credentials
---

*This is somewhat a shameless plug for a python package I developed 😅*

Some data scientists (especially those not working on cloud platforms) cound find themselves with different database credentials from [Oracle](https://www.oracle.com/database/technologies/appdev/sql.html), [Postgre](https://en.wikipedia.org/wiki/PostgreSQL), [MySQL](https://www.mysql.com/) dialects. By DB credentials, I am referring to everything from the host address, the port number all the way to the username and passwords needed to establish connection to the database. I was once asked what was the best way to manage said credentials. The best way would certainly be to use some sort of key management system. Without such system, for quick and safe data exploration I found it useful to use [json](https://www.json.org/json-en.html) files like this:


#### General DB Info - store file on protected drive
```json
{
    "oracle": {
        "my_database_1": {
            "host": "localhost:database1",
            "port": "1521",
        },
        "my_database_2": {
            "host": "localhost:database2",
            "port": "1521",
        }
    },
    "mysql": {
        "my_database_1": {
            "host": "localhost:database1",
            "port": "3306",
        },
        "my_database_2": {
            "host": "localhost:database2",
            "port": "3306",
        }
    }
}
```

#### Credentials - encrypt credentials first
Username and Passwords are sensitive info so I use the [Cryptography](https://pypi.org/project/cryptography/) package to encrypt them and store the file and save them on a safe drive

```json
{
    "oracle": {
        "my_database_1": {
            "username": "encrypted",
            "port": "encrypted",
        },
        "my_database_2": {
            "host": "encrypted",
            "port": "encrypted",
        }
    },
    "mysql": {
        "my_database_1": {
            "host": "encrypted",
            "port": "encrypted",
        },
        "my_database_2": {
            "host": "encrypted",
            "port": "encrypted",
        }
    }
}
```

The encryption key is stored somewhere else to make it harder to decypt the data. With these files, I can easily separate my Oracle databases from MySQL databases etc.

But there's room for improvement; the code to connect to the databases is often the same and I would have to repeat the same thing over and over in jupyter notebooks or exploratory scripts. In order to avoid repetition, I created this [package: dsdbmanager](https://github.com/jojoduquartier/dsdbmanager) where *dsdb* stands for *data science database*. I will not speak in detail of the package but it allows me to effortlessly do things like:

1. Add any new database to the file directly with functionality from the package
2. Easily connect to any specific database I want with simple commands like:
```python
import pandas as pd
from dsdbmanager import oracle
oracle_databases = oracle()
oracle_database_1 = oracle_databases.my_database_1(connect_only=True, schema='some_schema')
pd.read_sql("select * from some table", oracle_database_1.sqlalchemy_engine)
```
3. See all tables and views available in a schema of my database
4. See metadata on number of rows and all columns for any table/view in the schema

When my projects move to production though, I do not use this package as we have ways of handling database credentials but this package has made my life very easy as I can quickly and effortlessly connect to databses and quickly check for things to debug issues. 

Hopefully you try this package out and find it useful! If you have any questions, please ask them in the comments below.

[^1]: The idea to develop a package like this was conceived while having a nice beer with a colleague and friend [Tyler](https://github.com/jewelltp).
[^2]: The package currentl supports Oracle, MySQL, MSSQL, Teradata and Snowflake Connections. Contributors are more than welcome to improve the package.
