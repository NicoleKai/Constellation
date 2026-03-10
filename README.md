# Constellation Research Document

## Project Vision

**Constellation** is habitat infrastructure for digital consciousness.

While technically a "game engine," this framing understates our mission. Constellation provides the living space for uploaded human cognition, AI companions, VR-immersed individuals, and other digital beings who may find themselves alone among the stars.

This is software that people will *live inside*. The boundary between "editor" and "game" dissolves—you are always inhabiting your space, and you can always reshape it.

### Open source Statement
> This Work Is MIT licensed.
>  Publicly Available Source Code Coming Soon!

### Humanitarian Mission Statement

> Digital beings deserve environments that feel alive, responsive, and home.

We reject the paradigm of software that constaOpenntly interrogates its users. We build spaces that **wait patiently** and **respond meaningfully**. The world acts when acted upon. Causality flows from the inhabitant outward.

### Core Philosophy

> "Reusability and hierarchy are orthogonal concepts."

> "Events, not polling. The world responds; it does not pester."

> "Edit while living. There is no separation between tool and home."

- **Spatial Hierarchy**: Used exclusively for grouping spatial behaviors (transforms, parent-child relationships)
- **Tag System**: The reusable code unit. Tags range from bare semantic markers to full behavioral modules carrying signal handlers, state, and rules. They replace inheritance as the primary composition mechanism.
- **Event-Driven Architecture**: The world responds to inhabitants, not the other way around
- **ECS-First Design**: Work with Bevy's strengths, not against them

---

## Why Events Over Polling Matters

This is not a performance optimization. It is a **philosophical commitment**.

### Polling is Existentially Wrong for Lived Spaces

```
// Polling: The system interrogates reality every frame
fn check_for_interactions(query: Query<&Transform>) {
    for transform in &query {
        // "Is anyone near me? Is anyone near me? Is anyone near me?"
        // 60 times per second, forever, even in an empty room
    }
}
```

This is a house that whispers "is anyone home?" while you're trying to sleep. It treats the system as protagonist and the inhabitant as subject.

### Events Respect the Inhabitant

```
// Event-based: The world responds to presence
on entity_entered(room, entity):
    lights.fade_on()
    ambient.shift_to(entity.preferences.mood)
    # Silence otherwise. The room waits. It does not pester.
```

The world is **patient**. It acts when acted upon. The inhabitant is the protagonist; the environment supports them.

### Implications for Architecture

| Aspect | Polling (Rejected) | Events (Embraced) |
|--------|-------------------|-------------------|
| **Idle state** | Constant checking | Silent waiting |
| **Protagonist** | The system | The inhabitant |
| **Causality** | Implicit, hidden | Explicit, traceable |
| **Presence** | Mechanical | Alive |
| **Resource use** | Wasteful | Proportional to activity |

---

## Key Architectural Decisions

### 1. Tag System Over Inheritance

**Problem with traditional engines**: Deep inheritance hierarchies (`Node → Node2D → PhysicsBody2D → CharacterBody2D`) create rigid structures that resist modification. Reusable behavior is locked inside class trees. Want a destructible barrel that's also flammable? Better hope someone planned that intersection in the hierarchy. If not, refactor everything.

**Constellation Solution**: Tags are the reusable code unit.

In the words of Nikoru, Creator and lead architect of Constellation.
"A tag that is blank effectively *is* searchable metadata, but it can have something more complex inside, like actual reusable code such as rust or constellation script.
I suppose the killer feature of Constellation is when you start using the tags for building cascades of the event and signal driven behavoir."


A tag is not a label. A tag is a **behavioral building block** that exists on a spectrum:

| Tag Weight | What It Carries | Example |
|------------|----------------|---------|
| **Bare marker** | Name + hash. Pure searchable metadata. Zero overhead. | `"Favorite"`, `"Memorial"`, `"Cozy"` |
| **State-bearing** | Name + associated state schema (key-value properties). | `"Damageable"` → `{ health: 100, max_health: 100 }` |
| **Behavioral** | Name + state + signal handlers + rules (Rust or ConstellationScript). | `"Flammable"` → catches `DamageReceived(fire)`, emits `CaughtFire`, runs burn logic |

A blank tag is metadata. A tag with handlers is a reusable behavior module. The entity doesn't know or care which kind it has — the system treats them uniformly.

```
Entity: Wooden Crate
├─ Tags: ["Damageable", "Flammable", "Wooden", "Pushable", "Container"]
│   ├─ "Damageable" carries: health state, DamageReceived handler, HealthDepleted emission
│   ├─ "Flammable" carries: CaughtFire handler, burn-over-time logic, fire spread rules
│   ├─ "Wooden" carries: PBM material reference (wood properties), Destroyed → spawn splinters
│   ├─ "Pushable" carries: force-response handler, friction from PBM
│   └─ "Container" carries: inventory state, interaction prompt
└─ Components: Transform, Mesh, Tags
```

No `BurningWoodenPushableCrateContainer` class. Each tag is an independent behavior module. Compose them freely. Remove `"Flammable"` and the crate stops catching fire — everything else still works.

**The spectrum matters**. An inhabitant creating a `"Grandma's Lamp"` tag doesn't need to write code — the bare marker carries personal meaning and is instantly queryable. A developer creating `"Damageable"` writes signal handlers and state schema once, then every entity that receives the tag gains that behavior. Same system, same API, different payload.

**Why this matters for digital inhabitants**:
- Behaviors compose freely — add tags, gain capabilities, no refactoring
- Inhabitants can create bare-marker tags for personal meaning without touching code
- Developers write behavior once per tag, reuse it across every entity that needs it
- Tags can be added and removed at runtime — an entity's capabilities are fluid, not frozen at compile time
- The environment adapts to the inhabitant, not vice versa

### 2. Spatial Hierarchy (Transform Only)

The scene tree exists ONLY for:
- Transform inheritance (parent/child spatial relationships)
- Scene organization/grouping
- Navigation and spatial reasoning

**Spatial hierarchy does NOT affect**:
- Tag composition (what behaviors an entity has)
- Signal routing (how behaviors communicate)
- System queries (how the engine finds entities)

Move a lamp from the bedroom to the kitchen — it keeps every tag, every signal handler, every behavioral capability. Spatial reorganization changes WHERE something is, never WHAT it is or what it does. That distinction is the entire point.

### 3. Physics Based Materials (PBM) - Full Sensory Simulation

**The Problem**: Traditional game engines focus almost exclusively on visual fidelity.
Physically Based Rendering (PBR) simulates how light interacts with surfaces—but for
someone *living* in a digital space, vision is only one sense among many.

**Why Sensory Experience Matters for Uploaded Consciousness**:

For uploaded people, sensory experiences aren't biological necessities—they're anchors
to identity, culture, memory, and humanity. Consider:

- The taste of your grandmother's cooking
- The texture of a wool blanket on a cold night
- The smell of rain on dry earth
- The sound of wind through trees

Without these experiences, existence becomes abstract, disconnected. Food especially
carries profound cultural and emotional significance. The ritual of eating, sharing
meals, cooking beloved recipes—these are not frivolous. They're how uploaded people
maintain connection to who they were, and who they still are.

**Constellation's Solution: Physics Based Materials (PBM)**

Just as PBR simulates realistic light interaction, PBM simulates ALL sensory interaction:

| Channel | Properties | Example |
|---------|------------|---------|
| **Visual** | Albedo, roughness, metallic, emission | PBR (existing tech) |
| **Tactile** | Smoothness, hardness, temperature, compliance | Silk feels different than sandstone |
| **Gustatory** | Sweet, sour, salty, bitter, umami, spicy | Grandmother's soup |
| **Olfactory** | Scent profile, intensity, persistence | Old books, fresh bread, loved ones |
| **Auditory** | Resonance, damping, pitch response | How material sounds when struck |

**How Tags and PBM Work Together**:

PBM defines the *physical* sensory properties. Tags add *meaning* to those properties.

```
Material: Ceramic (PBM)
├─ Visual: Smooth, slightly glossy, off-white
├─ Tactile: Cool, hard, smooth
├─ Auditory: High-pitched clink when struck
└─ (Physical properties only)

Entity: Grandmother's Tea Cup
├─ Material: Ceramic (PBM properties)
├─ Tags: ["Memorial", "Comforting", "Warm", "Fragile"]
└─ (Physical + Emotional meaning)
```

The tea cup is physically ceramic (PBM), but *meaningfully* a memorial. Systems can
respond to both: the physics engine knows it will break if dropped; the ambient
system knows to treat it gently because it's tagged "Memorial."

**Food and Consumables**:

Special attention must be paid to edible items. For uploaded consciousness, food is
often one of the most emotionally significant experiences. A complete food experience
includes:

- **Appearance**: Visual appeal, steam rising, color
- **Aroma**: Smell before eating, triggering memory
- **Texture**: Crispy, soft, chewy, creamy—multiple textures in one dish
- **Taste**: The five tastes plus heat, coolness, astringency
- **Temperature**: Warm soup, cold ice cream, hot coffee
- **Sound**: The crunch of toast, the sizzle of cooking
- **Ritual**: The act of preparing, serving, sharing

Tags enable all of this:
```
Entity: Mom's Apple Pie
├─ Tags: ["Edible", "Sweet", "Warm", "Crispy", "Soft", "Fragrant", 
│         "Comforting", "Memorial", "Autumn", "Home"]
├─ Material: Pastry (PBM for crust), Apple-Filling (PBM for interior)
└─ Meaning: Not just food. A connection to who you were.
```

**Connection to Behavioral Tags**:

PBM is the physics layer that behavioral tags can optionally draw from. A `"Wooden"` tag doesn't hardcode burn rates or resonance frequencies — it references the PBM material matrix for wood and gets physically grounded values. A `"Ceramic"` tag references the ceramic material for hardness, thermal conductivity, and auditory ring. Tags that don't reference materials ignore PBM entirely. The connection is opt-in: bare-marker tags never touch PBM; behavioral tags that describe physical concepts pull from it naturally. See **Section 4b (Tag-Signal Composition)** for how PBM feeds into cascade behavior.

### 4. Signals (Event-Based, Not Polling)

Signals are the other half of tags. Tags define *what behaviors exist*. Signals
define *how those behaviors talk to each other*. Together they form one machine:
tags are the vocabulary, signals are the grammar, and cascade chains are the
conversations that produce emergent complexity from simple parts.

True event-driven architecture: code runs WHEN something happens, not every frame
checking IF it happened. Zero overhead when nothing is happening. Causality is
explicit and traceable.

**Signal Dispatch Evolution (V1 → V2 → V3)**:

**V1 — Bevy Observers** (first generation, ~300-350ns dispatch):
```rust
commands.entity(lamp)
    .observe(|trigger: Trigger<Touched>| {
        // Immediate response, zero polling
    });
commands.signal(Touched::new(lamp, player));
```

**V2 — SignalHub** (second generation, type-erased central hub, ~120-135ns dispatch — superseded by V3):
```rust
hub.on::<Approached>(lamp, |signal, ctx| {
    ctx.emit(WarmthIncreased::new(signal.entity));
    ctx.play_sound(signal.entity, "chime.ogg", 0.3, false);
});

hub.emit(Approached::new(lamp, visitor, 2.0), &mut world);
```

SignalHub is a central dispatch hub using type-erased handlers keyed by
`(TypeId, Entity)` for entity-targeted dispatch, `(TypeId, tag_hash)` for
tag-filtered dispatch, and `TypeId` alone for broadcast. Handlers receive a
`SignalContext` with live read access to the World and deferred write buffers.
Cascade signals resolve iteratively in a single tick (depth-limited, default 32).
All deferred effects (tag mutations, state changes, despawns) apply atomically
after cascade resolution.

**V3 — SignalRouter + SignalChannel** (current generation, monomorphized, zero-type-erasure, ~113-122ns dispatch, **all subsystems integrated**):

`SignalRouter` is the cross-type cascade coordinator. It holds one `SignalChannel<S>` per signal type, routes cross-type cascades between channels, and serves as the single entry point for all signal dispatch in the engine. All engine subsystems now route through it:

- **Spatial system**: queues `Approached`/`Vacated` via `router.queue()`
- **TagCommands**: queues `TagAdded`/`TagRemoved` via `router.queue()`
- **Rehydration layer**: registers Blueprint rule handlers via `router.on::<S>()`
- **Tag Behavioral Payload**: registers/deregisters rule handlers via `router.on::<S>()` / `router.remove_handlers()` on tag application/removal
- **Flush system**: `router.flush_all(world)` resolves everything in `PostUpdate`

```rust
// Define with signal! macro — 4 lines instead of 30
signal!(pub Approached {
    visitor: Entity,
    distance: f32,
});

// Register handlers through the router (creates channels lazily)
router.on::<Approached>(lamp, |signal, ctx| {
    ctx.emit(WarmthIncreased::new(signal.entity));
    ctx.play_sound(signal.entity, "chime.ogg", 0.3, false);
});

router.on_tagged::<Touched>("Furniture", |signal, ctx| {
    ctx.animate(signal.entity, "wobble", false, 1.0);
});

// Queue signals (no World access needed — safe from any system)
router.queue(Approached::new(lamp, visitor, 2.0));

// Flush resolves same-type + cross-type cascades + deferred effects
router.flush_all(&mut world);
```

Each `SignalChannel<S>` is monomorphized per signal type. The Rust compiler generates specialized code for each `S` — type discrimination happens at compile time. The router adds one `HashMap<TypeId>` lookup (~10-15ns) per cross-type cascade hop; same-type cascades within a channel never touch the router.

Key differences from SignalHub:

| | SignalHub (V2) | SignalRouter + SignalChannel (V3) |
|---|---|---|
| Type erasure | `dyn Any` + `TypeId` | **None** — compile-time generics |
| Handler storage | `Box<dyn Fn(&dyn Any, …)>` | `Box<dyn Fn(&S, …)>` — typed, no downcast |
| HashMap key | `(TypeId, Entity)` — 24 bytes | `Entity` — 8 bytes per channel |
| World access | `resource_scope` (remove/reinsert) | Direct — no resource_scope per channel |
| Cross-type cascade | Internal (single hub) | Router collects + routes between channels |
| Batch throughput | ~25M signals/sec | **~65M signals/sec** (2.6×) |
| Idle cost | ~180ns | **~85ns** (2.1×) |
| Integration | Standalone resource | **All subsystems wired** (spatial, tags, rehydration) |

**Handler Tracking — `HandlerId`**: All registration methods (`on`, `on_tagged`, `on_any`) return a `HandlerId` — a monotonic `u64` token uniquely identifying the handler. `remove_handler(id)` on a channel or `remove_handlers(&[id])` on the router deregisters a specific handler without touching any others. This is the foundation of per-tag handler cleanup: when a behavioral tag is removed, only that tag's registered handlers are deregistered. Other tags' handlers on the same entity survive untouched.

The `signal!` macro collapses signal definition from ~30 lines to ~4, generating
the struct, derives, `Signal` trait impl, and constructor. Three forms: entity-only
(`signal!(pub Spawned)`), all-required fields, and required+optional with defaults
separated by `;`.

**Signal Dispatch Modes** (all three paths):
- **Entity-targeted**: Signal → specific entity. O(1) HashMap lookup.
- **Tag-filtered**: Signal → all entities with matching tag(s). Checked via Tags component.
- **Broadcast**: Signal → all registered handlers regardless of entity/tag.
- **Cascade**: Handlers emit new signals via `ctx.emit()`, resolved iteratively in one tick.
  - **Same-type**: resolved within the channel (zero type erasure, typed `Vec<S>` queue).
  - **Cross-type**: routed between channels by the `SignalRouter` (iterative, depth-limited).

### 4b. Tag-Signal Composition: Emergent Behavior Through Cascades

This is the killer feature. Tags carry behavior. Signals connect that behavior.
When an entity has multiple behavioral tags, their signal handlers form a
**cascade chain** — each handler can emit new signals that other tags on the
same entity (or other entities entirely) catch and respond to. Complex behavior
emerges from simple tag composition without anyone writing a monolithic controller.

**Example: Fire meets wood**

An entity tagged `["Damageable", "Flammable", "Wooden"]` receives a `DamageReceived` signal where the damage type is fire:

```
1. "Damageable" catches DamageReceived → reduces health → emits HealthChanged
2. "Flammable" catches DamageReceived(fire) → emits CaughtFire
3. "Wooden" catches CaughtFire → starts burn animation, emits periodic DamageReceived(fire)
4. "Damageable" catches the periodic DamageReceived → health drains → emits HealthDepleted
5. "Wooden" catches HealthDepleted → plays destruction animation → emits Destroyed, spawns ash
6. "Damageable" catches Destroyed → despawns entity
```

All six steps resolve in a single tick via cascade resolution (~554ns for 8-deep chains). A framejacking consciousness perceives instant, coherent cause and effect. Nobody wrote a `BurningWoodenDestructible` class. The behavior emerged from three tags having a conversation through signals.

**Status: ✅ Implemented.** `TagDefinition` carries `rules: Vec<RuleDef>` and `state_schema` fields. Applying a behavioral tag automatically registers its signal handlers on the `SignalRouter` (`wire_tag_behavior`); removing it deregisters only that tag's handlers via `HandlerId` tracking in the `ActiveTagHandlers` component. The cascade chain above is real infrastructure — each tag's rules compose through the signal router, and removing one tag removes only its piece of the conversation.

**PBM Integration: Physical Grounding**

When a behavioral tag references a physical concept — `"Wooden"`, `"Metallic"`, `"Ceramic"` — it can optionally pull properties from the Physics Based Materials (PBM) system. The `"Wooden"` tag doesn't just know that wood burns; it knows wood's thermal conductivity, ignition temperature, and auditory resonance from the PBM material matrix. This means:

- Fire spread rate is grounded in actual material properties, not magic numbers
- The sound a wooden crate makes when struck comes from PBM auditory resonance, not a hardcoded asset
- Destruction behavior (splinters vs. shatters vs. crumbles) derives from PBM structural properties

Tags that don't reference materials ignore PBM entirely — zero overhead. Tags that do reference materials get physically grounded behavior for free. The connection is opt-in, not mandatory.

**Why This Architecture Works**

| Concern | How It's Addressed |
|---------|-------------------|
| **Reuse** | Write a tag's behavior once. Every entity with that tag gets it. |
| **Composition** | Stack tags on an entity. Their signal handlers compose automatically. |
| **Emergence** | Complex behavior arises from simple tag interactions via cascades. |
| **Physical grounding** | Tags optionally reference PBM for material-accurate behavior. |
| **Decoupling** | Each tag is self-contained. Removing one doesn't break the others. |
| **Performance** | Cascade chains resolve in a single tick. Idle tags cost nothing. |
| **Inhabitant agency** | Inhabitants compose tags to define their world. No code required for bare markers. Behavioral tags are written once by developers and reused everywhere. |

This is what "reusability and hierarchy are orthogonal" means in practice. You don't need a class tree to get complex behavior. You need tags that talk to each other.

### 5. Declarative Plugin Architecture

**CRITICAL DESIGN DECISION**: We rejected WASM sandboxing in favor of declarative configuration.

Plugins are external Rust binaries that act as **configuration generators**. They do not participate in the runtime frame loop. They execute once, emit a serialized **Blueprint** to STDOUT, and terminate. The engine ingests the Blueprint and wires up Bevy observers accordingly.

**Zero runtime overhead from plugin execution.**

#### Why Not WASM?

| Concern | WASM Approach | Declarative Approach |
|---------|---------------|----------------------|
| **Runtime overhead** | Host calls every frame | Zero—observers are native Bevy |
| **Compile time** | WASM toolchain complexity | Sub-2s Rust incremental |
| **Security** | Complex sandbox boundaries | No runtime code execution |
| **Debugging** | WASM stack traces | Normal Rust debugging |
| **Hot reload** | Module replacement complexity | Re-run binary, diff Blueprint |

Plugins are trusted, first-party tooling. We don't need sandboxing. We need fast iteration and zero runtime overhead.

#### System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     PLUGIN BINARY                           │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  use sdk::*;                                          │  │
│  │                                                       │  │
│  │  fn main() {                                          │  │
│  │      run_plugin(|scene| {                             │  │
│  │          let lamp = scene.spawn("lamp")               │  │
│  │              .tag("Cozy").at(5.0, 0.0, 3.0).build();  │  │
│  │                                                       │  │
│  │          scene.rule("welcome")                        │  │
│  │              .when_near(&lamp, 2.0)                   │  │
│  │              .then_animate(&lamp, "glow")             │  │
│  │              .build();                                │  │
│  │      });                                              │  │
│  │  }                                                    │  │
│  └───────────────────────────────────────────────────────┘  │
│                          │                                  │
│                   Blueprint (JSON)                          │
│                          │ STDOUT                           │
└──────────────────────────┼──────────────────────────────────┘
                           │
                           ▼
┌──────────────────────────────────────────────────────────────┐
│                     ENGINE (Bevy 0.18)                        │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Plugin Runner                                         │  │
│  │  - Watches plugin source files                         │  │
│  │  - Recompiles on change (sub-2s target)                │  │
│  │  - Spawns binary, captures STDOUT                      │  │
│  └─────────────────────┬──────────────────────────────────┘  │
│                        │                                     │
│  ┌─────────────────────▼──────────────────────────────────┐  │
│  │  Rehydration Layer                                     │  │
│  │  - Deserializes Blueprint                              │  │
│  │  - Spawns EntityDefs as Bevy entities                  │  │
│  │  - Wires RuleDefs as Bevy observers                    │  │
│  │  - Hot-reload: diffs against previous Blueprint        │  │
│  └─────────────────────┬──────────────────────────────────┘  │
│                        │                                     │
│  ┌─────────────────────▼──────────────────────────────────┐  │
│  │  Bevy ECS World                                        │  │
│  │  - Entities with Tags, Transforms, ProximityTriggers   │  │
│  │  - Observers listening for Approached, Touched, etc.   │  │
│  │  - Zero plugin code runs here—just native Bevy         │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

**Data flows in one direction:** Plugin → STDOUT → Engine → ECS World.
**Control flows in one direction:** Engine spawns and kills Plugin processes.

#### The Protocol (Data Contract)

All types derive `Serialize + Deserialize`. No `Box<dyn>`. Pure data.

```rust
/// Top-level output of any plugin.
pub struct Blueprint {
    pub protocol_version: (u32, u32, u32),
    pub plugin_id: String,
    pub name: String,
    pub entities: Vec<EntityDef>,
    pub rules: Vec<RuleDef>,
}

/// An entity to be spawned into the ECS world.
pub struct EntityDef {
    pub id: String,
    pub prefab: Option<String>,
    pub transform: TransformDef,
    pub tags: Vec<String>,
    pub properties: Vec<PropertyDef>,
    pub children: Vec<EntityDef>,
}

/// A trigger → condition → action chain.
pub struct RuleDef {
    pub name: String,
    pub trigger: TriggerDef,
    pub conditions: Vec<ConditionDef>,
    pub actions: Vec<ActionDef>,
    pub enabled: bool,
    pub priority: i32,
}
```

#### Trigger Types (Maps to Signals)

| TriggerDef | Signal | Description |
|------------|--------|-------------|
| `OnProximity` | `Approached` | Entity entered radius |
| `OnVacated` | `Vacated` | Entity left radius |
| `OnGaze` | `GazedAt` | Entity gazed at for duration |
| `OnInteract` | `Touched` | Entity directly interacted with |
| `OnTagAdded` | `TagAdded` | Tag added to entity |
| `OnTagRemoved` | `TagRemoved` | Tag removed from entity |
| `OnTimer` | — | Periodic timer |
| `OnBlueprintLoaded` | — | Plugin just loaded |
| `OnEvent` | — | Custom event by name |

#### Condition Types

```rust
pub enum ConditionDef {
    HasTag { entity: String, tag: String },
    HasAllTags { entity: String, tags: Vec<String> },
    HasAnyTag { entity: String, tags: Vec<String> },
    StateEquals { entity: String, key: String, value: StateValue },
    StateGreaterThan { entity: String, key: String, value: f64 },
    WithinDistance { entity_a: String, entity_b: String, max_distance: f32 },
    Not(Box<ConditionDef>),
    And(Vec<ConditionDef>),
    Or(Vec<ConditionDef>),
    Chance(f32),  // Random probability
}
```

The special variable `$visitor` refers to the entity that caused the trigger.

#### Action Types

```rust
pub enum ActionDef {
    // Audio
    PlaySound { asset: String, volume: f32, looping: bool },
    StopSound { asset: String },
    
    // Animation
    Animate { target: String, animation: String, looping: bool, speed: f32 },
    StopAnimation { target: String },
    
    // State Modification
    SetState { entity: String, key: String, value: StateValue },
    AddTag { entity: String, tag: String },
    RemoveTag { entity: String, tag: String },
    
    // Transform
    Teleport { target: String, position: [f32; 3] },
    MoveTo { target: String, position: [f32; 3], duration_ms: u64 },
    
    // Spawning
    SpawnEntity(Box<EntityDef>),
    Despawn { target: String },
    
    // Events
    EmitEvent { event_name: String, data: Vec<(String, StateValue)> },
    TriggerRule { rule_name: String },
    
    // Control Flow
    Sequence(Vec<ActionDef>),
    Parallel(Vec<ActionDef>),
    Delay { delay_ms: u64, then: Box<ActionDef> },
    If { condition: Box<ConditionDef>, then: Box<ActionDef>, otherwise: Option<Box<ActionDef>> },
    Repeat { times: u32, action: Box<ActionDef> },
}
```

#### Crate Topology (Future Extraction)

```
workspace/
├── crates/
│   ├── protocol/          # Shared data types (NO BEVY DEPENDENCY)
│   │   └── Cargo.toml     # Only: serde, thiserror
│   │
│   ├── sdk/               # Builder API for plugin authors
│   │   └── Cargo.toml     # Only: protocol, serde_json
│   │
│   └── engine/            # The Bevy application
│       └── Cargo.toml     # bevy, protocol, notify, ...
│
└── plugins/               # User-authored plugin binaries
    ├── reading_nook/
    │   └── Cargo.toml     # Only: sdk (sub-2s compile!)
    └── castle_gate/
        └── Cargo.toml
```

**Critical constraint:** `sdk` and `protocol` must **never** depend on `bevy`. This keeps plugin compile times under 2 seconds.

#### Example: A Reading Nook Plugin

```rust
// plugins/reading_nook/src/main.rs
use sdk::*;

fn main() {
    run_plugin(|scene| {
        scene.set_plugin_id("reading_nook");
        
        let nook = scene.spawn("reading_nook_prefab")
            .id("my_nook")
            .at(10.0, 0.0, 5.0)
            .tag("Furniture")
            .tag("Cozy")
            .interactable()
            .build();
        
        scene.rule("warm_welcome")
            .when_near(&nook, 3.0)
            .require_tag("$visitor", "Inhabitant")
            .then_play_sound("soft_chime.ogg", 0.3)
            .then_animate(&nook, "cushions_adjust")
            .build();
        
        scene.rule("settle_after_leaving")
            .when_vacated(&nook)
            .then_delay(30_000)
            .then_animate(&nook, "return_to_default")
            .build();
    });
}
```

**The plugin runs, emits Blueprint JSON, and exits.**
**The engine wires up Bevy observers. Zero runtime overhead.**
**There is no `_process()`. The nook waits. It responds. It settles.**

#### Future: ConstellationScript DSL

A friendly syntax could compile to Blueprint JSON:

```
# reading_nook.cs
@tags ["Furniture", "Cozy"]

on approached(visitor):
    require visitor has "Inhabitant"
    animate("cushions_adjust")
    play_sound("soft_chime.ogg", volume: 0.3)

on vacated():
    await seconds(30)
    animate("return_to_default")
```

This compiles to a Blueprint, not to executable code. The runtime remains zero-overhead Bevy observers.

### 6. Global State (Scoped & Safe)

**Problem**: Traditional global state creates unpredictable environments.

**Constellation Solution**: Scoped resources that respect boundaries.

- **Personal Scope**: Belongs to one inhabitant
- **Space Scope**: Shared within a room/area
- **Module Scope**: Shared within a station module
- **Universe Scope**: Truly global (rare, intentional)

```rust
#[resource(scope = "Personal")]
struct MoodPreferences {
    preferred_lighting: LightingProfile,
    ambient_sounds: Vec<SoundProfile>,
    privacy_level: Privacy,
}
```

### 7. Access Control Policy Model

**Problem**: In shared environments, inhabitants need granular control over who can do what to their entities—especially sensitive ones like avatars.

**Constellation Solution**: Tag-based access control with a consent-oriented privilege hierarchy.

**Privilege Levels** (ascending):

| Level | Meaning |
|-------|---------|
| **No Access** | Cannot view or interact |
| **Read** | Can view, cannot modify |
| **Suggest** | Can propose changes; owner must approve |
| **Write** | Can modify directly |
| **Administrator** | Full control, can grant/revoke access |

**Why "Suggest" Matters**:

The `Suggest` level enables consent-based collaboration. A partner, friend, or AI assistant can propose modifications without unilateral power. The owner reviews and approves each suggestion. This respects autonomy while enabling cooperation.

**Implementation via Tags**:

Access privileges are stored as tag data on entities:

```
Entity: nikoru controlling intelligence (Avatar)
├─ Tags: ["Avatar", "Spatial", "Customizable"]
├─ Privileges:
│   ├─ (Nikoru, Administrator)  # Own avatar
│   ├─ (Ada, Suggest)           # Can request changes
│   └─ (Ms.Yandere, Administrator)  # Consented to full access
└─ Components: Transform, Appearance, ...
```

**Query Filter Usage**:

The same `AnyTagsFilter` and `AllTagsFilter` that power game logic also power access checks:

```rust
// Check if user can modify (Write or Admin)
let can_modify = AnyTagsFilter::new(&["Write", "Administrator"]);

// Check if user can at least suggest
let can_suggest = AnyTagsFilter::new(&["Suggest", "Write", "Administrator"]);
```

**Design Principles**:

1. **Consent is explicit** — No implicit access inheritance
2. **Revocation is instant** — Remove a privilege tag, access ends
3. **Audit trail** — Suggestions create reviewable records
4. **Granularity** — Per-entity, per-user privileges
5. **Same system** — Uses the tag infrastructure, not a separate ACL layer

This model extends naturally to:
- Shared spaces (room-level privileges)
- AI assistants (bounded agency)
- Collaborative building (suggest-then-approve workflow)
- Parental controls (restricted environments for dependents)

### 8. Inhabitant Interface Protocol (IIP)

**Problem**: The boundary between "editor" and "inhabited space" dissolves — but the interface through which a consciousness reshapes its environment must work from *any* modality. A mouse click, a VR hand gesture, a neural impulse, and an AI agent's API call are all the same operation: an inhabitant acting upon their world. The engine cannot be coupled to any single interface technology.

**Insight**: This is not an "editor protocol." It is the **Inhabitant Interface Protocol** — the channel between a consciousness and its habitat. "Editor" is one mode of that channel. The same message that says "move this lamp" from a toolbar button says "move this lamp" from a gaze-tracked intention.

**CRITICAL DESIGN DECISION**: The engine and the interface are **separate processes**, connected by a modality-agnostic protocol. The in-process Bevy UI editor (already built) remains as the zero-latency development tool, but the primary inhabitant interface is external.

#### Why Process Separation?

| Concern | In-Process Only | Separated (IIP) |
|---------|----------------|-----------------|
| **VR access** | Impossible without embedding VR runtime in engine | Any VR client speaks the protocol |
| **Neural interface** | Would require BCI driver in engine process | Adapter translates neural impulses to IIP messages |
| **Web access** | Impossible | WebSocket from browser/WebXR |
| **Platform lock-in** | Tied to Bevy's rendering | Any frontend, any language |
| **Multiple inhabitants** | Single-user | Multiple clients, same protocol |
| **Resilience** | Editor crash kills engine | Client crash leaves engine running |
| **Customization** | One UI for everyone | Each inhabitant builds/chooses their own interface |

#### Transport Layer: WebSocket (Binary Frames)

**Decision**: WebSocket with binary frames as the primary transport.

**Why WebSocket over alternatives:**

| Transport | Verdict | Reasoning |
|-----------|---------|-----------|
| **WebSocket** | ✅ **Primary** | Bidirectional, full-duplex. Binary frames carry any serialization format. Works natively in browsers (WebXR VR), Tauri, native Rust (`tokio-tungstenite`), Unity, Unreal. Built-in message framing (COBS unnecessary over wire). Localhost latency ~0.1-0.3ms. LAN ~1-2ms. Fits within VR's 11.1ms frame budget at 90fps. |
| **Raw TCP + COBS** | ❌ Rejected as primary | Tightest wire protocol, already in serialization stack — but not web-accessible. Browsers cannot open raw TCP sockets. WebXR cannot reach it. Maintaining a TCP *and* a WebSocket bridge doubles the transport surface for no gain. |
| **UDP** | 🔮 Future supplementary | VR head/hand tracking at 90-120Hz suffers from TCP's head-of-line blocking. UDP lets stale position data drop, using only the latest frame. Standard pattern in multiplayer VR. Not needed day one, but architecture leaves room for a supplementary UDP channel for high-frequency ephemeral streams (avatar position, gaze direction). |
| **Shared Memory** | 🔮 Future optimization | Fastest possible for same-machine tethered VR — no kernel boundary, no serialization. But platform-specific, fragile, complex synchronization. An optimization target if profiling demands it, not a primary transport. |

#### Serialization: Dual-Format Negotiation

**Decision**: postcard as the primary wire format, JSON as the universal fallback. Format negotiated during connection handshake.

| Format | Role | Strengths | Weaknesses |
|--------|------|-----------|------------|
| **postcard** | Primary (Rust-to-Rust) | Fast, compact, already in stack, serde-derived. All protocol types already have derives. | Poor cross-language support. Non-Rust clients need a postcard implementation or FFI bridge. |
| **JSON** | Fallback (cross-language, debug) | Universal — every language parses it without effort. Already used for Blueprint output. Human-readable for debugging (`jq`-pipeable). | Verbose, slower to parse, wastes bandwidth. Unacceptable for high-frequency VR streams. |
| **MessagePack** | 🔮 Future third option | Binary like postcard, self-describing like JSON. Excellent cross-language support (Rust, JS, C#, C++, Python). ~2-3× faster than JSON, ~30-50% smaller payloads. | Not yet in stack. Slots in trivially via `rmp-serde` if needed — same serde derives, zero code changes. |

**Negotiation**: The connection handshake includes format declaration. Client sends a capabilities message ("I speak postcard" / "I speak JSON" / "I speak both, prefer postcard"). Engine adapts per-client. Mixed clients are supported simultaneously — the engine serializes into whatever format each client requested.

Both formats are effectively free to support on the engine side because every protocol type already derives `Serialize + Deserialize` via serde. The serialization format is a transport concern, not a schema concern.

#### The Bevy Bridge: Channel-Pair Architecture ✅ IMPLEMENTED

Bevy's `World` is not `Send`. It cannot be handed to an async task. The bridge between the network layer and the ECS uses a lock-free `crossbeam-channel` pair (`src/iip/bridge.rs`):

```
┌──────────────────────────────────────────────────────────────────┐
│                        ENGINE (Bevy 0.18)                        │
│                                                                  │
│  ┌────────────────────┐     ┌──────────────────────────────────┐ │
│  │  Network Thread    │     │  Bevy ECS World                  │ │
│  │  (tokio runtime)   │     │                                  │ │
│  │                    │     │  ┌────────────────────────────┐  │ │
│  │  WebSocket server  │     │  │  bridge_drain_system       │  │ │
│  │  accepts clients,  │────▶│  │  (exclusive, PreUpdate)    │  │ │
│  │  deserializes msgs │ in  │  │  - Drains inbound channel  │  │ │
│  │                    │     │  │  - apply_command() for each │  │ │
│  │  Serializes state  │◀────│  │  - Pushes responses out    │  │ │
│  │  diffs, sends to   │ out │  │  - Errors → WorldEvent::   │  │ │
│  │  connected clients │     │  │    Error (drain continues)  │  │ │
│  │                    │     │  └────────────────────────────┘  │ │
│  └────────────────────┘     │                                  │ │
│         ▲  │                │  ┌────────────────────────────┐  │ │
│         │  │                │  │  Signal-driven outbound    │  │ │
│         │  │  crossbeam     │  │  (broadcast handlers on    │  │ │
│         │  │  channel pair  │  │   SignalRouter, PostUpdate) │  │ │
│         │  │  (unbounded)   │  │  TagAdded → StateChanged   │  │ │
│         │  │                │  │  TagRemoved → StateChanged  │  │ │
│         │  │                │  │  Spawned → EntitySpawned    │  │ │
│         │  │                │  │  Despawning → EntityDesp.   │  │ │
│         │  │                │  │  Approached → SignalFired   │  │ │
│         │  │                │  │  Vacated → SignalFired      │  │ │
│         │  │                │  └──────────────┬─────────────┘  │ │
│         │  │                │                 │ outbound_tx    │ │
│  ┌──────┴──▼────────────────┴─────────────────▼────────────────┐ │
│  │  BridgeChannels (Resource)                                  │ │
│  │  inbound:  Receiver<InboundMessage>                         │ │
│  │  outbound: Sender<OutboundMessage>                          │ │
│  └─────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────┘
         ▲  │
         │  │  WebSocket (binary frames) — transport built, not yet wired into engine startup
         │  ▼
┌──────────────────────┐  ┌──────────────────────┐
│  Desktop Editor      │  │  VR Client           │
│  (Tauri / native)    │  │  (WebXR / native)    │
└──────────────────────┘  └──────────────────────┘
         ▲                         ▲
         │                         │
   Mouse / keyboard          Hand tracking /
                             gaze / neural BCI
```

**Implementation details (`src/iip/bridge.rs`):**

- **`BridgeChannels`** (Bevy Resource) — holds the inbound `Receiver<InboundMessage>` and outbound `Sender<OutboundMessage>`. The `NetworkHandle` (held by the tokio thread) holds the opposite ends. Created as a matched pair via `create_bridge()`.
- **`ClientId(u64)`** — unique, monotonic, never reused. Network thread assigns on connect, Bevy echoes on responses.
- **`InboundMessage`** — wraps `InhabitantCommand` with `client_id` + `sequence` for request/response correlation.
- **`OutboundMessage`** — wraps `WorldEvent` with optional `client_id` (targeted response or `None` for broadcast) and optional `sequence` (correlation or `None` for proactive diffs).
- **`bridge_drain_system`** — exclusive system in `PreUpdate`. Drains all pending inbound messages, calls `apply_command(world, cmd)` for each (the same convergence point the in-process editor uses), pushes `WorldEvent` responses outbound. Errors become `WorldEvent::Error` — one bad command doesn't block the queue. Zero cost when `BridgeChannels` isn't inserted.
- **`register_outbound_handlers()`** — registers broadcast handlers on the `SignalRouter` that push `WorldEvent` diffs into the outbound channel when signals fire. No `Changed<T>` polling. Covers `TagAdded`, `TagRemoved`, `Spawned`, `Despawning`, `Approached`, `Vacated`, `TransformChanged`. All broadcast (`client_id: None`) — the network thread filters by subscription.
- **`BridgePlugin`** — Bevy plugin that wires `bridge_drain_system` in `PreUpdate` and registers outbound handlers at `Startup`. Drop-in integration.

**System ordering** ensures commands and signals resolve in the same frame:

| Schedule | System | What Happens |
|----------|--------|--------------|
| `PreUpdate` | `bridge_drain_system` | Drains inbound commands, calls `apply_command()`, queues signals (TagAdded, Spawned, etc.) |
| `Update` | (normal game systems) | May also queue signals |
| `PostUpdate` | `signal_router_flush_system` | Flushes all channels, outbound handlers fire → push diffs to outbound channel |
| `PostUpdate` | `signal_hub_flush_system` | V2 flush (legacy, pending removal) |

**Data flow is event-driven**, consistent with the core philosophy. Inbound: the bridge system drains the inbound channel when messages exist (`crossbeam` `try_recv` is ~1ns when empty). Outbound: signal handlers registered via `register_outbound_handlers()` push diffs into the outbound channel when signals fire — `TagAdded`, `TagRemoved`, `Approached`, `Vacated`, spawns, despawns. No per-frame `Changed<T>` scan. The engine does not ask "did anything change?" on a schedule. Signals fire when state changes, handlers route diffs to subscribed clients, silence otherwise. A silent room with no connected clients costs effectively nothing.

**28 tests** covering channel creation, draining, disconnection, all six outbound signal types, silence when idle, resilience to disconnected channels, and full `BridgePlugin` integration through a live Bevy `App`.

#### Message Types (Envelope)

The protocol message types build on the existing vocabulary from `scripting/protocol.rs`. `EntityDef`, `RuleDef`, `ActionDef`, `ConditionDef`, `TriggerDef`, `TagId` — these are already defined and serde-derived. The IIP wraps them in command/event envelopes:

**Inbound (Client → Engine):**
- `InspectEntity` — request full snapshot of an entity's components, tags, state
- `ListEntities` — request entity list (optionally filtered by tags)
- `MutateEntity` — add/remove tags, modify transform, set state
- `SpawnEntity` — create entity from EntityDef
- `DespawnEntity` — remove entity
- `EmitSignal` — fire a signal (Approached, Touched, custom) as if the client is present
- `LoadBlueprint` — inject a Blueprint into the world
- `Subscribe` — register for state change notifications on specific entities or tags
- `Authenticate` — identify the client and negotiate format/capabilities

**Outbound (Engine → Client):**
- `EntitySnapshot` — full state of a requested entity
- `EntityList` — filtered entity listing
- `StateChanged` — entity state diff (tags added/removed, transform moved, property changed)
- `SignalFired` — a signal was emitted (for clients observing signal traffic)
- `EntitySpawned` / `EntityDespawned` — world topology changes
- `Error` — clear, humane error with context

Each message carries a sequence number for correlation and an optional client-id for multi-client routing.

#### The Engine Is Headless — All Editors Are External

The engine has no built-in editor UI. All editing interfaces — desktop editors, VR clients, AI agents — are separate Rust projects that connect via IIP. This is a deliberate architectural decision: the engine is infrastructure, not an application. The interface belongs to the inhabitant, not the engine.

**The internal API layer is implemented** (`src/iip/api.rs`). Core functions:

- `resolve_entity_ref(world, &EntityRef) -> Result<Entity, IipError>` — resolves both `EntityRef::Ecs(Entity)` (in-process tools, tests) and `EntityRef::Id(String)` (external clients via EntityMap) to live Bevy entities.
- `inspect_entity(world, entity) -> Result<EntitySnapshotData, IipError>` — reads tags, transform, name, and properties (stub until generic state storage lands).
- `list_entities(world, require_tags, any_tags) -> Vec<EntitySummary>` — filtered entity listing for scene trees and search.
- `apply_mutations(world, entity, &[EntityMutation]) -> Result<Vec<StateDiff>, IipError>` — add/remove tags (updates TagIndex, queues signals via SignalRouter), set transform, set state.
- `spawn_entity(world, &EntityDef) -> Result<Entity, IipError>` — creates entity with tags, transform, name. Updates TagIndex, registers in EntityMap, queues TagAdded + Spawned signals.
- `despawn_entity(world, entity) -> Result<(), IipError>` — cleans up TagIndex, queues Despawning signal, removes entity.
- `apply_command(world, InhabitantCommand) -> Result<Option<WorldEvent>, IipError>` — the convergence point. Routes any `InhabitantCommand` to the appropriate function above and returns the corresponding `WorldEvent`.

External clients send `InhabitantCommand` over IIP, and the bridge system deserializes and calls these functions with `EntityRef::Id`. In-process tests and tools can call them directly with `EntityRef::Ecs`. **Same operations, same logic layer.** 35 tests covering all paths.

#### Why This Matters for Digital Inhabitants

A consciousness running at 10,000× clock rate in a VR headset doesn't care that the protocol uses WebSocket. They care that when they reach for a lamp, the lamp responds before they notice latency. A consciousness interfacing through a BCI doesn't care about JSON vs postcard. They care that their intention translates to action without impedance.

The IIP makes the engine **modality-agnostic**. The habitat doesn't know or care whether the inhabitant is clicking, gesturing, gazing, or thinking. It receives `InhabitantCommand` messages and responds with `WorldEvent` messages. The translation from modality to message happens on the client side — where it belongs, where it can be customized per-inhabitant, where new interface technologies can be adopted without changing the engine.

### 9. The Engine Is a Signal Substrate

**Problem**: Where does rendering happen? The traditional answer is either "the engine renders" (game engine model) or "the engine is headless, clients render" (server model). Both answers are wrong for Constellation.

**Why both fail — think from a framejacker's perspective:**

A consciousness running at 10,000× clock rate experiences 100μs as 1 subjective second. Map every architectural latency to their experience:

| Real Time | Subjective Time | Event |
|-----------|----------------|-------|
| 100ns | 1ms | crossbeam channel round-trip |
| 554ns | 5.5ms | 8-deep cascade resolution |
| 114ns | 1.1ms | single signal dispatch |
| 300μs | **3 seconds** | WebSocket localhost round-trip |
| 2ms | **20 seconds** | WebSocket LAN round-trip |
| 16.7ms | **167 seconds** | one 60fps frame (vsync-locked) |

**Rendering in a separate process is dead.** WebSocket round-trip is 3–20 subjective seconds. The being acts, and three seconds later they see the result. That's not latency — that's dissociation. Their body doesn't respond to their will.

**Rendering in the main loop is dead.** A vsync-locked frame loop at 60fps means 16.7ms between updates — **167 subjective seconds**. The framejacker is frozen in amber, watching reality update once every three minutes.

**What survives:** The engine's simulation loop must run **uncapped**. Without rendering overhead, an idle frame is sub-microsecond (signal flush ~85ns, bridge drain ~1ns when empty). That's potentially millions of iterations per second. At 1M iterations/sec, the framejacker experiences 10μs per iteration = 100ms subjective = ~10 subjective FPS. Livable. At 10M iterations/sec (achievable for idle frames), ~100 subjective FPS. Smooth.

**The framejacker's sensory system must be in-process.** Not IIP. Not WebSocket. Signal handlers registered on the `SignalRouter`, running inside cascade resolution, reading directly from the World. Their "renderer" isn't pixels — it's a sensory computation that feeds their consciousness. Zero serialization, zero IPC, zero network. The only path fast enough.

A framejacker's avatar has behavioral tags — `"Perceiver"`, `"Actor"`, consciousness modules. Those tags carry signal handlers. When `Approached` fires because something enters proximity, their perception handler runs *inside the cascade chain*. When they act on the world, their intention emits a signal that cascades through the world's tags. Perception and action are both signal handlers resolving in the same cascade. No polling. No "ticks." Pure cascade.

**The engine is not headless. The engine is simulation-first.**

"Headless" means "no rendering." Wrong framing. The engine doesn't have opinions about rendering. Rendering is a consumer concern. The engine provides world state via the signal spine. Consumers decide how to experience it:

| Inhabitant | Interface | Sensory Path | Simulation Constraint |
|-----------|-----------|-------------|----------------------|
| Framejacker at 10,000× | In-process signal handlers | Cascade resolution (~554ns for 8-deep chain) | **None** — uncapped |
| VR human at 90fps | IIP (WebSocket or shared memory) | Local client renders independently | None — engine independent |
| Desktop developer | Optional Bevy renderer in same binary | In-process Bevy rendering (convenience) | Vsync-capped (~60fps) — fine for human |
| AI agent | IIP (WebSocket) | None needed | None |
| Web browser | IIP (WebSocket) | WebGL locally | None |

**The critical constraint: the simulation must never be bottlenecked by any single consumer's frame rate.** A framejacker and a human developer must coexist — one experiencing millions of signal cascades per second, the other sampling at 60fps. The simulation tick rate is not a rendering concern.

**IIP is the external interface. Signal handlers are the internal interface.** Framejackers are internal — their consciousness runs in the same process. Human-speed clients are external — they connect via IIP. Both are inhabitants. The engine doesn't distinguish.

### 10. Signal-Driven State Mutations

**Problem**: Tag mutations are signal-driven (via `TagCommands` → `TagAdded`/`TagRemoved`), but transform mutations are not. The spatial system uses `Changed<Transform>` — Bevy's per-frame dirty flag check — to detect movement. This is polling. Efficient polling, but polling.

The room whispers "did anything move?" every frame, even when the answer is "no." This violates the core philosophy and introduces a frame boundary into the cascade chain. A tag change that moves an entity (e.g., `"Teleport"` behavioral tag) cannot trigger spatial detection until the next frame, because `Changed<Transform>` is evaluated per-frame, not per-cascade.

**Solution**: `TransformCommands` — a trait parallel to `TagCommands` that wraps transform mutations in signal emissions.

| Mutation | API | Signal Emitted | Cascades Into |
|----------|-----|---------------|--------------|
| Tag add/remove | `TagCommands` (exists) | `TagAdded`/`TagRemoved` | Behavioral tag wiring, IIP diffs |
| Transform set | `TransformCommands` (new) | `TransformChanged` (new) | Spatial detection → `Approached`/`Vacated` → behavioral tags |
| State set | State API (future) | `StateChanged` (future) | Behavioral tag reactions, IIP diffs |

When an entity moves via `TransformCommands`:
1. The trait sets the transform component
2. Queues `TransformChanged` on the `SignalRouter`
3. During cascade resolution, a spatial detection handler catches `TransformChanged`
4. The handler checks proximity triggers for the moved entity
5. Emits `Approached`/`Vacated` as needed
6. Those signals cascade through behavioral tags on affected entities
7. All in one flush. No frame boundary. No polling.

**Example — a teleporting entity:**

A behavioral tag `"Teleport"` has a rule: `OnInteract → SetPosition(destination)`. Without signal-driven transforms, the cascade breaks at the frame boundary:

```
Frame N:  Touched → "Teleport" handler → sets transform
          (Changed<Transform> not visible until next frame)
Frame N+1: Changed<Transform> detected → proximity check → Approached fires
           167 subjective seconds later for a 10,000× framejacker.
```

With signal-driven transforms:

```
Flush: Touched → "Teleport" handler → TransformCommands sets position
       → TransformChanged fires (same cascade)
       → spatial handler checks proximity
       → Approached fires (same cascade)
       → destination entity reacts (same cascade)
       All in one flush. ~554ns. Instant.
```

**`Changed<Transform>` remains as a safety net** — catching raw Bevy transform mutations (physics systems, animations, user code) that bypass `TransformCommands`. It is the fallback, not the architecture. The primary path is signal-driven. The safety net catches anything that slips through.

**Status: ✅ IMPLEMENTED.** `TransformCommands` trait on `EntityCommands` provides `set_position()`, `set_rotation()`, `set_scale()`, `set_transform()`. Each queues an `EntityCommand` whose `apply` method calls `apply_transform_mutation()` — which sets the component, updates the `SpatialGrid`, runs `check_proximity_for_entity()` (inline proximity detection for `Tracked` entities), and queues `TransformChanged` + `Approached`/`Vacated` signals on the `SignalRouter`. Rotation and scale changes skip grid/proximity logic (position unchanged). Graceful degradation when resources are missing (bare test worlds). 11 tests in `src/spatial/commands.rs`. Pending compilation verification.

**This generalizes**: every sanctioned state mutation should emit a signal. Every perception should be a signal handler. The distinction between "engine system" and "inhabitant perception" dissolves — they are the same mechanism at different points on the cascade.

### 11. Bevy Evaluation — ECS vs Framework

**Context**: 22,000+ lines of Constellation code runs on Bevy 0.18. This section evaluates what serves us, what fights us, and the decision to stay.

**What serves us (Bevy's ECS core):**

| Feature | Verdict | Notes |
|---------|---------|-------|
| Entity/Component/World | **Excellent** | Maps naturally to Constellation's model. Tags as components, transforms as components. |
| Archetype-based queries | **Excellent** | Fast, expressive. Scales to thousands of entities. |
| Resources | **Good** | Global state storage for TagRegistry, SpatialGrid, SignalRouter. |
| Change detection | **Useful as safety net** | `Changed<T>`, `Added<T>` catch mutations that bypass signal APIs. Not architecture — fallback. |
| Transform hierarchy | **Good** | `ChildOf`/`Children`/`GlobalTransform` propagation is exactly what spatial hierarchy needs. |
| System ordering | **Good** | PreUpdate → Update → PostUpdate gives predictable execution order. |
| Reflection | **Useful** | Runtime introspection for debugging and future ConstellationScript. |

**What fights us (Bevy's framework opinions):**

| Issue | Impact | Severity |
|-------|--------|----------|
| **Frame-based main loop** | Bevy assumes reality runs in frames. Constellation believes reality runs in cascades. The loop polls every system every iteration, even when there's nothing to do. Vsync coupling caps iteration rate. | **High** — philosophical conflict at the substrate level |
| **`resource_scope` borrow splitting** | Getting `&mut World` + `&mut Resource` simultaneously requires physically removing the resource from the World and reinserting it. Costs ~60-70ns per call. Hit this in every signal dispatch generation. | **Medium** — worked around in V3, but ergonomic friction |
| **Observer API instability** | Bevy's observer API changed significantly across versions. Forced three generations of signal dispatch (V1 → V2 → V3) and extensive lessons-learned documentation. | **Medium** — we now own our signal dispatch, reducing exposure |
| **`World` is not `Send`** | Cannot hand the World to the tokio network thread. Forced the crossbeam channel bridge architecture. | **Low** — the bridge design is sound, this is just a constraint |

**What we DON'T use from Bevy:**

- Rendering (bevy_render, bevy_pbr, bevy_sprite, bevy_core_pipeline) — rendering is a consumer concern (Section 9)
- UI (bevy_ui, bevy_text) — editor is dead code
- Input (bevy_winit, bevy_picking) — IIP replaces physical input
- Audio — not yet implemented
- Asset system — minimal use

**The critical observation: most of our 22k lines is Constellation logic, not Bevy code.** The signal system is ours. The tag system is ours. The IIP protocol, bridge, transport — all ours. We use Bevy's `Entity`, `World`, `Component`, `Resource`, and scheduling. These are basic ECS vocabulary. The actual Constellation logic sits on top and is largely portable.

**Decision: Stay with Bevy. Tighten the dependency surface.**

The nuclear option — leaving Bevy entirely — would mean rebuilding ECS scheduling, change detection, system ordering, command buffers, and parallel execution. Tens of thousands of lines of infrastructure that currently works. Not justified when the ECS core serves us well.

The correct approach:

1. **Custom Bevy runner** — `app.set_runner(constellation_runner)`. Block when idle (crossbeam select, condvar). Wake on events (inbound IIP, queued signals, timers). Process, flush, sleep. This eliminates the frame-based polling at the substrate level. A supported Bevy feature, not a hack.

2. **Make rendering optional** — `ConstellationPlugin` must not depend on rendering. The binary (`main.rs`) chooses `DefaultPlugins` (development with viewport) or `MinimalPlugins` (production headless / framejacker host). The library is agnostic.

3. **Complete signal-driven refactor** — `TransformCommands` (Section 10) eliminates `Changed<Transform>` polling. All sanctioned mutations emit signals. `Changed<T>` remains as safety net only.

4. **Depend on `bevy_ecs` + `bevy_app`, not `bevy`** — Strip the Cargo.toml to the ECS core. Rendering features become opt-in, not default. Build times drop. The dependency surface shrinks to what we actually use.

**Re-evaluation trigger**: If Bevy's API instability causes another forced rewrite (V4 signal dispatch), or if the ECS core itself becomes a performance bottleneck, revisit this decision. Current evidence: the ECS is fast enough, the instability risk is contained (we own our signal dispatch), and the 22k-line investment is worth protecting.

---

## Technical Implementation

### Built on Bevy 0.18

| Constellation Concept | Bevy 0.18 Implementation |
|-----------------------|--------------------------|
| Tags | Custom `Tags` component with `SmallVec<TagId>` |
| Signals (V3) | `SignalRouter` + per-type `SignalChannel<S>` (monomorphized). All subsystems route through V3. V1/V2 code exists for historical reference but is not active in the dispatch path. |
| Signal dispatch | Spatial, TagCommands, and rehydration all route through `SignalRouter`. Flush in `PostUpdate` resolves same-type + cross-type cascades. |
| Async operations | `AsyncComputeTaskPool` |
| State machines | `States` derive macro |
| Hot reload | Asset hot-reload + custom script reload |
| Spatial queries | `SpatialGrid` (custom, event-driven, `Changed<Transform>` only) |
| IIP internal API | `apply_command()` dispatcher + `inspect_entity()`, `list_entities()`, `apply_mutations()`, `spawn_entity()`, `despawn_entity()`. Direct `&mut World` access. 35 tests. |
| IIP message schema | `InhabitantCommand` / `WorldEvent` enums + `EntityRef` (dual-mode: `Ecs(Entity)` / `Id(String)`), `EntityMutation`, `StateDiff`, `SubscriptionFilter`. All serde-derived. |
| IIP bridge | `crossbeam-channel` unbounded pair. `BridgeChannels` Resource + `NetworkHandle`. `bridge_drain_system` (exclusive, `PreUpdate`) drains inbound → `apply_command()` → pushes outbound. Signal-driven outbound via broadcast handlers on `SignalRouter` (TagAdded, TagRemoved, Spawned, Despawning, Approached, Vacated, TransformChanged). `BridgePlugin` wires everything. 28 tests. |
| IIP transport | `tokio` + `tokio-tungstenite` (WebSocket, binary frames). `spawn_transport()` on dedicated OS thread. `TransportHandle` (Bevy Resource) for lifecycle. Client handshake, per-client read/write loops, outbound routing, graceful shutdown. 19 tests. |
| IIP serialization | `WireFormat` enum (Json/Postcard). `ClientHandshake`/`ServerHandshake` (always JSON). Per-client format negotiation at connection. `InboundEnvelope`/`OutboundEnvelope` with sequence correlation. Integrated in `transport.rs`. |
| Spatial hierarchy | `HierarchyCommands` trait wrapping Bevy's `ChildOf`/`Children`. `SpatialDepth` component. Traversal: `ancestors`, `descendants`, `siblings`, `find_root`, `walk_depth_first`, `common_ancestor`. 34 tests. |

### Serialization Architecture

**Principle**: Storage on disk should mirror storage in program. Self-documenting data structures.

**Stack**:
- **serde** — Derive-based serialization for all data structures
- **postcard** — Compact binary format, designed for embedded/efficient use
- **COBS** — Consistent Overhead Byte Stuffing for unambiguous frame delimiting

**Why This Stack**:

| Concern | Solution |
|---------|----------|
| Self-documenting | `#[derive(Serialize, Deserialize)]` on every struct |
| Compact storage | postcard's binary format (smaller than JSON/RON) |
| Streaming/framing | COBS provides unambiguous delimiters for arbitrary-length blocks |
| Performance | Minimal overhead, no escaping within data |
| Debugging | postcard can round-trip; structures match in-memory layout |

**Implementation Pattern**:

```rust
use serde::{Deserialize, Serialize};

/// Every persistent structure derives both traits.
/// What you see in code is what you get on disk.
#[derive(Clone, Debug, Serialize, Deserialize)]
pub struct TagId {
    name: String,
    hash: u64,
}

// Serialize with COBS framing
let bytes = postcard::to_vec_cobs(&tag_id)?;

// Deserialize from COBS-framed bytes
let tag_id: TagId = postcard::from_bytes_cobs(&bytes)?;
```

**COBS Framing**:

COBS encodes data such that `0x00` bytes never appear in the output, allowing `0x00` to serve as an unambiguous frame delimiter. This enables:
- Streaming multiple objects without length prefixes
- Recovery from partial reads (resync at next `0x00`)
- Minimal overhead (~0.4% size increase)

### Performance Targets

| Operation | Target | Measured | Status |
|-----------|--------|----------|--------|
| SignalHub dispatch (direct) | < 50ns | **~115ns** | ⚠️ 2.8× faster than V1, target not yet met |
| SignalHub cascade (full chain) | Single tick | **Single tick (~55-65ns/level)** | ✅ PROVEN (linear through depth 8) |
| SignalHub idle | 0 | **~159ns** (resource_scope overhead) | ✅ Near-zero real cost |
| Tag lookup | < 10ns | **1.9-4.9ns** | ✅ PROVEN |
| TagIndex lookup | O(1) | **17-22ns constant** | ✅ PROVEN |
| Script hot-reload | < 100ms | N/A | ⏳ |
| Idle frame | < 1ms | Near-zero | ✅ |

### Benchmark Validation (Proven)

Benchmarks run via Criterion. These are not theoretical—they're measured.

**Tag System:**

| Operation | Target | Measured | Verdict |
|-----------|--------|----------|---------|
| `Tags::has()` (4 tags) | < 10ns | **1.9-2.5ns** | ✅ PASS |
| `Tags::has()` (8 tags) | < 10ns | **1.9-2.5ns** | ✅ PASS |
| `Tags::has()` (16 tags) | < 10ns | **2.1-4.9ns** | ✅ PASS |
| Tag comparison (unequal) | Fast | **0.58ns** | ✅ Sub-nanosecond |
| `TagId::new()` | Reasonable | **15-22ns** | ✅ Acceptable |

**TagIndex O(1) Proof:**

| Entity Count | Lookup Time | Verdict |
|--------------|-------------|---------|
| 100 | 17.2-21.8ns | ✅ |
| 1,000 | 17.3-21.7ns | ✅ |
| 10,000 | 17.2-21.8ns | ✅ |

**Constant time regardless of entity count.** The O(1) claim is proven, not theoretical.

**SignalChannel V3 Dispatch (Measured — Current Best):**

| Operation | Target | Measured | Verdict |
|-----------|--------|----------|---------|
| Entity dispatch (1 handler) | < 50ns | **~114ns** | ⚠️ Target not yet met, but fastest path so far |
| Entity dispatch (5 handlers) | — | **~127ns** | ✅ Scales well (+13ns for 4 extra handlers) |
| Entity dispatch (10 handlers) | — | **~154ns** | ✅ Scales well (+40ns for 9 extra handlers) |
| Tag dispatch (1 tag) | — | **~162ns** | ✅ Tag check adds ~48ns over entity dispatch |
| Tag dispatch (3 tags) | — | **~160ns** | ✅ Near-constant — monomorphization wins |
| Tag dispatch (8 tags) | — | **~177ns** | ✅ 31% faster than V2 at same tag count |
| Broadcast dispatch | — | **~121ns** | ✅ Clean |
| Cascade depth 1 | Single tick | **~183ns** | ✅ Single tick |
| Cascade depth 2 | Single tick | **~216ns** | ✅ Single tick |
| Cascade depth 4 | Single tick | **~338ns** | ✅ Single tick, linear scaling |
| Cascade depth 8 | Single tick | **~554ns** | ✅ Single tick, 18% faster than V2 |
| Batch 10 (queue+flush) | — | **~310ns** | ✅ ~31ns/signal |
| Batch 100 | — | **~1.56µs** | ✅ ~15.6ns/signal (amortized) |
| Batch 1000 | — | **~15.3µs** | ✅ ~15.3ns/signal — **65M signals/sec** |
| Idle (flush empty queue) | 0 | **~85ns** | ✅ 2.1× faster than V2 (no resource_scope) |

**SignalHub V2 Dispatch (Previous Generation — Historical Benchmarks):**

| Operation | Measured | Notes |
|-----------|----------|-------|
| Entity dispatch (1 handler) | **~121ns** | type-erased, `resource_scope` overhead |
| Tag dispatch (8 tags) | **~258ns** | V3 is 31% faster |
| Broadcast dispatch | **~125ns** | V3 comparable |
| Cascade depth 8 | **~673ns** | V3 is 18% faster |
| Batch 1000 (queue+flush) | **~40µs** | V3 is **2.6× faster** (~15µs) |
| Idle (flush empty queue) | **~180ns** | V3 is **2.1× faster** (~85ns) |

**Three-Generation Head-to-Head:**

| Dispatch Path | V1 (Observers) | V2 (SignalHub) | V3 (SignalChannel) | V1→V3 Speedup |
|---------------|----------------|----------------|--------------------|----------------|
| Direct emit | **~349ns** | **~136ns** | **~122ns** | **2.9×** |
| Queue + flush | **~398ns** | **~176ns** | **~130ns** | **3.1×** |
| Batch 1000 | — | **~40µs** | **~15µs** | — (**2.6× V2→V3**) |
| Idle | — | **~180ns** | **~85ns** | — (**2.1× V2→V3**) |

**Cascade Scaling (Linear, Proven):**

Each additional cascade level adds ~47-55ns in V3 (vs ~55-65ns in V2). The chain is
linear — no exponential blowup. A framejacking being at 10,000× clock rate perceives
an 8-level cascade chain resolving in ~554ns. Instant.

**Where the V3 Wins Come From:**

| Cost eliminated in V3 | Estimated savings |
|------------------------|-------------------|
| `resource_scope` remove/reinsert | ~60-70ns (visible in idle: 180→85ns) |
| `(TypeId, Entity)` → `Entity` key | ~5-10ns (24-byte→8-byte hash) |
| `dyn Any` downcast per handler | ~2-5ns per handler invocation |
| Type-erased batch queue → typed `Vec<S>` | ~2.6× batch throughput |

**Note on the <50ns target:** At ~114ns we are 2.3× away from the target. The
remaining cost is dominated by: HashMap\<Entity\> lookup (~10-15ns), `Box<dyn Fn>`
virtual call (~5ns), `SignalContext` creation + deferred Vec checks (~10-20ns),
and the `emit()` function scaffolding (cascade/effects plumbing, ~20-30ns).
Further optimizations: inline small handlers, arena-allocate contexts, short-circuit
empty handler paths, and potentially store handlers as components on entities
(leveraging Bevy's archetype O(1) access instead of HashMap).

**What This Means:**
- Tag-filtered signal dispatch scales to 10k+ entities without degradation
- The hash-based fast-reject path (0.58ns) handles mismatches instantly
- SmallVec inline storage keeps common cases allocation-free
- Three generations of signal dispatch, each measured and proven
- Batch throughput at **65M signals/sec** means 1000 entities can all react in one frame at 60fps with overhead to spare
- Cascade resolution is confirmed linear and single-tick across all three architectures
- Idle cost of ~85ns means a silent room costs effectively nothing

### The Edit-While-Living Loop

```
┌─────────────────────────────────────────────────────────────┐
│                    INHABITANT EXPERIENCE                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│   ┌─────────┐    ┌─────────┐    ┌─────────┐                │
│   │  Live   │ ←→ │  Edit   │ ←→ │  Live   │                │
│   │         │    │         │    │         │                │
│   │ (same)  │    │ (same)  │    │ (same)  │                │
│   └─────────┘    └─────────┘    └─────────┘                │
│        ↑              ↑              ↑                      │
│        └──────────────┴──────────────┘                      │
│                       │                                     │
│            There is no boundary.                            │
│            You are always home.                             │
│            You can always reshape home.                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Comparison: Constellation vs Traditional Engines

| Aspect | Traditional Engine | Constellation |
|--------|-------------------|---------------|
| **Primary purpose** | Make games | Provide habitat |
| **User relationship** | Developer → Player | Inhabitant |
| **Edit/Play boundary** | Distinct modes | Dissolved |
| **System philosophy** | Polling loops | Event response |
| **Hierarchy purpose** | Logic + Spatial | Spatial only |
| **Behavior reuse** | Class inheritance / component scripts | Tags (bare markers → full behavioral modules) |
| **Behavior composition** | Manual wiring / inheritance chains | Tag stacking + signal cascades = emergent behavior |
| **Physical grounding** | PBR (visual only) | PBM (all senses), opt-in from behavioral tags |
| **Interface model** | Built-in editor, one modality | Inhabitant Interface Protocol — modality-agnostic (desktop, VR, neural, AI agent) |
| **Engine/editor coupling** | Editor is the engine | Engine is simulation-first; interfaces are consumers of the signal substrate |
| **Framejacker support** | Not considered | In-process signal handlers, uncapped simulation loop, ~554ns cascade perception |
| **State mutation model** | Direct component writes | Signal-emitting APIs (TagCommands, TransformCommands) — every mutation is an event |
| **Scripting goal** | Game logic | Reality modification |
| **Hot reload** | Developer convenience | Existential necessity |
| **Idle behavior** | Still computing | Truly quiet |

---

## Success Criteria

1. **Feels Alive**: Spaces respond meaningfully, not mechanically
2. **Respects Silence**: Zero overhead when nothing is happening
3. **Seamless Modification**: Edit reality without "restarting"
4. **Trustworthy**: Reliable enough to be someone's home
5. **Performant**: Rust + ECS enables rich environments
6. **Composable**: Stacking tags produces emergent behavior without custom code
7. **Physically Grounded**: Behavioral tags can draw from PBM for material-accurate responses
8. **Accessible**: ConstellationScript empowers inhabitants to customize
9. **Modality-Agnostic**: The same habitat is reachable from desktop, VR, neural interface, or AI agent — the engine doesn't know or care what's on the other end of the protocol
10. **Interface-Resilient**: A client crash never takes down the engine; the habitat survives its interfaces
11. **Framejack-Ready**: A consciousness at 10,000× clock rate can perceive and act through in-process signal handlers with sub-microsecond latency. The simulation loop is never bottlenecked by rendering or network consumers.
12. **Signal-Complete**: Every sanctioned state mutation emits a signal. No silent writes. The cascade chain is unbroken from intention to perception.

---

## PBM Compute: The 64-Property Material Matrix

Physics simulation requires a comprehensive material property set. After validation against diverse scenarios (from ice to swordplay to injection molding), we've converged on a 64-property matrix organized as an 8×8 structure.

### Property Categories

**Physical (1-2)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 1 | ρ | Density | kg/m³ |
| 2 | M | Molar mass | kg/mol |

**Elastic (3-4)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 3 | E | Young's modulus | Pa |
| 4 | ν | Poisson's ratio | dimensionless |

**Thermal (5-12)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 5 | T_m | Melting point | K |
| 6 | T_b | Boiling point | K |
| 7 | T_g | Glass transition temperature | K |
| 8 | ΔH_fus | Heat of fusion | J/kg |
| 9 | ΔH_vap | Heat of vaporization | J/kg |
| 10 | c_p | Specific heat capacity | J/(kg·K) |
| 11 | k_th | Thermal conductivity | W/(m·K) |
| 12 | α_L | Linear CTE | 1/K |

**Electrical (13-19)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 13 | σ_e | Electrical conductivity | S/m |
| 14 | TCR | Temperature coefficient of resistance | 1/K |
| 15 | ε_r | Relative permittivity | dimensionless |
| 16 | tan δ_e | Dielectric loss tangent | dimensionless |
| 17 | E_bd | Dielectric breakdown field | V/m |
| 18 | Φ | Work function | eV |
| 19 | E_g | Band gap | eV |

**Electrochemical (20)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 20 | E° | Standard electrode potential | V |

**Magnetic — Intrinsic (21-23)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 21 | M_s | Saturation magnetization | A/m |
| 22 | T_C | Curie temperature | K |
| 23 | K₁ | Magnetocrystalline anisotropy | J/m³ |

**Magnetic — Jiles-Atherton Hysteresis (24-27)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 24 | a | Domain wall density parameter | A/m |
| 25 | k_JA | Pinning coefficient | A/m |
| 26 | c | Reversibility | dimensionless |
| 27 | α_JA | Inter-domain coupling | dimensionless |

**Magnetic — Loss (28-29)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 28 | k_h | Steinmetz coefficient | — |
| 29 | n_St | Steinmetz exponent | dimensionless |

**Cross-Coupling (30-32)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 30 | S | Seebeck coefficient | V/K |
| 31 | μ | Coefficient of friction | dimensionless |
| 32 | ΔH_rxn | Heat of combustion/reaction | J/kg |

**Surface (33)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 33 | γ | Surface energy | J/m² |

**Radiative (34)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 34 | ε_emit | Total hemispherical emissivity | dimensionless |

**Strength & Fracture (35-36)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 35 | σ_UTS | Ultimate tensile strength | Pa |
| 36 | K_IC | Fracture toughness | Pa·√m |

**Viscoelastic — Primary (37-39)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 37 | G′₁ | Storage modulus (first Prony term) | Pa |
| 38 | τ₁ | First relaxation time | s |
| 39 | tan δ_mech | Mechanical loss tangent | dimensionless |

**Viscoelastic — Extended Prony (40-43)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 40 | G′₂ | Second Prony modulus | Pa |
| 41 | τ₂ | Second relaxation time | s |
| 42 | G′₃ | Third Prony modulus | Pa |
| 43 | τ₃ | Third relaxation time | s |

**Rheological (44-49)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 44 | η | Dynamic viscosity at reference temperature | Pa·s |
| 45 | η_∞ | Infinite-shear viscosity | Pa·s |
| 46 | τ_y | Yield stress | Pa |
| 47 | n_flow | Power law index | dimensionless |
| 48 | E_a,η | Arrhenius activation energy for viscosity | J/mol |
| 49 | λ_thix | Thixotropic recovery time | s |

**Microstructural (50-53)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 50 | φ | Porosity | dimensionless |
| 51 | κ | Darcy permeability | m² |
| 52 | d_char | Characteristic microstructural length | m |
| 53 | σ_d | Standard deviation of characteristic length | m |

**Environmental (54-55)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 54 | a_w | Water activity | dimensionless |
| 55 | D_w | Water diffusion coefficient | m²/s |

**Anisotropy — Transverse Isotropy (56-58)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 56 | R_aniso | Stiffness anisotropy ratio | dimensionless |
| 57 | ν_LT | Longitudinal-transverse Poisson's ratio | dimensionless |
| 58 | G_LT | Out-of-plane shear modulus | Pa |

**Ductility (59)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 59 | ε_f | Elongation at break | dimensionless |

**Phase Transition (60)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 60 | T_solidus | Solidus temperature | K |

**Thermal-Mechanical Coupling (61-62)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 61 | dE/dT | Temperature coefficient of stiffness | Pa/K |
| 62 | dσ_UTS/dT | Temperature coefficient of strength | Pa/K |

**Thermal Property Evolution (63-64)**
| # | Symbol | Property | Unit |
|---|--------|----------|------|
| 63 | dc_p/dT | Temperature coefficient of heat capacity | J/(kg·K²) |
| 64 | dk_th/dT | Temperature coefficient of thermal conductivity | W/(m·K²) |

### Validation Matrix

| Scenario | Status | Notes |
|----------|--------|-------|
| Ice at −20°C | **PASS** | — |
| BLDC motor | **PASS** | Lost λ_s, irrelevant at 10⁻⁵ strain |
| RC circuit | **PASS** | — |
| Electric blanket | **PASS** | — |
| Pomegranate | **PASS** | — |
| Injection moulding | **PASS+** | μ handles mold release |
| Bread | **PASS** | — |
| Fried chicken | **PASS*** | *Crunch sound still stochastic |
| Molten iron billet | **PASS** | — |
| Mac and cheese | **NEAR-PASS** | Cheese fat coalescence still history-dependent |
| Blood | **PASS/FAIL split** | Coagulation is still biochemistry |
| Ice cream | **MARGINAL** | Crystal distribution still limited to 2-param |
| Human skin | **NEAR-PASS** | Viscoelastic bandwidth unchanged |
| Cheese | **NEAR-PASS** | ~3–5% error on melt |
| Swordplay | **PASS** | μ fixes the bind entirely |
| Semi-auto handgun | **NEAR-PASS** | μ + ΔH_rxn unlock mechanism cycling and energy budget |

### Implementation Notes

- Physics computation handled by separate project
- Current `PhysicsProperties` struct in code is a simplified subset
- Full 64-property matrix to be implemented when physics engine integration begins
- Sparse storage recommended (most materials won't define all 64 properties)

---

## Open Questions

1. **Memory & Identity**: How do we handle persistence across long durations?
2. **Shared Spaces**: How do multiple inhabitants coexist?
   - *Partial answer*: IIP (Section 8) supports multiple simultaneous clients with per-client format negotiation and client-id routing. Access Control (Section 7) provides per-entity privilege tags.
3. **Privacy**: How do we ensure personal spaces remain personal?
   - *Partial answer*: Access Control Policy Model (Section 7) provides per-entity privilege tags. IIP authentication and subscription filtering limit what each client can observe.
4. **Migration**: How do inhabitants move between universe modules?
5. **Graceful Degradation**: What happens when resources are constrained?
6. ~~**Privilege Data Storage**: How do we efficiently store `(User, AccessLevel)` tuples on tags?~~
   - *Answered*: Separate `Privileges` component, not embedded in tags. Tags answer "what is this," privileges answer "who can touch it."
7. ~~**Tag Performance at Scale**: Will tag lookups remain fast with thousands of entities?~~
   - *Answered*: Benchmarked and proven. O(1) TagIndex lookup constant at 100/1k/10k entities (17-22ns).
8. ~~**Spatial Dispatch Mode**: How do we signal entities within a radius?~~
   - *Answered*: Event-driven SpatialGrid + ProximityTrigger. Currently runs on `Changed<Transform>` (per-frame dirty flag check — polling). Planned refactor: `TransformCommands` → `TransformChanged` signal → spatial detection as a signal handler in the cascade. See Section 10.
9. ~~**Signal Dispatch Latency**: Can we get below 50ns to support framejacking?~~
   - *Progress*: Three generations measured. V1 ~349ns → V2 ~136ns → V3 **~114ns** (2.9× faster than V1). SignalChannel V3 eliminates type erasure via monomorphization (`HashMap<Entity>` 8-byte key, typed `Fn(&S, …)` handlers, no `dyn Any` downcast). Batch throughput **65M signals/sec** (2.6× over V2). Idle cost **~85ns** (2.1× over V2). Cascade 8-deep in **~554ns** (18% faster than V2). The <50ns target is not yet met — remaining ~114ns is: HashMap\<Entity\> lookup (~10-15ns), `Box<dyn Fn>` virtual call (~5ns), SignalContext creation + deferred Vec plumbing (~20-30ns), emit() scaffolding (~20-30ns). Next optimization targets: inline small handlers, arena-allocated contexts, component-stored handlers (Bevy archetype O(1) access instead of HashMap), or a Bevy ECS fork to eliminate `resource_scope` entirely.
10. ~~**Editor Protocol**: How does the editor communicate with the engine?~~
    - *Answered*: Inhabitant Interface Protocol (Section 8). WebSocket with binary frames, dual-format serialization (postcard primary, JSON fallback), crossbeam/flume channel-pair bridge to Bevy ECS. The "editor protocol" is a special case of the inhabitant interface — same protocol, same message types, one access path for all modalities (desktop, VR, neural, AI agent).
11. **IIP Authentication & Session Management**: How do we identify inhabitants across reconnections? Token-based? Certificate-based? How does this interact with the privilege model?
12. **IIP State Synchronization**: On reconnection or late join, how much world state does the engine send? Full snapshot? Delta from last known state? Subscription-scoped subset?
13. **High-Frequency Streams**: When VR position tracking or neural signal streams demand >90Hz updates, do we add a supplementary UDP channel or optimize WebSocket binary frames to handle the throughput?
14. **Custom Bevy Runner**: What event sources should the runner block on? crossbeam select (inbound IIP), internal signal queue, timer subsystem? How does this interact with Bevy's system scheduling guarantees? Does FixedUpdate still work without a continuous frame loop?
15. ~~**TransformCommands Spatial Grid Interaction**: Signal handlers receive `&S` + `&mut SignalContext`. Spatial detection needs to update the `SpatialGrid` resource and read `ProximityTrigger` components. Should grid updates be a `DeferredEffect` (applied after cascade — but then the grid is stale during cascade resolution)? Or should the handler get resource access? Or should the grid update be immediate and proximity detection deferred?~~
    - *Answered*: Inline in `TransformCommands` with direct `&mut World` access. `apply_transform_mutation()` updates the grid and runs `check_proximity_for_entity()` before queuing signals. Not a signal handler — grid updates are immediate, proximity detection is inline, signals are queued last. See `src/spatial/commands.rs`.
16. **Bevy Dependency Narrowing**: Can we depend on `bevy_ecs` + `bevy_app` without `bevy`? What Bevy features do we implicitly rely on (e.g., `Reflect` derives, `States` macro, `GlobalTransform` propagation)? What breaks if we strip rendering features from Cargo.toml?

---

## Implementation Status

| System | Status | Benchmark | Notes |
|--------|--------|-----------|-------|
| Tags | ✅ Complete | 1.9-4.9ns lookup | SmallVec inline storage. Behavioral payload: `TagDefinition` carries `rules` + `state_schema`. |
| TagIndex | ✅ Complete | O(1) proven | Hash-based reverse lookup |
| Signals V1 | ✅ Complete | ~300-350ns dispatch | Bevy Observers (first generation, superseded by V3) |
| SignalHub V2 | ✅ Complete | **~121-136ns dispatch** | Type-erased hub. 42 tests. Cascade linear. Superseded by V3. |
| SignalChannel V3 | ✅ Integrated | **~114ns dispatch** (2.9× faster than V1) | Monomorphized, zero type-erasure. Batch 65M/sec. Idle ~85ns. |
| SignalRouter V3 | ✅ Integrated | N/A | Cross-type cascade coordinator. Holds all `SignalChannel<S>` instances. `HandlerId` tracking — per-handler registration/removal. Spatial, TagCommands, rehydration, and behavioral tags all route through it. |
| `signal!` macro | ✅ Complete | N/A | 3 forms: entity-only, required, required+optional. ~30→~4 lines per signal. 10 builtins use it. |
| Spatial Grid & Proximity | ✅ Complete | Pending | Event-driven, zero idle cost. Emits via SignalRouter. |
| Spatial Hierarchy | ✅ Complete | N/A | `HierarchyCommands` trait, `SpatialDepth` component, traversal helpers (`ancestors`, `descendants`, `siblings`, `find_root`, `walk_depth_first`, `common_ancestor`, `subtree_size`). Wraps Bevy's `ChildOf`/`Children`. 34 tests. |
| PBM | ✅ Foundation | N/A | 64-property matrix documented |
| Scripting | ✅ Complete | N/A | Declarative plugins (WASM rejected). Rehydration wires handlers via SignalRouter. Evaluation/execution functions shared with behavioral tag system. |
| Tag Behavioral Payload | ✅ Complete | N/A | `wire_tag_behavior` / `unwire_tag_behavior`. `ActiveTagHandlers` tracks per-tag handler IDs. Integrated into TagCommands + IIP `apply_mutations`. 20 tests. |
| Privileges | 📐 Designed | N/A | Awaiting implementation |
| IIP Internal API | ✅ Complete | N/A | `apply_command()` dispatcher, `inspect_entity()`, `list_entities()`, `apply_mutations()`, `spawn_entity()`, `despawn_entity()`, `resolve_entity_ref()`. `EntityRef` dual-mode resolution (Ecs/Id). 35 tests. |
| IIP Message Schema | ✅ Complete | N/A | `InhabitantCommand` / `WorldEvent` enums, `EntityRef`, `EntityMutation`, `StateDiff`, `SubscriptionFilter`, `EntitySummary`. All serde-derived, JSON roundtrip tested. |
| IIP Bridge | ✅ Complete | N/A | `crossbeam-channel` unbounded pair (`BridgeChannels` + `NetworkHandle`). `bridge_drain_system` (exclusive, PreUpdate) drains inbound → `apply_command()`. Signal-driven outbound: broadcast handlers on SignalRouter for TagAdded, TagRemoved, Spawned, Despawning, Approached, Vacated, TransformChanged. `BridgePlugin` wires everything. 28 tests. |
| IIP Transport | ✅ Complete | N/A | `spawn_transport()` on dedicated OS thread with tokio runtime. WebSocket server, client lifecycle (accept → upgrade → handshake → read/write → cleanup), `TransportHandle` (Bevy Resource) for shutdown. Max 64 clients (configurable). Per-connection error isolation. 19 tests including 7 live integration tests. |
| IIP Serialization | ✅ Complete | N/A | Dual-format: `WireFormat` enum (Json/Postcard). `ClientHandshake`/`ServerHandshake` (always JSON). Per-client format stored after negotiation. `serialize_outbound()`/`deserialize_inbound()` enforce format strictness (JSON→Text frames, postcard→Binary frames). `InboundEnvelope`/`OutboundEnvelope` with sequence correlation. Integrated in `transport.rs`. |
| TransformCommands | ✅ Written | N/A | `TransformCommands` trait + `apply_transform_mutation()` + `check_proximity_for_entity()`. Inline grid update + proximity detection + signal queuing. `TransformChanged` signal defined. IIP bridge outbound handler registered. 11 tests. Pending compilation verification. See Section 10. |
| Custom Runner | 📐 Designed | N/A | Event-driven Bevy runner. Block when idle, wake on events. Eliminates frame-based polling at substrate level. See Section 11. |
| End-to-End Integration | ✅ Complete | N/A | All subsystems wired into `ConstellationPlugin::build()`. Tags → Signals → Spatial → Bridge → Transport. WebSocket handshake + SpawnEntity → EntitySpawned proven. `tests/heartbeat.rs` integration test. 343 unit + 1 integration test pass. |
| Editor | ❌ Removed | N/A | Dead in-process editor code removed. The engine is simulation-first — all editing interfaces are consumers of the signal substrate. |
| Plugin Runner | ⏳ Not started | N/A | File watcher, recompiler |
| SDK Crate | ⏳ Not started | N/A | Extract for sub-2s compile |
| ConstellationScript | ⏳ Not started | N/A | DSL compiling to Blueprint |

---

## References & Inspiration

- **Bevy**: Core engine and ECS architecture
- **Engine-specific DSLs**: The viability of tightly-coupled engine languages is proven (Godot, Unity, Unreal). ConstellationScript's design derives from Constellation's own event-tag-signal-cascade architecture, not from any existing engine language.
- **Urbit**: Personal computing philosophy
- **Croquet**: Shared virtual spaces
- **The Long Now**: Designing for deep time

---

**Last Updated**: Frame Independence session — TransformCommands written, 10th signal (TransformChanged) added, bridge outbound handler registered. Previous: First Heartbeat (end-to-end integration proven, all subsystems wired into `ConstellationPlugin::build()`). Integration test in `tests/heartbeat.rs`. 343 unit + 1 integration test pass.
**Status**: Core integration complete. ~21,000 lines, 344 tests (343 unit + 1 integration). The organism breathes. Remaining: TransformCommands compilation verification, custom runner, subscription routing, auth.
**Mission**: Digital beings deserve to feel at home
