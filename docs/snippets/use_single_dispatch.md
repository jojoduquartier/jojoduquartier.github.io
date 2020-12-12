---
disqus: jojoduquartier
---

...

---
Same Function Name - Different Data Types
---
Published: December 2020

#### Context 

Whether you are writing code to process data or build machine learning models, you will find yourself is situations where you want to do one thing but on different structures like a numpy array, a pandas dataframe etc.

Say you have to write a function to randomly sample data from a structure that could be either a numpy array or a pandas dataframe.

1. You could create two different functions
```python
def sample_numpy(data, n: int = 10):
    pass

def sample_dataframe(data, n: int = 10):
    pass
```

2. You could write one function and check the type of the function
```python
import numpy as np
import pandas as pd

def sample(data, n: int = 10):
    if isinstance(data, np.ndarray):
        # do something
        pass
    if isinstance(data, pd.DataFrame):
        # do something
        pass
    
    raise NotImplementedError()
```

If you go with the first approach and need to handle more data types, you can easily end up with many functions and tracking the names would be cumbersome. If you went with the second approach, the function will quickly span multiple lines. There is a better approach, one that allows the use the *exact same function name* and split the functions so that you actually handle each data type separately (as if you wrote different functions).

Enters [singledispatch](https://docs.python.org/3/library/functools.html#functools.singledispatch)! I highly recommend reading on this `decorator` and use it whenever you can.

#### The Snippet

```python
import numpy as np
import pandas as pd
from functools import singledispatch


@singledispatch
def sample(data, n: int = 10):
    raise NotImplementedError(f"Not yet implemented for {type(data)} type")


@sample.register(np.ndarray)
def v1(data: np.ndarray, n: int = 10):
    # we want replacement is n is bigger than the # observations
    indices = np.random.choice(data.shape[0], n, replace=(n > len(data)))
    return data[indices].copy()


@sample.register(pd.DataFrame)
def v2(data: pd.DataFrame, n: int = 10):
    return data.sample(n, replace=(n > len(data)))
```

#### An Example

Assuming you have the code from the block above
```python
df = pd.read_csv(
    "https://raw.githubusercontent.com/scikit-learn/scikit-learn/master/sklearn/datasets/data/iris.csv"
)

numpy_representation = df.values

# try it on the pandas dataframe
sample(df)
sample(df, 1000)  # should have replacement

# try it on the numpy array from the dataframe
sample(numpy_representation)
sample(numpy_representation, 1000)

# now try it on something you have not implemented it for
sample([1, 2, 3, 4, 5])
```

[^1]: Please read up on this cool decorator from python. It became available with python 3.4 and is (in my opinion) very useful!
[^2]: If you use python 3.8+, checkout the [singledispatchmethod](https://docs.python.org/3/library/functools.html#functools.singledispatchmethod) for dealing with class methods.