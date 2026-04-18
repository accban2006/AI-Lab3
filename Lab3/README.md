# AI Lab 3: Vacuum Agent Simulation

This project implements a simulation of vacuum cleaning agents in a grid-based environment, as part of an Artificial Intelligence course laboratory exercise.

## Overview

The simulation features a graphical user interface (GUI) that allows users to observe different types of vacuum agents navigating and cleaning a grid environment. The environment consists of:

- **Clean tiles** (white)
- **Dirty tiles** (gray)
- **Walls** (black)
- **Home position** (blue) - starting point for agents

Agents can perform the following actions:
- Move forward
- Turn left/right
- Suck dirt
- No operation (do nothing)

## Agents

The project includes three types of vacuum agents:

### 1. RandomVacuumAgent
- **Behavior**: Chooses actions randomly when no dirt is present
- **Strategy**: If dirt is detected, sucks immediately; otherwise performs random movement or turns
- **Performance**: Limited by randomness, may not efficiently cover the entire space

### 2. ReactiveVacuumAgent
- **Behavior**: Reacts to immediate percepts (bump sensors and dirt detection)
- **Strategy**: Sucks when dirt is found, turns randomly when hitting walls, moves forward otherwise
- **Performance**: Better than random but still reactive, no planning ahead

### 3. MyVacuumAgent
- **Behavior**: Uses search algorithms (BFS/DFS/Greedy/A*) to systematically explore and clean the environment
- **Strategy**: Maintains an internal map of the world, plans optimal paths to unexplored areas
- **Performance**: Most efficient, explores systematically and avoids redundant movements

## Search Algorithms

The `MyVacuumAgent` implements four different search algorithms to navigate the environment and find unknown/dirty areas. Each algorithm uses the agent's internal world map to plan paths.

### 1. Breadth-First Search (BFS)
- **Strategy**: Explores all neighboring cells at the current depth before moving to the next depth level
- **Data Structure**: Queue (FIFO - First In, First Out)
- **Properties**: 
  - Guarantees shortest path in unweighted grids
  - Explores systematically outward from start
  - Memory usage grows with frontier size
- **Implementation**: Uses a queue to store (position, path) tuples, exploring in order: North, East, South, West

### 2. Depth-First Search (DFS)
- **Strategy**: Explores as far as possible along each branch before backtracking
- **Data Structure**: Stack (LIFO - Last In, First Out)
- **Properties**:
  - May not find optimal path
  - Lower memory usage than BFS
  - Can get stuck in deep branches
- **Implementation**: Uses a stack to store (position, path) tuples, exploring in order: South, East, North, West

### 3. Greedy Best-First Search
- **Strategy**: Always moves to the closest unexplored cell based on Manhattan distance
- **Data Structure**: Priority queue (min-heap)
- **Heuristic**: `h(n) = min(Manhattan_distance(n, unknown_cell))` for all unknown cells
- **Properties**:
  - Fast but not optimal
  - Can get stuck in local optima
  - Greedy approach prioritizes immediate gains
- **Implementation**: Uses heapq with heuristic scores, exploring closest unknown cells first

### 4. A* Search
- **Strategy**: Combines actual cost with estimated cost to goal using `f(n) = g(n) + h(n)`
- **Data Structure**: Priority queue with cost tracking
- **Cost Function**: 
  - `g(n)`: Actual cost from start to current node (steps taken)
  - `h(n)`: Manhattan distance heuristic to nearest unknown cell
  - `f(n)`: Total estimated cost
- **Properties**:
  - Optimal when heuristic is admissible
  - More informed than BFS/DFS
  - Higher memory usage but better performance
- **Implementation**: Maintains separate g-cost tracking to avoid re-exploring nodes with higher costs

### Algorithm Selection
The agent can be configured to use any of these algorithms by setting `self.current_algorithm` to:
- `"BFS"` - Breadth-First Search
- `"DFS"` - Depth-First Search  
- `"Greedy"` - Greedy Best-First Search
- `"A*"` - A* Search

### Search Execution Flow
1. **Perception**: Update internal world map with current cell state
2. **Planning**: If no current route, run selected search algorithm to find next target
3. **Pathfinding**: Search finds path to nearest unknown cell or back to home
4. **Execution**: Follow planned path step-by-step using `move_to()` method
5. **Termination**: When all cells explored, return home and shut down

### How to change search algorithm in the code
1. Go to myvacuumagent.py and go to line 114
2. You can see something like this  self.current_algorithm = "A*" 
3. go change what inside "" to change the search algorithm
### Performance Metrics
- **Nodes Explored**: Total cells examined during search
- **Steps Taken**: Total actions performed
- **Score**: +100 for cleaning dirt, -1 for each action
- **Optimality**: BFS and A* guarantee optimal paths; Greedy and DFS do not

## Features

- **Interactive GUI**: Visual simulation with real-time updates
- **Configurable Environment**:
  - Grid sizes: 5x5, 10x10, 15x15, 20x20, 5x10, 10x5
  - Adjustable dirt and wall biases
  - Multiple random seeds for reproducible environments
- **Performance Tracking**: Agents accumulate points for cleaning dirt and penalties for actions
- **Data Collection**: Automated performance analysis for different algorithms and grid sizes

## Installation

### Prerequisites
- Python 3.x
- Tkinter (usually included with Python)
- IPython (for grid visualization utilities)

### Setup
1. Clone or download the project files
2. Ensure all files are in the same directory structure
3. Make sure the `images/` folder contains the required PNG files:
   - `vacuum_cleaner_east.png`
   - `vacuum_cleaner_south.png`
   - `vacuum_cleaner_west.png`
   - `vacuum_cleaner_north.png`
   - `blank.png`

## Usage

### Running the GUI Simulation
```bash
python run_lab2.py
```

This launches the main GUI application where you can:
- Select environment parameters (size, biases, seed)
- Choose an agent type
- Control simulation (Prepare, Run, Stop, Step)
- View real-time logs and performance

### Collecting Performance Data
```bash
python collect_data.py
```

This script runs automated experiments comparing BFS and DFS algorithms on MyVacuumAgent across different grid sizes and collects performance metrics.

## Project Structure

```
Lab3/
├── agents.py              # Base agent and environment framework
├── collect_data.py        # Performance data collection script
├── ipythonblocks.py       # Grid visualization utilities
├── run_lab2.py           # Main GUI application entry point
├── utils.py              # Utility functions
├── images/               # GUI image assets
│   ├── vacuum_cleaner_*.png
│   └── blank.png
└── lab2/                 # Vacuum simulation package
    ├── __init__.py       # GUI and simulation logic
    ├── vacuum.py         # VacuumEnvironment class
    ├── myvacuumagent.py  # Intelligent search-based agent
    ├── randomvacuumagent.py    # Random movement agent
    └── reactivevacuumagent.py  # Reactive agent
```

## Key Classes

### VacuumEnvironment
- Manages the grid world with dirt, walls, and agents
- Handles agent actions and percept generation
- Tracks performance metrics

### Agent Classes
- All inherit from base `Agent` class
- Implement different decision-making strategies
- Maintain internal state and performance tracking

## Performance Metrics

Agents are scored based on:
- **+100 points** for each dirt cleaned
- **-1 point** for each action taken
- Starting performance: -1000 points

Higher scores indicate better performance.

## Configuration Options

### Environment Settings
- **Grid Dimensions**: Various sizes from 5x5 to 20x20
- **Dirt Bias**: Probability of dirt spawning (0.1, 0.2, 0.5)
- **Wall Bias**: Probability of wall spawning (0.0, 0.1, 0.2, 0.5)
- **Random Seed**: For reproducible environments

### Simulation Controls
- **Delay**: Step timing for continuous runs (10ms to 1000ms)
- **Max Iterations**: Agents stop after 100 iterations

## Dependencies

- `tkinter` - GUI framework
- `random` - Random number generation
- `heapq` - Priority queue for search algorithms
- `IPython` - Notebook integration (optional)

## Notes

- The simulation uses a discrete grid where (1,1) is the home position
- Agents start facing East (positive x-direction)
- Walls form the perimeter of the environment
- The GUI requires image files in the `images/` directory
- Data collection suppresses debug output for clean performance analysis

## Educational Purpose

This project demonstrates fundamental AI concepts including:
- Agent-based systems
- Search algorithms (BFS/DFS)
- Reactive vs. deliberative agents
- Performance evaluation
- GUI development for simulations