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

Here is a breakdown of the tasks to recreate this project from scratch.

### Task 1: Create the `Cardholder` Abstract Class

1.  Define an abstract class `Cardholder`.
2.  Add protected fields for `name`, `acctNumber`, `prevBalance`, `payment`, and an array of `double` for `purchases`.
3.  Create a constructor that accepts the account number and name.
4.  Implement getter and setter methods for all fields.
5.  Add methods to calculate interest (`getInterest()`), current balance (`getCurrentBalance()`), and minimum payment (`getMinPayment()`).
6.  Define an abstract method `calcPurchasePoints()` which will be implemented by the subclasses.
7.  Override the `toString()` method to create a formatted string representation of the cardholder's data.

### Task 2: Implement the Concrete `Cardholder` Subclasses

1.  **`SapphireCardholder`**:
    - Extend `Cardholder`.
    - Implement `calcPurchasePoints()` to return 1 point for every dollar spent on total purchases.
2.  **`DiamondCardholder`**:
    - Extend `Cardholder`.
    - Add a discount rate for purchases.
    - Implement `calcPurchasePoints()` to award 3 points per dollar after a discount.
3.  **`BlueDiamondCardholder`**:
    - Extend `Cardholder`.
    - Implement a higher discount rate.
    - Implement `calcPurchasePoints()` to award 5 points per dollar after a discount, with a bonus if purchases exceed a certain threshold.

### Task 3: Implement the `CurrentBalanceComparator`

1.  Create a class `CurrentBalanceComparator` that implements `Comparator<Cardholder>`.
2.  Override the `compare()` method to sort two `Cardholder` objects based on their current balance in descending order.

### Task 4: Create the Custom `InvalidCategoryException`

1.  Create a new class `InvalidCategoryException` that extends `Exception`.
2.  In the constructor, call the `super()` constructor with a descriptive error message like "*** invalid category ***".

### Task 5: Implement the `CardholderProcessor`

1.  Create a class `CardholderProcessor`.
2.  Add two private fields: an array of `Cardholder` objects and an array of `String` for invalid records.
3.  Implement a method `readCardholderFile(String fileName)`:
    - Use a `Scanner` to read the file line by line.
    - Inside a `try-catch` block, parse each line using `String.split(";")` or a `Scanner` with a delimiter.
    - Use a `switch` statement on the category to create the correct `Cardholder` subclass.
    - Populate the object with data from the line.
    - Add the new object to the `Cardholder` array.
    - Catch `InvalidCategoryException` and `NumberFormatException`, adding the invalid line to the `invalidRecords` array.
4.  Implement methods to generate reports:
    - `generateReport()`: Loop through the cardholders and use their `toString()` method.
    - `generateReportByName()`: Sort the array using `Arrays.sort()` (which uses the `compareTo` method in `Cardholder`) and then generate the report.
    - `generateReportByCurrentBalance()`: Sort the array using `Arrays.sort()` with an instance of `CurrentBalanceComparator`, then generate the report.
    - `generateInvalidRecordsReport()`: Loop through the `invalidRecords` array and format the output.

### Task 6: Create the Main Application

1.  Create a class `CardholdersPart3App` with a `public static void main(String[] args)` method.
2.  Check if a command-line argument (the file name) is provided.
3.  Create an instance of `CardholderProcessor`.
4.  Call the `readCardholderFile()` method, wrapped in a `try-catch` block to handle `FileNotFoundException`.
5.  Call the various report generation methods and print their output to the console.

## Hints and Tips

- **Use Abstract Classes**: The use of an abstract `Cardholder` class is a great example of polymorphism. It allows you to treat all types of cardholders uniformly in the `CardholderProcessor` while still allowing each subclass to have its own unique behavior.
- **Comparator Interface**: The `Comparator` interface is a powerful tool for defining custom sorting logic. It keeps the sorting logic separate from the object's main class.
- **Exception Handling**: Custom exceptions make your code cleaner and your error handling more specific.
- **File I/O**: Pay close attention to `FileNotFoundException` when dealing with file input. Always provide helpful error messages to the user.
- **Command-Line Arguments**: Remember that `args[0]` holds the first argument. Always check `args.length` to avoid an `ArrayIndexOutOfBoundsException`.
