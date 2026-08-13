# Lesson 7: Cameras

## Learning Objectives

- Implement cameras using `SKCameraNode`
- Create a moving background
- Identify different ways of creating moving backgrounds
- Practice refactoring old code

## Moving Background

Up to now AstroJunk has a static background image, if you chose that approach — it stays the same throughout the entire game. Many games use a moving background to give the player a sense of real movement, similar to how side-scrolling platformers scroll the background opposite the player's motion to reinforce a feeling of speed.

**Heads up:** the `backgroundNode()` factory below replaces your Lesson 1 background with two tiled copies that scroll and reposition themselves — it doesn't extend your existing single background node. Delete or stop using that node once this is working.

## How To?

- **Moving the background** — make all nodes in the scene that should move children of a background layer. Then, to scroll the game, move the background layer, and its children move with it.
- **Moving the camera** — use SpriteKit's `SKCameraNode` class. Add a camera node to the scene; the camera node's position represents the center of the current view.

## SKCameraNode

```swift
var cameraNode : SKCameraNode!
...
var cameraNode = SKCameraNode()
addChild(cameraNode)
camera = cameraNode
cameraNode.position = CGPoint(x: size.width/2, y: size.height/2)
```

Create the camera node instance in `didMove(to:)`.

We want the camera to move from bottom to top:

```swift
let cameraMovePointsPerSec: CGFloat = 0.6
...
func moveCamera() {
       cameraNode.position.y += cameraMovePointsPerSec
}
```

Call this in `update`. Try running at this point — you'll see everything start moving down, and end up with a black screen once all nodes go offscreen. That's expected; we'll fix it below.

### Creating the Background Node

```swift
func backgroundNode() -> SKSpriteNode {
    // 1
    let backgroundNode = SKSpriteNode()
    backgroundNode.anchorPoint = CGPoint.zero
    backgroundNode.name = "background"
    // 2
    let background1 = SKSpriteNode(imageNamed: "background")
    background1.anchorPoint = CGPoint.zero
    background1.position = CGPoint(x: 0, y: 0)
    background1.size = UIScreen.main.bounds.size
    backgroundNode.addChild(background1)
    // 3
    let background2 = SKSpriteNode(imageNamed: "background")
    background2.anchorPoint = CGPoint.zero
    background2.position = CGPoint(x: 0, y: background1.size.height)
    background2.size = UIScreen.main.bounds.size
    backgroundNode.addChild(background2)
    // 4
    backgroundNode.size = CGSize(width: background1.size.width , height: background1.size.height + background2.size.height)
    return backgroundNode
}
```

An easy way to achieve a scrolling effect: split the background into different images, and as one goes offscreen, reposition it to the end of the sequence.

```swift
for i in 0...1 {
  let background = backgroundNode()
  background.anchorPoint = CGPoint.zero
  background.position = CGPoint(x: 0, y: CGFloat(i)*background.size.height)
  background.name = "background"
  background.zPosition = -1
  addChild(background)
}
```

This creates two copies of the background and sets their positions so the second copy begins where the first ends.

```swift
var playableRect: CGRect
var cameraRect : CGRect {
  let x = cameraNode.position.x - size.width/2 + (size.width - playableRect.width)/2
  let y = cameraNode.position.y - size.height/2 + (size.height - playableRect.height)/2
  return CGRect(x: x, y: y, width: playableRect.width, height: playableRect.height)
}
override init(size: CGSize) {
    playableRect = CGRect(x: 0, y: 0, width: size.width, height: size.height)
    super.init(size: size)
}
```

These helper properties calculate the visible area.

```swift
enumerateChildNodes(withName: "background") { node, _ in
  let background = node as! SKSpriteNode
  if background.position.y + background.size.height <
    self.cameraRect.origin.y {
    background.position = CGPoint(
    x: background.position.x,
    y: background.position.y + background.size.height*2) }
}
```

For each background node, check whether its top edge is below the current visible playable area (i.e., offscreen). If so, move it up by double its height — placing it above the other visible background node. This code goes at the end of `moveCamera()`.

## Checkpoint: Run It

Try running the game now — you should have a moving background, though the scrolling may look rough, and all other nodes (ship, debris, meteorites) will go offscreen.

## Fixing All Nodes

```swift
ship.position.y = cameraNode.position.y - 200
```

The ship needs to stay onscreen — make its position depend on the camera node's position. Apply the same principle to the rest of the elements onscreen.

## Other Alternatives

Particle emitters can also be used for scrolling background effects:

![background](assets/backgroundemitter.gif)

Think about other ways you could achieve a parallax effect (multiple background layers scrolling at different speeds, for example).

## Checkpoint: Refactor

You've likely put a lot of code directly in `GameScene` by now — not the preferred practice. Refactor AstroJunk so its code is modular and easy to navigate: split responsibilities into separate files/classes (movement, spawning, scoring, background scrolling, etc.) rather than one large scene file.

This is the finishing touch on the core AstroJunk build from Lessons 1-7. From here, later lessons (Physics, SceneKit, GameKit) stand more on their own, and you'll move toward designing your own original game for the [final project](../../Assignments/Project.md).

**Next:** [Lesson 8 - SceneKit](../08-SceneKit/Lesson.md)
