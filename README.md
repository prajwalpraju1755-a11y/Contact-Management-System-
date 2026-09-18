# Contact-Management-System-
#include <iostream>
#include <vector>
#include <string>
#include <fstream>
#include <sstream>
#include <algorithm>

class Contact {
public:
    std::string name;
    std::string phone;
    std::string email;
    std::string address;

    Contact(std::string n, std::string p, std::string e, std::string a)
        : name(n), phone(p), email(e), address(a) {}
};

class ContactBook {
private:
    std::vector<Contact> contacts;
    const std::string filename = "contacts.txt";

    // Helper to trim whitespaces
    std::string trim(const std::string& str) {
        size_t first = str.find_first_not_of(" \t\r\n");
        if (first == std::string::npos) return "";
        size_t last = str.find_last_not_of(" \t\r\n");
        return str.substr(first, (last - first + 1));
    }

public:
    ContactBook() {
        loadFromFile();
    }

    ~ContactBook() {
        saveToFile();
    }

    void addContact() {
        std::string name, phone, email, address;
        std::cin.ignore();
        
        std::cout << "\nEnter Name: ";
        std::getline(std::cin, name);
        std::cout << "Enter Phone: ";
        std::getline(std::cin, phone);
        std::cout << "Enter Email: ";
        std::getline(std::cin, email);
        std::cout << "Enter Address: ";
        std::getline(std::cin, address);

        contacts.push_back(Contact(trim(name), trim(phone), trim(email), trim(address)));
        std::cout << "Contact added successfully!\n";
    }

    void displayAll() const {
        if (contacts.empty()) {
            std::cout << "\nNo contacts found.\n";
            return;
        }
        std::cout << "\n--- Contact List ---\n";
        for (size_t i = 0; i < contacts.size(); ++i) {
            std::cout << i + 1 << ". Name: " << contacts[i].name 
                      << " | Phone: " << contacts[i].phone 
                      << " | Email: " << contacts[i].email 
                      << " | Address: " << contacts[i].address << "\n";
        }
    }

    void searchContact() const {
        if (contacts.empty()) {
            std::cout << "\nNo contacts available to search.\n";
            return;
        }
        std::cin.ignore();
        std::string query;
        std::cout << "\nEnter Name or Phone to search: ";
        std::getline(std::cin, query);
        query = trim(query);

        bool found = false;
        for (const auto& contact : contacts) {
            if (contact.name == query || contact.phone == query) {
                std::cout << "\nContact Found:\n";
                std::cout << "Name:    " << contact.name << "\n";
                std::cout << "Phone:   " << contact.phone << "\n";
                std::cout << "Email:   " << contact.email << "\n";
                std::cout << "Address: " << contact.address << "\n";
                found = true;
            }
        }
        if (!found) {
            std::cout << "No matching contact found.\n";
        }
    }

    void editContact() {
        displayAll();
        if (contacts.empty()) return;

        int index;
        std::cout << "\nEnter the number of the contact you want to edit: ";
        std::cin >> index;

        if (index < 1 || index > static_cast<int>(contacts.size())) {
            std::cout << "Invalid selection.\n";
            return;
        }

        std::cin.ignore();
        Contact& contact = contacts[index - 1];

        std::string input;
        std::cout << "Enter new Name (leave blank to keep '" << contact.name << "'): ";
        std::getline(std::cin, input);
        if (!trim(input).empty()) contact.name = trim(input);

        std::cout << "Enter new Phone (leave blank to keep '" << contact.phone << "'): ";
        std::getline(std::cin, input);
        if (!trim(input).empty()) contact.phone = trim(input);

        std::cout << "Enter new Email (leave blank to keep '" << contact.email << "'): ";
        std::getline(std::cin, input);
        if (!trim(input).empty()) contact.email = trim(input);

        std::cout << "Enter new Address (leave blank to keep '" << contact.address << "'): ";
        std::getline(std::cin, input);
        if (!trim(input).empty()) contact.address = trim(input);

        std::cout << "Contact updated successfully!\n";
    }

    void deleteContact() {
        displayAll();
        if (contacts.empty()) return;

        int index;
        std::cout << "\nEnter the number of the contact you want to delete: ";
        std::cin >> index;

        if (index < 1 || index > static_cast<int>(contacts.size())) {
            std::cout << "Invalid selection.\n";
            return;
        }

        contacts.erase(contacts.begin() + (index - 1));
        std::cout << "Contact deleted successfully!\n";
    }

    void loadFromFile() {
        std::ifstream file(filename);
        if (!file.is_open()) return; // File doesn't exist yet, which is fine for first run

        std::string line;
        while (std::getline(file, line)) {
            std::stringstream ss(line);
            std::string name, phone, email, address;
            
            std::getline(ss, name, '|');
            std::getline(ss, phone, '|');
            std::getline(ss, email, '|');
            std::getline(ss, address, '|');

            if (!name.empty()) {
                contacts.push_back(Contact(name, phone, email, address));
            }
        }
        file.close();
    }

    void saveToFile() const {
        std::ofstream file(filename);
        if (!file.is_open()) {
            std::cerr << "Error: Could not save contacts to file.\n";
            return;
        }

        for (const auto& contact : contacts) {
            file << contact.name << "|" 
                 << contact.phone << "|" 
                 << contact.email << "|" 
                 << contact.address << "\n";
        }
        file.close();
    }
};

int main() {
    ContactBook book;
    int choice = 0;

    while (choice != 6) {
        std::cout << "\n===============================\n";
        std::cout << "    CONTACT MANAGEMENT SYSTEM  \n";
        std::cout << "===============================\n";
        std::cout << "1. Add New Contact\n";
        std::cout << "2. Display All Contacts\n";
        std::cout << "3. Search Contact (Name/Phone)\n";
        std::cout << "4. Edit Contact\n";
        std::cout << "5. Delete Contact\n";
        std::cout << "6. Save & Exit\n";
        std::cout << "Enter your choice (1-6): ";
        std::cin >> choice;

        if (std::cin.fail()) {
            std::cin.clear();
            std::cin.ignore(1000, '\n');
            std::cout << "Invalid input. Please enter a number between 1 and 6.\n";
            continue;
        }

        switch (choice) {
            case 1: book.addContact(); break;
            case 2: book.displayAll(); break;
            case 3: book.searchContact(); break;
            case 4: book.editContact(); break;
            case 5: book.deleteContact(); break;
            case 6: std::cout << "Saving contacts and exiting... Goodbye!\n"; break;
            default: std::cout << "Invalid choice. Please select options from 1 to 6.\n";
        }
    }

    return 0;
}