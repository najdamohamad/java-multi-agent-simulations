# Multi-Agent Simulations in Java

Six interactive simulations in Java: Conway's Game of Life, the Immigration game, Schelling's segregation model, bouncing balls, Boids flocking, and a predator/prey Boids variant. All of them run on a shared discrete-event scheduler.

> Academic project, Ensimag (Grenoble INP), 2021. Second-year object-oriented programming lab ("TPL POO"), team project with three developers.

## Overview

The project models several classic multi-agent systems with an object-oriented design. Each simulation is split into three parts:

- a **model class**, which holds the state and the update rule (`GameOfLife`, `Boids`, and so on)
- a **simulator class**, which implements the provided `gui.Simulable` interface and draws the state through `GUISimulator`
- a **test launcher** with a `main` method

Simulations are driven by a central **event manager**. Each step is an `Event` with a timestamp, held in a priority queue, so several simulations can run at different speeds on the same clock.

## What was implemented

| Package | Simulation | Notes |
|---|---|---|
| `simulateurBalles` | Bouncing balls | `Balls` moves and bounces a set of balls inside the window |
| `jeuDeLaVieConway` | Conway's Game of Life | Grid of alive/dead cells (`Etat` enum) |
| `jeuDeLImmigration` | Immigration game | Generalises Life to *n* states. A cell moves to the next state when enough neighbours are in it |
| `segregationSchelling` | Schelling segregation | Cells with too many differently coloured neighbours (above a tolerance threshold) move to vacant cells |
| `boids` | Boids flocking | The three Reynolds rules (cohesion, separation, velocity matching), drawn as oriented `Triangle`s |
| `boids` | Predators / prey | `Predateurs` adds two boid types: predators chase prey, and kills are tracked (`updateKills`) |
| `event` | Discrete-event engine | `Event`, `EventManager`, and one event subclass per simulation |

`TestEnsembleDesFonctions` is an interactive console menu that launches any of the six simulations.

## Technical highlights

- **Inheritance used for reuse**:
  - `GameOfImmigration extends GameOfLife`, and `Segregation extends GameOfImmigration`, so the cellular automata share their grid logic
  - `Boids extends Balls`, and `Predateurs extends Boids`, so the agents share their position logic
- **Discrete-event scheduling**:
  - `EventManager` is a singleton backed by a `PriorityQueue<Event>`
  - `Event` implements `Comparable<Event>` and orders events by date
  - each simulation event (for example `GameOfLifeEvent`) runs one step of its model, then schedules its successor at `date + 1`
- **Separation of model and view**: the model classes contain no GUI code, and the `*Simulator` classes adapt them to the provided `Simulable` interface (`next()`, `restart()`).
- **Custom graphics**: `Triangle` implements `GraphicalElement` to draw boids oriented along their velocity vector.

## Tech stack

Java (Swing-based `gui.jar` provided by Ensimag) · Java Collections (`PriorityQueue`, `ArrayList`, `Vector`) · Make · Javadoc

## Project structure

```
codeEtudiants/              main project (final version)
├── src/
│   ├── simulateurBalles/   bouncing balls
│   ├── jeuDeLaVieConway/   Conway's Game of Life
│   ├── jeuDeLImmigration/  Immigration game
│   ├── segregationSchelling/ Schelling model
│   ├── boids/              Boids and predator/prey
│   ├── event/              discrete-event manager
│   └── TestEnsembleDesFonctions.java   interactive launcher
├── bin/gui.jar             provided GUI library
├── doc_gui/                Javadoc for the GUI library
└── Makefile
mohamad's/                  early individual prototype (balls, Game of Life)
src_exemple_code_collection/  course examples on Java collections
```

## Build and run

Requirements: a JDK. All commands run from `codeEtudiants/`.

```bash
cd codeEtudiants
make                              # compile all simulations into bin/

make exeConwaySimulator           # Game of Life
make exeImmigrationSimulator      # Immigration game
make exeSegregationSimulator      # Schelling segregation
make exeBallsSimulator            # bouncing balls
make exeBoidsSimulator            # Boids
make exePredateursSimulator       # predators / prey

java -classpath bin:bin/gui.jar TestEnsembleDesFonctions   # interactive menu
make docs                         # generate Javadoc into docs/
```
