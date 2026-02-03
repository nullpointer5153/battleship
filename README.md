# battleship
Overview:
This project is a complete implementation of the classic Battleship game, designed with an emphasis on clean state management, correctness, and debuggable game logic rather than graphical complexity. The goal of this project was to build a deterministic, testable game engine that cleanly separates game state, player actions, and rule enforcement.

The code prioritizes clarity, correctness, and traceability, making it easy to reason about game behavior and edge cases such as invalid moves, repeated shots, and win conditions.

Features:
Turn based gameplay with clear player state transitions
Configurable board size and ship placement
Validation of ship placement and player moves
Detection of hits, misses, sunk ships, and win conditions
Deterministic game flow suitable for debugging and testing
Design Approach

The game is structured as a state driven system rather than a monolithic loop. Core game concepts such as the board, ships, and player actions are modeled explicitly, allowing the game engine to enforce rules consistently and avoid hidden side effects.

Key design principles:

Explicit game state representation
Clear separation between game logic and input handling
Defensive checks against invalid or undefined states
Predictable execution flow for easier debugging
This approach mirrors how embedded or systems level software is structured, where correctness and observability are more important than visual presentation.

Code Structure:

A typical layout of the project is as follows:

src/
├── main.c / main.cpp        # Game loop and high level control flow
├── game.c / game.cpp        # Core game state and turn management
├── board.c / board.cpp      # Board representation and shot validation
├── ship.c / ship.cpp        # Ship data structures and hit tracking
├── player.c / player.cpp    # Player actions and move handling
└── utils.c / utils.cpp      # Helper functions and shared utilities


Each module owns a well defined responsibility, which helps prevent logic duplication and makes it easier to test components independently.

Game Flow:
Initialize game state and boards
Place ships while enforcing placement rules
Alternate player turns
Validate shots and update board state
Check for ship destruction and win conditions
Terminate game when all ships are sunk
At each step, state transitions are explicit and validated to prevent illegal game states.
Error Handling and Validation
The game includes checks for:
Out of bounds shots
Duplicate shots
Invalid ship placement
Illegal state transitions

These checks ensure the game behaves predictably even when given incorrect or unexpected input.

Building and Running:
Example build instructions:

gcc -Wall -Wextra -O2 src/*.c -o battleship
./battleship


If using C++:

g++ -Wall -Wextra -O2 src/*.cpp -o battleship
./battleship

Possible Extensions:
AI opponent with deterministic or heuristic strategies
Automated test cases for move validation and win detection
Logging or replay support for debugging game sessions
Separation of engine and UI for alternative front ends
AI opponent with deterministic or heuristic strategies
Automated test cases for move validation and win detection

Logging or replay support for debugging game sessions

Separation of engine and UI for alternative front ends
