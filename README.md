import java.util.Scanner;

class Account {
    int accountNumber;
    int pin;
    double balance;
    String[] transactionHistory;
    int transactionCount;

    public Account(int accountNumber, int pin, double initialBalance) {
        this.accountNumber = accountNumber;
        this.pin = pin;
        this.balance = initialBalance;
        this.transactionHistory = new String[10]; // Fixed size for simplicity
        this.transactionCount = 0;
    }

    public boolean validatePin(int inputPin) {
        return this.pin == inputPin;
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        balance += amount;
        addTransaction("Deposited: $" + amount);
    }

    public boolean withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
            addTransaction("Withdrew: $" + amount);
            return true;
        } else {
            return false;
        }
    }

    public boolean transfer(Account targetAccount, double amount) {
        if (amount <= balance) {
            balance -= amount;
            targetAccount.deposit(amount);
            addTransaction("Transferred: $" + amount + " to Account " + targetAccount.accountNumber);
            return true;
        } else {
            return false;
        }
    }

    public void printTransactionHistory() {
        System.out.println("Transaction History:");
        for (int i = 0; i < transactionCount; i++) {
            System.out.println(transactionHistory[i]);
        }
    }

    public void addTransaction(String transaction) {
        if (transactionCount < transactionHistory.length) {
            transactionHistory[transactionCount++] = transaction;
        } else {
            System.out.println("Transaction history is full.");
        }
    }
}

class ATM {
    Account[] accounts;
    int accountCount;
    Account currentAccount;

    public ATM(int maxAccounts) {
        accounts = new Account[maxAccounts]; // Fixed size array for accounts
        accountCount = 0;
    }

    public void addAccount(Account account) {
        if (accountCount < accounts.length) {
            accounts[accountCount++] = account;
        } else {
            System.out.println("ATM account limit reached.");
        }
    }

    public Account findAccount(int accountNumber) {
        for (int i = 0; i < accountCount; i++) {
            if (accounts[i].accountNumber == accountNumber) {
                return accounts[i];
            }
        }
        return null;
    }

    public boolean login(int accountNumber, int pin) {
        Account account = findAccount(accountNumber);
        if (account != null && account.validatePin(pin)) {
            currentAccount = account;
            return true;
        }
        return false;
    }

    public void logout() {
        currentAccount = null;
    }

    public Account getCurrentAccount() {
        return currentAccount;
    }
}

public class JavaProject2i{
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        ATM atm = new ATM(5); // ATM can handle up to 5 accounts

        // Create some sample accounts
        atm.addAccount(new Account(12345, 1234, 500.00));
        atm.addAccount(new Account(67890, 5678, 1000.00));

        // ATM System loop
        while (true) {
            System.out.println("Welcome to the ATM!");
            System.out.print("Enter your account number: ");
            int accountNumber = scanner.nextInt();
            System.out.print("Enter your PIN: ");
            int pin = scanner.nextInt();

            if (atm.login(accountNumber, pin)) {
                System.out.println("Login successful!");

                while (true) {
                    System.out.println("\nATM Menu:");
                    System.out.println("1. Transactions History");
                    System.out.println("2. Withdraw");
                    System.out.println("3. Deposit");
                    System.out.println("4. Transfer");
                    System.out.println("5. Quit");
                    System.out.print("Choose an option: ");
                    int choice = scanner.nextInt();

                    switch (choice) {
                        case 1:
                            atm.getCurrentAccount().printTransactionHistory();
                            break;
                        case 2:
                            System.out.print("Enter amount to withdraw: ");
                            double withdrawAmount = scanner.nextDouble();
                            if (atm.getCurrentAccount().withdraw(withdrawAmount)) {
                                System.out.println("Withdraw successful. Current balance: $" + atm.getCurrentAccount().getBalance());
                            } else {
                                System.out.println("Insufficient funds.");
                            }
                            break;
                        case 3:
                            System.out.print("Enter amount to deposit: ");
                            double depositAmount = scanner.nextDouble();
                            atm.getCurrentAccount().deposit(depositAmount);
                            System.out.println("Deposit successful. Current balance: $" + atm.getCurrentAccount().getBalance());
                            break;
                        case 4:
                            System.out.print("Enter account number to transfer to: ");
                            int targetAccountNumber = scanner.nextInt();
                            System.out.print("Enter amount to transfer: ");
                            double transferAmount = scanner.nextDouble();
                            Account targetAccount = atm.findAccount(targetAccountNumber);
                            if (targetAccount != null && atm.getCurrentAccount().transfer(targetAccount, transferAmount)) {
                                System.out.println("Transfer successful.");
                            } else {
                                System.out.println("Transfer failed. Check the account number and your balance.");
                            }
                            break;
                        case 5:
                            System.out.println("Thank you for using the ATM. Goodbye!");
                            atm.logout();
                            break;
                        default:
                            System.out.println("Invalid option. Please try again.");
                    }

                    if (choice == 5) break;
                }
            } else {
                System.out.println("Invalid account number or PIN. Please try again.");
            }
        }
    }
}
