# Java Credit Card Management System

## Project Overview

This project is a command-line Java application designed to manage customer credit card accounts. It processes a file of cardholder data, calculates balances, interest, and rewards points, and then generates a series of reports. The system features a tiered cardholder structure, where different types of cardholders receive different benefits.

This document serves as a guide for understanding, running, and recreating the project.

## Features

- **Tiered Cardholder System**: Includes three levels of cardholders: Sapphire, Diamond, and Blue Diamond, each with unique benefits.
- **Data Processing from File**: Reads customer account data from a semicolon-delimited text file.
- **Transaction Calculations**: For each cardholder, it calculates:
  - Interest on the previous balance.
  - Current balance.
  - Minimum payment due.
  - Purchase points earned.
- **Multiple Report Generation**: Generates four distinct reports:
  - A monthly summary in the original order from the data file.
  - A report sorted alphabetically by cardholder name.
  - A report sorted by the current balance in descending order.
  - A report detailing any invalid data records found in the input file.
- **Custom Exception Handling**: Uses a custom exception (`InvalidCategoryException`) to manage records with an invalid cardholder category.

## Project Structure

The project is organized into several key Java files:

- **`Cardholder.java`**: An `abstract` base class that defines the common attributes and methods for all cardholders, such as name, account number, previous balance, and payment. It includes an `abstract` method `calcPurchasePoints()` that must be implemented by its subclasses.

- **`SapphireCardholder.java`**, **`DiamondCardholder.java`**, **`BlueDiamondCardholder.java`**: These are concrete subclasses of `Cardholder`. Each class represents a different tier of cardholder and provides a specific implementation for calculating purchase points and, where applicable, discounts.

- **`CardholderProcessor.java`**: This is the main logic hub of the application. It is responsible for reading the input file, parsing each line to create the appropriate cardholder object, and storing them. It also contains the methods for generating all the required reports.

- **`CardholdersPart3App.java`**: The main application class with the `main` method. It handles command-line arguments to get the input file name and orchestrates the process by calling the `CardholderProcessor`.

- **`CurrentBalanceComparator.java`**: A utility class that implements the `Comparator` interface to sort cardholders based on their current balance.

- **`InvalidCategoryException.java`**: A custom exception class used to flag records with an unrecognized cardholder category code.

## How to Compile and Run

To compile and run this project, you will need to have a Java Development Kit (JDK) installed.

### Compilation

1.  Open a terminal or command prompt.
2.  Navigate to the directory containing all the `.java` files (e.g., `proj 11/`).
3.  Compile all the Java source files using the `javac` command:

    ```bash
    javac *.java
    ```

### Execution

1.  After successful compilation, run the application using the `java` command.
2.  You must provide the path to a valid data file as a command-line argument.

    ```bash
    java CardholdersPart3App cardholder_data_1.txt
    ```

    Replace `cardholder_data_1.txt` with the name of your data file. The application will then print the generated reports to the console.

## Data File Format

The application expects a semicolon-delimited text file (`.txt`) with the following format for each line:

`category;acctNumber;name;prevBalance;payment;purchase1;purchase2;...`

- **`category`**: A single digit representing the cardholder tier (`1` for Sapphire, `2` for Diamond, `3` for Blue Diamond).
- **`acctNumber`**: The account number (String).
- **`name`**: The cardholder's name (String).
- **`prevBalance`**: The previous month's balance (double).
- **`payment`**: The payment made by the customer (double).
- **`purchases`**: A variable number of purchases made during the month, a each a double.

**Example Data:**

```
1;10001;Smith, Pat;1200.0;200.0;12.0;34.5;67.8
2;10002;Jones, Sam;5000.0;500.0;120.0;340.5
3;10003;Williams, Jo;10000.0;1000.0;2500.50
```

## Step-by-Step Guide to Recreate the Project

Here is a more detailed, beginner-friendly guide to recreating this project.

### Task 1: Create the `Cardholder` Abstract Class

An **abstract class** is like a blueprint for other classes. It can't be used to create objects directly, but it defines common features for its subclasses. Here, `Cardholder` will define what all cardholders have in common.

1.  **Create the file `Cardholder.java`**.
2.  **Define the class structure**:

    ```java
    import java.text.DecimalFormat;
    import java.util.Arrays;

    public abstract class Cardholder implements Comparable<Cardholder> {
        // Fields (variables) for the class
        protected String category;
        protected String acctNumber;
        protected String name;
        protected double prevBalance;
        protected double payment;
        protected double[] purchases;

        public static final double INTEREST_RATE = 0.01;

        // Constructor: A special method to create objects
        public Cardholder(String acctNumberIn, String nameIn) {
            acctNumber = acctNumberIn;
            name = nameIn;
            purchases = new double[0]; // Initialize as an empty array
        }

        // --- Getters and Setters ---
        // Methods to get and set the values of the fields.
        // Example:
        public String getName() {
            return name;
        }

        public void setName(String nameIn) {
            name = nameIn;
        }
        // (Add getters and setters for all other fields)

        // --- Business Logic Methods ---
        public double totalPurchases() {
            double total = 0;
            for (double purchase : purchases) {
                total += purchase;
            }
            return total;
        }

        public double getInterest() {
            return (prevBalance - payment) * INTEREST_RATE;
        }

        public double getCurrentBalance() {
            return prevBalance - payment + getInterest() + totalPurchases();
        }

        // Abstract method: Must be implemented by subclasses
        public abstract int calcPurchasePoints();

        // toString method: For printing the object's state
        @Override
        public String toString() {
            // Use DecimalFormat for nice currency formatting
            DecimalFormat df = new DecimalFormat("$#,##0.00");
            return category + "\nAcctNo/Name: " + acctNumber + " " + name
                + "\nPrevious Balance: " + df.format(prevBalance)
                /* ... and so on for the other fields ... */;
        }

        // compareTo method: For sorting by name
        @Override
        public int compareTo(Cardholder other) {
            return this.name.compareTo(other.name);
        }
    }
    ```

### Task 2: Implement the Concrete `Cardholder` Subclasses

Now, create the specific types of cardholders. They will **inherit** all the properties from `Cardholder` and provide their own implementation for `calcPurchasePoints()`.

1.  **Create the file `SapphireCardholder.java`**:

    ```java
    public class SapphireCardholder extends Cardholder {
        // Constructor
        public SapphireCardholder(String acctNumberIn, String nameIn) {
            super(acctNumberIn, nameIn); // Calls the parent class constructor
            this.category = "Sapphire Cardholder";
        }

        // Implementation of the abstract method
        @Override
        public int calcPurchasePoints() {
            // 1 point per dollar of total purchases
            return (int) totalPurchases();
        }
    }
    ```
2.  **Create `DiamondCardholder.java` and `BlueDiamondCardholder.java`** in a similar way, following the logic described in their respective `.java` files.

### Task 3: Implement the `CurrentBalanceComparator`

A **Comparator** is used to define custom sorting rules. We'll use it to sort cardholders by their balance.

1.  **Create the file `CurrentBalanceComparator.java`**:

    ```java
    import java.util.Comparator;

    public class CurrentBalanceComparator implements Comparator<Cardholder> {
        @Override
        public int compare(Cardholder c1, Cardholder c2) {
            // Sort in descending order
            if (c1.getCurrentBalance() > c2.getCurrentBalance()) {
                return -1;
            } else if (c1.getCurrentBalance() < c2.getCurrentBalance()) {
                return 1;
            } else {
                return 0;
            }
        }
    }
    ```

### Task 4: Create the Custom `InvalidCategoryException`

A **custom exception** helps us handle specific errors gracefully.

1.  **Create the file `InvalidCategoryException.java`**:

    ```java
    public class InvalidCategoryException extends Exception {
        public InvalidCategoryException() {
            super("*** invalid category ***");
        }
    }
    ```

### Task 5: Implement the `CardholderProcessor`

This class does the heavy lifting: reading the file and managing the data.

1.  **Create the file `CardholderProcessor.java`**.
2.  **Add fields** for the cardholder array and invalid records array.
3.  **Implement the `readCardholderFile` method**:

    ```java
    import java.io.File;
    import java.io.FileNotFoundException;
    import java.util.Scanner;
    import java.util.Arrays;

    public class CardholderProcessor {
        private Cardholder[] cardholders = new Cardholder[0];
        private String[] invalidRecords = new String[0];

        public void readCardholderFile(String fileName) throws FileNotFoundException {
            Scanner fileScanner = new Scanner(new File(fileName));
            while (fileScanner.hasNext()) {
                String line = fileScanner.nextLine();
                try (Scanner lineScanner = new Scanner(line)) {
                    lineScanner.useDelimiter(";");
                    char category = lineScanner.next().charAt(0);
                    // ... (rest of the parsing logic)

                    // Example for Sapphire
                    if (category == '1') {
                        String acct = lineScanner.next();
                        String name = lineScanner.next();
                        SapphireCardholder sc = new SapphireCardholder(acct, name);
                        // ... (set balances, purchases, etc.)
                        // Add to array
                        cardholders = Arrays.copyOf(cardholders, cardholders.length + 1);
                        cardholders[cardholders.length - 1] = sc;
                    }
                    // ... (else if for other categories)
                } catch (Exception e) {
                    // Add to invalid records
                    invalidRecords = Arrays.copyOf(invalidRecords, invalidRecords.length + 1);
                    invalidRecords[invalidRecords.length - 1] = line + " *** " + e.toString();
                }
            }
        }
        // ... (Implement report generation methods)
    }
    ```

### Task 6: Create the Main Application

This class will run the entire application.

1.  **Create `CardholdersPart3App.java`**:

    ```java
    import java.io.FileNotFoundException;

    public class CardholdersPart3App {
        public static void main(String[] args) {
            // Check for command-line argument
            if (args.length == 0) {
                System.out.println("File name expected as command line argument.");
                System.out.println("Program ending.");
                return; // Exit
            }

            String fileName = args[0];
            CardholderProcessor processor = new CardholderProcessor();

            try {
                processor.readCardholderFile(fileName);
                // Print reports
                System.out.println(processor.generateReport());
                System.out.println(processor.generateReportByName());
                // ... etc.
            } catch (FileNotFoundException e) {
                System.out.println("*** Attempted to read file: " + fileName);
            }
        }
    }
    ```

## Hints and Tips

- **Use Abstract Classes**: The use of an abstract `Cardholder` class is a great example of polymorphism. It allows you to treat all types of cardholders uniformly in the `CardholderProcessor` while still allowing each subclass to have its own unique behavior.
- **Comparator Interface**: The `Comparator` interface is a powerful tool for defining custom sorting logic. It keeps the sorting logic separate from the object's main class.
- **Exception Handling**: Custom exceptions make your code cleaner and your error handling more specific.
- **File I/O**: Pay close attention to `FileNotFoundException` when dealing with file input. Always provide helpful error messages to the user.
- **Command-Line Arguments**: Remember that `args[0]` holds the first argument. Always check `args.length` to avoid an `ArrayIndexOutOfBoundsException`.
