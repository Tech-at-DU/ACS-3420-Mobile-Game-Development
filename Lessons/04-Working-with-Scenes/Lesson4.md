# Lesson 4: Working with Scenes

## Learning Objectives

1. Identify and describe:
   - what game mechanics are and how to implement them
   - the functional relationship between `SKView` and `SKScene`, and how to use them to create new scenes
   - how to create transitions between game scenes using `SKTransition`
2. Implement, in AstroJunk: game mechanics, including a Game Over scene that reacts to win/loss conditions (victory conditions)

## Game Mechanics

Read about [game mechanics](https://en.wikipedia.org/wiki/Game_mechanics). What stands out most in Wikipedia's definition of the term?

Then think about AstroJunk's current state:

- What game mechanisms are most obviously missing so far?
- What game mechanics would add the most value if you added them first? Does the order you develop mechanics in matter?

Some of the most obviously missing mechanisms at this point: a way to rack up points (for asteroid/bomb hits, debris slipping by the ship, or debris the ship collects), and victory conditions — which depend on that score accrual existing first. Also missing: game levels, and "juice" (sound effects, explosions on collision, etc. — covered in the next lesson).

## Working with Scenes

Up to now, you've been working with a single scene. That's fine for learning basic functionality, but to really engage users you'll want more than one scene can provide. Most games share a common set of standard features:

![loading](assets/loading.PNG)

A **loading scene** — displayed while other content loads.

![menu](assets/menu.PNG)

**Menus** — might show the game's logo, let the player start a new game or resume a paused one, navigate to another scene, show high scores or game rules, or choose a game mode.

![gameover](assets/gameover.PNG)

**Victory/loss conditions** — most games respond to "Game Over" or other win/lose conditions by presenting choices (scenes) to the user once gameplay ends.

![levels](assets/levels.PNG)

**Game levels** — a great way to engage users is to let them progress to a new level through mastery of previous ones; each level is typically its own scene (or set of scenes).

Learning how to work with scenes adds real depth to the user experience.

## Building Blocks

Scenes are the basic building blocks of games. Typically, you design self-contained scenes for the parts of your game, then transition between them as necessary. See Apple's [SKTransition docs](https://developer.apple.com/documentation/spritekit/sktransition) for reference as you read on.

### View Controllers

View controllers and their lifecycle methods play a key role in bringing your scenes to life. But remember: in an iOS game app, the view you present is *not* the usual `UIView` a view controller manages — it's a subclass of `SKView`, which has game-specific behaviors built in.

### SKScene

`SKScene` is a subclass of `SKEffectNode`, which is a subclass of `SKNode`. As the root node in a tree of SpriteKit nodes, an `SKScene` object organizes all of the active SpriteKit content, animating and rendering it for display.

```Swift
class SKScene : SKEffectNode
```

### SKView

To display a scene, you present it from an `SKView` object:

```Swift
class SKView : UIView
```

`SKView` is a `UIView` subclass that renders a SpriteKit scene, with game-based features above and beyond a plain `UIView`. An `SKView` lets you:

1. control the timing of the view's screen updates, including pausing a scene
2. configure performance-related toggles unique to your app
3. display metrics in the corner of the view for debugging, e.g.:
   - `showsFPS` — displays the current frame rate
   - `showsNodeCount` — displays the current count of `SKNode`s being displayed
   - `showsPhysics` — displays a visual representation of `SKPhysicsBody`s

### `presentScene()`

You present a scene by calling `presentScene(_:)` on the scene's `SKView` object:

```Swift
func presentScene(_ scene: SKScene?)
```

When called, the new scene immediately replaces the current one, if any.

### SKScene's Lifecycle Methods

`SKScene` comes with several functions you can override to be notified when the scene is loaded, presented, or changes size. Of these, the one you'll use most is `didMove(to:)`, which tells you when the scene is presented by a view:

```Swift
func didMove(to view: SKView)
```

When you call `presentScene(_:)`, SpriteKit calls your scene's `didMove(to:)` before presenting it in a view — a good place to do initial setup of your scene's contents.

## Creating Scenes

1. Create a new subclass of `SKScene` (be sure to `import SpriteKit`).
2. Implement its desired behavior, which can include:
   - `init(size:)` or a custom initializer
   - `update(_:)`
   - `didMove(to:)` and/or other `SKScene` lifecycle methods
   - functions for touches or movement
3. Load and present it wherever your app's requirements call for — often in a view controller's lifecycle method, or a function in the default `GameScene` class.

*Tip: for ideas on methods to implement in a new scene, review the overridden methods in the default `GameScene` class provided by Xcode's SpriteKit template.*

**Example** — a newly created `SKScene` subclass called `NewScene`, with several stubbed-out functions:

```Swift
import Foundation
import SpriteKit

class NewScene: SKScene {

    override init(size: CGSize) {
        // do initial configuration work here
        super.init(size: size)
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

    override func update(_ currentTime: TimeInterval) {
        // Called before each frame is rendered
    }

    override func didMove(to view: SKView) {
        /*
         Use this method to implement any custom behavior for
         your scene when it is about to be presented by a view.
         For example, you might use this method to create the scene's contents.
        */
    }
}
```

And a basic example of instantiating and presenting `NewScene` from a view controller's `viewWillAppear()`:

```Swift
override func viewWillAppear(_ animated: Bool) {
     let myNewScene = NewScene()
     myNewScene.size = self.view.bounds.size
     if let spriteView = self.view as? SKView { // cast self.view as an SKView before calling presentScene()
         spriteView.presentScene(myNewScene)
     }
 }
```

### The `scaleMode` Property

Because your scene might appear on screens of different sizes (iPhone, iPad, etc.), you need to determine how it should be sized to fit the `SKView`. This is controlled by the `scaleMode` property:

```Swift
var scaleMode: SKSceneScaleMode { get set }
```

`scaleMode` is backed by the `SKSceneScaleMode` enum, with these cases:

- `.fill`
- `.aspectFill`
- `.aspectFit`
- `.resizeFill`

The default value is `SKSceneScaleMode.fill`. See [scaleMode - Apple Docs](https://developer.apple.com/documentation/spritekit/skscene/1519562-scalemode) and [Scaling a Scene's Content to Fit the View](https://developer.apple.com/documentation/spritekit/skscene/scaling_a_scene_s_content_to_fit_the_view) for more.

## Changing Scenes (Transitions)

The simple, direct presentation shown above works fine, but most scenes benefit from a more dramatic transitional effect.

### SKTransition

`SKTransition` performs an animated transition to a new scene, providing continuity so the change isn't so abrupt:

```Swift
class SKTransition : NSObject
```

*Source: https://developer.apple.com/documentation/spritekit/sktransition*

**Example** — `scaleMode` property with a `crossFade` transition:

```Swift
myNewScene = NewScene(size: size)
myNewScene.scaleMode = .aspectFill

let crossFade = SKTransition.crossFade(withDuration: 0.5)
view?.presentScene(myNewScene, transition: crossFade)
```

### Creating Scenes with a Custom `init()`

Another common pattern is a custom initializer on your scene class. In the example below, we add one extra parameter — a boolean that's `true` if this is the player's first time playing, `false` otherwise — stored in a `firstTime` property:

```Swift
class GameLoadingScene: SKScene {
  let firstTime:Bool
  init(size: CGSize, firstTime: Bool) {
    self.firstTime = firstTime
    super.init(size: size)
  }
  required init(coder aDecoder: NSCoder) {
    fatalError("init(coder:) has not been implemented")
  }
}
```

And use that boolean flag to make conditional decisions at key points in the scene's lifecycle, such as `didMove(to:)`:

```Swift
override func didMove(to view: SKView) {
  if (!firstTime) {
    // If not first time, start regular game
  } else {
    // If players first time, do first time things (i.e., explain rules, offer video explaining gameplay, etc.)
  }
}
```

## Checkpoint: Game Over Scene

Follow the [Game Mechanics and Scenes activity](assignments/activity.md) to add a `GameOverScene` to AstroJunk, along with the counter and win/loss logic that triggers it.

## After This Lesson

**Challenge** — add a menu to AstroJunk's Game Over scene that, on winning, offers the player:
- "Replay"/"Restart" — replays the level just played
- "Next Level" — offers a new level (you'll need to create at least one additional level)
- "High Scores" — presents a list of the highest scores so far

**Get sound files ready for the next lesson** — you'll need background music and sound effects for game events (collisions, win/loss, etc.). Research what audio file types iOS supports, and gather files before starting Lesson 5.

## Further Reading

- The "Enabling Visual Statistics for Debugging" section of [SKView - from Apple docs](https://developer.apple.com/documentation/spritekit/skview)
- [SKSceneDelegate - from Apple docs](https://developer.apple.com/documentation/spritekit/skscenedelegate)
- [Gameplay - wikipedia](https://en.wikipedia.org/wiki/Gameplay)
- [Scaling a Scene's Content to Fit the View - from Apple docs](https://developer.apple.com/documentation/spritekit/skscene/scaling_a_scene_s_content_to_fit_the_view)
- SpriteKit's Scene Editor: [Introduction to the SpriteKit Scene Editor - a tutorial by Ray Wenderlich](https://www.raywenderlich.com/620-introduction-to-the-spritekit-scene-editor) and [Creating a Scene from a File - Apple docs](https://developer.apple.com/documentation/spritekit/skscene/creating_a_scene_from_a_file)

## Additional Resources

1. [Game mechanics - wikipedia](https://en.wikipedia.org/wiki/Game_mechanics)
2. [SKView - Apple Docs](https://developer.apple.com/documentation/spritekit/skview)
3. [SKTransition - Apple Docs](https://developer.apple.com/documentation/spritekit/sktransition)
4. [Drawing SpriteKit Content in a View - Apple Docs](https://developer.apple.com/documentation/spritekit/drawing_spritekit_content_in_a_view)
5. [Nodes for Scene Building - Apple Docs](https://developer.apple.com/documentation/spritekit/nodes_for_scene_building)
6. [scaleMode - Apple Docs](https://developer.apple.com/documentation/spritekit/skscene/1519562-scalemode)
7. [SpriteKit – Understanding SKScene scaleMode - an article](https://infinitecortex.com/2014/01/spritekit-understanding-skscene-scalemode/)
8. [`didMove(to:)` - Apple Docs](https://developer.apple.com/documentation/spritekit/skscene/1519607-didmove)
9. [Transitioning Between Two Scenes - Apple Docs](https://developer.apple.com/documentation/spritekit/sktransition/transitioning_between_two_scenes)

**Next:** [Lesson 5 - Adding Juice](../05-Juice/Lesson5.md)
