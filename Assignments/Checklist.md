# Course Checklist

Everything below is required to complete this course. There's no proctored exam — completion is demonstrated by finishing this checklist: every lesson checkpoint, the self-assessment, and the final project.

If you're working with an advisor or mentor, this is the artifact to review together at check-ins.

## Game Design

- [ ] Play & Reflect write-up (favorite game, what makes it entertaining, genre check)
- [ ] Game Prototype Workshop: built a grid-based, turn-based analog game with written rules
- [ ] Playtested the prototype (with another person or solo) and wrote up the Insights questions

## Lesson 1: Intro to iOS Game Development

- [ ] Read [iOS Game Development: An Evolution](../Lessons/01-Intro-iOS-Game-Development/assignments/Intro.md)
- [ ] Warm-up: simple sprite created in the middle of the screen in a new Xcode project
- [ ] Read [Notes on Frame Rates](../Lessons/01-Intro-iOS-Game-Development/assignments/frame_rates.md) and wrote down 3 key takeaways
- [ ] AstroJunk project created, following [Space Junk](../Lessons/01-Intro-iOS-Game-Development/assignments/activity_1_space_junk.md)
- [ ] Experimented with the [Vector playground](../Lessons/01-Intro-iOS-Game-Development/playgrounds/Vector.playground)
- [ ] Movement added to all AstroJunk nodes (no collision handling yet)

## Lesson 2: Actions, Touch Events, Collisions

- [ ] Debris/meteors spawn at random positions, move top to bottom, and spin
- [ ] Bounding-box collision detection implemented; ship colliding with objects removes them
- [ ] Touch-based left/right movement implemented for the ship
- [ ] Playable area bounds check implemented (ship can't leave the screen)
- [ ] Each game object (Spaceship, Meteor, Debris) lives in its own class file

## Lesson 3: Selecting an Architecture

- [ ] At least one AstroJunk game object refactored into a component-based design (`Component` + `GameObject` base classes)
- [ ] Studied the GameplayKit water dispenser example and answered its analysis questions
- [ ] State machine diagrammed for AstroJunk (stretch: implemented with `GKStateMachine`)

## Lesson 4: Working with Scenes

- [ ] `GameOverScene` created and wired up per [the activity](../Lessons/04-Working-with-Scenes/assignments/activity.md)
- [ ] Win/loss counter and restart-on-tap behavior working

## Lesson 5: Adding Juice

- [ ] Background music added, stops appropriately on Game Over
- [ ] Sound effects added to at least collisions and scoring events
- [ ] Particle emitter added (e.g., fire trailing the ship)

## Lesson 6: Physics

- [ ] Playtested AstroJunk start-to-finish and noted rough edges
- [ ] Experimented in the [Physics Playground](../Lessons/06-Physics/assets/PhysicsPlayground.playground.zip)
- [ ] Collision detection switched from bounding-box to SpriteKit physics bodies

## Lesson 7: Cameras

- [ ] Moving background or `SKCameraNode`-based scrolling implemented
- [ ] All onscreen nodes stay correctly positioned relative to the camera
- [ ] `GameScene` refactored so logic isn't all in one file (modular by responsibility)

## Lesson 8: SceneKit

- [ ] Completed [Bouncing Shapes](../Lessons/08-SceneKit/assets/instructions.md) (part 1)
- [ ] Switch statement extended to handle every shape in the enum
- [ ] Completed [Bouncing Shapes Part 2](../Lessons/08-SceneKit/assets/instructions2.md) (materials, render loop, spawning/removal)

## Lesson 9: GameKit

- [ ] Found the relevant GameKit classes for challenges, achievements, leaderboards, turn-based and real-time matches
- [ ] At least one GameKit feature (e.g., a leaderboard) wired into a game and verified in the Game Center sandbox

## Self-Assessment

- [ ] Worked through the [Study Guide](StudyGuide.md) and can speak to all four learning outcomes without notes

## Final Project

See [Project.md](Project.md) for full requirements. Summary:

- [ ] Game Design Document completed (copy of the template, filled out)
- [ ] At least one user testing session conducted and documented in the Game Design Document
- [ ] Original game built in SpriteKit implementing core mechanics plus at least 5 more features from the required list (6 total)
- [ ] Self-review checklist in [Project.md](Project.md) completed
- [ ] (Optional) Game uploaded to TestFlight

## Done?

Once every box above is checked, you've met all four course learning outcomes and completed the course.
