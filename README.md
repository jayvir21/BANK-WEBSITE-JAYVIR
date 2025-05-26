# simple banking website-JAYVIR
THIS IS A EAMPLE OF A BANK  WEBSITE IN WHICH YOUCAN CHECK YOUR BALANCE , DEPOSIT MONEY, WITHDRAW MONEY AND 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Define a structure to represent a bank account
typedef struct {
    int accountNumber;
    char accountName[50];
    double balance;
} BankAccount;

// Function to display the main menu
void displayMenu() {
    printf("\n*** Bank Transaction Menu ***\n");
    printf("1. Check Balance\n");
    printf("2. Deposit Money\n");
    printf("3. Withdraw Money\n");
    printf("4. Exit\n");
    printf("Enter your choice: ");
}

int main() {
    // Initialize a BankAccount structure
    BankAccount account = {0, "", 0.0};

    int choice, accountNumber;
    char accountName[50];
    double amount;
    char dummy;

    // Get account details from the user
    printf("Enter account number: ");
    scanf("%d", &accountNumber);
    account.accountNumber = accountNumber;

    printf("Enter account name: ");
    scanf("%c", &dummy); // Consume newline character
    scanf("%[^\n]", accountName);
    strcpy(account.accountName, accountName);

    // Main loop to process user choices
    while (1) {
        displayMenu();

        scanf("%d", &choice);

        switch (choice) {
            case 1: // Check Balance
                printf("Your balance: $%.2f\n", account.balance);
                break;
            case 2: // Deposit Money
                printf("Enter amount to deposit: $");
                scanf("%lf", &amount);
                if (amount > 0) {
                    account.balance += amount;
                    printf("Deposit successful. New balance: $%.2f\n", account.balance);
                } else {
                    printf("Invalid amount.\n");
                }
                break;
            case 3: // Withdraw Money
                printf("Enter amount to withdraw: $");
                scanf("%lf", &amount);
                if (amount > 0 && amount <= account.balance) {
                    account.balance -= amount;
                    printf("Withdrawal successful. New balance: $%.2f\n", account.balance);
                } else if (amount > account.balance) {
                    printf("Insufficient funds.\n");
                } else {
                    printf("Invalid amount.\n");
                }
                break;
            case 4: // Exit
                printf("Exiting...\n");
                return 0;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    }

    return 0;
}
