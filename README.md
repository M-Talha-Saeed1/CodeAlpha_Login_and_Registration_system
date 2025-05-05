# CodeAlpha_Login_and_Registration_syste
This is the C++ program for login and registering the user.
This C++ program implements a simple login and registration system using file handling and basic input validation. User credentials are stored in a plain text file named users.txt, and the system supports:
	•	Registering new users with validation checks
	•	Logging in existing users
	•	Basic username, password, and email validation
	•	Persistent storage via file I/O

⸻

Key Features
	1.	Input Validation:
	•	Usernames must be 3–20 characters long and contain only alphanumeric characters or underscores.
	•	Passwords must be at least 6 characters long.
	•	Emails must contain both @ and . (simple check).
	2.	Registration:
	•	Verifies if the username already exists.
	•	If all checks pass, appends user data (username,password,email) to users.txt.
	3.	Login:
	•	Verifies the entered username and password by reading users.txt.
	•	On successful login, displays a welcome message and the user’s email.
	4.	File Handling:
	•	Uses ifstream and ofstream for reading and writing user data.
	•	Handles input trimming to avoid whitespace issues.
	5.	User Interface:
	•	Console menu with options to register, log in, or exit.
	•	Handles non-integer input gracefully for menu choices.

⸻

Use Case

This code is great for educational purposes or small projects where you want to understand:
	•	Basic string manipulation
	•	Simple validation logic
	•	Persistent data storage using text files
	•	Input/output in C++







#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <sstream>
#include <algorithm>
#include <iomanip>

// Function to trim whitespace from both ends of a string
std::string trim(const std::string& str) {
    size_t first = str.find_first_not_of(" \t\n\r"); // Include tabs and newlines
    if (std::string::npos == first) {
        return "";
    }
    size_t last = str.find_last_not_of(" \t\n\r");
    return str.substr(first, (last - first + 1));
}

// Function to check if a string is a valid email address (basic check)
bool isValidEmail(const std::string& email) {
    // Basic email validation: Check for @ and .
    size_t atPos = email.find('@');
    size_t dotPos = email.find('.');
    return (atPos != std::string::npos && dotPos != std::string::npos && atPos < dotPos);
}

// Function to check if a username is valid
bool isValidUsername(const std::string& username) {
    //  Check if the username contains only alphanumeric characters and underscores
    return std::all_of(username.begin(), username.end(), [](char c){
        return std::isalnum(c) || c == '_';
    });
}

// Function to check if a username already exists
bool usernameExists(const std::string& username, const std::string& filename = "users.txt") {
    std::ifstream file(filename);
    std::string line;
    while (std::getline(file, line)) {
        std::stringstream ss(line);
        std::string storedUsername, storedPassword, storedEmail;
        // Read username, password, and email, separated by commas.
        std::getline(ss, storedUsername, ',');
        std::getline(ss, storedPassword, ',');
        std::getline(ss, storedEmail, ','); // Read the email as well
        if (trim(storedUsername) == username) {
            file.close();
            return true; // Username exists
        }
    }
    file.close();
    return false; // Username does not exist
}

// Function to register a new user
bool registerUser(const std::string& username, const std::string& password, const std::string& email, const std::string& filename = "users.txt") {
    // Validate username, email, and password
    if (!isValidUsername(username)) {
        std::cout << "Invalid username.  Only alphanumeric characters and underscores are allowed." << std::endl;
        return false;
    }
    if (username.length() < 3 || username.length() > 20) {
        std::cout << "Invalid username. Username must be between 3 and 20 characters." << std::endl;
        return false;
    }
    if (usernameExists(username, filename)) {
        std::cout << "Username already exists. Please choose a different username." << std::endl;
        return false;
    }
    if (!isValidEmail(email)) {
        std::cout << "Invalid email address." << std::endl;
        return false;
    }
    if (password.length() < 6) {
        std::cout << "Password must be at least 6 characters long." << std::endl;
        return false;
    }

    // Store user information in the file
    std::ofstream file(filename, std::ios::app); // Append to the file
    if (file.is_open()) {
        // Store username, password, and email, separated by commas.
        file << username << "," << password << "," << email << std::endl;
        file.close();
        std::cout << "User registered successfully." << std::endl;
        return true;
    } else {
        std::cout << "Error opening file for registration." << std::endl;
        return false;
    }
}

// Function to login a user
bool loginUser(const std::string& username, const std::string& password, const std::string& filename = "users.txt") {
    std::ifstream file(filename);
    std::string line;
    while (std::getline(file, line)) {
        std::stringstream ss(line);
        std::string storedUsername, storedPassword, storedEmail; // Also retrieve email
        std::getline(ss, storedUsername, ',');
        std::getline(ss, storedPassword, ',');
        std::getline(ss, storedEmail, ','); // Read the email
        if (trim(storedUsername) == username && trim(storedPassword) == password) {
            file.close();
            std::cout << "Login successful. Welcome, " << username << "!" << std::endl;
            std::cout << "Email: " << storedEmail << std::endl; // Display the email
            return true;
        }
    }
    file.close();
    std::cout << "Invalid username or password." << std::endl;
    return false;
}

int main() {
    int choice;
    std::string username, password, email; // Added email

    while (true) {
        std::cout << "\nLogin / Registration System" << std::endl;
        std::cout << "1. Register" << std::endl;
        std::cout << "2. Login" << std::endl;
        std::cout << "3. Exit" << std::endl;
        std::cout << "Enter your choice: ";
        std::cin >> choice;

        // added to handle non int inputs
        if (std::cin.fail()) {
            std::cout << "Invalid input. Please enter a number." << std::endl;
            std::cin.clear();
            std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
            continue;
        }
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n'); // Clear the input buffer

        switch (choice) {
            case 1:
                std::cout << "Enter username: ";
                std::getline(std::cin, username);
                std::cout << "Enter email: ";
                std::getline(std::cin, email);
                std::cout << "Enter password: ";
                std::getline(std::cin, password);
                registerUser(username, password, email);
                break;
            case 2:
                std::cout << "Enter username: ";
                std::getline(std::cin, username);
                std::cout << "Enter password: ";
                std::getline(std::cin, password);
                loginUser(username, password);
                break;
            case 3:
                std::cout << "Exiting program." << std::endl;
                return 0;
            default:
                std::cout << "Invalid choice. Please try again." << std::endl;
        }
    }
    return 0;
}
