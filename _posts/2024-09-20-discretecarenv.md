---
title: "DiscreteCarEnv: A Custom Environment in Gymnasium"
description: >-
  A simple gymnasium environment designed to benchmark RL generalization.
author: luisaraujo
date: 2024-09-20 20:55:00 +0800
categories: [reinforcement-learning, gymnasium]
tags: [gym, rl, custom-environment]
pin: true
---

## [DiscreteCarEnv](https://github.com/luisaraujo99/DiscreteCarEnv): A Custom Gymnasium Environment

To study RL, it is quite common to use game environments as the chosen MDPs. Differently from "real-world" scenarios, like driving a car, managing a portfolio of financial assets or controlling shop-floor production processes, games are perfect to analyze algorithms and parameters because they are easily accessible by other researchers, making comparison and benchmarking possible, but also because they are safe, in the sense that it is possible to explore bad actions without real harmful consequences. As a modest contribute to the RL community, I have created an RL oriented python game environment. It was developed using the [pygame](https://www.pygame.org/docs/) library and [gymasium](https://gymnasium.farama.org/tutorials/gymnasium_basics/environment_creation/), and the main goal is to control a car in *grid-world* maps and reach a final state as fast as possible. The maps are fully customizable by simply creating *csv* files.

### Simple setup

Create one or more csv files in the same folder, where entries can have one of the following values:
- **0** - Track
- **1** - Grass
- **2** - Initial square
- **3** - Finish square
- **4** - Wall 
- **5** - Ice


<img src="/assets/img/track_example.png" alt="track_example" height="80%" width="80%">

In the example bellow it is assumed that there is a */maps* folder with at least one *csv* map file:

```python
from gymnasium.envs.registration import register
import gymnasium as gym

register(
    id="DiscreteCarEnv-v0",
    entry_point="discrete_car_env:DiscreteCarEnv",
    max_episode_steps=100,
)


env = gym.make("DiscreteCarEnv-v0", tracks_path='maps' , render_mode="human")
observation, info = env.reset(seed=42)
for _ in range(1000):
   action = env.action_space.sample()
   observation, reward, terminated, truncated, info = env.step(action)

   if terminated or truncated:
      observation, info = env.reset()

env.close()
```