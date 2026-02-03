#include <TFT_eSPI.h>


// Define the pins used for the TFT display
#define TFT_CS    10
#define TFT_DC    9
#define TFT_RST   8


// Define the size of the display
#define TFT_WIDTH  240
#define TFT_HEIGHT 320


// Define the size of the game grid
#define GRID_WIDTH  10
#define GRID_HEIGHT 10


// Define the size of a single grid square
#define SQUARE_SIZE 30


// Define the positions of the dpad and select/deselect buttons
#define DPAD_X    30
#define DPAD_Y    280
#define SELECT_X  200
#define SELECT_Y  280
#define DESELECT_X 200
#define DESELECT_Y 240


// Define the colors used for the grid, ships, and hits/misses
#define GRID_COLOR    TFT_WHITE
#define SHIP_COLOR    TFT_BLUE
#define HIT_COLOR     TFT_RED
#define MISS_COLOR    TFT_BLACK
#define SELECT_COLOR  TFT_GREEN
#define DESELECT_COLOR TFT_RED


// Initialize the TFT display
TFT_eSPI tft = TFT_eSPI(TFT_WIDTH, TFT_HEIGHT);


// Define the game grids for each player
int player1_grid[GRID_WIDTH][GRID_HEIGHT];
int player2_grid[GRID_WIDTH][GRID_HEIGHT];


// Define the current player (1 or 2)
int current_player = 1;


// Define the position of the dpad
int dpad_x = DPAD_X;
int dpad_y = DPAD_Y;


// Define whether the select/deselect buttons are selected
bool select_button_selected = false;
bool deselect_button_selected = false;


// Initialize the game grids for each player
void init_grids() {
  for (int x = 0; x < GRID_WIDTH; x++) {
    for (int y = 0; y < GRID_HEIGHT; y++) {
      player1_grid[x][y] = 0;
      player2_grid[x][y] = 0;
    }
  }
}


// Draw the game grid for the current player
void draw_grid() {
  // Clear the screen
  tft.fillScreen(TFT_BLACK);
 
  // Draw the grid lines
  for (int x = 0; x <= GRID_WIDTH; x++) {
    tft.drawLine(x * SQUARE_SIZE, 0, x * SQUARE_SIZE, GRID_HEIGHT * SQUARE_SIZE, GRID_COLOR);
  }
  for (int y = 0; y <= GRID_HEIGHT; y++) {
    tft.drawLine(0, y * SQUARE_SIZE, GRID_WIDTH * SQUARE_SIZE, y * SQUARE_SIZE, GRID_COLOR);
  }
 
  // Draw the player's ships and hits/misses
  for (int x = 0; x < GRID_WIDTH; x++) {
    for (int y = 0; y < GRID_HEIGHT; y++) {
      int color = GRID_COLOR;
      if (current_player == 1 && player1_grid[x][y] == 1) {
        color = SHIP_COLOR;
      } else if (current_player == 2 && player2_grid[x][y] == 1) {
        color = SHIP_COLOR;
      } else if (current_player == 1 && player2_grid[x][y] == 2) {
        color = HIT_COLOR;
      } else if (current_player == 1 && player2_grid[x][y] == 3) {
        color = MISS_COLOR;
      } else if (current_player == 2 && player1_grid[x][y] == 2) {
color = HIT_COLOR;
} else if (current_player == 2 && player1_grid[x][y] == 3) {
color = MISS_COLOR;
}
tft.fillRect(x * SQUARE_SIZE + 1, y * SQUARE_SIZE + 1, SQUARE_SIZE - 1, SQUARE_SIZE - 1, color);
}
}


// Draw the select/deselect buttons
int select_color = select_button_selected ? SELECT_COLOR : TFT_WHITE;
int deselect_color = deselect_button_selected ? DESELECT_COLOR : TFT_WHITE;
tft.fillRect(SELECT_X, SELECT_Y, SQUARE_SIZE - 1, SQUARE_SIZE - 1, select_color);
tft.fillRect(DESELECT_X, DESELECT_Y, SQUARE_SIZE - 1, SQUARE_SIZE - 1, deselect_color);
tft.drawRect(SELECT_X, SELECT_Y, SQUARE_SIZE - 1, SQUARE_SIZE - 1, TFT_BLACK);
tft.drawRect(DESELECT_X, DESELECT_Y, SQUARE_SIZE - 1, SQUARE_SIZE - 1, TFT_BLACK);
}


// Update the position of the dpad based on input
void update_dpad(int input) {
if (input == 0 && dpad_x > DPAD_X) {
dpad_x -= SQUARE_SIZE;
} else if (input == 1 && dpad_x < DPAD_X + (GRID_WIDTH - 1) * SQUARE_SIZE) {
dpad_x += SQUARE_SIZE;
} else if (input == 2 && dpad_y > DPAD_Y) {
dpad_y -= SQUARE_SIZE;
} else if (input == 3 && dpad_y < DPAD_Y + (GRID_HEIGHT - 1) * SQUARE_SIZE) {
dpad_y += SQUARE_SIZE;
}
}


// Handle the select/deselect buttons
void handle_buttons(int button1, int button2) {
if (button1 == 1 && button2 == 0) {
// Select button is pressed
if (dpad_x == SELECT_X && dpad_y == SELECT_Y) {
select_button_selected = !select_button_selected;
deselect_button_selected = false;
}
// Deselect button is pressed
if (dpad_x == DESELECT_X && dpad_y == DESELECT_Y) {
deselect_button_selected = !deselect_button_selected;
select_button_selected = false;
}
}
}


// Place a ship or mark a hit/miss on the current player's grid
void handle_grid(int button1, int button2) {
if (button1 == 0 && button2 == 1 && select_button_selected) {
// Place ship or mark hit
if (current_player == 1) {
player1_grid[dpad_x / SQUARE_SIZE][dpad_y / SQUARE_SIZE] = 1;
} else {
player2_grid[dpad_x / SQUARE_SIZE][dpad_y / SQUARE_SIZE] = 1;
}
} else if (button1 == 0 && button2 == 1 && deselect_button_selected) {
// Mark miss
if (current_player == 1) {
player1_grid[dpad_x / SQUARE_SIZE][dpad_y / SQUARE_SIZE] = 3;
} else {
player2_grid[dpad_x / SQUARE_SIZE][dpad_y / SQUARE_SIZE] = 3;
}
}
}


void setup() {
// Initialize the TFT display
tft.init();
tft.setRotation(1);
tft.fillScreen(TFT_BLACK);


// Initialize the game grids
for (int i = 0; i < GRID_WIDTH; i++) {
for (int j = 0; j < GRID_HEIGHT; j++) {
player1_grid[i][j] = 0;
player2_grid[i][j] = 0;
}
}


// Set the initial position of the dpad
dpad_x = DPAD_X;
dpad_y = DPAD_Y;


// Set the initial player and turn
current_player = 1;
current_turn = 1;
}


void loop() {
// Update the display
draw_display();


// Get input from the dpad and buttons
int dpad_input = get_dpad_input();
int button1 = digitalRead(BUTTON1_PIN);
int button2 = digitalRead(BUTTON2_PIN);


// Update the position of the dpad
update_dpad(dpad_input);


// Handle button presses
handle_buttons(button1, button2);


// Handle grid interactions
handle_grid(button1, button2);


// Check if the game is over
if (game_over()) {
// Display the winner
tft.fillScreen(TFT_BLACK);
tft.setCursor(0, 0);
tft.setTextColor(TFT_WHITE, TFT_BLACK);
tft.setTextSize(2);
tft.print("Player ");
tft.print(current_player);
tft.print(" wins!");
while (1) {}
}


// Update the current turn and player
if (button1 == 1 && button2 == 1) {
current_turn++;
if (current_player == 1) {
current_player = 2;
} else {
current_player = 1;
}
}
}
// Draws the game display
void draw_display() {
// Draw the player grids
draw_player1_grid();
draw_player2_grid();


// Draw the dpad
tft.fillRect(dpad_x, dpad_y, DPAD_SIZE, DPAD_SIZE, TFT_RED);
tft.drawRect(dpad_x, dpad_y, DPAD_SIZE, DPAD_SIZE, TFT_WHITE);
tft.fillRect(dpad_x + DPAD_PADDING, dpad_y, DPAD_PADDING, DPAD_SIZE, TFT_WHITE);
tft.fillRect(dpad_x, dpad_y + DPAD_PADDING, DPAD_SIZE, DPAD_PADDING, TFT_WHITE);


// Draw the current player and turn
tft.setCursor(0, 0);
tft.setTextColor(TFT_WHITE, TFT_BLACK);
tft.setTextSize(2);
tft.print("Player ");
tft.print(current_player);
tft.print(" - Turn ");
tft.print(current_turn);
}


// Draws player 1's grid
void draw_player1_grid() {
tft.fillRect(0, 20, GRID_WIDTH * GRID_SIZE, GRID_HEIGHT * GRID_SIZE, TFT_WHITE);
for (int i = 0; i < GRID_WIDTH; i++) {
for (int j = 0; j < GRID_HEIGHT; j++) {
if (player1_grid[i][j] == 1) {
tft.fillRect(i * GRID_SIZE, j * GRID_SIZE + 20, GRID_SIZE, GRID_SIZE, TFT_BLUE);
} else if (player1_grid[i][j] == -1) {
tft.fillRect(i * GRID_SIZE, j * GRID_SIZE + 20, GRID_SIZE, GRID_SIZE, TFT_RED);
}
tft.drawRect(i * GRID_SIZE, j * GRID_SIZE + 20, GRID_SIZE, GRID_SIZE, TFT_BLACK);
}
}
}


// Draws player 2's grid
void draw_player2_grid() {
tft.fillRect(0, (GRID_HEIGHT + 1) * GRID_SIZE + 20, GRID_WIDTH * GRID_SIZE, GRID_HEIGHT * GRID_SIZE, TFT_WHITE);
for (int i = 0; i < GRID_WIDTH; i++) {
for (int j = 0; j < GRID_HEIGHT; j++) {
if (player2_grid[i][j] == 1) {
tft.fillRect(i * GRID_SIZE, j * GRID_SIZE + (GRID_HEIGHT + 1) * GRID_SIZE + 20, GRID_SIZE, GRID_SIZE, TFT_BLUE);
} else if (player2_grid[i][j] == -1) {
tft.fillRect(i * GRID_SIZE, j * GRID_SIZE + (GRID_HEIGHT + 1) * GRID_SIZE + 20, GRID_SIZE, GRID_SIZE, TFT_RED);
}
tft.drawRect(i * GRID_SIZE, j * GRID_SIZE + (GRID_HEIGHT + 1) * GRID_SIZE + 20, GRID_SIZE, GRID_SIZE, TFT_BLACK);
}
}
}


// Gets input from the dpad
int get_dpad_input() {
int x_val = analogRead(DPAD_X_PIN);
int y_val = analogRead(DPAD_Y_PIN);


// Check for left input
if (x_val < DPAD_THRESHOLD_LEFT) {
return DPAD_LEFT;
}


// Check for right input
if (x_val > DPAD_THRESHOLD_RIGHT) {
return DPAD_RIGHT;
}


// Check for up input
if (y_val < DPAD_THRESHOLD_UP) {
return DPAD_UP;
}


// Check for down input
if (y_val > DPAD_THRESHOLD_DOWN) {
return DPAD_DOWN;
}


// No input
return DPAD_NONE;
}


// Gets input from the select button
bool get_select_input() {
return digitalRead(SELECT_PIN) == LOW;
}


// Initializes the player grids
void init_grids() {
// Clear the grids
for (int i = 0; i < GRID_WIDTH; i++) {
for (int j = 0; j < GRID_HEIGHT; j++) {
player1_grid[i][j] = 0;
player2_grid[i][j] = 0;
}
}


// Place player 1's ships
place_ship(4, player1_grid);
place_ship(3, player1_grid);
place_ship(3, player1_grid);
place_ship(2, player1_grid);
place_ship(2, player1_grid);
place_ship(2, player1_grid);
place_ship(1, player1_grid);
place_ship(1, player1_grid);
place_ship(1, player1_grid);


// Place player 2's ships
place_ship(4, player2_grid);
place_ship(3, player2_grid);
place_ship(3, player2_grid);
place_ship(2, player2_grid);
place_ship(2, player2_grid);
place_ship(2, player2_grid);
place_ship(1, player2_grid);
place_ship(1, player2_grid);
place_ship(1, player2_grid);
}


// Places a ship on the grid
void place_ship(int length, int grid[GRID_WIDTH][GRID_HEIGHT]) {
bool placed = false;
while (!placed) {
// Pick a random starting point
int x = random(0, GRID_WIDTH);
int y = random(0, GRID_HEIGHT);


css:
// Check if the ship can be placed there
bool can_place = true;
for (int i = 0; i < length; i++) {
  if (x + i >= GRID_WIDTH || grid[x + i][y] != 0) {
    can_place = false;
    break;
  }
}


// Place the ship if possible
if (can_place) {
  for (int i = 0; i < length; i++) {
    grid[x + i][y] = 1;
  }
  placed = true;
}


}
}


// Checks if a player has won the game
bool check_win(int grid[GRID_WIDTH][GRID_HEIGHT]) {
for (int i = 0; i < GRID_WIDTH; i++) {
for (int j = 0; j < GRID_HEIGHT; j++) {
if (grid[i][j] == 1) {
return false;
}
}
}
return true;
}

void switch_player() {
  player = 3 - player;
  current_row = 0;
  current_col = 0;
  draw_board();
}

void handle_shot() {
  if (digitalRead(button_select_pin) == LOW) {
    if (board[current_row][current_col] == 0) {
      tft.fillRect(0, screen_height - 20, screen_width, 20, TFT_BLACK);
      tft.setCursor(0, screen_height - 20);
      tft.print("Miss!");
      delay(1000);
    } else {
      tft.fillRect(0, screen_height - 20, screen_width, 20, TFT_BLACK);
      tft.setCursor(0, screen_height - 20);
      tft.print("Hit!");
      delay(1000);
      board[current_row][current_col] = 0;
      bool game_over = true;
      for (int row = 0; row < board_size; row++) {
        for (int col = 0; col < board_size; col++) {
          if (board[row][col] == 1) {
            game_over = false;
            break;
          }
        }
        if (!game_over) {
          break;
        }
      }
      if (game_over) {
        tft.fillScreen(TFT_WHITE);
        tft.setCursor(0, screen_height / 2 - 20);
        tft.print("Player ");
        tft.print(player);
        tft.print(" wins!");
        delay(3000);
        reset_game();
      }
    }
    switch_player();
  }
}

void reset_game() {
  memset(board, 0, sizeof(board));
  ship_index = 0;
  selected_row = -1;
  selected_col = -1;
  ship_placed = false;
  player = 1;
  current_row = 0;
  current_col = 0;
  draw_board();
}

void handle_dpad_up() {
  if (digitalRead(dpad_up_pin) == LOW) {
    current_row = max(current_row - 1, 0);
    draw_board();
  }
}

void handle_dpad_down() {
  if (digitalRead(dpad_down_pin) == LOW) {
    current_row = min(current_row + 1, board_size - 1);
    draw_board();
  }
}

void handle_dpad_left() {
  if (digitalRead(dpad_left_pin) == LOW) {
    current_col = max(current_col - 1, 0);
    draw_board();
  }
}

void handle_dpad_right() {
  if (digitalRead(dpad_right_pin) == LOW) {
    current_col = min(current_col + 1, board_size - 1);
    draw_board();
  }
}

//Here is the complete code for the 2 player battleship game using the TFT_eSPI.h library:
#include <TFT_eSPI.h>

const int screen_width = 320;
const int screen_height = 240;

const int dpad_up_pin = 2;
const int dpad_down_pin = 3;
const int dpad_left_pin = 4;
const int dpad_right_pin = 5;
const int button_select_pin = 6;
const int button_deselect_pin = 7;

const int board_size = 10;
int board[board_size][board_size];

TFT_eSPI tft = TFT_eSPI();

int player = 1;
int current_row = 0;
int current_col = 0;
int ship_index = 0;
bool ship_placed = false;
int selected_row = -1;
int selected_col = -1;
int ship_lengths[] = {5, 4, 3, 3, 2};

void setup() {
pinMode(dpad_up_pin, INPUT_PULLUP);
pinMode(dpad_down_pin, INPUT_PULLUP);
pinMode(dpad_left_pin, INPUT_PULLUP);
pinMode(dpad_right_pin, INPUT_PULLUP);
pinMode(button_select_pin, INPUT_PULLUP);
pinMode(button_deselect_pin, INPUT_PULLUP);

tft.init();
tft.setRotation(1);
tft.fillScreen(TFT_WHITE);

reset_game();
}

void loop() {
handle_dpad_up();
handle_dpad_down();
handle_dpad_left();
handle_dpad_right();
handle_ship_placement();
handle_shot();
}

void draw_board() {
tft.fillScreen(TFT_WHITE);
tft.drawRect(0, 0, screen_width, screen_height, TFT_BLACK);
for (int row = 0; row < board_size; row++) {
for (int col = 0; col < board_size; col++) {
if (board[row][col] == 1) {
tft.fillRect(col * 30 + 2, row * 30 + 2, 26, 26, TFT_BLACK);
} else if (board[row][col] == 2) {
tft.fillRect(col * 30 + 2, row * 30 + 2, 26, 26, TFT_RED);
}
}
}
tft.fillRect(current_col * 30 + 2, current_row * 30 + 2, 26, 26, TFT_BLUE);
tft.setCursor(0, screen_height - 20);
tft.print("Player ");
tft.print(player);
if (ship_placed) {
tft.print(" ");
tft.print(ship_lengths[ship_index]);
tft.print("-ship");
}
}

void handle_ship_placement() {
if (digitalRead(button_select_pin) == LOW) {
if (!ship_placed) {
if (selected_row != -1 && selected_col != -1) {
int ship_length = ship_lengths[ship_index];
bool valid = true;
if (current_row == selected_row) {
int start_col = min(selected_col, current_col);
int end_col = max(selected_col, current_col);
for (int col = start_col; col <= end_col; col++) {
if (board[current_row][col] != 0) {
valid = false;
break;
}
}
if (valid) {
for (int col = start_col; col <= end_col; col++) {
board[current_row][col] = 1;
}
ship_index++;
if (ship_index >= 5) {
ship_placed = true;
selected_row = -1;
selected_col = -1;
ship_index = 0;
switch_player();
} else {
selected_row = -1;
selected_col = -1;
draw_board();
}
}
} else if (current_col == selected_col) {
int start_row = min(selected_row, current_row);
int end_row = max(selected_row, current_row);
for (int row = start_row; row <= end_row; row++) {
if (board[row][current_col] != 0) {
valid = false;
break;
}
}
if (valid) {
for (int row = start_row; row <= end _row; row <= end_row; row++) {
board[row][current_col] = 1;
}
ship_index++;
if (ship_index >= 5) {
ship_placed = true;
selected_row = -1;
selected_col = -1;
ship_index = 0;
switch_player();
} else {
selected_row = -1;
selected_col = -1;
draw_board();
}
}
}
}
}
} else if (digitalRead(button_deselect_pin) == LOW) {
if (selected_row != -1 && selected_col != -1) {
selected_row = -1;
selected_col = -1;
draw_board();
}
}
}

void handle_shot() {
if (digitalRead(button_select_pin) == LOW) {
if (player == 1) {
int row = current_row;
int col = current_col;
if (board[row][col] == 1) {
board[row][col] = 2;
draw_board();
if (check_game_over()) {
game_over();
} else {
switch_player();
draw_board();
}
} else if (board[row][col] == 0) {
board[row][col] = 2;
switch_player();
draw_board();
}
} else {
// player 2's turn
int row = current_row;
int col = current_col;
if (board[row][col] == 1) {
board[row][col] = 2;
draw_board();
if (check_game_over()) {
game_over();
} else {
switch_player();
draw_board();
}
} else if (board[row][col] == 0) {
board[row][col] = 2;
switch_player();
draw_board();
}
}
}
}

bool check_game_over() {
for (int row = 0; row < board_size; row++) {
for (int col = 0; col < board_size; col++) {
if (board[row][col] == 1) {
return false;
}
}
}
return true;
}

void game_over() {
tft.fillScreen(TFT_WHITE);
tft.setCursor(0, 0);
tft.print("Game over! Player ");
tft.print(player);
tft.print(" wins!");
}

void switch_player() {
if (player == 1) {
player = 2;
} else {
player = 1;
}
}

void reset_game() {
for (int row = 0; row < board_size; row++) {
for (int col = 0; col < board_size; col++) {
board[row][col] = 0;
}
}
player = 1;
current_row = 0;
current_col = 0;
ship_index = 0;
ship_placed = false;
selected_row = -1;
selected_col = -1;
draw_board();
}

void handle_dpad_up() {
if (digitalRead(dpad_up_pin) == LOW) {
if (current_row > 0) {
current_row--;
draw_board();
}
}
}

void handle_dpad_down() {
if (digitalRead(dpad_down_pin) == LOW) {
if (current_row < board_size - 1) {
current_row++;
draw_board();
}
}
}

void handle_dpad_left() {
if (digitalRead(dpad_left_pin)

} else {
// deselect ship
selected_row = -1;
selected_col = -1;
draw_board();
}
}

void handle_dpad_right() {
if (digitalRead(dpad_right_pin) == LOW) {
if (current_col < board_size - 1) {
current_col++;
draw_board();
}
}
}

void draw_board() {
tft.fillScreen(TFT_WHITE);
draw_board_outline();
draw_ships();
draw_current_cell();
}

void draw_board_outline() {
tft.drawRect(board_x, board_y, cell_size * board_size, cell_size * board_size, TFT_BLACK);
for (int i = 1; i < board_size; i++) {
int x = board_x + i * cell_size;
int y = board_y + i * cell_size;
tft.drawLine(board_x, y, board_x + board_size * cell_size, y, TFT_BLACK);
tft.drawLine(x, board_y, x, board_y + board_size * cell_size, TFT_BLACK);
}
}

void draw_ships() {
for (int row = 0; row < board_size; row++) {
for (int col = 0; col < board_size; col++) {
if (board[row][col] == 1) {
tft.fillRect(board_x + col * cell_size, board_y + row * cell_size, cell_size, cell_size, TFT_BLACK);
} else if (board[row][col] == 2) {
tft.fillRect(board_x + col * cell_size, board_y + row * cell_size, cell_size, cell_size, TFT_RED);
}
}
}
}

void draw_current_cell() {
tft.fillRect(board_x + current_col * cell_size, board_y + current_row * cell_size, cell_size, cell_size, TFT_BLUE);
if (selected_row != -1 && selected_col != -1) {
int start_row = min(selected_row, current_row);
int end_row = max(selected_row, current_row);
int start_col = min(selected_col, current_col);
int end_col = max(selected_col, current_col);
tft.fillRect(board_x + start_col * cell_size, board_y + start_row * cell_size, (end_col - start_col + 1) * cell_size, (end_row - start_row + 1) * cell_size, TFT_BLUE);
}
}
void draw_text() {
tft.setTextColor(TFT_BLACK, TFT_WHITE);
tft.setTextWrap(false);
tft.setCursor(text_x, text_y);
tft.setTextSize(2);
tft.print("Player ");
tft.print(player_turn);
tft.setCursor(text_x, text_y + 25);
tft.print("Select a cell");
}

void draw_winner(int winner) {
tft.fillScreen(TFT_WHITE);
tft.setCursor(text_x, text_y);
tft.setTextColor(TFT_BLACK, TFT_WHITE);
tft.setTextSize(2);
tft.print("Player ");
tft.print(winner);
tft.print(" wins!");
}

void handle_select_button() {
if (digitalRead(select_button_pin) == LOW) {
if (selected_row == -1 || selected_col == -1) {
// select ship
selected_row = current_row;
selected_col = current_col;
draw_board();
} else {
// place ship
int start_row = min(selected_row, current_row);
int end_row = max(selected_row, current_row);
int start_col = min(selected_col, current_col);
int end_col = max(selected_col, current_col);
if (can_place_ship(start_row, end_row, start_col, end_col)) {
place_ship(start_row, end_row, start_col, end_col);
selected_row = -1;
selected_col = -1;
draw_board();
switch_player_turn();
} else {
// invalid placement
selected_row = -1;
selected_col = -1;
draw_board();
}
}
}
}

bool can_place_ship(int start_row, int end_row, int start_col, int end_col) {
if (start_row < 0 || start_col < 0 || end_row >= board_size || end_col >= board_size) {
return false;
}
for (int row = start_row; row <= end_row; row++) {
for (int col = start_col; col <= end_col; col++) {
if (board[row][col] != 0) {
return false;
}
}
}
return true;
}

void place_ship(int start_row, int end_row, int start_col, int end_col) {
for (int row = start_row; row <= end_row; row++) {
for (int col = start_col; col <= end_col; col++) {
board[row][col] = player_turn;
}
}
}

void switch_player_turn() {
if (player_turn == 1) {
player_turn = 2;
} else {
player_turn = 1;
}
}

void initialize_board() {
for (int row = 0; row < board_size; row++) {
for (int col = 0; col < board_size; col++) {
board[row][col] = 0;
}
}
}

void initialize_game() {
tft.init();
tft.setRotation(1);
tft.fillScreen(TFT_WHITE);
pinMode(dpad_up_pin, INPUT_PULLUP);
pinMode(dpad_down_pin, INPUT_PULLUP);
pinMode(dpad_left_pin, INPUT_PULLUP);
pinMode(dpad_right_pin, INPUT_PULLUP);
pinMode(select_button_pin, INPUT_PULLUP);
player_turn = 1;
current_row = 0;
current_col = 0;
selected_row = -1;
selected_col = -1;
initialize_board();
draw_text();
}
void loop() {
handle_dpad_input();
handle_select_button();
}

void handle_dpad_input() {
if (digitalRead(dpad_up_pin) == LOW) {
if (current_row > 0) {
current_row--;
draw_board();
}
} else if (digitalRead(dpad_down_pin) == LOW) {
if (current_row < board_size - 1) {
current_row++;
draw_board();
}
} else if (digitalRead(dpad_left_pin) == LOW) {
if (current_col > 0) {
current_col--;
draw_board();
}
} else if (digitalRead(dpad_right_pin) == LOW) {
if (current_col < board_size - 1) {
current_col++;
draw_board();
}
}
}

void draw_board() {
tft.fillScreen(TFT_WHITE);
draw_grid();
draw_cells();
draw_text();
}

void draw_grid() {
tft.drawRect(0, 0, cell_size * board_size, cell_size * board_size, TFT_BLACK);
for (int i = 1; i < board_size; i++) {
tft.drawFastVLine(i * cell_size, 0, cell_size * board_size, TFT_BLACK);
tft.drawFastHLine(0, i * cell_size, cell_size * board_size, TFT_BLACK);
}
}

void draw_cells() {
for (int row = 0; row < board_size; row++) {
for (int col = 0; col < board_size; col++) {
if (board[row][col] == player_turn) {
tft.fillRect(col * cell_size + 1, row * cell_size + 1, cell_size - 1, cell_size - 1, TFT_BLUE);
} else if (board[row][col] != 0) {
tft.fillRect(col * cell_size + 1, row * cell_size + 1, cell_size - 1, cell_size - 1, TFT_RED);
}
}
}
}

