# Lesson 5: Adding Juice

## Learning Objectives

- Define game feel, or "juice"
- Work with SpriteKit's particle effects to create special effects
- Implement, in AstroJunk:
  - background music
  - sound effects, in response to game events/mechanics
  - SpriteKit particle systems to add explosions/fire in response to game entity events (collisions, etc.)

## Adding Juice

The key to making a popular game app is polish: adding loads of well-considered details that set your game apart and bring it to life. To push your game to the next level — to really entertain and delight users, to get them to engage — you'll want to add what's known as "juice."

Adding juice means tricking out your game with special effects such as:

- Scenes and scene transitions
- Simple animation (scaling, rotation, movement, and more)
- **Music and sound effects**
- Game mechanics (including victory or game over conditions)
- **Particle effects** (explosions, collisions, fire, etc.)

When every interaction in your game world results in a cascade of visual and audible feedback, it adds real depth to the user experience and keeps players coming back. As you develop your game, you'll get in the habit of adding juice to it as you go.

### But What Is It, Really?

"Juicing is about taking a game that works and adding layers of satisfaction to improve game feel. Satisfaction is created by the senses — every visual and auditory input has the ability to make something virtual work in a way that's more believable." Watch this short explainer on the concept: [Juice it or lose it](https://www.youtube.com/watch?v=216_5nu4aVQ).

Animations, when done right, inject a sense of movement and reality into games. It's what makes them look alive and enjoyable. But juice should always support your core gameplay — you can't add effects at random; they should be well thought out. A good way to think about it: **what do you want the player to feel?**

For example, in a story-telling or horror game you'd want sounds and animations that build mystery and suspense; in an action game, you'd want more joyful or bouncy animations.

### Juicy Effects

How you choose to add juice depends on the game, but common examples include:

- An effect when a collision happens
- Dust when a character lands a big jump
- Sparkles after a collision
- Small pauses
- A trail to reinforce movement
- Animating scale and rotation

**Checkpoint:** Play a couple of games on your phone that you consider well-polished, and pay attention to what adds juice — animations, sound cues, particle effects, screen shake. Note down 3-5 specific juice techniques you noticed and consider whether AstroJunk could use something similar.

## Sound

George Lucas, creator of the *Star Wars* movies, once said: "The sound and music are 50% of the entertainment in a movie." It's just as important in game apps. One of the biggest mistakes a game developer can make is underestimating the power of audio — great sound effects and background music immerse players in the game's world.

### Sound in SpriteKit Apps

iOS supports three built-in mechanisms for sound effects and music in SpriteKit:

1. **`AVAudioPlayer`** — part of the `AVFoundation` framework. Provides playback of audio data from a file or memory. Gives you the most power and control, and requires the most work to implement.
2. **`SKAction`** — lets you create sound *actions*, subclasses of `SKAction` you can use like any other action (including participation in group or sequence actions). Best for short, one-time audio clips (not for background music or other long-playing files). Sound actions are instantaneous, making them useful for chaining or reusing `SKAction` objects.
3. **`SKAudioNode`** — a subclass of `SKNode` that plays audio. Good for background music or other audio you want to start and stop, or for sound FX. Because it's a subclass of `SKNode`, you can also manipulate it with `SKAction` objects. Affords more power and control than `SKAction`, but has fewer controlling functions than `AVAudioPlayer`.

### Adding Background Music

Music plays an important role in setting the pace and emotional tone of a movie — the same is true in game apps. Adding background music is easy, but there are important considerations.

1. **For long-running files** — use `AVAudioPlayer` for background music.
2. **Prevent sound delays** — load files ahead of time to avoid playback delays (weighed against memory: lots of long-running sound files eat up memory). Typically music and large audio files should be streamed if possible; small sound effects are better preloaded into memory for fast playback.
3. **Create utility classes** — for handling audio, including `AVAudioPlayer` functions, loading sound files, and so on.
4. **Use Asset Catalogs** — create a sound folder inside an asset catalog to organize and load audio files.

Two implementation options: `AVAudioPlayer` gives more power and control; `SKAudioNode` is easier to implement. See [example implementations](Sound.md) for both.

## Adding Sound FX

Sound effects that play at just the right moments significantly enhance immersion. One of the best strategies: tie effects to game events.

1. **Game state events** — game start, game over (win/lose), new level achieved, game paused, game reloaded from a saved state.
2. **Player events** — collisions, points scored, loot/powers/weapons acquired.

**Simple example** — sound actions created with `SKAction.playSoundFileNamed(...)`, often set as constants in a utility file or a project's `GameScene` file:

```swift
let soundGameStart = SKAction.playSoundFileNamed("gameStart.wav", waitForCompletion: true)
let soundCoinDrop = SKAction.playSoundFileNamed("coinDrop.wav", waitForCompletion: true)
let soundPointScored = SKAction.playSoundFileNamed("pointScored.wav", waitForCompletion: true)
let wallCollisionSound: SKAction = SKAction.playSoundFileNamed("hitWall.wav", waitForCompletion: false)
let soundGameOver = SKAction.playSoundFileNamed("player_die.wav", waitForCompletion: false)
let soundWin = SKAction.playSoundFileNamed("winning.wav", waitForCompletion: false)
```

To play them, pass them to `run()`, just as with any `SKAction`:

```swift
run(wallCollisionSound)
```

Because they're defined before they're needed, they're preloaded into memory, which prevents the game from stalling the first time you play any of them.

**Sequence example** — sound actions created with `SKAction.playSoundFileNamed(...)` can be included in group or sequence actions, alongside other actions tied to the same game event:

```Swift
let sequenceWallCollisionAction = SKAction.sequence([action1, action2, wallCollisionSound])
```

**Random selection example** — set up an array of similar effects and randomly choose one to play, to add variety:

```swift
let soundCarCrashesArray = [
  SKAction.playSoundFileNamed("carCrash1.wav", waitForCompletion: false),
  SKAction.playSoundFileNamed("carCrash2.wav", waitForCompletion: false),
  SKAction.playSoundFileNamed("carCrash3.wav", waitForCompletion: false)
]
```

Then, depending on game conditions, choose which effect to run — or randomize the index so similar events sound less repetitive:

```swift
run(soundCarCrashesArray[2])
```

## Checkpoint: Add Audio to AstroJunk

Find some sounds (free sound effect sites like freesound.org or opengameart.org work well) and:

- Add background music that plays while the game runs. Stop it at Game Over and any other appropriate game states.
- Add sound FX to appropriate game events (collisions, points scored, etc.).

## Particle Emitter

Particle systems are an effective way to add special effects — simulating water, smoke, fire, snow, stars, sparks, rain, bubbles, explosions, and more.

![particle2](assets/particle2.png)

*Source: 2D Apple Games by Tutorials*

The effect is created using a small image texture and a configuration file. SpriteKit gives you a special node, `SKEmitterNode`, whose purpose is to create particle systems and render them as quickly as possible. You can create particles programmatically or with the particle editor — the editor is helpful since you can change values and see the animated result immediately.

To use it, create a new file with the iOS/Resource/SpriteKit Particle File template. This creates an `.sks` file you can modify with the editor. In code, instantiate the particles with the file and a position, then add the emitter as a child in the scene:

```Swift
let fireEmitter = SKEmitterNode(fileNamed: "Fire.sks")!
fireEmitter.position = CGPoint(x: 200, y: 200)
addChild(fireEmitter)
```

See [particle emitter properties](particleProperties.md) for what each property does.

## Checkpoint: Add a Particle Emitter

Create an `.sks` file for a new particle emitter — add fire to the spaceship as it flies. Play with the values of the particle emitter until you're happy with the result.

Here's an example of how it might turn out:

![particle2](assets/fire.gif)

## After This Lesson

Catch AstroJunk up so it includes everything covered through this lesson: movement, collisions, touch controls, scenes, and now audio + particle effects.

## Additional Resources

1. The "Controlling the Audio of a Node" section of the [Action Initializers - Apple docs](https://developer.apple.com/documentation/spritekit/skaction/action_initializers)
2. Functions and properties in the "Configuring and Controlling Playback" and "Managing Information About a Sound" sections of the [AVAudioPlayer - Apple docs](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
3. [Website on Game Feel](https://gameanalytics.com/blog/squeezing-more-juice-out-of-your-game-design.html)
4. [George Lucas Quote](https://quotefancy.com/quote/1021372/George-Lucas-The-sound-and-music-are-50-of-the-entertainment-in-a-movie)
5. [AVFoundation - wikipedia](https://en.wikipedia.org/wiki/AVFoundation)
6. [AVFoundation - Apple Docs](https://developer.apple.com/av-foundation/)
7. [AVFoundation - Apple Docs](https://developer.apple.com/documentation/avfoundation)
8. [Audio Track Engineering - Apple Docs](https://developer.apple.com/documentation/avfoundation/audio_track_engineering)
9. [About AVFoundation - Apple Docs](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/00_Introduction.html)
10. [SKAudioNode - Apple Docs](https://developer.apple.com/documentation/spritekit/skaudionode)
11. [Background music with SKAudioNode, an intro, plus game over - an article](https://www.hackingwithswift.com/read/36/6/background-music-with-skaudionode-an-intro-plus-game-over)
12. [`playSoundFileNamed(_:waitForCompletion:)` - Apple Docs](https://developer.apple.com/documentation/spritekit/skaction/1417664-playsoundfilenamed)
13. [`listener` (property) - Apple docs](https://developer.apple.com/documentation/spritekit/skscene/1520363-listener)

**Next:** [Lesson 6 - Physics](../06-Physics/Lesson.md)
