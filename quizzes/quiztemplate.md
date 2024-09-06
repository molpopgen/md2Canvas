1. What's the answer
[*] Right
[] Wrong
... oops

GROUP
pick: 1
1. What's the answer to this
[*] Right
[] Wrong
... oops

1. What's the answer to this one
[*] Right
[] Wrong
... oops
END_GROUP



```{.python3 .run}
import numpy as np
import textwrap

x = np.random.randint(10, 100, 1)[0]
y = np.random.randint(10, 100, 1)[0]

q = rf"""
1. What is ${x}$ + ${y}$?
[*] ${x+y}$
[ ] ${x*y}$
... Looks like you multiplied...

2. What is $2$ times the answer to the previous question?
[*] ${2*(x+y)}$
[ ] ${2*x*y}$
... Looks like you multiplied...
"""


print(textwrap.dedent(q))
```

GROUP
```{.python3 .run}
import textwrap

import numpy as np

used = []
for _ in range(10):
    x = np.random.randint(10, 100, 1)[0]
    y = np.random.randint(10, 100, 1)[0]
    while (x, y) in used:
        x = np.random.randint(10, 100, 1)[0]
        y = np.random.randint(10, 100, 1)[0]
    used.append((x, y))
    q = rf"""
    1. What is the sum of ${x}$ and ${y}$?
    = {int(x+y)}
    """
    print(textwrap.dedent(q))
```
END_GROUP
