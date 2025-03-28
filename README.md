# React Fiber Architecture: A Deep Dive

## Introduction

React Fiber represents a complete rewrite of React's reconciliation algorithm, developed over two years by the React team. This new architecture fundamentally changes how React schedules and executes rendering work.

### Key Objectives
- **Incremental Rendering**: Split rendering into chunks distributable across frames
- **Task Prioritization**: Assign different priorities to updates (e.g., animations vs data updates)
- **Work Control**: Pause, abort, or reuse work as needed
- **Concurrency**: Enable new concurrent rendering capabilities

### About This Guide
This document serves as a technical exploration of Fiber's architecture. It:
- Uses plain language with minimal jargon
- Links to external references where appropriate
- Will evolve alongside Fiber's development

*Disclaimer: This is not an official React documentation but has been reviewed by React team members for accuracy.*

## Prerequisites

Before continuing, ensure familiarity with:
1. [React Components, Elements, and Instances](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html)
2. [Reconciliation Algorithm](https://facebook.github.io/react/docs/reconciliation.html)
3. [React Basic Theoretical Concepts](https://github.com/reactjs/react-basic)
4. [React Design Principles](https://facebook.github.io/react/contributing/design-principles.html) (especially scheduling)

## Core Concepts

### Reconciliation Revisited
Reconciliation is React's diffing algorithm that:
- Compares virtual DOM trees
- Determines minimal update operations
- Optimizes for performance while maintaining declarative API

Key reconciliation behaviors:
- Different component types trigger full subtree replacement
- List diffing uses stable keys for element tracking

### Separation of Concerns
React's architecture separates:
- **Reconciler**: Computes state changes (Fiber implements this)
- **Renderer**: Applies changes to environment (DOM, Native, etc.)

This separation enables cross-platform support while sharing core logic.

### Scheduling Philosophy
React uses a pull-based scheduling model where:
- Work can be deferred when not critical
- Updates are batched and coalesced
- User interactions receive highest priority

Fiber enables this through:
- Work segmentation
- Priority tracking
- Task interruption/resumption

## Fiber Architecture

### What is a Fiber?
A fiber is:
1. A unit of work
2. A virtual stack frame
3. A component instance representation

Key characteristics:
- Reimplemented call stack with manual control
- In-memory work tracking
- Enables scheduling optimizations

### Fiber Object Structure
Each fiber is a JavaScript object containing:

| Property | Description |
|----------|-------------|
| `type` | Component function/class or host tag string |
| `key` | Reconciliation identifier |
| `child` | First child component |
| `sibling` | Next sibling component |
| `return` | Parent component |
| `pendingProps` | Props before execution |
| `memoizedProps` | Props after execution |
| `pendingWorkPriority` | Scheduling priority level |
| `alternate` | Current/work-in-progress fiber link |
| `output` | Renderer-specific result |

### Priority Levels
React defines multiple priority levels (higher numbers = lower priority):

1. Synchronous (highest)
2. Task
3. Animation
4. High
5. Low
6. Offscreen (lowest)

### Work-in-Progress Pattern
React maintains two fiber trees:
1. **Current**: Rendered UI
2. **Work-in-progress**: Next version being computed

The `alternate` property links matching fibers across trees.

## Implementation Details

### Fiber Creation
- New fibers created via `cloneFiber`
- Reuses alternates when possible
- Lazy instantiation for performance

### Priority Handling
```js
function hasPriority(fiber, threshold) {
  return fiber.pendingWorkPriority !== 0 && 
         fiber.pendingWorkPriority <= threshold;
}
```

### Tree Relationships
Component structure:
```js
function Parent() {
  return [<Child1 />, <Child2 />]
}
```
Creates fiber relationships:
- Parent.child = Child1
- Child1.sibling = Child2
- Child1.return = Parent
- Child2.return = Parent

## Future Topics
Upcoming sections will cover:
1. Work prioritization algorithms
2. Task interruption/resumption
3. Side-effect handling
4. Coroutines for layout/context
5. Complete update lifecycle

## Related Resources
- [React Fiber Source Code](https://github.com/facebook/react/commits/master/src/renderers/shared/fiber)
- [What's Next for React (ReactNext 2016)](https://youtu.be/aV1271hd9ew)

---

### Key Improvements:
1. **Better Organization**: Clear section hierarchy with logical flow
2. **Enhanced Readability**: More concise paragraphs with better scannability
3. **Visual Elements**: Added tables for property descriptions
4. **Technical Precision**: Clarified core concepts with concrete examples
5. **Consistent Terminology**: Unified language throughout
6. **Improved Structure**: Better separation of conceptual vs implementation details
7. **Future Focus**: Clear roadmap for upcoming topics

Would you like me to elaborate on any particular section or add additional technical details?
