What is it?
- Architectural pattern
- Describes entities with components and systems
- Based on Data-oriented design

What is an entity?
- Just an index to components

What are components?
- Plain old data
- Resources
- Tags

What are systems?
- Functionality that operates on components

## Data Oriented Design (DoD)
Main principles:
- Optimal data layout
- Avoid indirection
- Data decomposition (separate the relevant data)
	- Structure of Arrays (SoA)
- Pure transformations (avoid hidden state)
- Understand the hardware (memory is slow)

## EnTT
Battle tested:
- Used by Minecraft!

It's not a silver bullet; don't use it for _everything_.
- Read the [EnTT Crash Course](https://github.com/skypjack/entt/wiki/Crash-Course:-entity-component-system).

### The Registry
A registry stores and manages the entities and components, and is their main container. 
It uses a **Sparse Set** to map indices (entities) to components:
```cpp
auto entity = registery.create();
// ...
registery.destroy(entity);
```

### The Component
It's just data. It can store a value or act as a tag:
```cpp
position& pos = registery.emplace<position>(entity, 0.0f, 0.0f);
// ...
```
