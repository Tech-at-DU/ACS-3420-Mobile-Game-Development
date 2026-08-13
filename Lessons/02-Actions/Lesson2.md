# Lesson 2: Actions, Touch Events, Collisions

## Why?

To give your game life, you'll want your nodes to move in various ways, respond to touch events, and emulate real-world reactions when two or more game elements bump into each other.

## Learning Objectives

1. Identify and describe the behavior and implementation of these `SKAction` types: group, repeating (two variations), `removeFromParent()`, wait, run block
2. Identify and describe:
   - the main idea behind collision detection
   - how users can interact with game elements using touch events
3. Design strategies for debugging collision detection behavior and a game's playable area
4. Implement various actions (scaling, rotation, etc.), collision detection, and touch events in AstroJunk

## Group Actions

`group(_:)` creates an action that runs a collection of actions **in parallel**. Creating groups is similar to creating sequences — pass a collection of `SKAction` objects to `group(_:)`:

```Swift
let groupAction = SKAction.group([action1, action2, action3])
```

Instead of running actions one after the other, a group action runs them all at once. When the group executes, every action in the collection starts immediately and runs in parallel. The `duration` of the group is the longest duration among its actions — the group doesn't complete until the longest-running action finishes. If an action in the group has a shorter duration, it completes, then idles until the group completes the remaining actions (this matters most when repeating a group, covered below).

You can combine groups and sequences: run two sequences at the same time by combining them into a group, or create sequences that contain group actions. `group(_:)` is also reversible — it creates a new group action containing the reverse of each action in the group.

## Repeating Actions

In addition to chaining actions with `sequence(_:)` and `group(_:)`, you can create one action and have it execute another action multiple times. `SKAction` provides two built-in functions for repeating a target action:

1. `repeat(_:count:)` — repeats another action a specified number of times.
2. `repeatForever(_:)` — repeats another action forever.

```Swift
let moveUp = SKAction.move(to: CGPoint(x: 50.0, y: 0.0), duration: 5.0)
let moveDown = SKAction.move(to: CGPoint(x: -50.0, y: 0.0), duration: 5.0)

// Execute move actions in sequence
let moveUpAndDown = SKAction.sequence([moveUp, moveDown])

// Repeat execution of sequence 7 times
let repeatUpDownMovement = SKAction.repeat(moveUpAndDown, count: 7)

mySprite.run(repeatUpDownMovement)
```

Example of `repeatForever(_:)`:

```Swift
let moveUp = SKAction.move(to: CGPoint(x: 50.0, y: 0.0), duration: 5.0)
let moveDown = SKAction.move(to: CGPoint(x: -50.0, y: 0.0), duration: 5.0)

let moveUpAndDown = SKAction.sequence([moveUp, moveDown])
let repeatUpDownMovement = SKAction.repeatForever(moveUpAndDown)

mySprite.run(repeatUpDownMovement)
```

Both repeating actions are reversible — reversing either creates a new action that is the reverse of the specified action, repeated the same number of times or forever.

## Stopping Actions

Repeating an action forever raises the obvious question: when does it stop? There are two basic ways to stop actions while running:

1. `removeAllActions()` — stops **all** actions currently running on the node.
2. `removeAction(forKey:)` — removes an action based on a unique key given to it when `run(_:withKey:)` was called.

`run(_:withKey:)` adds an identifiable action to the node's list of actions. It's identical to `run(_:)`, but the action is stored so it can be retrieved later. Example — using `removeAction(forKey:)` with a unique identifier so you only remove the action matching that key (checking `action(forKey:)` first to ensure it's not already running):

```Swift
func startPenguinFlying() {
  if penguin.action(forKey: "flying") == nil {
    penguin.run(
      SKAction.repeatForever(penguinAnimation),
      withKey: "flying")
} }
func stopPenguinFlying() {
  penguin.removeAction(forKey: "flying")
}
```

### `removeFromParent()`

Building up a massive list of nodes in a game is never good. Removing nodes from the scene graph when they're no longer needed is critical for performance. When nodes are no longer needed — done moving, offscreen, no longer visible — they still get factored into physics calculations and might still require SpriteKit to check whether they need to be redrawn. Over extended playing time, inactive nodes can build up, consuming all available device memory and prompting iOS to terminate your app — from the user's perspective, this looks like a crash.

**Option 1** — call `removeFromParent()` on the `SKNode` object directly (returns nothing):

```Swift
func removeFromParent()
```

```Swift
spriteNode.removeFromParent()
```

**Option 2** — create a `removeFromParent()` action, which returns an `SKAction` object that removes the node from its parent when it executes:

```Swift
class func removeFromParent() -> SKAction
```

This action is not reversible — the reverse of this action is the same action.

```Swift
// Create move action
let moveAction = SKAction.move(to: CGPoint(x: -dragon.size.width/2, y: actualY),
                               duration: TimeInterval(actualDuration))

// Create remove action
let removalAction = SKAction.removeFromParent()

// Run move action, then apply the removal action to dragon sprite
dragon.run(SKAction.sequence([moveAction, removalAction]))
```

Either `removeFromParent()` option can be used efficiently within `sequence` actions, as shown above.

### Wait Action

The wait-for-duration action makes the sprite wait for a period of time before doing anything else. This is most useful combined with a sequence action, to simulate pauses in the game.

```Swift
let waitAction = SKAction.wait(forDuration:0.5)
```

After declaring the action, include it in any sequence to create a delay.

### Run-Block Action

What if there's a piece of code you want executed as part of the game? Call it inside a run-block action and treat it as any other action — including it in a sequence:

```Swift
let updateScores = SKAction.run(){
  //update some variables here
}
let sequence = SKAction.sequence([actionMove, updateScores, waitAction, actionMove])
```

## Checkpoint: More Actions for AstroJunk

Next steps for AstroJunk:

1. Use actions to control the creation of debris and meteors.
   - Newly created debris and meteors should originate at random positions in the scene and move from top to bottom.
   - Make falling elements spin (rotate) as they fall. Try applying different spinning speeds to different elements.

**Stretch:** Add bombs to the game that grow and shrink while falling (pulsating).

## Collision Detection

So far you know how to get sprite nodes on screen and how to make them move, but nothing works as a playable game yet — because objects don't interact with each other. Collision detection is how you detect when objects collide, so you can decide what should follow: an explosion? Points added to the score? Collecting power-ups or lives? It's your job to handle all these possibilities.

There are several ways to handle collision detection. One uses SpriteKit's built-in physics engine, which we'll cover in a later lesson. For now, we'll use the easiest approach: bounding-box collision detection.

### The Main Idea

1. Get all the elements in the scene into a collection or list so you can check for collisions on each one. This is easy if you give every node a name at the moment of creation.
2. Use the method `enumerateChildNodes(withName:using:)` to find all the nodes matching a name.
3. Loop over the lists to check for collisions. Each node has a `frame` property representing its location on screen. This frame is a rectangle, so even round sprites have a rectangular bounding box.
4. With access to the frames, use `CGRect`'s `intersects(_:)` method to check for collisions.

### Scoring

A collision is also the natural place to award points. Add a score variable and an `SKLabelNode` to display it:

```swift
var score = 0
let scoreLabel = SKLabelNode(fontNamed: "Arial")

// in didMove(to:)
scoreLabel.position = CGPoint(x: size.width / 2, y: size.height - 50)
addChild(scoreLabel)

// wherever a collision awards points
score += 1
scoreLabel.text = "Score: \(score)"
```

## Checkpoint: Collision Detection in AstroJunk

Next steps for AstroJunk:

- Include collision detection using the frame bounding-box technique.
- Detect when the ship collides with either meteors or debris.
- Once the ship collides with objects, make the object disappear from the scene and update the score.
- Debug the frame of the objects to make the collision as realistic as possible. (Tip: if the bounding box looks larger than the sprite, shrink it with `insetBy(dx:dy:)`.)

## Touch Events

We need a way to interact with the game — a way to control how elements move in the scene. We can achieve this with touch events, using several methods:

```Swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {}

override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {}

override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {}

override func touchesCancelled(_ touches: Set<UITouch>, with event: UIEvent?) {}
```

These handle different stages in the lifetime of a touch: begins, moves, ends, and cancelled. Inside these methods you can access a set of touches — a set, because a scene can have multiple simultaneous touches.

Access the first touch in the set:

```Swift
guard let touch = touches.first else { return }
```

Or iterate through all of them:

```Swift
for touch in touches {
}
```

Every touch has a `location` property you can use to see where the touch happened in the scene:

```Swift
let location = touch.location(in: self)
```

There's a lot you can do with touches: have a sprite follow your touches, activate special powers, shoot enemies, and more.

## Checkpoint: Touch-Based Movement

Using touches, handle how the spaceship moves. It needs to move either left or right. Think about the best approach and implement it.

## Playable Area

The ship now moves across the screen. Depending on your implementation, the ship might go off the scene — but it needs to stay visible at all times. You can fix this several ways: have it bounce back, or stay at the edge of the screen. It's up to you.

Create a method to check the bounds of the game:

```Swift
func boundsCheckSpaceship() {
}
```

Grab the value of the limit that corresponds to the bottom-left and bottom-right of the screen:

```Swift
let bottomLeft = CGPoint(x: ship.size.width/2, y: 0)
let bottomRight = CGPoint(x: size.width - ship.size.width/2, y: 0)
```

Use these values to compare them with the ship's position to decide what to do. Think about where you should call this method.

**Stretch challenge:** Debug the playable area (make it visible with a rectangle) and check that it works the same across multiple screen sizes.

## Checkpoint: Class Organization

Before moving on, make sure each active element (SpriteKit node) in AstroJunk lives in its own separate class — i.e., separate class files for your `Spaceship`, `Meteor`, and `Debris` elements.

## Further Reading

- [Accessing and Modifying the Node Tree - from Apple docs](https://developer.apple.com/documentation/spritekit/sknode/accessing_and_modifying_the_node_tree)
- [Controlling Actions Precisely by Using Names - from Apple docs](https://developer.apple.com/documentation/spritekit/skaction/controlling_actions_precisely_by_using_names)
- [SKTransformNode - from Apple docs](https://developer.apple.com/documentation/spritekit/sktransformnode)
- `removeChildren(in:)`, `removeAllChildren()`

## Additional Resources

1. [Action Initializers - from Apple docs](https://developer.apple.com/documentation/spritekit/skaction/action_initializers)
2. [`group(_:)` method - from Apple docs](https://developer.apple.com/documentation/spritekit/skaction/1417688-group)
3. [`wait(forDuration:)` method - from Apple docs](https://developer.apple.com/documentation/spritekit/skaction/1417788-wait)
4. [`repeat(_:count:)` method - from Apple docs](https://developer.apple.com/documentation/spritekit/skaction/1417750-repeat)
5. [`repeatForever(_:)` method - from Apple docs](https://developer.apple.com/documentation/spritekit/skaction/1417676-repeatforever)
6. [SKNode > removeFromParent() - from Apple](https://developer.apple.com/documentation/spritekit/sknode/1483119-removefromparent)
7. [SKAction > removeFromParent() - from Apple](https://developer.apple.com/documentation/spritekit/skaction/1417748-removefromparent)
8. [How To Make a Game Like Space Invaders with SpriteKit and Swift: Part 2 - A tutorial from raywenderlich](https://www.raywenderlich.com/1163-how-to-make-a-game-like-space-invaders-with-spritekit-and-swift-part-2)

**Next:** [Lesson 3 - Selecting an Architecture](../03-Selecting-an-Architecture/Lesson3.md)
