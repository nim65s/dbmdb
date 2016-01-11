---
title: HowTo get beamer & revealjs slides from markdown
subtitle: thanks to pandoc
shorttitle: md → beamer & revealjs
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

## Lists

- eggs
- butter
- ham
- world domination

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
        self.perimeter = 2 * π * r
        self.surface = π * r ** 2
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

# Medias

## Images

![Cat](media/chat.jpg)

## GIFs

![Totoro](media/totoro_anime.gif)

## Videos

%[Maru](media/maru.mp4)
