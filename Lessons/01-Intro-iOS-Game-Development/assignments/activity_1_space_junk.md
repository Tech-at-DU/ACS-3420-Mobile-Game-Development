# Space Junk Removal

## Your Assignment
Your client, Intergalactic Rubbish Removal, Inc., is on a mission to rid the cosmos of the massive accumulation of [space junk](https://en.wikipedia.org/wiki/Space_debris).

To gain support for their cause, they have commissioned you to create a free iOS game app to illustrate to children of all ages the process and pitfalls of collecting space garbage.

### Requirements/characteristics
The final released version of the game will have the following characteristics:

1. App Name: AstroJunk
2. Protagonist (user entity) &mdash; User will pilot a single spaceship: an interstellar garbage scow
3. Game Type &mdash; game will be similar to a **Fixed Shooter**<sup>1</sup> game (ie, like the classic Space Invaders game):
- Ship is restricted to movement along the X axis
- Target objects move in only one direction (top to bottom) and only originate (descend) from the top of the screen
- Each level of the game is contained within a single screen
3. Game Play:
- The objective of the game is to collect as many pieces of floating space junk as possible, while avoiding collisions with hostile objects (meteorites or alien bombs)
- Points are awarded for each piece of debris captured, and the total score tabulated and presented on screen
- If pilot fails to avoid a hostile object, the collision of the object with the ship will result in the ship exploding &mdash; and Game Over!

<!-- TODO: rewrite this as a User Story? -->

### Setup

1. Xcode → File → New → Project → **iOS → Game** template
2. Interface: **Storyboard**, Language: **Swift**, Game Technology: **SpriteKit**
3. Product Name: `AstroJunk`
4. Delete the placeholder `Spaceship` node/logic Xcode drops into the template's `GameScene.swift`
5. Drag [these images](../assets/gameAssets.zip) (or your own) into `Assets.xcassets`

### Build it in code, not the Scene Editor

Xcode gives you two ways to place nodes: the `.sks` Scene Editor (drag and drop) or code (`didMove(to:)`). **Use code.** Every later lesson — spawning meteors, subclassing nodes, scrolling backgrounds — assumes your nodes were created in code, and reworking a Scene-Editor setup later costs you more time than it saves now.

### Fix Three Template Defaults First

The Xcode template ships with defaults that cause confusing bugs if you leave them as-is. Fix these before placing any sprites:

1. **Skip loading `GameScene.sks` entirely.** It still has a leftover "Hello, World!" label baked in, and its canvas size (set at design time, often landscape) won't match your device's actual screen — so anything positioned with `size.width/2` can end up in the wrong place. In `GameViewController.swift`, replace the line that loads `SKScene(fileNamed: "GameScene")` with:

   ```swift
   let scene = GameScene(size: view.bounds.size)
   scene.scaleMode = .resizeFill
   view.presentScene(scene)
   ```

   Now `scene.size` always matches the real screen, on any device.

2. **Set the origin explicitly.** A freshly created `SKScene` defaults to `anchorPoint = (0.5, 0.5)` — `(0, 0)` lands at the *center* of the screen, not the bottom-left shown in the lesson's diagram. Set it in `didMove(to:)` so the rest of the course's positioning math (which assumes bottom-left) works as written:

   ```swift
   self.anchorPoint = .zero
   ```

3. **Set `zPosition` on everything.** Unlike UIKit views, two SpriteKit nodes with the same `zPosition` (the default is `0` for every node) don't reliably draw in the order you added them — you can add your background last and still see it appear behind the ship. Give every node an explicit `zPosition`: background furthest back, gameplay elements in the middle, UI on top.

### Asset Reference

`gameAssets.zip` uses non-obvious file names — here's what's in it:

| File | Suggested use |
|---|---|
| `playerShip2_red.png` | ship |
| `meteorBrown_big3.png`, `meteorGrey_med1.png`, `meteorGrey_big4.png` | meteorites |
| `wingRed_2.png`, `wingGreen_6.png`, `beam0.png` | space debris |
| `vectorstock_16606572.png` | background |

### TODO: For this phase of the game

Applying the concepts from this lesson (creating a sprite, setting its `position`, `addChild`), create one static instance of each of these in `didMove(to:)`:

- background scene
- meteorites
- space debris
- space ship

Here's the pattern for the ship — repeat it for the other three:

```swift
let ship = SKSpriteNode(imageNamed: "playerShip2_red")
ship.position = CGPoint(x: size.width / 2, y: 100)
ship.zPosition = 1
addChild(ship)
```

These are placeholders to confirm your assets and positioning work — no motion yet. **Heads up:** meteors and debris don't stay single static instances for long. Lesson 2 replaces this one-off placement with a spawner that creates them repeatedly at random positions, so don't over-invest in exact placement here.

### Example

How the game will look and behave at this point.
![gameStage1](../assets/game.png)


## Additional Resources

1. [Fixed Shooter Game - wikipedia](https://en.wikipedia.org/wiki/Shoot_%27em_up#Fixed_shooters) 
2. [Space Invaders - wikipedia](https://en.wikipedia.org/wiki/Space_Invaders)
