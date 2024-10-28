---
title: "ProcGen algorithms for DiscreteCarEnv"
description: >-
  A simple gymnasium environment designed to benchmark RL generalization.
author: luisaraujo
date: 2024-09-20 20:55:00 +0800
categories: [reinforcement-learning, gymnasium]
tags: [gym, rl, custom-environment]
pin: true
---

## ProcGen algorithms for DiscreteCarEnv

Procedural generation refers to a computational tool that generates artificial data trough a combination of procedures. Instead of "manually" designing data from scratch, or transforming it, a *Procedural generation* (proc-gen) algorithm can be used to **automatically** create new data, such as computer generated graphics, maps or levels in games, music, etc.

In the context of RL, procedural generation can be particularly beneficial because it allows the generation of massive amounts of environments with the desired diversity, which has been essential to both train and evaluate RL algorithm generalization, across unseen tasks [^1]. For the *DiscreteCarEnv*, there are several restrictions in order to have a map that is naturally playable, such as at least one connection between an initial and a terminal state. Instead of designing such maps "by hand", we propose two procedural generation algorithms. A first, one that allows the generation of maps of increasing width (*i.e.,* $ N $) using a well-known *fractal*, and a second which allows you to generate diverse maps for a given matrix dimension, using a graph traversal algorithm.

#### Sierpinski carpet maps

The first algorithm is not pseudo-random and is inspired in the famous geometric *fractal*, the *Sierpinski's carpet*. The original idea is to start with a square and dividing it into nine equal squares, then the central square is colored with a different color (or removed, so to speak). This exact same procedure can then be applied to the other 8 remaining squares, and recursively, *ad infinitum* or stopping at some given iteration. This procedure can be adapted to generate a track, for example starting with an iced-square (value 5), "dividing" it into 9, but this time replacing the 8 remaining ones by in-track squares (value 0). This can be done for a desired $k \in \mathbb{N},k{>2}$ number of steps, generating a map of size $3^{k}$. At the end, the algorithm just needs to add an initial and a final state.

```python
def sierpinski_carpet(n):
    if n == 0:
        return np.array([[5]])

    size = 3**n
    prev_size = 3 ** (n - 1)
    prev_carpet = sierpinski_carpet(n - 1)
    
    carpet = np.zeros((size, size))

    for i in range(3):
        for j in range(3):
            if not (i == 1 and j == 1): 
                carpet[
                    i * prev_size : (i + 1) * prev_size,
                    j * prev_size : (j + 1) * prev_size,
                ] = prev_carpet

    return carpet
```

Examples of generated maps for order 2,3 and 4:

<div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; align-items: center;">
    <img src="/assets/img/sierpinski9.png" alt="Sierpinski Carpet with depth 9" style="width: 100%; height: auto;">
    <img src="/assets/img/sierpinski27.png" alt="Sierpinski Carpet with depth 27" style="width: 100%; height: auto;">
    <img src="/assets/img/sierpinski81.png" alt="Sierpinski Carpet with depth 81" style="width: 100%; height: auto;">
</div>

[^1]: Cobbe, Karl, Hesse, Chris, Hilton, Jacob, Schulman, John. "Leveraging Procedural Generation to Benchmark Reinforcement Learning." *Proceedings of the 37th International Conference on Machine Learning*, PMLR, 2020. [PDF](https://proceedings.mlr.press/v119/cobbe20a/cobbe20a.pdf).
