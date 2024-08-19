#include <iostream>
#include <vector>
#include <string>
#include <iomanip>

using namespace std;
class Transaction {
public:
    string type;
    double amount;
    double balanceAfterTransaction;
    
    Transaction(string t, double amt, double balance)
        : type(t), amount(amt), balanceAfterTransaction(balance) {}
    
    void displayTransaction() const {
        cout << left << setw(15) << type
             << right << setw(10) << amount
             << right << setw(20) << balanceAfterTransaction << endl;
    }
};
class Account {
private:
    int accountNumber;
    double balance;
    vector<Transaction> transactions;

public:
    Account(int accNumber, double initBalance = 0.0)
        : accountNumber(accNumber), balance(initBalance) {}

    int getAccountNumber() const { return accountNumber; }
    double getBalance() const { return balance; }

    void deposit(double amount) {
        balance += amount;
        transactions.push_back(Transaction("Deposit", amount, balance));
    }

    bool withdraw(double amount) {
        if (amount > balance) {
            cout << "Insufficient balance!" << endl;
            return false;
        }
        balance -= amount;
        transactions.push_back(Transaction("Withdrawal", amount, balance));
        return true;
    }

    bool transfer(Account &toAccount, double amount) {
        if (withdraw(amount)) {
            toAccount.deposit(amount);
            transactions.push_back(Transaction("Transfer to Account " + to_string(toAccount.getAccountNumber()), amount, balance));
            toAccount.transactions.push_back(Transaction("Transfer from Account " + to_string(accountNumber), amount, toAccount.getBalance()));
            return true;
        }
        return false;
    }

    void displayTransactions() const {
        cout << "Transaction History for Account " << accountNumber << ":\n";
        cout << left << setw(15) << "Type" 
             << right << setw(10) << "Amount" 
             << right << setw(20) << "Balance After Transaction\n";
        cout << "-------------------------------------------------------\n";
        for (const auto& transaction : transactions) {
            transaction.displayTransaction();
        }
    }

    void displayAccountInfo() const {
        cout << "Account Number: " << accountNumber << "\n";
        cout << "Balance: $" << fixed << setprecision(2) << balance << "\n";
    }
};

class Customer {
private:
    string name;
    int customerID;
    vector<Account> accounts;

public:
    Customer(string custName, int custID)
        : name(custName), customerID(custID) {}

    string getName() const { return name; }
    int getCustomerID() const { return customerID; }

    void createAccount(int accountNumber, double initialDeposit = 0.0) {
        accounts.push_back(Account(accountNumber, initialDeposit));
    }

    Account* getAccount(int accountNumber) {
        for (auto& account : accounts) {
            if (account.getAccountNumber() == accountNumber) {
                return &account;
            }
        }
        return nullptr;
    }

    void displayCustomerInfo() const {
        cout << "Customer Name: " << name << "\n";
        cout << "Customer ID: " << customerID << "\n";
        cout << "Accounts:\n";
        for (const auto& account : accounts) {
            account.displayAccountInfo();
        }
    }
};

int main() {
    Customer customer1("John Doe", 101);
    customer1.createAccount(12345, 500.0);
    customer1.createAccount(67890, 1000.0);

    Account* account1 = customer1.getAccount(12345);
    Account* account2 = customer1.getAccount(67890);

    if (account1 && account2) {
        account1->deposit(200.0);
        account1->withdraw(100.0);
        account1->transfer(*account2, 150.0);

        customer1.displayCustomerInfo();

        cout << "\n";
        account1->displayTransactions();
        cout << "\n";
        account2->displayTransactions();
    } else {
        cout << "Error: Account not found!" << endl;
    }

    return 0;
}
