import copy
import time

# Constants
EMPTY, PEG = 'E', 'P'
BOARD_SIZE = 7
MOVES = [(-2, 0), (2, 0), (0, -2), (0, 2)]  # Up, Down, Left, Right

# Initial Board Configuration
INITIAL_BOARD = [
    [' ', ' ', 'P', 'P', 'P', ' ', ' '],
    [' ', ' ', 'P', 'P', 'P', ' ', ' '],
    ['P', 'P', 'P', 'P', 'P', 'P', 'P'],
    ['P', 'P', 'P', 'E', 'P', 'P', 'P'],
    ['P', 'P', 'P', 'P', 'P', 'P', 'P'],
    [' ', ' ', 'P', 'P', 'P', ' ', ' '],
    [' ', ' ', 'P', 'P', 'P', ' ', ' ']
]

# Validate move
def is_valid_move(board, y, x, dy, dx):
    """Check if the move is valid (jumping over a peg into an empty space)."""
    ny, nx = y + dy, x + dx
    my, mx = y + dy // 2, x + dx // 2  # Middle peg being jumped
    return (
        0 <= ny < BOARD_SIZE and 0 <= nx < BOARD_SIZE and
        board[y][x] == PEG and
        board[my][mx] == PEG and
        board[ny][nx] == EMPTY
    )

# Apply a move
def apply_move(board, y, x, dy, dx):
    """Apply a move and return the new board."""
    new_board = copy.deepcopy(board)
    ny, nx = y + dy, x + dx
    my, mx = y + dy // 2, x + dx // 2
    new_board[y][x] = EMPTY
    new_board[my][mx] = EMPTY
    new_board[ny][nx] = PEG
    return new_board

# Encode board as tuple (for hashing)
def encode_board(board):
    return tuple(tuple(row) for row in board)

# Check if solved
def is_solved(board):
    """Returns True if only one peg remains."""
    return sum(row.count(PEG) for row in board) == 1

# DFS + Backtracking Solver
def solve_brainvita(board, max_moves=36):
    """Solves Brainvita using DFS (backtracking) and finds an optimal 36-move solution."""
    stack = [(board, [], 0)]  # (current board, moves path, move count)
    visited = set()
    
    while stack:
        board, path, move_count = stack.pop()

        if is_solved(board) and move_count <= max_moves:
            return path, board  # Return the solved board

        if move_count >= max_moves:
            continue  # Stop if move limit is reached

        # Explore all possible moves
        for y in range(BOARD_SIZE):
            for x in range(BOARD_SIZE):
                if board[y][x] == PEG:
                    for dy, dx in MOVES:
                        if is_valid_move(board, y, x, dy, dx):
                            new_board = apply_move(board, y, x, dy, dx)
                            board_tuple = encode_board(new_board)

                            if board_tuple not in visited:
                                visited.add(board_tuple)
                                new_path = path + [(y, x, dy, dx)]
                                stack.append((new_board, new_path, move_count + 1))

    return None, board  # No solution found

# Print the board
def print_board(board):
    for row in board:
        print(' '.join(row).replace('E', '.'))
    print()

# Execute and Display AI Moves
def execute_moves_with_display(board, moves):
    """Apply all AI moves on the board and display each step."""
    print("\n- Initial Board:\n")
    print_board(board)
    time.sleep(1)  # Small delay for visibility

    for i, (y, x, dy, dx) in enumerate(moves, 1):
        board = apply_move(board, y, x, dy, dx)
        print(f"\n- Move {i}: Peg ({y}, {x}) → ({y+dy}, {x+dx})\n")
        print_board(board)
        time.sleep(0.5)  # Delay to visualize step-by-step moves

    return board

# Run the AI Solver
if __name__ == "__main__":
    print(" Starting Brainvita AI Solver...\n")

    # Solve the game in minimal moves (≤ 36)
    solution, solved_board = solve_brainvita(INITIAL_BOARD, max_moves=36)

    if solution:

        # Show all moves on the board
        final_board = execute_moves_with_display(copy.deepcopy(INITIAL_BOARD), solution)
        
        print("\n AI solved the game in", len(solution), "moves!\n")
        print("\n AI Wins! Final Board:\n")
        print_board(final_board)

    else:
        print(" No solution found within 36 moves.")

