# Lesson 3: Selecting an Architecture

## Learning Objectives

1. Identify and describe the pros and cons of the most popular iOS game architectures: inheritance-based, component-based, and state machine
2. Refactor an existing code base into a component-based architecture
3. Examine a simple state machine using GameplayKit

## Common iOS Game Architectures

There are more than a few software design patterns that *could* be useful for a given game app, but only a small set are commonly used for iOS game development. This lesson covers the high-level design, benefits, and shortcomings of the patterns most commonly used to make iOS games.

Before reading on, take a moment to look at AstroJunk's current code. So far you've applied only generic OOP concepts to it — maybe just the basic tenets of MVC. Keep that starting point in mind as you compare it to the architectures below.

## Inheritance-Based Architecture

In an inheritance-based (or "hierarchy-based") architecture, each game object is a **subclass** of a more general **base class**, and all game objects ultimately derive from that base class.

The first step is to define a single base class common to all game objects — by convention, often named `GameObject`. It can hold all the **behaviors and properties** common to any game object, especially general tasks like being updated every frame. All other game objects then inherit from `GameObject`, though subclasses can also be customized for their own needs.

![inheritance-based](assets/inheritance-based.png)

Note that `GameObject` isn't required to extend either `SKNode` or `SKSpriteNode`, but doing so is a very common form of inheritance-based layout in iOS games.

### Example

The following three classes illustrate a simple inheritance-based game architecture. The `Princess` and `Dragon` subclasses each inherit and override `update(deltaTime:)` from the base class (`GameObject`), along with whatever custom behaviors and properties each subclass needs.

```swift
class GameObject: SKSpriteNode {
    func update(deltaTime : Float) {
       // 'deltaTime' is the number of seconds since update() was called last
      // Override this function in subclasses to update the object state
      //(i.e., changes in position, direction, etc.)
    }
}
```

```swift
class Princess: GameObject {
    // Set initial amt of spells and magic powers
    var magicPowersRemaining : Int = 20
    // some other game object this object is interacting with
    var target : GameObject?

    override func update(deltaTime: Float) {
        super.update(deltaTime: deltaTime)
        // Do Princess-specific update tasks
    }
}
```

```swift
class Dragon: GameObject {
    // Set initial amt of fire units
    var firePowerRemaining : Int = 40
    // some other game object this object is interacting with
    var target : GameObject?

    override func update(deltaTime: Float) {
        super.update(deltaTime: deltaTime)
        // Do Dragon-specific update tasks
    }
}
```

It's also common with this pattern to create intermediate subclasses of `GameObject` for each specific type of game element. For example, if your game has dragons, ogres, harpies, and cyclops, all with common traits, creating a `Monster` (or `Creature`) subclass of `GameObject` lets you add behaviors common to all of those elements, while still inheriting the generic behaviors from `GameObject`.

### Benefits

One key advantage: each object can stand on its own. In the example above, all of a `Princess` object's behaviors live inside that single object, without needing any other object to do the work. Inheritance-based architecture is also:

- the simplest to implement
- built on familiar concepts (object/class inheritance)

### Drawbacks

An inheritance-based layout works fine and is easy to implement for simple games. But as your game grows in complexity, an inheritance hierarchy creates its own set of challenges:

- You end up with a hierarchy of game object subclasses multiple levels deep, which becomes difficult to keep track of as the code base expands.
- Your initial `GameObject` base class evolves as you add elements, and moving more code into it makes it long and difficult to work with.
- Not all game elements necessarily belong under the same generic base class — should weapons really derive from the same base class as creatures?
- Code for various game "systems" — like drawing or collision detection — ends up mixed together in the same object hierarchy.

## Component-Based Architecture

The basic idea behind component-based architecture — also known as an "Entity Component System" — is to prefer **composition** over inheritance. It seeks to eliminate the problems of deep, wide inheritance hierarchies that are hard to understand, maintain, and extend.

In a component-based architecture, all of your game objects (entities) derive from the same base class, but they're **defined** by what **components** they have — not by the type of subclass they inherit from. Each entity has a list of components. When the game updates, or the object is added to or removed from the game, or some other game event occurs, the object notifies each component in its list of the event.

![component-based](assets/component-based.png)

*[Source](http://cowboyprogramming.com/2007/01/05/evolve-your-heirachy/)*

The first step is a base `Component` class:

```swift
class Component {
    // The game object this component is associated with
    var gameObject : GameObject?
    func update(deltaTime : Float) {
        // Update this component
    }
}
```

Next, a base class for game objects, holding a collection of all components associated with a given object:

```Swift
class GameObject {
    // The list of Component objects belonging to this object
    var components : [Component] = []
        func update(deltaTime : Float) {
          // Update this object by updating all of its components
            for component in self.components {
                component.update(deltaTime: deltaTime)
          }
      }
        // And other functions, to add and remove components...
}
```

Then create specific components as customized subclasses of `Component`, subclass `GameObject` to create new entity objects, and define each entity's gameplay capabilities by assigning it its own specific set of components.

### Benefits

Component-based architecture is the most commonly used architecture in game development. Game objects (entities) are reduced to simple structures that connect various functional components, which can really boost game production.

**Scalability** — to add new features or change how something works, create a new component. No fussing with class hierarchies or dependencies, and new entity types can be created programmatically at runtime.

**Reusability** — rather than reusing code via superclasses, you reuse it by giving similar entities similar components. Since components are self-contained, they can be interchanged to create new entities, or moved from one game into another.

**Flexibility** — a component-based layout means you can be flexible with your design without worrying about inheritance issues, and it lends itself to different kinds of games and different ways of storing entities.

**Consistency** — when all your game entities are instances of the same class, with a standardized component interface, you avoid the hassle of inheritance trees and dependency diagrams and can focus on core game functionality.

*[Source](https://www.raywenderlich.com/2806-introduction-to-component-based-architecture-in-games)*

### Drawbacks

The drawback of component-based architecture is increased level of effort:

- **Initially** — you need to create more base classes up front than you would for inheritance-based.
- **As your game grows** — creating multiple copies of an object takes more effort, since you need to create and add the same set of components each time.

Apple's GameplayKit framework provides classes that let you construct your own entity-component system — more on GameplayKit shortly.

## Checkpoint: Refactor to Component-Based Architecture

Refactor AstroJunk's game objects to use a component-based architecture:

1. Create a `Component` base class and a `GameObject` base class that holds a list of components (as shown above).
2. Pick one of your game objects (e.g., the spaceship or a meteor) and break its behavior into separate components — for example, a `MovementComponent` and a `RenderComponent`.
3. Wire the new component-based object into the game and confirm it behaves exactly as it did before the refactor.

The goal isn't to convert everything at once — it's to feel the difference between "is-a" (inheritance) and "has-a" (composition) in your own code before moving on.

## State Machines

At some point in game development, you'll encounter the need to work with finite state machines.

**What is a FSM?** *"A structure that allows you to define complex behaviors."* We can define complex behaviors and encapsulate them in a single object called a state. Each state should describe a very simple action.

The easiest way to visualize state machines is by drawing FSM diagrams. Take a basic example: a button, with two states — Pressed and Released. We could track this with a boolean:

```swift
class somebutton: UIButton {
  let isPressed = false
    if(isPressed){
      //the button has been pressed, do something
    }else{
      //the button has been released do something else
    }
}
```

![buttonStates](assets/button.png)

The arrows show the relationship between the two states — you can move from one to the other at any time.

### Checkpoint: Sketch a State Machine

Consider the movement of a main character in a game that can:
- stand
- jump (single tap)
- attack mid-jump (double tap)
- duck (long press)

Sketch (on paper, or in any diagramming tool) how you'd represent this with an FSM diagram before reading on.

The main points a working FSM captures:

- A fixed set of states the machine can be in — standing, jumping, attacking, diving.
- The machine can only be in one state at a time — the character can't be jumping and standing simultaneously.
- A sequence of inputs is sent to the machine — long presses and taps, in this example.
- Each state has a set of transitions, each associated with an input and pointing to another state. When an input arrives that matches a transition for the current state, the machine changes to that state.

### State Machines in Code

When you start building a game, it's easy to put all state-dependent code in one place — the `update` method, for example. As the game grows more complex, that becomes harder to maintain.

You could represent 4 states with 4 booleans, but think about what that implies going forward: a lot of if-else statements to check the current state, and the risk of bugs from mistakenly setting some combination of booleans wrong.

Defining all the states in the game up front, along with the rules for which transitions are allowed, lets you reason about complicated behaviors far more easily. **GameplayKit** has a ready-to-use solution for state machines you can take advantage of.

### Checkpoint: Study a State Machine Example

Work through [this example of a water dispenser](https://developer.apple.com/library/archive/samplecode/Dispenser_GameplayKit/Introduction/Intro.html#//apple_ref/doc/uid/TP40016460), which simulates a water dispenser that can only be in one state at a time: empty, full, partially full, serving, or refilling. Using a state machine here makes it easy to enforce that restriction and organizes the logic for each state (how the graphics move, what's enabled/disabled, etc.).

As you go through it, answer:

- What are all the possible states?
- What happens in each state?
- How are transitions between states handled?
- Does it matter what the previous state was before entering a new one?
- How is it possible that we can't get multiple glasses of water on screen?
- Why can't we request a glass when the dispenser is empty?
- What would you add to the simulation?

### Checkpoint: State Machine for AstroJunk

Diagram a state machine for AstroJunk (e.g., ship states like flying, hit, exploding — or game states like playing, paused, game-over). As a stretch challenge, implement it in code using GameplayKit's `GKStateMachine`.

## Further Reading

- [Game mechanics - wikipedia](https://en.wikipedia.org/wiki/Game_mechanics)
- [Entities and Components - from Apple docs](https://developer.apple.com/library/archive/documentation/General/Conceptual/GameplayKit_Guide/EntityComponent.html)
- [The Command Pattern - an article](https://gameprogrammingpatterns.com/command.html)
- The Observer Pattern, the Strategy Pattern

## Additional Resources

1. [State machines with GameplayKit](https://developer.apple.com/library/archive/documentation/General/Conceptual/GameplayKit_Guide/StateMachine.html)
2. [Entity component system - wikipedia](https://en.wikipedia.org/wiki/Entity_component_system)
3. [GameplayKit - from Apple docs](https://developer.apple.com/documentation/gameplaykit)
4. [Entity Component System - an article](http://www.roguebasin.com/index.php?title=Entity_Component_System)
5. [Refactoring Game Entities with Components - an article](http://cowboyprogramming.com/2007/01/05/evolve-your-heirachy/)
6. [Software design pattern - wikipedia](https://en.wikipedia.org/wiki/Software_design_pattern)
7. [Design Patterns: Elements of Reusable Object-Oriented Software](https://en.wikipedia.org/wiki/Design_Patterns) — an extremely important book in the field of software development, co-written by the "Gang of Four"
8. [Top 5 Design Patterns in Swift for iOS App Development - an article](https://rubygarage.org/blog/swift-design-patterns)
9. [Basic Parent-Child Concepts in Sprite Kit](http://spritekitlessons.com/child-basics-in-sprite-kit-adding-removing-finding/)
10. [FSM - an article](https://gamedevelopertips.com/finite-state-machine-game-developers/)
11. [FSM - Game Programming Patterns](https://gameprogrammingpatterns.com/state.html)
12. [Component based architecture](https://www.oreilly.com/library/view/ios-swift-game/9781491920794/ch01.html)

**Next:** [Lesson 4 - Working with Scenes](../04-Working-with-Scenes/Lesson4.md)
