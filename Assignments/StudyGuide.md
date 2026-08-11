# Self-Assessment Guide

*Overview:* There's no proctored final exam in this independent study. Instead, use this guide to review each of the course's learning outcomes and honestly assess your own understanding. Where you're unsure, go back and reread the relevant lesson, or rebuild the relevant piece of AstroJunk from scratch without looking at your old code — that's usually the fastest way to find out if a concept actually stuck.

Work through this before starting the [final project](Project.md) — the project will exercise every outcome below, so gaps you find now are cheaper to fix than gaps you find mid-project.

## Outcome 1: Build game projects using Xcode, Swift, SpriteKit

Covered in Lessons 1, 2, 4, 5, and 7, building AstroJunk across nodes, actions, touch events, collisions, scenes, and juice (particles, sound, labels).

Check your understanding — can you, without referencing the lessons:
- Explain the main elements needed to build a game with SpriteKit (nodes, scenes, actions, physics bodies)?
- Describe at least three different ways to add "juice" to a game, and why each one matters for game feel?
- Explain the difference between moving a node manually (setting `position` each frame) versus using `SKAction`?

## Outcome 2: Identify trade-offs of different gaming architectures and select the best fit for a game

Covered in Lesson 3.

Check your understanding:
- Can you describe the inheritance-based, component-based, and state machine architectures, and give a scenario where each is the better fit?
- Could you explain, to someone unfamiliar with the topic, why component-based architecture tends to scale better than deep inheritance hierarchies?

## Outcome 3: Execute testing and debugging strategies specific to iOS games

Covered through the debugging habits built into every lesson's checkpoints, and explicitly in Lesson 6 (Physics) and Lesson 8 (SceneKit).

Check your understanding:
- Can you explain why knowing certain information at runtime (FPS, node count, physics body outlines) matters for debugging a game specifically, as opposed to a typical app?
- Can you describe the relationship between FPS and the number of active nodes in a scene?
- Have you actually used `showsFPS`, `showsNodeCount`, and `showsPhysics` on a real project, and interpreted what they told you?

## Outcome 4: Integrate native game engines into an iOS project

Covered in Lesson 6 (Physics), Lesson 8 (SceneKit), and Lesson 9 (GameKit).

Check your understanding:
- Can you explain how physics works in SpriteKit — physics bodies, category/collision/contact bit masks?
- Can you identify the purpose of state machine delegate methods (from Lesson 3's GameplayKit example)?
- Do you know what GameKit/Game Center adds to a game (leaderboards, achievements, matchmaking)?
- Can you describe the similarities and differences between SpriteKit and SceneKit?

## If You're Working with an Advisor or Mentor

If this course is part of a formal independent study with an advisor, this guide is a good basis for a check-in conversation — walk through each outcome together and discuss where you feel solid versus shaky, rather than treating it as a written test to pass or fail.
