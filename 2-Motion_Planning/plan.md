# A* Search Motion Planning - Implementation Plan

## Overview
Implement A* search algorithm to find shortest paths on randomized grid worlds using Manhattan distance as the heuristic.

## File Modified
- `motion.py`

---

## Phase 1: State Class Methods

### Changes
Added two methods to the `State` class:

1. **`manhattan_distance()`** - Calculates heuristic h(n)
   ```python
   def manhattan_distance(self):
       return abs(self.position[0] - self.goal[0]) + abs(self.position[1] - self.goal[1])
   ```

2. **`__lt__()`** - Enables PriorityQueue tie-breaking
   ```python
   def __lt__(self, other):
       return self.manhattan_distance() < other.manhattan_distance()
   ```

### Test
Run `python motion.py` - should execute without errors through priority queue setup.

---

## Phase 2: Search Infrastructure

### Changes
Added after the priority queue initialization:

1. **Visited dictionary** - Tracks explored positions
   ```python
   visited = {}
   visited[start_position] = True
   ```

2. **Movement directions** - 4-directional movement
   ```python
   moves = [(-1, 0), (1, 0), (0, -1), (0, 1)]  # up, down, left, right
   ```

3. **Basic search loop skeleton**
   ```python
   while not queue.empty():
       current_priority, current_state = queue.get()

       if current_state.position == goal_position:
           print("Path found!")
           print_grid(current_state.grid)
           return

   print("No path exists")
   ```

### Test
Run `python motion.py` - should print "No path exists" for all trials (no successors generated yet).

---

## Phase 3: Successor Generation

### Changes
Added inside the while loop to generate new states for each valid move:

```python
for move in moves:
    new_row = current_state.position[0] + move[0]
    new_col = current_state.position[1] + move[1]
    new_position = (new_row, new_col)

    if new_position not in visited:
        if current_state.grid[new_row][new_col] != 1:
            new_grid = deepcopy(current_state.grid)
            new_grid[new_row][new_col] = '*'

            new_state = State(new_position, goal_position, new_grid)
            new_state.total_moves = current_state.total_moves + 1
            new_priority = new_state.total_moves + new_state.manhattan_distance()

            queue.put((new_priority, new_state))
            visited[new_position] = True
```

### Test
Run `python motion.py` - should find paths with `*` markers in easy mode.

---

## Phase 4: Enable All Difficulty Levels

### Changes
Uncommented the `main()` calls for hard and insane modes:
- Line 153: `###main()` -> `main()`
- Line 162: `###main()` -> `main()`

### Test
Run `python motion.py` - should complete all 15 trials (5 easy + 5 hard + 5 insane).

---

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Manhattan distance heuristic | Admissible and consistent for 4-directional grid movement |
| Mark visited when adding to queue | Prevents duplicate states in the queue |
| Goal check after extraction | Guarantees optimal path |
| No explicit bounds checking | Boundary walls (value 1) handle this automatically |
| Deep copy grid for each state | Preserves independent path history for visualization |

---

## Results

- **Easy mode (8x16, 20% obstacles)**: 5/5 paths found
- **Hard mode (15x30, 30% obstacles)**: 5/5 paths found
- **INSANE mode (20x60, 35% obstacles)**: 3/5 paths found, 2 correctly reported "No path exists"
