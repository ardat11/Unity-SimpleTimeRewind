# Unity Time Rewind & Event-Based Player System

This repository provides a modular **time rewind system** for Unity, built on top of a **decoupled, event-driven player architecture**.  
The system is designed to be scalable, maintainable, and flexible enough to support complex temporal mechanics without tight coupling between gameplay systems.

---

## Features

- Event-based player input and animation handling
- Per-object rewind using custom state definitions
- Scene-wide global rewind coordination
- Optional visual and slow-motion rewind effects
- Ghost / copy mode for past-state visualization
- Clean lifecycle-safe event subscriptions

---

## Quick Start (No Code)

1. Add **PlayerEventBase** to your player root object  
2. Add **TimeManager** to any object that should manage rewindable entities  
3. Assign a parent GameObject to `ManagedGO` (contains rewindable components)  
4. Implement `IRewindable` on components you want to rewind  
5. Add **SceneTimeManager** once per scene for global rewind control  
6. Trigger rewind via player events or global events

The system will automatically start recording states and handle rewind logic.

---

## System Overview

The architecture is built around three core layers:

1. **Player Event Layer**
2. **Local Time Management**
3. **Scene-Level Time Coordination**

Each layer communicates through events rather than direct references.

---

## Player Event Architecture

### PlayerEventBase

Acts as a centralized event container attached to the player object.

It exposes two independent event groups:

- **Gameplay Events**
  - Movement
  - Jump
  - Dash
  - Attack
  - Throw
  - Rewind Start / Stop

- **Animation Events**
  - Set animator bool parameters
  - Set animator float parameters

This separation ensures gameplay logic and animation logic remain independent.

---

### PlayerEventSystem

A lightweight base class for systems that need access to player events.

It automatically resolves `PlayerEventBase` from the parent hierarchy, allowing safe subscription and unsubscription during Unity’s lifecycle.

---

## Time Rewind System

### IRewindable Interface

Any component that supports rewinding must implement `IRewindable`.

Each rewindable component:
- Owns a unique ID
- Defines how its state is saved
- Defines how its state is restored

This allows full control over what “rewind” means per object.

---

### TimeManager

`TimeManager` handles time recording and rewinding for a defined group of objects.

#### Responsibilities

- Periodically save object states
- Store states in ordered buffers
- Rewind objects step-by-step
- Control rewind duration and interval
- Apply optional visual and time-scale effects

#### Key Capabilities

- **Buffered State Recording**  
  States are saved at fixed intervals. Older states are discarded once the buffer limit is reached.

- **Independent Object Rewind**  
  Each object is rewound using its unique ID, ensuring correct state restoration.

- **Copy Mode (Ghost Preview)**  
  An optional semi-transparent, non-interactive clone visualizes delayed past states.

- **Optional Rewind Effects**  
  Includes screen fade and slow-motion effects, fully configurable and optional.

---

## Scene-Level Time Control

### SceneTimeManager

`SceneTimeManager` coordinates all active `TimeManager` instances in a scene.

It enables:
- Centralized tracking of time managers
- Synchronized global rewind
- Shared rewind duration across multiple systems

This allows large-scale rewind effects without direct dependencies.

---

## Global Event System

### GameEvents

`GameEvents` is a static event dispatcher used for cross-system communication.

Supported events include:
- Registering and unregistering `TimeManager` instances
- Triggering global rewind

This design avoids singletons and hard references.

---

## ID Management

### RandomIdGiver

Ensures every rewindable component receives a unique, collision-free ID.

This guarantees correct state-to-object mapping during rewind operations.

---

## Design Goals

- High modularity
- Low coupling between systems
- Clear separation of responsibilities
- Extensible rewind logic
- Predictable and safe event lifecycles

---

## Suitable Use Cases

- Time rewind mechanics
- Ghost or replay systems
- Puzzle games with temporal logic
- Action games with time manipulation
- Experimental or narrative-driven mechanics

---

## Notes

- The system is gameplay-agnostic and works for both 2D and 3D projects.
- Visual effects are optional and easily replaceable.
- Global time scaling can be refactored for per-entity effects if required.

---

