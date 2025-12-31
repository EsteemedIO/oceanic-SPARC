# Goal Planner (GOAP) Rules

## Overview

The Goal Planner mode implements Goal-Oriented Action Planning (GOAP), a technique from game AI for intelligent multi-step planning. It enables agents to dynamically create and adapt plans to achieve complex objectives.

## Core Concepts

### World State
A set of boolean or numeric conditions representing the current situation:
```javascript
current_state = {
  code_written: true,
  tests_written: false,
  tests_passing: false,
  deployed: false,
  monitoring_enabled: false
}
```

### Goal State
The desired end conditions:
```javascript
goal_state = {
  deployed: true,
  monitoring_enabled: true
}
```

### Actions
Operations that transform state:
```yaml
Action: write_tests
  Preconditions: {code_written: true}
  Effects: {tests_written: true}
  Cost: 2

Action: run_tests
  Preconditions: {tests_written: true}
  Effects: {tests_passing: true}
  Cost: 1

Action: deploy
  Preconditions: {tests_passing: true}
  Effects: {deployed: true}
  Cost: 3

Action: setup_monitoring
  Preconditions: {deployed: true}
  Effects: {monitoring_enabled: true}
  Cost: 1
```

## Planning Algorithm

### A* Pathfinding Logic
1. Start from current state
2. Find applicable actions (preconditions met)
3. For each action, compute:
   - g(n) = actual cost from start
   - h(n) = heuristic estimate to goal
   - f(n) = g(n) + h(n)
4. Expand lowest f(n) first
5. Continue until goal state reached

### Heuristic Function
Count unsatisfied goal conditions:
```javascript
h(state) = count(goalConditions.filter(c => state[c] !== goal[c]))
```

## OODA Loop Execution

### Observe
- Check current state after each action
- Detect unexpected changes
- Log state transitions

### Orient
- Compare actual vs expected state
- Identify deviations
- Assess impact on plan

### Decide
- If on track: continue plan
- If minor deviation: adjust current action
- If major deviation: trigger replanning

### Act
- Execute next planned action
- Update world state
- Report progress

## Integration with SPARC

| GOAP Phase | SPARC Phase | Activity |
|------------|-------------|----------|
| State Assessment | Specification | Define requirements, current state |
| Action Analysis | Pseudocode | Design action sequences |
| Plan Generation | Architecture | Structure execution plan |
| Execution Monitoring | Refinement | TDD, iterate on implementation |
| Goal Achievement | Completion | Validate and deploy |

## Delegation Rules

When to delegate to other modes:

- **code**: Implementation actions requiring code generation
- **tdd**: Test-first implementation actions
- **architect**: Actions requiring system design decisions
- **debug**: Actions that encounter errors
- **security-review**: Actions touching sensitive areas
- **integration**: Actions combining multiple services
- **devops**: Deployment and infrastructure actions

## Plan Output Format

```markdown
## Goal Plan: [Objective Name]

### Current State
- condition1: value
- condition2: value

### Goal State
- target1: value
- target2: value

### Action Sequence

1. **Action Name** (Cost: N)
   - Preconditions: [list]
   - Effects: [list]
   - Delegate to: [mode]

2. **Action Name** (Cost: N)
   ...

### Total Estimated Cost: X
### Replanning Triggers:
- If [condition], then [action]
```

## Error Handling

1. **Action Fails**: Log failure, update state, trigger replanning
2. **Precondition Not Met**: Skip action, find alternative path
3. **No Valid Plan**: Report infeasibility, suggest goal modifications
4. **Infinite Loop**: Detect cycles, backtrack or fail gracefully

## Best Practices

1. Keep actions atomic and well-defined
2. Use accurate cost estimates
3. Define clear preconditions and effects
4. Plan for replanning - expect changes
5. Validate goal achievement explicitly
6. Document plan decisions for debugging
