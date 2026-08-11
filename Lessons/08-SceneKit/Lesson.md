# Lesson 8: SceneKit

## Learning Objectives

- The role of SceneKit in game development
- Create a new project with SceneKit
- Make use of the built-in functions and available geometry
- Apply physics
- Make use of materials
- Understand how the rendering loop works and use it correctly

## SceneKit

"SceneKit combines a high-performance rendering engine with a descriptive API for import, manipulation, and rendering of **3D assets**."

## Everything Is a Node

SceneKit implements content as a **hierarchical tree structure of nodes**, also known as a **scene graph**. A scene consists of a root node, which defines a coordinate space for the world of the scene, and other nodes that populate the world with visible content.

These other nodes are the basic building blocks for the scene:
- 💡 lights
- 🎥 cameras
- 🔺 geometry
- particle emitters

![nodes](assets/nodes.png)

## Coordinates

![coordinates](assets/coordinates.png)

**Class `SCNScene`** — a container for the node hierarchy and global properties that together form a displayable 3D scene.

**Class `SCNView`** — a view for displaying 3D SceneKit content, a subclass of `UIView` on iOS.

The `SCNScene` class represents a scene. We display the scene onscreen inside an instance of `SCNView`.

## Getting Started

1. Create a new SceneKit project. Call it "Shapes."
2. Build & run. See what appears on screen.
3. Try dragging around.

### Exploring the Example

Before writing anything, poke around the template Xcode gave you:
1. What's going on in `GameViewController.swift`?
2. What is `art.scnassets`?
3. What is `ship.scn`?

### A Little Cleanup

1. Remove the `art.scnassets` folder.
2. Change the content of `GameViewController` to:

```swift
class GameViewController: UIViewController {
  override func viewDidLoad() {
    super.viewDidLoad()
  }
  override var shouldAutorotate: Bool {
    return true
  }
  override var prefersStatusBarHidden: Bool {
    return true
  }
}
```

3. Add a property for the view: `var scnView: SCNView!`
4. Add a method to set it up:

```swift
func setupView() {
  scnView = self.view as? SCNView
}
```

5. Do the same for the scene: `var scene: SCNScene!`

```swift
func setupScene() {
  scene = SCNScene()
  scnView.scene = scene
}
```

6. Call both setup methods in `viewDidLoad`.

This creates an instance of `SCNScene`, stores it in `scene`, and sets it as the one the view uses. That's the clean starting point — the rest is up to you.

## Checkpoint: Bouncing Shapes

Follow the [Bouncing Shapes instructions](assets/instructions.md). You'll need the empty-scene project you just set up.

## Scene Statistics

- **fps**: frames per second — the total number of consecutive frame redraws per second. The lower, the more poorly the game is performing. Aim for around 60fps for smooth-looking games.
- **◆**: total draw calls per frame — the number of visible objects drawn per frame. Lights affecting objects can increase this number.
- **(triangle)**: total polygons per frame — the total polygons used to draw a single frame across all visible geometry.
- **✸**: total visible light sources — recommended to stay under 3 at a time.

![stats](assets/stats.png)

**Frame time**: the total time it took to draw a single frame. A frame time of 16.7ms is required for 60fps. **The color chart**: frame time percentage breakdown per component.

## Checkpoint: Handle All Shapes

Right now a switch statement does the same thing for every case, no matter the random value. Improve it to handle all the shapes in the `ShapeType` enum. You'll need to know the parameters required to initialize each geometric shape — check [SceneKit's geometry documentation](https://developer.apple.com/documentation/scenekit/scngeometry).

## Physics

Physics bodies work similarly to SpriteKit. A physics body describes all the physical properties of a node: **shape, mass, friction, damping, and restitution**. The physics engine uses this to simulate real-world physics interactions — gravity, friction, and collisions with other bodies.

### Body Types

- **Static** — doesn't move, unaffected by forces.
- **Dynamic** — moved by the physics engine in response to forces/collisions.
- **Kinematic** — not automatically moved by the physics engine in response to forces/collisions.

### Adding a Physics Body

```swift
geometryNode.physicsBody = SCNPhysicsBody(type: .dynamic, shape: nil)
```

Passing `nil` for the physics shape tells SceneKit to auto-generate a shape based on the node's visual geometry. Build and run.

### Applying Forces

```swift
applyForce(direction: at: asImpulse:)
```

Pass an `SCNVector3` for both the force and the position where you want to apply it, and whether the force should be applied as an impulse. An **impulse** applies the force **only once**. Forces that aren't impulses are applied **at each step in the physics simulation** — SceneKit adds up all applied forces on the object and accelerates the body according to the net result.

```swift
let randomX = Float.random(in: -2...2)
let randomY = Float.random(in: 10...18)
let force = SCNVector3(x: randomX, y: randomY , z: 0)
let position = SCNVector3(x: 0.05, y: 0.05, z: 0.05)
geometryNode.physicsBody?.applyForce(force, at: position, asImpulse: true)
```

Build and run.

## Checkpoint: Bouncing Shapes, Part 2

Follow the [Bouncing Shapes Part 2 instructions](assets/instructions2.md), continuing with the project you've been using so far. This covers giving shapes materials/color, the SceneKit render loop and its `SCNSceneRendererDelegate`, and spawning/removing shapes over time.

## After This Lesson

Find out about **torque**, the other force that can be applied to shapes. What's a good use case for it?

## Additional Resources

1. [Scenes and nodes](https://developer.apple.com/documentation/scenekit/organizing_a_scene_with_nodes)
2. [SceneKit](https://developer.apple.com/documentation/scenekit)
3. [SCNScene](https://developer.apple.com/documentation/scenekit/scnscene)
4. [Render Loop](https://developer.apple.com/documentation/scenekit/scnscenerendererdelegate)
5. [How to make a game like Stack](https://www.raywenderlich.com/670-how-to-make-a-game-like-stack)
6. 3D Apple Games by Tutorials (book)

**Next:** [Lesson 9 - GameKit](../09-GameKit/Lesson.md)
