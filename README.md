# tic-tac-Toe-game
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
using namespace std;

// Room Class
class Room {
private:
    int roomNumber;
    string type;
    bool booked;
    string customerName;

public:
    Room(int number = 0, string roomType = "Standard") {
        roomNumber = number;
        type = roomType;
        booked = false;
        customerName = "";
    }

    int getRoomNumber() const {
        return roomNumber;
    }

    bool isBooked() const {
        return booked;
    }

    string getCustomerName() const {
        return customerName;
    }

    void bookRoom(string name) {
        booked = true;
        customerName = name;
    }

    void checkout() {
        booked = false;
        customerName = "";
    }

    void display() const {
        cout << "Room No: " << roomNumber
             << " | Type: " << type
             << " | Status: ";

        if (booked)
            cout << "Booked by " << customerName << endl;
        else
            cout << "Available" << endl;
    }
};

// Customer Class
class Customer {
private:
    string name;
    string phone;
    int roomNumber;

public:
    Customer(string n = "", string p = "", int r = 0) {
        name = n;
        phone = p;
        roomNumber = r;
    }

    string getName() const {
        return name;
    }

    int getRoomNumber() const {
        return roomNumber;
    }

    void display() const {
        cout << "Name: " << name
             << " | Phone: " << phone
             << " | Room: " << roomNumber << endl;
    }
};

// Hotel Management Class
class Hotel {
private:
    vector<Room> rooms;
    vector<Customer> customers;

public:

    Hotel() {
        // Create rooms
        for (int i = 101; i <= 105; i++)
            rooms.push_back(Room(i, "Standard"));

        for (int i = 201; i <= 203; i++)
            rooms.push_back(Room(i, "Deluxe"));

        loadData();
    }

    // Display all rooms
    void showRooms() {
        cout << "\n===== ROOM DETAILS =====\n";

        for (const auto &room : rooms)
            room.display();
    }

    // Book a room
    void bookRoom() {
        int roomNumber;
        string name, phone;

        cout << "\nEnter room number: ";
        cin >> roomNumber;

        for (auto &room : rooms) {

            if (room.getRoomNumber() == roomNumber) {

                // Prevent double booking
                if (room.isBooked()) {
                    cout << "Error: Room is already booked!\n";
                    return;
                }

                cin.ignore();

                cout << "Enter customer name: ";
                getline(cin, name);

                cout << "Enter phone number: ";
                getline(cin, phone);

                room.bookRoom(name);

                customers.push_back(
                    Customer(name, phone, roomNumber)
                );

                saveData();

                cout << "\nRoom booked successfully!\n";
                return;
            }
        }

        cout << "Room not found!\n";
    }

    // Checkout
    void checkout() {
        int roomNumber;

        cout << "\nEnter room number for checkout: ";
        cin >> roomNumber;

        for (auto &room : rooms) {

            if (room.getRoomNumber() == roomNumber) {

                if (!room.isBooked()) {
                    cout << "Room is already available.\n";
                    return;
                }

                room.checkout();

                // Remove customer record
                for (auto it = customers.begin();
                     it != customers.end(); ++it) {

                    if (it->getRoomNumber() == roomNumber) {
                        customers.erase(it);
                        break;
                    }
                }

                saveData();

                cout << "Checkout successful!\n";
                return;
            }
        }

        cout << "Room not found!\n";
    }

    // Search customer
    void searchCustomer() {
        string name;

        cin.ignore();

        cout << "\nEnter customer name to search: ";
        getline(cin, name);

        bool found = false;

        for (const auto &customer : customers) {

            if (customer.getName() == name) {
                customer.display();
                found = true;
            }
        }

        if (!found)
            cout << "Customer not found.\n";
    }

    // Save data to file
    void saveData() {

        ofstream roomFile("rooms.txt");
        ofstream customerFile("customers.txt");

        if (!roomFile || !customerFile) {
            cout << "Error opening file!\n";
            return;
        }

        for (const auto &room : rooms) {
            roomFile << room.getRoomNumber() << " "
                     << room.isBooked() << " "
                     << room.getCustomerName() << endl;
        }

        for (const auto &customer : customers) {
            customerFile << customer.getName() << "|"
                         << customer.getRoomNumber() << endl;
        }

        roomFile.close();
        customerFile.close();
    }

    // Load room data
    void loadData() {

        ifstream roomFile("rooms.txt");

        if (!roomFile)
            return;

        int roomNumber;
        bool booked;
        string customerName;

        while (roomFile >> roomNumber >> booked) {

            getline(roomFile, customerName);

            if (!customerName.empty() &&
                customerName[0] == ' ')
                customerName.erase(0, 1);

            for (auto &room : rooms) {

                if (room.getRoomNumber() == roomNumber) {

                    if (booked)
                        room.bookRoom(customerName);

                    break;
                }
            }
        }

        roomFile.close();
    }
};

// Main Function
int main() {

    Hotel hotel;

    int choice;

    do {
        cout << "\n=================================\n";
        cout << "       HOTEL MANAGEMENT SYSTEM\n";
        cout << "=================================\n";
        cout << "1. Show Rooms\n";
        cout << "2. Book Room\n";
        cout << "3. Checkout\n";
        cout << "4. Search Customer\n";
        cout << "5. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {

        case 1:
            hotel.showRooms();
            break;

        case 2:
            hotel.bookRoom();
            break;

        case 3:
            hotel.checkout();
            break;

        case 4:
            hotel.searchCustomer();
            break;

        case 5:
            cout << "\nThank you for using Hotel Management System!\n";
            break;

        default:
            cout << "Invalid choice!\n";
        }

    } while (choice != 5);

    return 0;
}