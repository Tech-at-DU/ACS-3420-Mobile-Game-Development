# Game Design

## Learning Objectives

1. Identify the importance of game design and its stages
2. Identify different game genres
3. Describe the MDA framework
4. Implement a basic prototype of a game

## Checkpoint: Play and Reflect

Pick your favorite mobile or tabletop game and spend 5-10 minutes playing it. Then answer, in writing:

- What makes the game entertaining?
- Why is it your favorite?
- What genre is it? Check it against this [list of genres](https://www.lsntap.org/sites/lsntap.org/files/game%20genres.pdf) — did you land on the genre you expected?

## Game Design

Game design is the art of applying design and aesthetics to create a game. It's the part of game development concerned with:

- creating goals
- defining rules
- creating challenges

It's what needs to happen before you start coding your game.

## The MDA Framework

MDA is one of several frameworks that help formalize game design, by breaking a game into 3 components:

![mda](assets/mda.png)
![mda2](assets/mda2.png)

### Mechanics

Describes the particular components of the game, at the level of data representation and algorithms.

### Dynamics

Describes the run-time behavior of the mechanics acting on player inputs and each other's outputs over time.

### Aesthetics

Describes the desirable emotional responses evoked in the player when they interact with the game system.

### Perspectives

![mda3](assets/mda3.png)

From the designer's perspective, mechanics give rise to dynamic system behavior, which leads to particular aesthetic experiences. From the player's perspective, aesthetics set the tone, born out in observable dynamics and, eventually, operable mechanics. Considering both perspectives helps you see how even small changes in one layer can cascade into others. (Hunicke, Robin & Leblanc, Marc & Zubek, Robert, 2004.)

### Aesthetics, Revisited

Now go backwards, from the player's perspective. When we describe why a game is fun, we usually use a limited vocabulary. Here's a more descriptive list:

![words](assets/vocabulary.png)

### Dynamics

Dynamics work to create aesthetic experiences.

**Challenge** — created by things like time pressure and opponent play.

**Fellowship** — encouraged by sharing information across a team, or by supplying winning conditions that are harder to achieve alone.

### Mechanics

Mechanics are actions, behaviors, and control mechanisms afforded to the player within a game context. Together with the game's content (levels, assets, and so on), mechanics support overall gameplay dynamics.

### Tuning

Understanding how changes in each component affect the player's experience takes a lot of testing and tuning. Define the purpose of the game first, and keep the other components of the framework as simple as possible in the beginning.

### Case Study: Jetpack Joyride

Watch [Depth in Simplicity: The Making of Jetpack Joyride](https://www.gdcvault.com/play/1015527/Depth-in-Simplicity-The-Making) and note how the designers applied mechanics/dynamics/aesthetics thinking to a simple, highly polished game.

## Game Design Basics

- ⚒ **Setup** — board setup, arranging pieces, starting values for the player
- 🔁 **Game loop**
  - Player turn — choose from and perform available actions
  - Computer turn — move or perform actions of NPCs and update the environment
  - Check game state — if "won," "lost," or "tied," end the game; otherwise repeat the loop
- 🌎 **World/environment** — board size and layout, barriers, tile stats, starting point, time limits
- ✨ **Objects** — location, abilities, value
- 💕 **Non-player characters (NPCs)** — movement, abilities, health, objects
- 🧙 **Player**

## Checkpoint: Game Prototype Workshop

Goal: learn how people react to things you've created, how they interact with those things, and how it's never exactly what you expect.

You'll design a simple analog game (pen, paper, and any other materials you like — physical or a tool like a shared whiteboard app). Requirements:

- The game must be **grid-based**, **turn-based**, and support **1 or 2 players**.
- The player can play against the game itself (you act as the "computer" running the NPCs/environment), or two players can play against each other.

You'll need to design not just the game, but instructions on how to play it and a clear set of rules.

**Brainstorm (10 min)** — sketch a few different game ideas before committing to one.

**Build the game (25 min)**
- Create the board and pieces — draw a board, make pieces.
- Write the player rules — what are the primary and secondary goals? Which abilities are available at which times?
- Write the computer/NPC rules — what needs updating each game loop? Does the world or do NPCs react to player actions? How do you know if the player has won, lost, or tied?

**Playtest it** — this step works best with another person, but you can still get real signal solo:

- **With a playtester:** hand them the game, explain the rules once out loud, then go silent and only operate as the "computer"/NPC while they play for 5-8 minutes. Resist the urge to coach them.
- **Solo:** play through it yourself as if you were a first-time player, forcing yourself to follow only what's written in your rules (not what you intended). Where you have to guess or fill a gap from memory, that's a sign the rules aren't complete yet.

**Insights** — write down your answers:
- Were your instructions as clear as you thought they were?
- Did the player win/lose faster than you expected?
- Which parts of the game were the most fun?
- Which parts were the most confusing?
- Did the player discover strategies you didn't anticipate?
- What would you change based on this playtest?

You'll use this same design → build → playtest → iterate loop for your [final project](../../Assignments/Project.md), so it's worth doing this exercise for real rather than rushing it.

## Additional Resources

1. Hunicke, Robin & Leblanc, Marc & Zubek, Robert. (2004). MDA: A Formal Approach to Game Design and Game Research. AAAI Workshop - Technical Report. 1.
2. [MDA video](https://www.youtube.com/watch?v=uepAJ-rqJKA)

**Next:** [Lesson 1 - Intro to iOS Game Development](../01-Intro-iOS-Game-Development/Lesson1.md)
