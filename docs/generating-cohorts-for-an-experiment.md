# How to allocate users to a random cohort for an A/B test

I create [https://api.getcohorts.com](https://api.getcohorts.com) because randomly allocating users to a cohort can be tricky. 

Ideally, a function that allocates a user to a random cohort will meet the following requirements:

### The function is idempotent, so the same user will be allocated to the same cohort every time.

This requirement is necessary because it's likely that the user will be exposed to your experiment multiple times. For example, you might be testing a new image on your website's homepage, and the user will return to the homepage several times. You'll want to make sure they see the same experience each time.

A simple way to implement an idempotent function would be to randomly assign the user to a cohort, and then cache its result. If the same user ever needs to be assigned to the cohort again, then the result from the cache is returned.

```python
# requires python 3.8+
import redis
import random
from typing import List

db = redis.Redis()

def allocate(user_id: int, experiment: str, cohorts: List[str]):
    key = f'experiment-{experiment}-{user_id}'
    if (cohort := db.get(key)) is not None:
        return cohort.decode('utf8')
    else:
        index = int(random.random() * len(cohorts))
        cohort = cohorts[index]
        db.set(key, cohort)
        return cohort
```

This methodology works very well, but it has the downside of requiring a database.

### The function is stateless, so there is no need to cache any results

### The function is accessible from as many locations as possible

### The function can be applied in batches on a datawarehouse

