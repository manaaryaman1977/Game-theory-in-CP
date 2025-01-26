#include <bits/stdc++.h>
using namespace std;

class ZobristHashing {
private:
    vector<vector<vector<uint64_t>>> zobristTable;
    uint64_t hashValue;
    int rows, cols;

public:
    // Constructor to initialize the Zobrist table
    ZobristHashing(int rows, int cols, int pieceTypes) : rows(rows), cols(cols) {
        hashValue = 0;
        zobristTable.resize(rows, vector<vector<uint64_t>>(cols, vector<uint64_t>(pieceTypes)));
        initializeZobristTable();
    }

    // Initialize the Zobrist table with random 64-bit numbers
    void initializeZobristTable() {
        random_device rd;
        mt19937_64 gen(rd());
        uniform_int_distribution<uint64_t> dist(0, UINT64_MAX);

        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                for (int k = 0; k < zobristTable[i][j].size(); ++k) {
                    zobristTable[i][j][k] = dist(gen);
                }
            }
        }
    }

    // Compute the hash value for the initial board state
    uint64_t computeInitialHash(const vector<vector<int>> &board) {
        hashValue = 0;
        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                if (board[i][j] != -1) { // -1 represents an empty cell
                    hashValue ^= zobristTable[i][j][board[i][j]];
                }
            }
        }
        return hashValue;
    }

    // Update the hash value when a piece is moved or removed
    void updateHash(int row, int col, int pieceType, bool isRemove) {
        if (pieceType != -1) {
            hashValue ^= zobristTable[row][col][pieceType];
        }
    }

    // Undo a move by reversing the XOR operation
    void undoMove(int row, int col, int pieceType) {
        if (pieceType != -1) {
            hashValue ^= zobristTable[row][col][pieceType];
        }
    }

    // Get the current hash value
    uint64_t getHashValue() const {
        return hashValue;
    }
};

// Example usage
int main() {
    int rows = 3, cols = 3, pieceTypes = 3; // Define the board size and number of piece types

    // Example board: -1 represents an empty cell, 0, 1, 2 represent piece types
    vector<vector<int>> board = {
        {0, -1, 1},
        {-1, 2, -1},
        {-1, -1, -1}
    };

    ZobristHashing zobrist(rows, cols, pieceTypes);

    // Compute the initial hash value
    uint64_t initialHash = zobrist.computeInitialHash(board);
    cout << "Initial Hash: " << initialHash << endl;

    // Update the hash: Move the Knight (piece type 1) from (0, 2) to (2, 1)
    zobrist.updateHash(0, 2, 1, true); // Remove the piece from (0, 2)
    zobrist.updateHash(2, 1, 1, false); // Place the piece at (2, 1)

    // Get the updated hash value
    uint64_t updatedHash = zobrist.getHashValue();
    cout << "Updated Hash: " << updatedHash << endl;

    // Undo the move and verify consistency
    zobrist.undoMove(2, 1, 1); // Remove the piece from (2, 1)
    zobrist.undoMove(0, 2, 1); // Place the piece back at (0, 2)

    uint64_t restoredHash = zobrist.getHashValue();
    cout << "Restored Hash: " << restoredHash << endl;

    return 0;
}
