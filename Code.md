#include <iostream>
#include <vector>
#include <thread>
#include <string>
#include <chrono>
#include <algorithm>

using namespace std;

// Introducing a function to tell the Hanoi story
void story(){
    string text = "The tower of Hanoi (also called the tower of Brahma or the Lucas tower) was invented by a French mathematician Édouard Lucas in the 19th century. It is associated with a legend of a Hindu temple where the puzzle was supposedly used to increase the mental discipline of young priests.";
    for(char c : text){
        cout << c << flush;
        // Print out the story word by word
        this_thread::sleep_for(chrono::milliseconds(30));
    }
    cout << endl;
    return;
}

// Define a stack class to handle the tower
class Stack {
private:
	// Define a linked list structure
    struct Node{
        double data;
        Node* next;
    };
	// Define a head
    Node* head;
	// arrNum to get the numbers of elements inside the list
    int arrNum;

public:
    // Constructor initializes the stack counter
    Stack() {
        head = NULL;
        arrNum = 0;
    }

    // Check if stack is empty
    bool isEmpty() {
        return arrNum == 0;
    }


    // Push a value onto the stack
    void push(double num) {
      	// Allocate a space of memory to the list
        Node* current = new Node;
        current->data = num;
        current->next = head;
        head = current;
        arrNum++;
    }

    // Pop the top value from the stack
    double pop() {
        if (isEmpty()) {
            cout << "The stack is empty..." << endl;
            return 1;
        }
        Node* temp = head;
        double element = temp->data;
        head = head->next;
        delete temp;
        arrNum--;
        return element;
    }

    // Display all current values in the stack
    void display() {
        if (isEmpty()) {
            cout << "" << endl;
        } else {
            cout << "The content in the stack is: " << endl;
            Node* temp;
            temp = head;
            while(temp != NULL){
              cout << temp->data << endl;
              temp = temp->next;
            }
        }
    }
};

// Convert a Stack to a vector<double>
vector<double> stackToVector(Stack &start) {
    vector<double> v;
    Stack temp;

    // Pop elements from the stack into vector v and temporary stack
    while (!start.isEmpty()) {
        double val = start.pop();
        v.push_back(val);
        temp.push(val);
    }

    // Restore the original stack
    while (!temp.isEmpty()) {
        start.push(temp.pop());
    }

    // Reverse the vector so the bottom disk comes first
    // Found and learned at https://www.geeksforgeeks.org/stdreverse-in-c/
    reverse(v.begin(), v.end());

    return v;
}

// Display the current state of the three rods
void printRods(Stack &rod1, Stack &rod2, Stack &rod3, int total_disks) {
    // Store the disks on each rod
    vector<vector<double>> rods(3);

    // Convert each Stack into a vector and make it into a 2D vector
    rods[0] = stackToVector(rod1);
    rods[1] = stackToVector(rod2);
    rods[2] = stackToVector(rod3);

    // Clear the console screen
    system("clear");

    // Print the rods from top layer to bottom
    for (int i = total_disks - 1; i >= 0; i--) {
        for (int rod = 0; rod < 3; rod++) {
            if (i < rods[rod].size()) {
                int diskSize = (int)rods[rod][i];
                int p = total_disks - diskSize;

                // Print left spaces then the disk and then right spaces
                for (int j = 0; j < p; j++) {
                    cout << ' ';
                }
                for (int j = 0; j < diskSize * 2 - 1; j++) {
                    cout << '=';
                }
                for (int j = 0; j < p; j++) {
                    cout << ' ';
                }
            } else {
                // Print a vertical bar '|'
                for (int j = 0; j < total_disks - 1; j++) {
                    cout << ' ';
                }
                cout << '|';
                for (int j = 0; j < total_disks - 1; j++) {
                    cout << ' ';
                }
            }
            cout << "\t";
        }
        cout << endl;
    }

    // Print rod labels (1, 2, 3)
    for (int rod = 1; rod <= 3; ++rod) {
        for (int j = 0; j < total_disks - 1; ++j) cout << ' ';
        cout << rod;
        for (int j = 0; j < total_disks - 1; ++j) cout << ' ';
        cout << "\t";
    }
    cout << endl;

    // Pause for some seconds
    this_thread::sleep_for(chrono::milliseconds(1000));
}


// Introducing the Hanoi function
void hanoi(int a, Stack &start, Stack &aux, Stack &target, string start_name, string aux_name, string target_name, 
           Stack &s1, Stack &s2, Stack &s3, int total_disks, int &total){
    // Set the primary condition for the function to end
    if(a == 1){
        double disk_to_move = start.pop();
        target.push(disk_to_move);
        cout << "Move disk " << disk_to_move << " from " << start_name << " to " << target_name << endl;
        printRods(s1, s2, s3, total_disks);
        total++;
        return;
    }
    // Recurse
    hanoi(a-1, start, target, aux, start_name, target_name, aux_name, s1, s2, s3, total_disks, total);
    // Pop one disk out from a rod to another
    double disk = start.pop();
    target.push(disk);
    // Count the total steps used
    total += 1;
    cout << "Move disk " << disk << " from " << start_name << " to " << target_name << endl;
    cout << "The number of steps is: " << total << endl;
    printRods(s1, s2, s3, total_disks);
    hanoi(a-1, aux, start, target, aux_name, start_name, target_name, s1, s2, s3, total_disks, total);
}


// void display(){
  
// }

int main()
{
    system("clear");

    // Declair the variables
    int total = 0;
    char user_input;
    int tower_disk;
    Stack start, aux, target;
    string start_name = "Rod 1", aux_name = "Rod 2", target_name = "Rod 3";
    int rod;

    // The user interface
    cout << "==============================\n";
    cout << "      Welcome to Hanoi!\n";
    cout << "==============================\n";
    cout << "Enter 's' to read the story, or enter 'p' to start the hanoi: ";
    cin.get(user_input);
    cin.ignore();

    // Ask the user for instructions
    if (user_input == 's' || user_input == 'S') {
        system("clear");
        story();
    }
    if (user_input == 'p' || user_input == 'P'){
        system("clear");
        cout << "Please enter how many disks you wish to have: ";
    	cin >> tower_disk;
        
        // Push the number of disks into the start rod
    	for(double i = tower_disk; i > 0; i--){
        	start.push(i);
    	}
        cout << "Please enter which rod you want to move to(2 or 3): ";
        cin >> rod;
        if (rod == 2){
            system("clear");
            hanoi(tower_disk, start, target, aux, start_name, target_name, aux_name, start, target, aux, tower_disk, total);
            // Print out the total steps used
    		cout << "Number of steps is: " << total << endl;
        }else{
            system("clear");
            hanoi(tower_disk, start, aux, target, start_name, aux_name, target_name, start, aux, target, tower_disk, total);
            // Print out the total steps used
    		cout << "Number of steps is: " << total << endl;
        }
    }
  
}
