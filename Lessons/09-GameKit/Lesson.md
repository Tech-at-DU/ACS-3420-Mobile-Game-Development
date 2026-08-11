# Lesson 9: GameKit

## Learning Objectives

- Describe how social games impact engagement.
- Identify the tools available with GameKit & Game Center.
- Locate useful resources for implementing a social game.

## Social Games

There are many different game genres, each with something that makes it great for a different audience. Games are inherently a social activity. Sometimes that social interaction is part of the game itself — competitive or cooperative multiplayer — but even for single-player experiences, players like to see and share their accomplishments.

Social games can range across a variety of techniques:

- Leaderboards
- Achievements
- Matchmaking challenges

## GameKit

GameKit offers features you can use to create social games — the ability to create apps that let players interact with each other:

- **Real-time network matches** — players can invite others to join their game, and can receive invitations even when your game isn't running.
- **Turn-based gaming** — a store-and-forward network match infrastructure where the match plays out over a series of turns, without requiring all players connected simultaneously.
- **Game Center** 🏆

## Game Center

A centralized game service that connects players to each other.

- **Achievements** — track a player's accomplishments in the game.
- **Leaderboards** — store and fetch player scores from Game Center.
- **Challenges** — let a player challenge others to complete an achievement or beat a leaderboard score.

![gamecenter](assets/gamecenter.png)

A few examples of how different players interact with Game Center: viewing scores earned in a game (both their own and other players'); a game with achievements sending a message to Game Center to update quest progress; games using Game Center's matchmaking to find and connect players' devices, exchanging data through Game Center's servers; a turn-based game sending a push notification when it's a player's turn.

## Checkpoint: Explore the Documentation

Look through the [GameKit documentation](https://developer.apple.com/documentation/gamekit) and find the related classes for:
- Challenges
- Achievements
- Leaderboards
- Turn-based games
- Real-time matches
- What is `GKGameCenterViewController`?

## How to Integrate Game Center

See the [Game Center programming guide](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008304). To make a game Game Center-aware you need:

- Authentication
- Game Center assets

![commongame](assets/commongame.png)
![gamecenteraware](assets/gamecenteraware.png)

## Checkpoint: Try It

Pick one GameKit feature — a leaderboard is the simplest starting point — and wire it into AstroJunk or your final project game. At minimum: authenticate the local player, submit a score, and confirm it shows up in the Game Center sandbox.

## Tutorial Resources

1. [Ray Wenderlich tutorial](https://www.raywenderlich.com/7544-game-center-for-ios-building-a-turn-based-game)
2. [Tutorial leaderboard](https://code.tutsplus.com/tutorials/game-center-and-leaderboards-for-your-ios-app--cms-27488)

**Next:** You've now covered every lesson topic. Head to the [Study Guide](../../Assignments/StudyGuide.md) to self-assess, then start the [Final Project](../../Assignments/Project.md).
