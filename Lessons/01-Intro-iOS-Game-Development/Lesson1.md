# Lesson 1: Intro to iOS Game Development

## Learning Objectives

- Identify SpriteKit's role in iOS game development
- Create and position nodes
- Describe how frames, frame rate, and the game loop relate to each other
- Move nodes with actions

## Why Build Games with iOS

For a game developer, there's no better platform. The development tools are well-designed and easy to learn. Plus, the App Store makes it simple to distribute your game to a massive audience — and get paid for it.

Start by reading [iOS Game Development: An Evolution](assignments/Intro.md), a short history of how iOS game frameworks got to where they are today (OpenGL ES → Cocos2D → SpriteKit).

## Nodes (Sprites)

Nodes are the building blocks of SpriteKit.

A **node** is an object that can be put inside a scene. Nodes in SpriteKit are commonly referred to as **sprites**. `SKNode` is the base class of all nodes — every onscreen asset is an `SKNode` or a subclass of it.

Example subclasses of `SKNode`:
- `SKScene` - an object that organizes all of the active SpriteKit content
- `SKShapeNode` - renders a shape defined by a Core Graphics path
- `SKVideo` - displays video content
- `SKLabel` - displays a text label

### Node Properties

SpriteKit offers many different node types, but they all share a common set of key properties inherited from `SKNode`:

- `position` (CGPoint)
- `xScale` (CGFloat): the horizontal scale of a node
- `yScale` (CGFloat): the vertical scale of a node
- `alpha` (CGFloat): the node's transparency
- `hidden` (Bool): whether the node should be visible
- `zRotation` (CGFloat): the angle, in radians, the node should be rotated
- `zPosition` (CGFloat): determines which nodes display on top of other nodes in the scene

### SKSpriteNode

The most common `SKNode` subclass you'll use is `SKSpriteNode`. An `SKSpriteNode` is a type of node that can display either a colored rectangle or an image.

### SKScene

An instance of `SKScene` represents an active scene of content in SpriteKit. A scene is the **root node** in a tree of SpriteKit nodes — this tree provides the content that the scene animates and renders for display.

### Positioning

A sprite's position is controlled by its `position` property, a `CGPoint` (a simple struct with `x` and `y`):

```Swift
public struct CGPoint {
  public var x: CGFloat
  public var y: CGFloat
  // ...
}
```

By default, SpriteKit positions sprites so they are centered at `(0, 0)`, which in SpriteKit represents the bottom left of the screen.

**Important:** Unlike the coordinate systems in UIKit and AppKit, where `0` on the y-axis is at the top-left, in SpriteKit `0` on the y-axis is at the **bottom-left** of the screen:

![XandY_in_spritekit](assets/XandY_in_spritekit.png)

*Source: https://hackernoon.com/swift-spritekit-basics-94b1798ab639*

**Simple example** — creating a sprite and setting its `position` property:

```Swift
// create sprite
let myCoolSprite = SKSpriteNode(color: SKColor.red,
                        size: CGSize(width: 64, height: 64))

// set sprite's position
myCoolSprite.position = CGPoint(x: 100, y: 100)
```

### Positioning Notes

1. When you set the position of a sprite, by default you are positioning the center of the sprite (this can be changed by setting the sprite's `anchorPoint`).
2. A sprite's position is within the coordinate space of its parent node. The position of any node is relative to the position of its parent's `anchorPoint`. This means you can add sprites as children of other sprites, and the child sprites will move with their parents.

## Creating a Sprite

To create a sprite and display it to your users:

1. Create an instance of `SKSpriteNode`
2. Configure its `size` and `position` properties
3. Add it to an `SKScene` object

Sprites are not visible unless they are inside an `SKScene` object — add them as children of the scene by calling the `addChild(_:)` method on the `SKScene` object.

```swift
// create sprite
let myCoolSprite = SKSpriteNode(color: SKColor.red,
                        size: CGSize(width: 64, height: 64))

// set sprite's position
myCoolSprite.position = CGPoint(x: 100, y: 100)

// add sprite as a child of a scene
myScene.addChild(myCoolSprite)
```

**Checkpoint:** Create a new Xcode project (iOS App with the SpriteKit template) and add a simple sprite in the middle of the screen.

## Frame Rate & the Game Loop

When we think about "real-time" game behaviors, you might imagine objects — players, vehicles, and so on — moving around the screen in what appears to be continuous motion.

What's really happening is that the screen redraws itself roughly every 1/60th of a second, and each time it redraws, the positions of some or all objects change slightly. Done quickly enough, this fools the human eye into perceiving continuous movement.

**Frames** — a concept borrowed from movie and video production. In an iOS game app, each individual picture drawn on screen is called a **frame**, just as each individual still image in a movie, animation, or video is called a frame.

![horse_in_motion_frames](assets/horse_in_motion_frames.png)

**Frame rate** — games typically try to draw frames at either 30 or 60 times per second and aim to keep that rate consistent so animations appear smooth. This rate is called the **frame rate**, measured in **frames per second (FPS)**. A low frame rate means your game is performing poorly for the user.

Understanding frame rates is a critical (and sometimes complex) part of optimizing an iOS game's performance. Read [Notes on Frame Rates](assignments/frame_rates.md) as background, then write down the 3 takeaways you consider most important.

**The Game Loop** — like most game engines, SpriteKit runs an endless rendering loop, often called a "game loop," to update and render (redraw) the screen.

![game_loop_frame-cycle_functions](assets/game_loop_frame-cycle_functions.png)

*Source: https://developer.apple.com/documentation/spritekit/skscene/responding_to_frame-cycle_events*

Steps in rendering each frame typically include:
1. Updating the scene & its objects
2. Evaluating actions
3. Simulating physics
4. Applying constraints
5. Rendering the scene

To optimize game performance — including frame rate — you'll want to interact with the game loop to manage node behavior within a scene. Example: objects that move out of sight still consume memory, which impacts performance. The game loop is an excellent point at which to evaluate status and remove objects no longer needed.

### The `update(_:)` Function

The `update(_:)` function performs app-specific logic to update your scene. You override it to perform per-frame game logic. It's called exactly once per frame, *before* any actions are evaluated and any physics are simulated.

Because it's called each frame, it's an excellent place to interact with the game loop and update the positions of your nodes:

```swift
override func update(_ currentTime: TimeInterval) {

    //TODO: Update nodes in this scene

}
```

## Checkpoint: Creating the AstroJunk App

Read and follow the [Space Junk](assignments/activity_1_space_junk.md) instructions to create your first app, AstroJunk 🛰🚀. This is the project you'll keep building on through Lessons 2-4.

## Movement

### The 2D Coordinate System

In 2D graphics, we deal with space that only has two dimensions: the X and Y axes.

![2D_coordinate_system](assets/2D_coordinate_system.png)

- X axis — the horizontal, left-to-right axis
- Y axis — the vertical axis, running from bottom to top

We call this the "2D coordinate system." To describe a specific location, known as a *point*, in a 2D coordinate space, you only need two numbers:

- X coordinate — the distance the point is from the origin on the horizontal axis
- Y coordinate — the distance the point is from the origin on the vertical axis

We typically write coordinates in parentheses: `(x coordinate, y coordinate)`. A point 7 units right of the origin and 4 units above it is written `(7,4)`. The central point of a coordinate system used in graphics is called the *origin*, written `(0,0)`.

### Movement with 2D Vectors

**In mathematics and physics**, a *vector* is a geometric object that has *magnitude* (length) and *direction*, frequently represented by a line segment with a direction — or graphically as an arrow — connecting an initial point `A` to a terminal point `B`:

![vector](assets/vector.png)

Vectors play an important role in physics: the velocity and acceleration of a moving object, and the forces acting on it, can all be described with vectors.

**In computer programming**, in its simplest definition, a *vector* is a complex value (or data structure) composed of two or more simple values (components). In game apps, we commonly use vectors to describe:
- on-screen positions (coordinates)
- velocities

Some languages represent 2D vectors as simple arrays, but in iOS it's useful and common to represent a 2D vector as a `CGPoint`. Because a `CGPoint`'s `x` and `y` are both `CGFloat`, it lends itself readily to calculations with other `CGFloat`s. `CGPoint` is used so often to describe vectors in iOS that Apple created `CGVector`, which is nearly identical under the hood.

**Position (or point) vectors** — in a 2D system such as UIKit or SpriteKit, you use a coordinate point to describe the position of a view or sprite on the x and y axes:

```Swift
let spritePosition = CGPoint(x: 3, y: 5)
```

An empty 2D vector — one with only zeros for each coordinate — can be written two ways:

```Swift
var velocity = CGPoint(x: 0, y: 0)
```
```Swift
var velocity = CGPoint.zero
```

**Velocity vectors** — vectors can also store *velocities*. A velocity represents how far a location changes over time. A 2D velocity vector represents a *direction* and a *length* (its *magnitude*).

For example: if an object is moving 4 units to the right and 6 units upward every second, you could write its velocity as `[4, 6]`. Every second, you could add the object's velocity to its current position.

![velocity_vector](assets/velocity_vector.png)

*Graph depicting a velocity vector from point `[0, 0]` to point `[4, 6]`.*

**Direction** — the line from the origin point `[0, 0]` to `[4, 6]` gives us the **direction** of the vector.

**Calculating vector length** — for the object above with velocity vector `[4, 6]`, what distance will it travel in a given second? To calculate the *length* (*magnitude*) of a vector: square each component, add the squares together, and take the square root of the result.

![calculate_vector_length_formula](assets/calculate_vector_length_formula.png)

```Swift
let vector = CGPoint(x: 5, y: 7)
let length = sqrt(vector.x * vector.x + vector.y * vector.y)

// 8.602325267042627
```

**Vector translation** — when you want to move an object closer to or farther from its origin point, you add its position and velocity vectors together. Adding two vectors — known as *vector translation* — means summing the respective components of each vector: the `x` coordinates of both, then the `y` coordinates.

```Swift
let startingPosition = CGPoint(x: 1, y: 4)
let velocity = CGPoint(x: 2, y: 3)

let newPosition = CGPoint(x: startingPosition.x + velocity.x,
                          y: startingPosition.y + velocity.y)

// newPosition coordinates: [3, 7]
```

**Checkpoint:** Experiment with the code in the [Vector playground](playgrounds/Vector.playground) to build your intuition for working with vectors before moving on.

## Movement with Actions

The manual approach to moving a node — setting its position over time — gives you power and control over movement. But you can move, change, rotate, or scale a sprite's position over time incrementally, and much more easily, using SpriteKit **actions** — usually with a single line of code. Actions also let you easily create combinations of movements by chaining them together.

### SKAction

`SKAction` is a powerful class used to bring nodes to life. Instances of `SKAction` change the structure or content of a node in some way — they represent an animation that a node in the scene executes. You can use `SKAction` objects to make *any* node in the scene perform an action: something that changes a property like position, size, transparency, or color. Actions can also change the node tree, play sounds, or execute custom code.

Example uses:
- change a node's position (or other property) over time
- change the behavior of the scene itself, such as a fadeout

You can also combine several actions together in:
- A sequence action
- A group action
- A repeating action

### How to Implement

To apply an action to a node:

1. Create an instance of `SKAction`
2. Run it by calling one of the built-in `run(_:)` functions on the node itself, passing in the action instance

**Creating actions** — call one of the many built-in static constructors (factory methods) of the `SKAction` class. This example uses `moveBy(x:y:duration:)` to make a sprite move `2` units along the x-axis and `3` units along the y-axis in `1` second:

```Swift
let simpleMoveAction = SKAction.moveBy(x: 20, y: 300, duration: 1)
```

Notice this is essentially the same motion you get from creating position and velocity vectors and adding them together — but in one line of code.

**Running actions** — once created, run an action on *any* `SKNode` object by invoking one of its `run(_:)` functions:

```Swift
myNode.run(simpleMoveAction)
```

You can also create your `SKAction` once and call `run(_:)` on several different nodes to apply the same action to each.

## Move Actions

Several `SKAction` methods let you easily move a node relative to its current position. The simplest is [`move(to:duration:)`](https://developer.apple.com/documentation/spritekit/skaction/1417768-move), which moves a node to a new position:

```Swift
class func move(to location: CGPoint,
       duration: TimeInterval) -> SKAction
```

Example:

```Swift
let newPosition = CGPoint(x: 100, y: 100)
let moveBottomLeftAction = SKAction.move(to: newPosition, duration:3.0)
node.run(moveBottomLeftAction)
```

Noteworthy variations:

1. `moveTo(x:duration:)` — moves a node horizontally.
2. `moveTo(y:duration:)` — moves a node vertically.

These let you specify a change in only one axis, which can save you keystrokes.

3. `moveBy(x:y:duration:)` — moves a node relative to its current position, by an offset, wherever it currently is. This pattern of `<action>To` and `<action>By` variations appears in many [Action Initializers](https://developer.apple.com/documentation/spritekit/skaction/action_initializers) on `SKAction`. `<action>By` functions are preferred when it matters because they are **reversible** (more on reversible actions in the next lesson).

## Sequence Actions

Many actions run over time by themselves — but what if you want several actions to occur immediately or in combination, run custom code, or remove a node from a scene when some other action completes?

The real power of `SKAction` lies in how easily you can combine actions with `sequence(_:)` or `group(_:)`. A `sequence` is an `SKAction` that runs other actions one after another. `SKAction.sequence(_:)` creates an action that runs a collection of actions in order.

It takes an array of `SKAction`s and runs them one after the other in listed order. The first action starts and runs to completion, then the next, and so on until all actions in the sequence have executed.

```Swift
let sequenceAction = SKAction.sequence([action1, action2, action3])
```

```Swift
let moveUpAction = SKAction.moveBy(x: 0, y: 30, duration: 2)
let moveDownAction = SKAction.moveBy(x: 0, y: -30, duration: 2)
let removeAction = SKAction.removeFromParent()
let sequenceAction = SKAction.sequence([moveUpAction, moveDownAction, removeAction])
node.run(sequenceAction)
```

In this example, the node moves up the y-axis by `30` points, then down by `30`, then is removed from the scene.

The sequence action is one of the most useful and commonly used actions in iOS game development. It's also reversible — reversing it creates a new sequence action that reverses the order of the actions.

## Checkpoint: Adding Movement to AstroJunk

Continue working on your AstroJunk app, adding movement to all nodes created so far. At the end of this checkpoint, your game should be in this state:

- The ship moves along the X axis, but when it hits the screen edge it will go off screen if moved too far left or right (we'll fix that boundary check in a later lesson).
- All sprites/nodes have their intended motions implemented.
- No collision detection is implemented yet — when objects collide, nothing happens (that's next lesson).

If you get stuck, review the material above, especially the [Vector playground](playgrounds/Vector.playground), if working with vectors is the obstacle.

## Further Reading

- The "Nodes that Draw" section in [Nodes for Scene Building - Apple docs](https://developer.apple.com/documentation/spritekit/nodes_for_scene_building)
- The "Use a Scene Delegate Instead of Subclassing a Scene" section in [Responding to Frame-Cycle Events](https://developer.apple.com/documentation/spritekit/skscene/responding_to_frame-cycle_events), [SKSceneDelegate](https://developer.apple.com/documentation/spritekit/skscenedelegate), and [Subclassing Scenes Versus Assigning a Delegate](https://developer.apple.com/documentation/spritekit/skscene/subclassing_scenes_versus_assigning_a_delegate)
- [preferredFramesPerSecond - Apple Docs](https://developer.apple.com/library/archive/documentation/3DDrawing/Conceptual/MTLBestPracticesGuide/FrameRate.html)
- Delta Time, `SKEffectNode`, the `SKWarpable` protocol, `SKTexture`, `anchorPoint`, `SKView`, `SKRenderer`

## Additional Resources

1. [SpriteKit - Apple Docs](https://developer.apple.com/documentation/spritekit)
2. [Introducing SpriteKit - A Tutorial](https://code.tutsplus.com/tutorials/introducing-spritekit--cms-28648)
3. [OpenGL ES - wikipedia](https://en.wikipedia.org/wiki/OpenGL_ES)
4. [OpenGL ES: The Standard for Embedded Accelerated 3D Graphics - The Khronos Group](https://www.khronos.org/opengles/)
5. [About OpenGL ES - Apple Docs](https://developer.apple.com/library/archive/documentation/3DDrawing/Conceptual/OpenGLES_ProgrammingGuide/Introduction/Introduction.html)
6. [Metal - Apple Docs](https://developer.apple.com/metal/)
7. [Cocos2d - Cocos2d](http://cocos2d.org)
8. [SKNode (class) - Apple Docs](https://developer.apple.com/documentation/spritekit/sknode)
9. [Responding to Frame-Cycle Events - Apple Docs](https://developer.apple.com/documentation/spritekit/skscene/responding_to_frame-cycle_events)
10. [Key frame - wikipedia](https://en.wikipedia.org/wiki/Key_frame)
11. [State (pattern) - from Game Programming Patterns](https://gameprogrammingpatterns.com/state.html)
12. [Persistence of vision: how does animation work? - an article](https://www.futurelearn.com/courses/explore-animation/0/steps/12222)
13. [Coordinate system - wikipedia](https://en.wikipedia.org/wiki/Coordinate_system)
14. [Vector (mathematics and physics) - wikipedia](https://en.wikipedia.org/wiki/Vector_(mathematics_and_physics)), [Vector space - wikipedia](https://en.wikipedia.org/wiki/Vector_space), [Euclidean vector - wikipedia](https://en.wikipedia.org/wiki/Euclidean_vector)
15. [Vector Algebra - an article](https://www.mathsisfun.com/algebra/vectors.html)
16. [Pythagoras' Theorem - an excellent resource for understanding vectors, etc.](https://www.mathsisfun.com/pythagoras.html)
17. [Practical use of Vector Math in Games - an article](https://www.gamedev.net/articles/programming/math-and-physics/practical-use-of-vector-math-in-games-r2968/)
18. [SKAction (class) - Apple Docs](https://developer.apple.com/documentation/spritekit/skaction)
19. [Action Initializers - Apple Docs](https://developer.apple.com/documentation/spritekit/skaction/action_initializers)
20. [Getting Started with Actions](https://developer.apple.com/documentation/spritekit/getting_started_with_actions#2982305)
21. [`sequence(_:)` (function) - Apple Docs](https://developer.apple.com/documentation/spritekit/skaction/1417817-sequence)

**Next:** [Lesson 2 - Actions, Touch Events, Collisions](../02-Actions/Lesson2.md)
