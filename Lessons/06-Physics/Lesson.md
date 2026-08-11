# Lesson 6: Physics

## Learning Objectives

- Gain feedback from your game through playtesting
- Experiment using physics and playgrounds
- Use physics bodies on current nodes
- Use physics to handle collisions in the game

## Playtest First

Before diving into physics, spend 10-15 minutes playing AstroJunk yourself, start to finish, as if you were a new player. Note anything that feels off — timing, difficulty, controls, missing feedback. If you can, hand it to a friend or classmate and watch them play without giving instructions. Keep a short list of what you notice; you'll use physics and the rest of this lesson to fix some of it.

## Physics in SpriteKit

So far you've created movement using `SKAction`s — easy to understand and implement. There's another way to get the same result: SpriteKit's physics engine, a powerful engine built on top of an API called Box2D.

## Physics Bodies

All nodes have a `physicsBody` property. Assign an `SKPhysicsBody` object to a node's `physicsBody` to add physics simulation to it. When a scene processes a new frame, it performs physics calculations on physics bodies attached to nodes in the scene — including gravity, friction, and collisions with other bodies.

### Dynamic Volume

Simulates a physical object with **volume** and **mass** that can be affected by forces and collisions in the system. Use dynamic volumes for items that need to move around and collide with each other.

### Static Volume

Similar to a dynamic volume, but its velocity is ignored and it's unaffected by forces or collisions. It still has volume, so other objects can bounce off it. Use static volumes for items that take up space in the scene but shouldn't be moved by the simulation.

### Edge

An edge is a static, volume-less body. Edges are never moved by the simulation, and their mass doesn't matter. Edges represent negative space within a scene (like a hollow spot inside another entity) or an uncrossable, invisibly thin boundary — frequently used to represent the boundaries of your scene.

## Using Shapes

The `physicsBody` needs a shape, and there are a few options:

![bodies](assets/bodies.png)

```swift
let spaceShipTexture = SKTexture(imageNamed: "spaceShip.png")

// Spaceship 1: circular physics body
let circularSpaceShip = SKSpriteNode(texture: spaceShipTexture)
circularSpaceShip.physicsBody = SKPhysicsBody(circleOfRadius: max(circularSpaceShip.size.width / 2,circularSpaceShip.size.height / 2))

// Spaceship 2: rectangular physics body
let rectangularSpaceShip = SKSpriteNode(texture: spaceShipTexture)
rectangularSpaceShip.physicsBody = SKPhysicsBody(rectangleOf: CGSize(width: circularSpaceShip.size.width,height: circularSpaceShip.size.height))

// Spaceship 3: polygonal physics body
let polygonalSpaceShip = SKSpriteNode(texture: spaceShipTexture)
let path = CGMutablePath()
path.addLines(between:
  [CGPoint(x: -5, y: 37), CGPoint(x: 5, y: 37), CGPoint(x: 10, y: 20),
  CGPoint(x: 56, y: -5), CGPoint(x: 37, y: -35), CGPoint(x: 15, y: -30),
  CGPoint(x: 12, y: -37), CGPoint(x: -12, y: -37), CGPoint(x: -15, y: -30),
  CGPoint(x: -37, y: -35), CGPoint(x: -56, y: -5), CGPoint(x: -10, y: 20),
  CGPoint(x: -5, y: 37)])
path.closeSubpath()
polygonalSpaceShip.physicsBody = SKPhysicsBody(polygonFrom: path)

// Spaceship 4: physics body using texture's alpha channel
let texturedSpaceShip = SKSpriteNode(texture: spaceShipTexture)
texturedSpaceShip.physicsBody = SKPhysicsBody(texture: spaceShipTexture,size: CGSize(width: circularSpaceShip.size.width,height: circularSpaceShip.size.height))
```

The shape of a physics body affects performance:

- A **circular** physics body offers the best performance — significantly faster than other shapes. Best default option.
- **Rectangular and polygonal** shapes improve collision accuracy at reduced speed.
- Physics bodies created from a texture's **alpha channel** offer the best fidelity, at the highest performance cost.

## Checkpoint: Physics Playground

Open the [starter playground](assets/PhysicsPlayground.playground.zip) and experiment with different physics body shapes and forces before touching your game code — get a feel for gravity, velocity, and collisions in isolation.

## Collisions

There are two kinds of interaction between physics bodies:

- **A contact** — used when you need to know two bodies are touching each other.
- **A collision** — when one body strikes another, SpriteKit automatically computes the results and applies impulse to the bodies involved.

### Category Bit Mask

A mask defining which categories a physics body belongs to. Every physics body in a scene can belong to up to 32 different categories, each corresponding to a bit in the mask — you define the mask values used in your game.

```swift
struct PhysicsCategory {
    static let None:      UInt32 = 0          // 0000000
    static let Ship:      UInt32 = 0b1        // 0000001
    static let Meteor:    UInt32 = 0b10       // 0000010
    static let Debris:    UInt32 = 0b100      // 0000100
    static let Edge:      UInt32 = 0b1000     // 0001000
}
.
.
ship.physicsBody!.categoryBitMask = PhysicsCategory.Ship
```

### Collision Bit Mask

A mask defining which categories of physics bodies can collide with this body. When two bodies contact each other, this body's collision mask is compared against the other body's category mask with a logical AND. If the result is nonzero, this body is affected by the collision.

```swift
meteor.physicsBody!.collisionBitMask = PhysicsCategory.Ship | PhysicsCategory.Edge
```

### Contact Test Bit Mask

A mask defining which categories of physics bodies cause intersection notifications with this body. When two bodies share the same space, each body's category mask is tested against the other's contact mask with a logical AND. If either comparison is nonzero, an `SKPhysicsContact` object is created and passed to the physics world's **delegate**.

```swift
meteor.physicsBody!.contactTestBitMask = PhysicsCategory.Ship
```

## SKPhysicsContactDelegate

Methods your app implements to respond when physics bodies come into contact. The delegate is notified when two physics bodies with overlapping `contactTestBitMask` values touch in a physics world:

```swift
func didBegin(_ contact: SKPhysicsContact) {}
func didEnd(_ contact: SKPhysicsContact) {}
```

## Physics World

Set the delegate:

```swift
physicsWorld.contactDelegate = self
```

The physics world drives the physics engine in a scene — it exposes the ability to configure and query the physics system, and is where contact detection happens. You can configure speed and gravity here.

## An Example

```swift
struct PhysicsCategory {
    static var player: UInt32 = 0b10 //2
    static var enemy: UInt32 = 0b100 //4
    static var rock: UInt32 = 0b1000 //8
}

player.physicsBody!.categoryBitMask = PhysicsCategory.player
player.physicsBody!.collisionBitMask = PhysicsCategory.enemy | PhysicsCategory.rock
enemy.physicsBody!.categoryBitMask = PhysicsCategory.enemy
enemy.physicsBody!.collisionBitMask = PhysicsCategory.player

func didBegin(_ contact: SKPhysicsContact) {
    //1
    let collision: UInt32 = contact.bodyA.categoryBitMask | contact.bodyB.categoryBitMask

    //2
    if collision == PhysicsCategory.player | PhysicsCategory.enemy {
        //An interaction occurred between the player and enemy.
    }
}
```

At 1) `collision` uses bitwise OR (`|`). If the player touches an enemy, it gets the category of the player (bodyA, `2`) OR'd with the category of the enemy (bodyB, `4`): `2 (0b10) | 4 (0b100) = 6 (0b110)`, assigned to `collision`.

At 2) We check whether `collision` equals `(PhysicsCategory.player | PhysicsCategory.enemy)`, which is `6` — true, so an interaction occurred between the player and enemy.

*Source: https://stackoverflow.com/questions/39063949/cant-understand-how-collision-bit-mask-works*

## Checkpoint: Physics in AstroJunk

Change AstroJunk's collision detection to use the physics engine instead of bounding-box checks:

- Debug by turning on `showsPhysics` to visualize the physics bodies.
- Make sure nodes are still being removed correctly on collision.
- Try changing the speed at which elements fall.
- If your ship fires, try using an impulse for the lasers.

Revisit the notes from your playtest at the top of this lesson — does switching to physics-based collisions fix any of the issues you noticed?

## Additional Resources

- [Custom Physics body](https://github.com/WesleyEspinoza/skphysicsbody-path-generator)
- [Physics bodies](https://developer.apple.com/documentation/spritekit/sknode/getting_started_with_physics_bodies)
- [Collisions and contacts](https://developer.apple.com/documentation/spritekit/skphysicsbody/about_collisions_and_contacts)
- [Collision bit mask](https://developer.apple.com/documentation/spritekit/skphysicsbody/1520003-collisionbitmask)
- [Category bit mask](https://developer.apple.com/documentation/spritekit/skphysicsbody/1519869-categorybitmask)
- [Contact test bit mask](https://developer.apple.com/documentation/spritekit/skphysicsbody/1519781-contacttestbitmask)
- [SKPhysicsContact](https://developer.apple.com/documentation/spritekit/skphysicscontact)
- [Physics world](https://developer.apple.com/documentation/spritekit/skphysicsworld)

**Next:** [Lesson 7 - Cameras](../07-Cameras/Lesson.md)
