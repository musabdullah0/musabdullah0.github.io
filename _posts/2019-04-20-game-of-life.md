---
layout: post
title: "game of life"
date: 2019-04-20
img: "/assets/game-of-life.png"
desc: "a simple game I built in JavaFX"
---

*Conway's Game of Life* is a zero player game where you set an initial
configuration and watch it develop over discrete time steps. It was created
in 1970 by John Conway as a **simulation game**, one that describes real
world processes. Surprisingly, it holds the same power as a **Turing
Machine** since it can compute anything that can be computed
*algorithmically*. You can read more about it [here](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Here is how it works. There is an "infinite" grid of squares, so that every
square has exactly 8 neighbors. Every square represents a cell which can be
either *alive* or *dead*. All are defaulted to being in the *dead* state, but
the user can flip some to the *alive* state in the beginning of the game. Once
the cells are set and the user presses **start**, he/she sits back and watches
it grow, stabilize, die, oscillate, or whatever else can happen. There are two
simple rules to advancing the game at each time-step:

1. If a live cell has exactly 2 **or** 3 live neighbors, it stays **alive**
2. If a dead cell has exactly 3 live neighbors, it becomes **alive**

I decided to write this game in *Java* because it seemed like an interesting game
and a simple introduction to GUI programming in JavaFX. The full code is on my
[github](https://github.com/musabdullah0), but here is the gist of it.
