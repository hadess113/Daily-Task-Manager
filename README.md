#include <iostream>
#include <fstream>
#include <string>
using namespace std;

// ------------------- Node for Linked List -------------------
struct Node {
    string data;
    Node* next;
};

// ------------------- Task Manager using Linked List -------------------
class TaskManager {
private:
    Node* head;
    string filename;

public:
    TaskManager(string file) {
        head = nullptr;
        filename = file;
        loadFromFile();
    }

    ~TaskManager() {
        saveToFile();
        clearList();
    }

    void addTask(string task) {
        Node* newNode = new Node{task, nullptr};
        if (!head) {
            head = newNode;
        } else {
            Node* temp = head;
            while (temp->next)
                temp = temp->next;
            temp->next = newNode;
        }
        cout << "Task added: " << task << endl;
        saveToFile();
    }

    void displayTasks() {
        if (!head) { cout << "No tasks available!\n"; return; }
        Node* temp = head;
        cout << "\nTasks:\n";
        int count = 1;
        while (temp) {
            cout << count++ << ". " << temp->data << endl;
            temp = temp->next;
        }
    }

    void deleteTask(int index) {
        if (!head) { cout << "No tasks to delete!\n"; return; }
        if (index == 1) {
            Node* temp = head;
            head = head->next;
            delete temp;
            cout << "Task deleted!\n";
            saveToFile();
            return;
        }
        Node* temp = head;
        for (int i = 1; i < index - 1 && temp->next; i++)
            temp = temp->next;
        if (temp->next) {
            Node* del = temp->next;
            temp->next = temp->next->next;
            delete del;
            cout << "Task deleted!\n";
            saveToFile();
        } else {
            cout << "Invalid index!\n";
        }
    }

private:
    void saveToFile() {
        ofstream out(filename);
        Node* temp = head;
        while (temp) {
            out << temp->data << endl;
            temp = temp->next;
        }
        out.close();
    }

    void loadFromFile() {
        ifstream in(filename);
        string line;
        while (getline(in, line)) {
            addTaskToListOnly(line);
        }
        in.close();
    }

    void addTaskToListOnly(string task) {
        // Used for loading from file without saving again
        Node* newNode = new Node{task, nullptr};
        if (!head) {
            head = newNode;
        } else {
            Node* temp = head;
            while (temp->next)
                temp = temp->next;
            temp->next = newNode;
        }
    }

    void clearList() {
        Node* temp = head;
        while (temp) {
            Node* next = temp->next;
            delete temp;
            temp = next;
        }
        head = nullptr;
    }
};

// ------------------- Stack using Linked List -------------------
class UndoStack {
private:
    Node* top;

public:
    UndoStack() { top = nullptr; }

    void push(string data) {
        Node* newNode = new Node{data, top};
        top = newNode;
    }

    string pop() {
        if (!top) return "";
        Node* temp = top;
        string val = temp->data;
        top = top->next;
        delete temp;
        return val;
    }

    void display() {
        Node* temp = top;
        cout << "Undo Stack: ";
        while (temp) { cout << temp->data << " "; temp = temp->next; }
        cout << endl;
    }
};

// ------------------- Main Menu -------------------
int main() {
    TaskManager tm("tasks.txt");  // file name
    UndoStack us;
    int choice;
    string task;
    int index;

    do {
        cout << "\n=== OOP + DSA Task Manager (with File Handling) ===\n";
        cout << "1. Add Task\n";
        cout << "2. View Tasks\n";
        cout << "3. Delete Task\n";
        cout << "4. Undo Last Delete\n";
        cout << "5. Exit\n";
        cout << "Enter choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                cin.ignore();
                cout << "Enter task: ";
                getline(cin, task);
                tm.addTask(task);
                break;

            case 2:
                tm.displayTasks();
                break;

            case 3:
                cout << "Enter task index to delete: ";
                cin >> index;
                cin.ignore();
                us.push(to_string(index)); // save index for undo
                tm.deleteTask(index);
                break;

            case 4:
                {
                    string val = us.pop();
                    if (val.empty())
                        cout << "Nothing to undo!\n";
                    else
                        cout << "Undo not fully implemented (placeholder)\n";
                }
                break;

            case 5:
                cout << "Exiting...\n";
                break;

            default:
                cout << "Invalid choice!\n";
        }
    } while(choice != 5);

    return 0;
}
