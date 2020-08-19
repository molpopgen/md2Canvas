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

% Problems based on random graphics used for the answers
% Also uses viridis for discrete plots, which is handy
% for accessibility.
% The question gets all unique combos of size 3 from 
% [1, 10), generates a plot with that many colors.
% Each plot is part of the answer field.
% This is an advanced one!
% This is also a bad question, and is just an example
% using random graphical answers.  Even with viridis,
% it isn't reasonable to expect someone to tell 7 
% from 8 different colors.  The color map
% saturates at the hot/cold extremes.

GROUP
pick: 1

```{.python3 .run}
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
import textwrap
import pandas as pd
import itertools
import random

# help from https://stackoverflow.com/questions/7941226/how-to-add-line-based-on-slope-and-intercept-in-matplotlib     
def makeplot(ncolors, qid):
    # pick colors uniformly from viridis
    viridis = matplotlib.cm.get_cmap('viridis', 12)
    cmap = viridis(np.linspace(0, 1, ncolors))
    intercept = 0
    f = plt.figure()
    axes = plt.gca()
    for slope in range(ncolors):
        x_vals = np.array(axes.get_xlim())
        y_vals = intercept + slope * x_vals
        plt.plot(x_vals, y_vals, '--', color=cmap[slope])
        intercept += 0.25
    pname = "random_colors_question_" + f"{qid}_{ncolors}.png"
    plt.xlim(0.0, 1.0)
    plt.savefig(pname)
    plt.close()
    return pname
    
def get_subsets():
    s = {i+1 for i in range(10)}
    n = 4
    return list(itertools.combinations(s, n))

combos = get_subsets()
combos = random.sample(combos, 10)
    
for qid, c in enumerate(combos):
    pngfiles = []
    for ncolors in c:
        pngfiles.append(makeplot(ncolors, qid))
    if qid not in c:
        q=rf"""
        1. Which plot has {qid} colors?
        [*] None of them
        [ ] ![]({pngfiles[0]})
        [ ] ![]({pngfiles[1]})
        [ ] ![]({pngfiles[2]})
        """
    else:
        answers = []
        for i, j in enumerate(c):
            if j == qid:
                answers.append('*')
            else:
                answers.append('')
        q=rf"""
        1. Which plot has {qid} colors?
        [{answers[0]}] ![]({pngfiles[0]})
        [{answers[1]}] ![]({pngfiles[1]})
        [{answers[2]}] ![]({pngfiles[2]})
        [{answers[3]}] ![]({pngfiles[3]})
        """
        
    print(textwrap.dedent(q))
``` 
 
END_GROUP
