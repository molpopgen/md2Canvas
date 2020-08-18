% simple example, from the README

```{.python3 .run}
import textwrap
for x in [2, 3]:
    print(textwrap.dedent(rf"""
        1.  What is ${x}\times 5$?
        *a) ${x*5}$
        b)  ${x+1}$
        """))
```

% questions with random values and grouped

GROUP
pick: 1
points per question: 1

```{.python3 .run}
import numpy as np
import math
import textwrap

for x in np.random.randint(low=10, high=100, size=3):
	print(textwrap.dedent(rf"""
		1. What is ${x} + 1$?
		[*] ${x+1}$
		[] {x}
		[] {math.sqrt(x)}
		"""))
```

END_GROUP


% Using matplotlib graphics
% NOTE: a real example would use viridis color map for accessibility!

```{.python3 .run}
import textwrap
import matplotlib.pyplot as plt
for c in ['red', 'blue']:
    plt.plot([1,2,3,4], color=c)
    plt.ylabel('some numbers')
    plt.savefig(c+'.png')
q=rf"""
1. What is your favorite color?
[*] ![The data for the question](red.png)
[*] ![The data for the question](blue.png)
"""
print(textwrap.dedent(q))
```

% Use pandas + latex + image magick to generate tables.

```{.python3 .run}
import textwrap
import pandas as pd
import subprocess
template = r'''\documentclass[preview]{{standalone}}
\usepackage{{booktabs}}
\begin{{document}}
{}
\end{{document}}
'''
data = [(1./3., 2./3.), (5, 4)]
df = pd.DataFrame(data,columns=['a','b'], index=['c','d'])
with open("table.tex", "w") as f:
    f.write(template.format(df.to_latex(float_format="{:,.2f}".format)))
subprocess.call(['pdflatex', 'table.tex'], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
subprocess.call(['convert', '-density','300','table.pdf', '-quality','90', 'latextable.png'])
q=rf"""
1. What is the answer to: 
   ![](latextable.png)
[*] yes
[] ![](latextable.png)
"""
print(textwrap.dedent(q))
```

% Calculation problem with many different flavors

GROUP
pick: 1

```{.python3 .run}
import math
import textwrap

import numpy as np
used = []
for _ in range(50):
    x = np.random.randint(11, 303, 1)[0]
    while x in used:
        x = np.random.randint(11, 303, 1)[0]
    used.append(x)
    answer = math.sqrt(x)
    q=rf"""
    1. What is the square root of ${x}$?
    = {answer} +- 0.0001
    """
    print(textwrap.dedent(q))
```

END_GROUP
