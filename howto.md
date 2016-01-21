---
title: Quick MarkDown Reference
subtitle: with pandoc or python
shorttitle: md → {article,slides} in {pdf,html}
author: Guilhem Saurel
date: 2016-01-05
LAAS: fr
mainfont: Source Serif Pro
sansfont: Source Sans Pro
monofont: Source Code Pro
revealjs-url: https://cdn.jsdelivr.net/reveal.js/3.0.0/
---

# Section title
## Slide title

How-to Slides

Formating: *em* **bold** `mono` ~~strikethrough~~
text~subscript~^superscript^

## (Un)ordered Lists

- eggs
- butter
- ham
- world domination

1. Thing
2. Do
3. Words
4. You

## Quotes, unicode

> 友情と恋とモビルスーツ

# And even more !

## Code
```python
#!/usr/bin/env python3
from math import pi as π

class Circle(object):
    """ defines a circle from its radius """
    def __init__(self, r):
        # such maths, very difficult, wow
        if not isinstance(r, (int, float)):
            raise AttributeError('wrong radius')
        self.P = 2 * π * r
        self.S = π * r ** 2
```

## Maths

$\begin{aligned}
\vec{\nabla} \cdot  \vec{\mathcal{E}} & = \frac{\rho}{\epsilon_0} \\
\vec{\nabla} \times \vec{\mathcal{E}} & = -\frac{\partial \vec{\mathcal{B}}}{\partial t} \\
\vec{\nabla} \cdot  \vec{\mathcal{B}} & = 0 \\
\vec{\nabla} \times \vec{\mathcal{B}} & = \mu_0\vec{\mathcal{J}} + \epsilon_0\frac{\partial \vec{\mathcal{E}}}{\partial t}
\end{aligned}$

## Tables

| Right | Left | Default | Center |
|------:|:-----|---------|:------:|
|   12  |  12  |    12   |    12  |
|  123  |  123 |   123   |   123  |
|  *1*  |  `1` |  **1**  |  ~~1~~ |

## Citations

Look ! The trees… They're moving ! @saurel2015

# Medias

## Images

![Cat](media/chat.jpg)

## GIFs

![Totoro](media/totoro_anime.gif)

## Videos

%[Maru](media/maru.mp4)
