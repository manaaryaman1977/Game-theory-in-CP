#include <iostream>
#include <cmath>
#include <vector>
#include <algorithm>
using namespace std;

// Function to implement the Minimax algorithm
int minimax(int depth, int nodeIndex, bool isMax, const vector<int>& scores, int height) {
    // Base case: If we have reached a leaf node
    if (depth == height) {
        return scores[nodeIndex];
    }

    // If the current level is the maximizer's turn
    if (isMax) {
        return max(
            minimax(depth + 1, nodeIndex * 2, false, scores, height),
            minimax(depth + 1, nodeIndex * 2 + 1, false, scores, height)
        );
    } 
    // If the current level is the minimizer's turn
    else {
        return min(
            minimax(depth + 1, nodeIndex * 2, true, scores, height),
            minimax(depth + 1, nodeIndex * 2 + 1, true, scores, height)
        );
    }
}

// Utility function to calculate the height of the game tree
int calculateHeight(int numLeaves) {
    return log2(numLeaves);
}

// Driver code
int main() {
    int numLeaves;

    // Take input from the user for the number of leaf nodes
    cout << "Enter the number of leaf nodes (must be a power of 2): ";
    cin >> numLeaves;

    // Check if the input number of leaves is a power of 2
    if ((numLeaves & (numLeaves - 1)) != 0) {
        cout << "Error: Number of leaf nodes must be a power of 2." << endl;
        return 1;
    }

    // Input the scores for the leaf nodes
    vector<int> scores(numLeaves);
    cout << "Enter the scores for the leaf nodes:" << endl;
    for (int i = 0; i < numLeaves; i++) {
        cin >> scores[i];
    }

    // Calculate the height of the game tree
    int height = calculateHeight(numLeaves);

    // Call the minimax function starting from the root
    int optimalValue = minimax(0, 0, true, scores, height);

    // Output the optimal value for the maximizing player
    cout << "The optimal value is: " << optimalValue << endl;

    return 0;
}
