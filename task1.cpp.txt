
#include <iostream>
#include <string>
#include <limits>
using namespace std;

struct BookRequestNode
{
    int studentID;
    string studentName;
    int bookID;
    string bookTitle;
    int priority;
    BookRequestNode* next;
};

class PriorityQueue
{
private:
    BookRequestNode* front;
    BookRequestNode* rear;
    int count;

    void Clear()
    {
        while (front != nullptr)
        {
            BookRequestNode* temp = front;
            front = front->next;
            delete temp;
        }

        rear = nullptr;
        count = 0;
    }

public:
    PriorityQueue()
    {
        front = nullptr;
        rear = nullptr;
        count = 0;
    }

    void Add(BookRequestNode* request)
    {
        request->next = nullptr;

        if (front == nullptr)
        {
            front = request;
            rear = request;
        }
        else if (request->priority < front->priority)
        {
            request->next = front;
            front = request;
        }
        else
        {
            BookRequestNode* current = front;

            while (current->next != nullptr &&
                   current->next->priority <= request->priority)
            {
                current = current->next;
            }

            request->next = current->next;
            current->next = request;

            if (request->next == nullptr)
            {
                rear = request;
            }
        }

        count++;
    }

    BookRequestNode* Remove()
    {
        if (front == nullptr)
        {
            return nullptr;
        }

        BookRequestNode* request = front;
        front = front->next;

        if (front == nullptr)
        {
            rear = nullptr;
        }

        request->next = nullptr;
        count--;

        return request;
    }

    bool IsEmpty()
    {
        return front == nullptr;
    }

    bool IsNotEmpty()
    {
        return front != nullptr;
    }

    void PrintQueue()
    {
        if (IsEmpty())
        {
            cout << "No pending book requests." << endl;
            return;
        }

        BookRequestNode* current = front;

        while (current != nullptr)
        {
            cout << "\nStudent ID: " << current->studentID << endl;
            cout << "Student Name: " << current->studentName << endl;
            cout << "Book ID: " << current->bookID << endl;
            cout << "Book Title: " << current->bookTitle << endl;
            cout << "Priority: " << current->priority << endl;
            cout << "-----------------------------" << endl;

            current = current->next;
        }
    }

    PriorityQueue(const PriorityQueue& other)
    {
        front = nullptr;
        rear = nullptr;
        count = 0;

        BookRequestNode* current = other.front;

        while (current != nullptr)
        {
            BookRequestNode* newNode = new BookRequestNode;

            newNode->studentID = current->studentID;
            newNode->studentName = current->studentName;
            newNode->bookID = current->bookID;
            newNode->bookTitle = current->bookTitle;
            newNode->priority = current->priority;
            newNode->next = nullptr;

            Add(newNode);
            current = current->next;
        }
    }

    PriorityQueue& operator=(const PriorityQueue& other)
    {
        if (this == &other)
        {
            return *this;
        }

        Clear();

        BookRequestNode* current = other.front;

        while (current != nullptr)
        {
            BookRequestNode* newNode = new BookRequestNode;

            newNode->studentID = current->studentID;
            newNode->studentName = current->studentName;
            newNode->bookID = current->bookID;
            newNode->bookTitle = current->bookTitle;
            newNode->priority = current->priority;
            newNode->next = nullptr;

            Add(newNode);
            current = current->next;
        }

        return *this;
    }

    ~PriorityQueue()
    {
        Clear();
    }
};

BookRequestNode* CreateRequest(
    int studentID,
    string studentName,
    int bookID,
    string bookTitle,
    int priority)
{
    BookRequestNode* request = new BookRequestNode;

    request->studentID = studentID;
    request->studentName = studentName;
    request->bookID = bookID;
    request->bookTitle = bookTitle;
    request->priority = priority;
    request->next = nullptr;

    return request;
}

int main()
{
    PriorityQueue requests;
    int choice;

    do
    {
        cout << "\n===== Library Book Request System =====" << endl;
        cout << "1. Add Book Request" << endl;
        cout << "2. Process Highest Priority Request" << endl;
        cout << "3. Display All Requests" << endl;
        cout << "4. Check Queue Status" << endl;
        cout << "5. Exit" << endl;
        cout << "Enter your choice: ";

        if (!(cin >> choice))
        {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Invalid input. Enter a number from 1 to 5." << endl;
            continue;
        }

        cin.ignore(numeric_limits<streamsize>::max(), '\n');

        switch (choice)
        {
        case 1:
        {
            int studentID, bookID, priority;
            string studentName, bookTitle;

            cout << "Enter Student ID: ";
            if (!(cin >> studentID))
            {
                cin.clear();
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Invalid Student ID." << endl;
                break;
            }

            cin.ignore(numeric_limits<streamsize>::max(), '\n');

            cout << "Enter Student Name: ";
            getline(cin, studentName);

            cout << "Enter Book ID: ";
            if (!(cin >> bookID))
            {
                cin.clear();
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Invalid Book ID." << endl;
                break;
            }

            cin.ignore(numeric_limits<streamsize>::max(), '\n');

            cout << "Enter Book Title: ";
            getline(cin, bookTitle);

            cout << "Enter Priority (1-3): ";
            if (!(cin >> priority))
            {
                cin.clear();
                cin.ignore(numeric_limits<streamsize>::max(), '\n');
                cout << "Invalid priority." << endl;
                break;
            }

            cin.ignore(numeric_limits<streamsize>::max(), '\n');

            if (priority < 1 || priority > 3)
            {
                cout << "Priority must be 1, 2, or 3." << endl;
                break;
            }

            BookRequestNode* request = CreateRequest(
                studentID, studentName, bookID, bookTitle, priority);

            requests.Add(request);

            cout << "Book request added successfully." << endl;
            break;
        }

        case 2:
        {
            BookRequestNode* processed = requests.Remove();

            if (processed == nullptr)
            {
                cout << "No requests available to process." << endl;
            }
            else
            {
                cout << "\n===== Processing Request =====" << endl;
                cout << "Student ID: " << processed->studentID << endl;
                cout << "Student Name: " << processed->studentName << endl;
                cout << "Book ID: " << processed->bookID << endl;
                cout << "Book Title: " << processed->bookTitle << endl;
                cout << "Priority: " << processed->priority << endl;

                delete processed;
            }

            break;
        }

        case 3:
            cout << "\n===== All Pending Requests =====" << endl;
            requests.PrintQueue();
            break;

        case 4:
            if (requests.IsEmpty())
            {
                cout << "Queue is empty." << endl;
            }
            else if (requests.IsNotEmpty())
            {
                cout << "Queue is not empty." << endl;
            }
            break;

        case 5:
            cout << "Exiting Library Book Request System." << endl;
            break;

        default:
            cout << "Invalid choice. Please select 1 to 5." << endl;
        }

    } while (choice != 5);

    return 0;
}