# battleship
Overview: This project is a complete implementation of the classic Battleship game, designed with an emphasis on clean state management, correctness, and debuggable game logic rather than graphical complexity. The goal of this project was to build a deterministic, testable game engine that cleanly separates game state, player actions, and rule enforcement.

The code prioritizes clarity, correctness, and traceability, making it easy to reason about game behavior and edge cases such as invalid moves, repeated shots, and win conditions.

Features: Turn based gameplay with clear player state transitions, configurable board size and ship placement, validation of ship placement and player moves, detection of hits, misses, sunk ships, and win conditions, deterministic game flow suitable for debugging and testing, design Approach

The game is structured as a state driven system rather than a monolithic loop. Core game concepts such as the board, ships, and player actions are modeled explicitly, allowing the game engine to enforce rules consistently and avoid hidden side effects.

Key design principles: Explicit game state representation, clear separation between game logic and input handling, defensive checks against invalid or undefined states, predictable execution flow for easier debugging. This approach mirrors how embedded or systems level software is structured, where correctness and observability are more important than visual presentation. Each module owns a well defined responsibility, which helps prevent logic duplication and makes it easier to test components independently.

Example build instructions: gcc -Wall -Wextra -O2 src/*.c -o battleship./battleship

Possible Extensions: AI opponent with deterministic or heuristic strategies, automated test cases for move validation and win detection, logging or replay support for debugging game sessions, separation of engine and UI for alternative front ends
AI opponent with deterministic or heuristic strategies
Automated test cases for move validation and win detection

Logging or replay support for debugging game sessions

Separation of engine and UI for alternative front ends
