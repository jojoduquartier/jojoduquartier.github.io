---
disqus: jojoduquartier
---

...

---
Close DB Connections
---
Published: December 2020

Nearly every data scientist work(ed)s on a project that requires connection to a database (DB). It is always a good idea to close DB connections after reading/writing the data you need. [SQLAlchemy](https://www.sqlalchemy.org/) has a `dispose` method for DB engines as well as objects to handle transactions that release resources automatically. Every DB API in python has a method to close connections; [CXOracle](https://cx-oracle.readthedocs.io/en/latest/) has a [close](https://cx-oracle.readthedocs.io/en/latest/user_guide/connection_handling.html#closing-connections) method for example.

When dealing with different databases, in order to not repeat myself and make sure I always close connections, I tend to use a snippet like this:

#### The Snippet
``` python
import sqlite3
import contextlib
from sqlalchemy import create_engine


@contextlib.contextmanager
def db_connections():
    # the engines created here could all be Oracle, MSSql, Snowflake etc.
    try:
        # connect to some in-memory engine
        sales_db = create_engine('sqlite:///')

        # another in-memory engine
        manufacturing_db = sqlite3.connect(':memory:')

        # yield results
        yield sales_db, manufacturing_db
    except Exception as e:
        # handle exceptions etc
        pass
    finally:
        sales_db.dispose()
        manufacturing_db.close()
```

[Context Managers](https://docs.python.org/3/reference/datamodel.html#context-managers) are very useful in python when it comes to providing and releasing resources (like opening a file to read from and releasing it for the next task). There is a utility [decorator](https://docs.python.org/3/library/contextlib.html#contextlib.contextmanager) that makes it easy to turn your existing functions into context-managers and that is what I used for this snippet.

#### An Example
``` python
with db_connections() as (sales_db, manufacturing_db):
    # do something like 
    # pd.read_sql(..., sales_db)
    # pd.read_sql(..., manufacturing_db)
    pass
```

Thank you!
