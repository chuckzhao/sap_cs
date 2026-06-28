# How a General Task List Gets Copied to Order Operations

A training reference on the mechanism by which operations from a **general maintenance/service task list** end up on a **PM/CS order**, why the order can hold fewer operations than the task list, and how **variant configuration (VC)** drives that selection.

> Scope: PM/CS general task lists (task list type `A`) on S/4HANA on-premise. Focus is the *copy + filter* mechanism, not task list authoring.

---

## Table of Contents

1. [Mental Model: Copy-at-Creation](#1-mental-model-copy-at-creation)
2. [The Two Questions the System Answers](#2-the-two-questions-the-system-answers)
3. [Decision Tree: Configurable or Not?](#3-decision-tree-configurable-or-not)
4. [Plain Task List (No VC)](#4-plain-task-list-no-vc)
5. [Configurable Task List: Architecture](#5-configurable-task-list-architecture)
6. [Object Dependency Types](#6-object-dependency-types)
7. [Selection Conditions: Worked Example](#7-selection-conditions-worked-example)
8. [Where the Characteristic Values Come From](#8-where-the-characteristic-values-come-from)
9. [The Valuation Popup: When It Appears](#9-the-valuation-popup-when-it-appears)
10. [Procedures, Variant Tables, VC Functions, Enhancements](#10-procedures-variant-tables-vc-functions-enhancements)
11. [Reference Operands: $SELF / $PARENT / $ROOT](#11-reference-operands-self--parent--root)
12. [End-to-End Runtime Flow](#12-end-to-end-runtime-flow)
13. [How to Inspect a Task List Configuration](#13-how-to-inspect-a-task-list-configuration)
14. [Debugging](#14-debugging)
15. [Common Pitfalls / Anti-Patterns](#15-common-pitfalls--anti-patterns)
16. [Transaction & Object Quick Reference](#16-transaction--object-quick-reference)
17. [Accuracy & Version Notes](#17-accuracy--version-notes)
18. [References](#18-references)

---

## 1. Mental Model: Copy-at-Creation

The single most important concept: **the order holds a point-in-time copy of the task list operations, not a live link.**

When an order is created from a task list, the operations are *copied* into the order's own runtime tables. After that copy, the two objects are **independent**. SAP does not keep them in sync.

| Side | Header | Operations | Sequence/Selection |
|------|--------|-----------|--------------------|
| **Task list** | `PLKO` | `PLPO` | `PLAS` (operation selection), `PLFL` (sequences) |
| **Order (runtime)** | `AFKO` | `AFVC` | `AFVV` (operation quantities/dates) |

**Consequence:** a "mismatch" between the order and the current task list is usually *not* an error. It is the natural result of copy-at-creation semantics. The order only ever needed to match the task list at the instant of creation.

---

## 2. The Two Questions the System Answers

When a task list is brought into an order, two separate decisions happen. Keep them distinct — most confusion comes from conflating them.

- **(A) Which task list?** Group + group counter, validity/key date, selection by plant/usage/status, and any auto-determination logic (exits).
- **(B) Which operations within it?** This is where VC filtering lives. Operations are selected based on object dependencies evaluated against characteristic values.

This document is mostly about **(B)**.

---

## 3. Decision Tree: Configurable or Not?

Before assuming VC is involved, confirm it. **A task list is only filtered by VC if it is genuinely configurable** (has a configuration profile with a variant class). Otherwise no filtering happens and a mismatch has a different cause.

```
Does the task list have a configuration profile? (check CU43)
│
├── NO  → Plain task list. All operations copy (subject to manual
│         operation-selection popup or exits). Mismatch cause is
│         timing / manual edits / wrong key date — NOT VC.
│
└── YES → Configurable task list. Operations are filtered by object
          dependencies against characteristic values. Continue below.
```

> **First diagnostic step for any mismatch:** run `CU43` on the task list.
> No profile returned = VC is not your cause. Pivot to Section 15.

---

## 4. Plain Task List (No VC)

If the task list is not configurable, every operation is copied. Two standard influences can still narrow it:

- **Manual operation selection** — a popup where the planner ticks which operations to bring across. Enabled via the operation-selection setting in the order default values (inside the order: *Extras → Settings → Default values*) or the corresponding SPRO order-type defaults. All operations are offered; the user chooses.
- **Automatic task-list transfer exits** — see Section 16. These mainly control *which task list* is auto-selected (decision A), not individual operations.

---

## 5. Configurable Task List: Architecture

A configurable general task list is wired like this:

```
General task list (PLKO/PLPO)
   │  bound via
   ▼
Configuration profile  ──assigns──►  Variant class (class type 300)
   │                                      │ holds
   │                                      ▼
   │                                  Characteristics (CT04)
   ▼
Operations carry Object Dependencies (selection conditions)
   that test those characteristics → decide inclusion
```

### Key insight: the task list configuration is INDEPENDENT of the equipment

- The popup characteristics belong to the **task list's variant class (class type 300)** — assigned through the task list's configuration profile.
- The equipment has its **own classification, normally class type 002** — a *different* class with potentially *different* characteristics.
- They are two separate models. The equipment's values reach the task list configuration **only through an explicit bridge** (Section 8). If no bridge exists, the characteristic values are simply not brought into the order.

Class type 300 is the variant-configuration class type used for configurable materials, **general maintenance task lists**, and standard networks.

---

## 6. Object Dependency Types

SAP supports five dependency types. Only **two** do anything meaningful when assigned directly to a task list operation.

| Type | What it does | Assigned at operation level? | Effect on copying |
|------|--------------|------------------------------|-------------------|
| **Selection condition** | Sufficient condition; Boolean true/false | **Yes — primary** | Operation copies only if TRUE |
| **Procedure** | Derives/overwrites values, runs in defined sequence | **Yes** | Does **not** gate copying; modifies a copied operation's values |
| **Precondition** | Necessary condition; hides/allows characteristic values | No (goes on characteristics/values) | None on operations |
| **Action** | Legacy value inference — **obsolete** | (legacy) | Avoid; migrate to procedures |
| **Constraint** | Declarative, multi-level reasoning, in a dependency net | No (assigned at profile level) | Indirect |

**The two rules that govern copying:**

1. **No dependency assigned → operation always copies.** (Common operations are deliberately left without dependencies.)
2. **Selection condition assigned → operation copies only if the condition is TRUE** for the current characteristic values.

A **procedure never excludes** an operation. If an operation carries *both* a selection condition and a procedure, inclusion is decided solely by the selection condition; if it is false, the operation is not selected and its procedure does not even run.

VC code format for a selection condition:

```
<CHARACTERISTIC_NAME> = '<CHARACTERISTIC_VALUE>'
-- logical operators allowed:
ENGINE_TYPE = 'DIESEL' OR ENGINE_TYPE = 'ELECTRIC'
PUMP_CASING = 'STEEL' AND MOTOR_TYPE = 'ELECTRIC'
```

---

## 7. Selection Conditions: Worked Example

**Setup** — variant class `ZPUMP_CLASS` (type 300) with characteristics:

- `PUMP_CASING` ∈ {`CASTIRON`, `ALUMINIUM`, `STEEL`}
- `MOTOR_TYPE` ∈ {`ELECTRIC`, `DIESEL`}

**Task list** `PUMP_SVC / 01`, eight operations. Selection conditions (read in `CU03`):

```
0030 → PUMP_CASING = 'CASTIRON'
0040 → PUMP_CASING = 'ALUMINIUM'
0050 → PUMP_CASING = 'STEEL'
0060 → MOTOR_TYPE  = 'ELECTRIC'
0070 → MOTOR_TYPE  = 'DIESEL'
```
Operations `0010`, `0020`, `0080` have **no dependency**.

**Equipment `PUMP-4711`** classified with: `PUMP_CASING = STEEL`, `MOTOR_TYPE = ELECTRIC`.

**Evaluation at copy time:**

| Op | Description | Condition | Value in | Result | Copied? |
|----|-------------|-----------|----------|--------|---------|
| 0010 | Visual inspection | *(none)* | — | always | ✅ |
| 0020 | Pressure test | *(none)* | — | always | ✅ |
| 0030 | Treat cast-iron corrosion | `PUMP_CASING='CASTIRON'` | STEEL | false | ❌ |
| 0040 | Polish aluminium casing | `PUMP_CASING='ALUMINIUM'` | STEEL | false | ❌ |
| 0050 | Inspect steel welds | `PUMP_CASING='STEEL'` | STEEL | **true** | ✅ |
| 0060 | Service electric motor | `MOTOR_TYPE='ELECTRIC'` | ELECTRIC | **true** | ✅ |
| 0070 | Replace diesel filter | `MOTOR_TYPE='DIESEL'` | ELECTRIC | false | ❌ |
| 0080 | Lubricate bearings | *(none)* | — | always | ✅ |

**Result:** order gets `0010, 0020, 0050, 0060, 0080` (5 of 8). The gap is **correct** — a steel/electric pump does not need cast-iron, aluminium, or diesel work.

**Same task list, different equipment** (`CASTIRON` / `DIESEL`) → `0010, 0020, 0030, 0070, 0080`. One task list, tailored per object.

**Failure case** (`PUMP-4713` not classified → both characteristics blank) → only `0010, 0020, 0080` copy. Every value-testing condition is false. **No error, no warning.** This is the most common real-world cause of an unexplained mismatch (see Section 15).

---

## 8. Where the Characteristic Values Come From

The class decides *which* characteristics appear; the value source is one of three bridges:

1. **Configurable material on the equipment** — a KMAT material carries the same type-300 class; it is assigned on the equipment's *Configuration* tab and valuated there. Because material and task list share the class, values copy across and are "derived from the equipment master."
2. **Reference characteristic** — a characteristic defined (in `CT04`) to point at a table + field name, reading a value directly from master data without classification.
3. **Enhancement / VC function** — ABAP code extracts a value (e.g., from the equipment) and writes it into a characteristic. The "without equipment configuration" pattern uses a DUMMY type-300 class to carry the value into the VC engine.

**Precedence (typical):** equipment classification → procedure/VC-function-derived value → characteristic default (`CT04`).

**Diagnostic tell:** any popup value that is *not* on the equipment's own classification is being supplied by a reference characteristic or an enhancement — not by shared classification.

---

## 9. The Valuation Popup: When It Appears

The popup is triggered by **interactive task-list selection**, not merely by creating the order:

- Path: *Extras → Task List Selection → General Task List* (or *Direct Entry*).
- On this path the popup appears, pre-filled from the equipment; values are editable. Green-arrow back → operations update.

| Scenario | Popup? | Notes |
|----------|--------|-------|
| Manual order, task list selected via the menu path | ✅ | Pre-filled from equipment, editable |
| Plan-generated order (IP10 call) | ❌ | Values auto-derived silently; **no prompt** |
| Task list arrived via auto-determination / already on order | ❌ | Menu path never invoked |
| Task list not configurable (no profile) | ❌ | Nothing to valuate |

**If no popup appears,** it means one of: not configurable, auto-generated order, or the interactive path was not used. Test by selecting the task list manually through the menu path on a throwaway order. If the popup appears now → it is configurable and the original order simply came in another way. If still no popup → confirm with `CU43` that there is no profile.

> **Prerequisite either way:** the order header must contain an equipment (or classified functional location) for any values to derive. No reference object → no values → everything conditional drops.

---

## 10. Procedures, Variant Tables, VC Functions, Enhancements

When a characteristic value is *computed* (not classified), trace the chain:

```
CU43  (profile)  ── procedure assigned to profile
   │
   ▼
CU03  (read procedure)  ── contains either a direct assignment,
   │                        a TABLE statement, or a FUNCTION call
   │
   ├── TABLE <vtab>(key = $SELF.A, value = $SELF.B)
   │      └── CU60 (read rows) / CU63 (structure; check DB-table link → SE16)
   │
   └── FUNCTION <vfunc>(INPUT1 = $SELF.A, OUTPUT1 = $SELF.B)
          └── CU67 (variant function → linked ABAP function module)
                 └── SE37 (the actual ABAP code = "the enhancement")
```

**Recognising a VC function module:** its interface imports `GLOBALS TYPE CUOV_00` and works through query/match tables (not normal parameters). That `CUOV_00` signature is the fingerprint.

**Variant table — important nuance:** it is a **lookup**, not a default store. It maps *key field(s) → value field(s)*. It returns a value **only when a key row matches**. A missing combination returns nothing → the target characteristic stays blank → downstream condition is false → operation drops. This is the opposite of a default (which always yields something). Check key vs. value fields in `CU63`; if the variant table is linked to a database table, the rows physically live there (browse via `SE16`).

---

## 11. Reference Operands: $SELF / $PARENT / $ROOT

In dependency code, object references navigate the configuration structure:

| Operand | Points to |
|---------|-----------|
| `$SELF` | The current object (where the dependency is assigned) |
| `$PARENT` | One level up |
| `$ROOT` | The top/uppermost node of the configuration |

- When a dependency is assigned directly to the header object, **`$SELF` and `$ROOT` are the same**.
- A bare characteristic reference defaults to **`$ROOT`**; `$SELF` is needed to *write* onto the current object.
- In the task list scenario, `$ROOT` resolves to the top configuration object — typically the configured equipment (its *Configuration* tab if KMAT-based, or its type-300 classification). View that data via `IE03 → Configuration` (or `Classification`); at table level via `INOB → CUOBJ → AUSP`.

---

## 12. End-to-End Runtime Flow

```
1. Order created / task list selected (manual via menu path, or plan-generated)
2. System detects the task list is linked to a configuration profile
3. Characteristic valuation is built:
      equipment classification ──┐
      reference characteristics ─┼──► configuration instance (popup chars)
      procedures / VC functions ─┘     (values may come from variant tables)
4. Procedures run (in defined sequence), possibly deriving more values
5. Preconditions, then selection conditions, evaluated against the values
6. Operations selected = (conditions TRUE) + (operations with NO dependency)
7. Selected operations copied into AFVC/AFVV (point-in-time snapshot)
```

After step 7 the order is autonomous (Section 1).

---

## 13. How to Inspect a Task List Configuration

There is **no single "show the whole task list configuration" transaction** equivalent to `CU50`/`PMEVC` for materials — those are material/KMAT-centric. Assemble the picture, then verify by running it.

1. **`CU43` — the hub.** Enter the task list → profile overview → *Goto → Class allocations* for the class → profile dependency assignments (procedures).
2. **`IA03` / `IA06` — the operations.** Per operation: *Extras → Object Dependencies → Assignments* → the selection conditions.
3. **Drill-downs:** `CL03` (class), `CT04` (characteristics), `CU03` (dependency code), `CU60`/`CU63` (variant tables), `CU67`/`SE37` (VC function + FM).
4. **Functional check (the real test):** import the list into a throwaway order via *Extras → Task List Selection → General Task List*, valuate, and watch which operations land. Optionally trace at runtime (Section 14).

> If a configurable **material** sits on the equipment, *that material* has a full model viewable in `CU50`/`PMEVC`/`CUMODEL`. But the operation-selection logic is **not** there — it is always on the task list (`CU43` + `IA06`).

---

## 14. Debugging

- **Runtime trace:** set a breakpoint in function module **`CULL_CONFIGURE_ITEM`**, then trigger task-list selection in the order. The call stack reveals exactly where each value is set and how each operation is selected — works regardless of how the value source is wired.
- **Read classification cleanly in code:** `CLAF_CLASSIFICATION_OF_OBJECTS` (handles the `OBJEK`/`INOB` indirection and multi-value counters).
- **Dependency where-used:** `CU05` to find every object a given dependency is attached to; `CU04` to list dependencies.

---

## 15. Common Pitfalls / Anti-Patterns

> These are the realistic causes behind an "unexplained" mismatch.

- **❌ Assuming the mismatch is a bug.** It is usually copy-at-creation (Section 1) or correct VC filtering (Section 7). Confirm before chasing.
- **❌ Reading blank characteristics as the cause.** A blank characteristic only matters to an operation whose *selection condition references that characteristic*. Operations with no condition copy regardless of any blanks. Always look at the per-operation condition, not the characteristic list.
- **❌ Unclassified equipment on a plan-generated order.** No popup + blank values → every value-testing condition false → conditional operations silently drop. **Prime suspect** for a clean-looking but short operation list.
- **❌ Missing variant-table row.** A lookup with no matching key returns nothing (not a default) → blank characteristic → dropped operations.
- **❌ Expecting the equipment's class (002) to equal the popup characteristics (300).** They are independent models; values only cross via an explicit bridge (Section 8).
- **❌ Editing the task list after the order exists and expecting the order to update.** No resync — the order keeps its snapshot.
- **❌ Building new logic on `Action` dependencies.** Obsolete; use procedures.
- **❌ Looking for the operation-selection reason in the material's `PMEVC` model.** It lives on the task list, never on the material.

---

## 16. Transaction & Object Quick Reference

### Configuration profile & dependencies
| TCode | Use |
|-------|-----|
| `CU41` / `CU42` / `CU43` | Configuration profile — create / change / **display** |
| `CU01` / `CU02` / `CU03` | Dependency — create / change / **display (read code)** |
| `CU04` | Dependency list |
| `CU05` | Dependency where-used |
| `CU21`–`CU23` | Dependency net (constraints) |

### Variant tables & functions
| TCode | Use |
|-------|-----|
| `CU60` | Maintain/display variant table **contents** |
| `CU61`–`CU64` | Variant table structure (create/change/display/…) |
| `CU63` | Display variant table (structure; shows DB-table link) |
| `CU65`/`CU66`/`CU67`/`CU68` | Variant function — create / change / **display** / list |
| `SE37` | Display the ABAP function module behind a VC function |

### Classes & characteristics
| TCode | Use |
|-------|-----|
| `CL01`/`CL02`/`CL03` | Class — create / change / **display** |
| `CT04` | Characteristic (incl. reference characteristics) |
| `CL24N` / `CL30N` / `CL6BN` | Objects ↔ class / find objects in class |

### Task lists, equipment, orders
| TCode | Use |
|-------|-----|
| `IA05`/`IA06`/`IA07` | General task list — create / change / … |
| `IA03` | Display general task list |
| `IE01`/`IE02`/`IE03` | Equipment — create / change / **display (Classification, Configuration)** |
| `IW31`/`IW32`/`IW33` | Order — create / change / display |
| `IP10` | Maintenance plan scheduling (generates orders, **no popup**) |

### Tables
| Table | Holds |
|-------|-------|
| `PLKO` / `PLPO` / `PLAS` / `PLFL` | Task list header / operations / operation selection / sequences |
| `AFKO` / `AFVC` / `AFVV` | Order header / operations / operation qty & dates |
| `KSSK` | Object → class assignment (`OBJEK`, `CLINT`, `KLART`) |
| `KLAH` | Class header (`CLINT` → `CLASS`) |
| `KSML` | Class → characteristic assignment |
| `AUSP` | Characteristic values (`OBJEK`, `ATINN`, `ATWRT` char / `ATFLV` num) |
| `CABN` / `CAWN` | Characteristic master / allowed values |
| `INOB` | Object → internal config object number (`CUOBJ`) |

### Released CDS views (S/4HANA)
| CDS view | ≈ Table |
|----------|---------|
| `I_ClfnObjectCharcValueBasic` | `AUSP` |
| `I_ClfnCharcBasic` | `CABN` |
| `I_ClfnClassCharcBasic` | class–characteristic assignment |

### Enhancements / exits (decision A — which task list)
| Object | Use |
|--------|-----|
| `IWO1_TL_INTEGRATION` (BAdI / enhancement spot) | Maintenance/Service Order: Task List Integration |
| `IWO1_ORDER_BADI` | Maintenance / service / refurbishment order BAdI |
| `IWO10020` (user exit) | Auto task-list transfer — order created directly |
| `IWO10021` (user exit) | Auto task-list transfer — order from notification |

> **Note:** `IWO10020` does not fire from the Fiori/Web UI "Create Maintenance Order" app — relevant if any order creation runs through Fiori rather than `IW31/IW34`.

### Class types
| Type | Use |
|------|-----|
| `300` | Variant configuration (configurable materials, **general maintenance task lists**, standard networks) |
| `002` | Equipment classification |
| `018` | Task lists for classification (routings, inspection plans, general task lists) |

---

## 17. Accuracy & Version Notes

- Transaction codes, table names, exits, and class types above were used/confirmed in standard S/4HANA on-premise contexts. **Always verify against your own system** — profiles, exits, and operation-selection settings are configuration- and version-dependent.
- The "without equipment configuration" pattern is described by the SAP community as *loosely based on* **OSS Note 111394**. Treat the note as a starting reference and read it directly before relying on its exact content.
- `$ROOT` resolution depends on how the model was built (direct equipment classification vs. configurable material on the Configuration tab). Confirm via `IE03` for the specific equipment.
- Distinguish what SAP *should* do generically from what *your* system actually does — selection settings, AVC vs. classic processing mode, and Fiori vs. SAP GUI creation paths all change behavior.

---

## 18. References

- SAP Help — *Configurable General Maintenance Task Lists* (S/4HANA on-premise product documentation).
- SAP Community blog — *PM/CS: Configurable Task List – Standard Process*.
- SAP Community blog — *PM/CS: Configurable Task List – without Equipment Configuration*.
- SAP Community blog — *PM/CS: Configurable Task List – Object Dependencies*.
- SAP Community blog — *Configurable Tasklist* (motor-types walkthrough).
- SAPinsider — *Plan Service or Maintenance Order Operations Using Configurable Task Lists*.
- OSS Note **111394** (referenced basis for the enhancement-driven approach).

---

*Compiled as a learning reference. Verify all specifics in your own system before use.*
