---
disqus: jojoduquartier
---

...

---
Try - Catch
---
Published: December 2020

#### Exception Handling on Multiple Functions
---

I often find myself splitting my code across modules each with their functions that I use in some sort of a `main` script or function for my projects. I like to log the progress of my code when it is deployed and I also like to keep one single log per project (when this makes sense obviously). When a function I imported is being used by the main function/script, I try to wrap it in some try/except (see below code block) block to make sure I catch any unexpected failure. 

``` python
try:
    # call the function
except:
    # do something and log it
finally:
    # release resources
```

In the spirit of [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), I started using this simple decorator for my functions so that I can avoid wrapping all functions with the try/except block.

#### The Snippet

``` python
from functools import wraps
from loguru._logger import Logger

def try_catch(logger: Logger):
    """
    Decorate a function with the module logger to not just track errors
    but catch errors on any function without repeating a try:...except:... block

    :param logger: a logger object, could be a python native logger as well
    """
    def proper_decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            try:
                output = func(*args, **kwargs)
                return output

            except Exception as e:
                logger.exception(f"Failure in {func.__name__}")
                raise e

        return wrapper

    return proper_decorator
```

I use the `wraps` decorator from `functools` so that I can use the function's actual name in my traceback. 

#### An Example

``` python
from loguru import logger

# set the logger handle etc. here

@try_catch(logger)
def my_function(numpy_array, user_constant):
    """
    Just a 1-D Numpy arrays divided by some constant.
    P.S. this example is designed to fail with constant 0
    i.e. I omit the ZeroDivision check
    """
    reciprocal = 1 / user_constant
    
    return reciprocal * numpy_array

# try
import numpy as np

my_function(np.array([0, 1]), 2)

my_function(np.array([0, 1]), 0) # Inspect the log and traceback
```

When `my_function` fails with user providing `0` for the constant, I want to see the full traceback and the decorator handles that.

I would suggest using this for functions dealing with databases or external communication that can easily fail unexpectedly.

Thank you!
