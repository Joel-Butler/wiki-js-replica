---
title: Simuation - First pass
description: 
published: true
date: 2021-11-26T14:43:24.256Z
tags: 
editor: markdown
dateCreated: 2021-11-26T14:29:14.443Z
---

# Overview
My objective here was to build out a first pass at a simulation that was low-effort enough that I could get it up and running in a day.

Key objectives:
* Produce something that works.
* Inject some basic Lua scripts and get some ideas for how to separate system and actor code so that modifying the latter is simple and potentially can be done in-game.
* Visualize the simulation using some *really* ***really*** basic visuals.

# Harvester simulation

My first little sim is going to be a bit of a 'gatherer' sim for several actors hunting for resources. I'll keep the upper limits and some of the limitations on movement in variables as this could be fun to muck around with. 

I'm going to use a simple grid of width 0 -> x, and height 0 ->y. 

In each grid cell, there is 0-n resources.

## Actors
For now lets keep them uniform, but customizing them for individuality I'm going to leave as a phase 2.

Each actor's job is to harvest resources. They start at an edge grid at random.

### Actor actions:
- **Move** to an adjacent gid segment
- **View** adjacent grid segments for resources
- **Gather** resources in current grid segment
- **Contest** a grid segment (in case two gatherers overlap into a segment). 

## Time: 
In a simulation time is a little more critical - my actors will not be able to multi-task, so they need to select and commit to an action and cannot re-select or adjust until that action is completed. 

### Time Variables: 
Time will be represented by a 'tick' I'll set some defaults here as follows for each action and for the grid itself:
- **Move** will default to 5 ticks (**TM**)
- **Contest** will take 20 ticks (**TC**)
- **View** will take 2 ticks (**TV**)
- **Gather** will take 40 ticks (**TG**)
- A grid will grow a random number of additional resources (0-**MaxGrowth) every 100 ticks **TGrow**. 

## Grid cells - actors or environment?
I can either consider the grid cells as scripted elements themselves or just part of the underlying environment. While underlying environment would be easier, treating these cells as agents that execute 'stuff' in the same time-bound manner might be useful for future expansion, so I'm going to consider each cell a simpler agent too. 