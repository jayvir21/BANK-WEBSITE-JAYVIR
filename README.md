#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// ANSI escape codes for colors
#define RESET   "\x1B[0m"
#define RED     "\x1B[31m"
#define GREEN   "\x1B[32m"
#define YELLOW  "\x1B[33m"
#define CYAN    "\x1B[36m"

#define MAX_USERS 100
#define MAX_TRANSACTIONS 50  // Max number of stored transactions
#define MAX_USERNAME_LENGTH 50
#define MAX_ACCOUNT_NUMBER_LENGTH 10
#define MAX_EMAIL_LENGTH 100
#define MAX_PASSWORD_LENGTH 20

// Manager credentials (you can change these if desired)
#define MANAGER_ID "manager"
#define MANAGER_PASSWORD "admin"

typedef struct {
    char email[MAX_EMAIL_LENGTH + 1];
    char password[MAX_PASSWORD_LENGTH + 1];
    char username[MAX_USERNAME_LENGTH + 1];
    char accountNumber[MAX_ACCOUNT_NUMBER_LENGTH + 1];
    float balance;
    // Transaction history
    float withdrawals[MAX_TRANSACTIONS];
    char timestamps[MAX_TRANSACTIONS][30];
    int transactionCount;
} User;

User users[MAX_USERS];
int userCount = 0;

void accountHandling(int userIndex);
void managerInterface();
int managerLogin();

void signUp() {
    if (userCount >= MAX_USERS) {
        printf(RED "User limit reached!\n" RESET);
        return;
    }

    User newUser;
    printf("Enter email ID: ");
    scanf("%s", newUser.email);

    printf("Enter password (max %d characters): ", MAX_PASSWORD_LENGTH);
    scanf("%s", newUser.password);

    printf("Enter username (max %d alphabetic characters): ", MAX_USERNAME_LENGTH);
    scanf("%s", newUser.username);
    
    printf("Enter account number (max %d numeric digits): ", MAX_ACCOUNT_NUMBER_LENGTH);
    scanf("%s", newUser.accountNumber);

    newUser.balance = 0.0;
    newUser.transactionCount = 0;  // Initialize transaction count
    users[userCount] = newUser;

    printf(GREEN "\nAccount created successfully!\nRedirecting to account management...\n" RESET);
    
    accountHandling(userCount);  // Redirect to account handling
    userCount++;  // Move to the next user slot
}

int login() {
    char email[MAX_EMAIL_LENGTH + 1];
    char password[MAX_PASSWORD_LENGTH + 1];

    printf("Enter your email ID: ");
    scanf("%s", email);
    printf("Enter your password: ");
    scanf("%s", password);

    for (int i = 0; i < userCount; i++) {
        if (strcmp(users[i].email, email) == 0 && strcmp(users[i].password, password) == 0) {
            return i;
        }
    }
    printf(RED "Login failed! Incorrect email or password.\n" RESET);
    return -1;
}

void deposit(int userIndex) {
    float amount;
    printf("Enter deposit amount: ₹");
    scanf("%f", &amount);
    users[userIndex].balance += amount;
    printf(GREEN "Deposit successful! Current balance: ₹%.2f\n" RESET, users[userIndex].balance);
}

void withdraw(int userIndex) {
    float amount;
    printf("Enter withdrawal amount: ₹");
    scanf("%f", &amount);
    
    if (amount > users[userIndex].balance) {
        printf(RED "Insufficient balance!\n" RESET);
    } else {
        users[userIndex].balance -= amount;
        // Record withdrawal details if there's space available
        if (users[userIndex].transactionCount < MAX_TRANSACTIONS) {
            int idx = users[userIndex].transactionCount;
            users[userIndex].withdrawals[idx] = amount;
            
            // Get current time
            time_t t = time(NULL);
            struct tm *tm = localtime(&t);
            strftime(users[userIndex].timestamps[idx], sizeof(users[userIndex].timestamps[0]), "%Y-%m-%d %H:%M:%S", tm);
            
            users[userIndex].transactionCount++;
        }
        printf(GREEN "Withdrawal successful! Current balance: ₹%.2f\n" RESET, users[userIndex].balance);
    }
}

void transactionBill(int userIndex) {
    printf("\n========= " YELLOW "TRANSACTION SUMMARY" RESET " =========\n");
    printf("Account Number: %s\n", users[userIndex].accountNumber);
    
    if (users[userIndex].transactionCount > 0) {
        printf("Transaction History:\n");
        for (int i = 0; i < users[userIndex].transactionCount; i++) {
            printf("  [%d] Withdrawn ₹%.2f on %s\n", i + 1, users[userIndex].withdrawals[i], users[userIndex].timestamps[i]);
        }
    } else {
        printf("No withdrawals yet.\n");
    }
    printf("Current Balance: ₹%.2f\n", users[userIndex].balance);
    printf("=======================================\n");
}

void accountHandling(int userIndex) {
    int choice;
    
    while (1) {
        printf("\nWelcome, %s!\n", users[userIndex].username);
        printf(CYAN "Choose an option:\n1. Deposit\n2. Withdraw\n3. Check Balance\n4. Transaction Bill\n5. Logout\n" RESET);
        scanf("%d", &choice);
        
        if (choice == 1) {
            deposit(userIndex);
        } else if (choice == 2) {
            withdraw(userIndex);
        } else if (choice == 3) {
            printf("Current balance: ₹%.2f\n", users[userIndex].balance);
        } else if (choice == 4) {
            transactionBill(userIndex);
        } else if (choice == 5) {
            printf(GREEN "Logged out successfully.\n" RESET);
            break;
        } else {
            printf(RED "Invalid choice!\n" RESET);
        }
    }
}

// Manager login: Returns 1 if credentials are correct, else 0.
int managerLogin() {
    char empID[MAX_EMAIL_LENGTH + 1];
    char password[MAX_PASSWORD_LENGTH + 1];
    
    printf("Enter Manager Employee ID: ");
    scanf("%s", empID);
    printf("Enter Manager Password: ");
    scanf("%s", password);
    
    if (strcmp(empID, MANAGER_ID) == 0 && strcmp(password, MANAGER_PASSWORD) == 0) {
        return 1;
    } else {
        printf(RED "Incorrect Manager credentials!\n" RESET);
        return 0;
    }
}

// Manager interface: Displays all user data.
void managerInterface() {
    printf("\n========== " YELLOW "MANAGER INTERFACE" RESET " ==========\n");
    if (userCount == 0) {
        printf("No user data available.\n");
    } else {
        for (int i = 0; i < userCount; i++) {
            printf("\nUser %d:\n", i + 1);
            printf("  Email: %s\n", users[i].email);
            printf("  Username: %s\n", users[i].username);
            printf("  Account Number: %s\n", users[i].accountNumber);
            printf("  Balance: ₹%.2f\n", users[i].balance);
            printf("  Transaction History:\n");
            if (users[i].transactionCount > 0) {
                for (int j = 0; j < users[i].transactionCount; j++) {
                    printf("    [%d] Withdrawn ₹%.2f on %s\n", j + 1, users[i].withdrawals[j], users[i].timestamps[j]);
                }
            } else {
                printf("    No transactions yet.\n");
            }
        }
    }
    printf("\n========== End of Manager Interface ==========\n");
}

int main() {
    int choice, userIndex;
    
    while (1) {
        printf("\n=========================\n");
        printf(YELLOW "      HBD BANK\n" RESET);
        printf("=========================\n");
        printf(CYAN "\n1. Sign Up\n2. Login\n3. Manager Login\n4. Exit\nChoose an option: " RESET);
        scanf("%d", &choice);
        
        if (choice == 1) {
            signUp();
        } else if (choice == 2) {
            userIndex = login();
            if (userIndex != -1) {
                accountHandling(userIndex);
            }
        } else if (choice == 3) {
            if (managerLogin()) {
                managerInterface();
            }
        } else if (choice == 4) {
            printf(GREEN "Exiting program.\n" RESET);
            break;
        } else {
            printf(RED "Invalid choice!\n" RESET);
        }
    }
    
    return 0;
}
