# sriram27
mini project for java - Enpense tracker

--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
PROGRAM :

import java.io.*;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Collections;
import java.util.Date;
import java.util.Scanner;

public class ExpenseTracker {
    private static final String FILENAME = "expenses.txt";
    private static ArrayList<Expense> expenses = new ArrayList<>();

    public static void main(String[] args) {
        loadExpensesFromFile();

        Scanner scanner = new Scanner(System.in);
        int choice;

        do {
            System.out.println("Expense Tracker Menu:");
            System.out.println("1. Record Expense");
            System.out.println("2. View Expenses");
            System.out.println("3. Expense Summary");
            System.out.println("4. Category-wise Analysis");
            System.out.println("5. Monthly Report");
            System.out.println("0. Exit");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    recordExpense(scanner);
                    break;
                case 2:
                    viewExpenses();
                    break;
                case 3:
                    displayExpenseSummary();
                    break;
                case 4:
                    categoryWiseAnalysis();
                    break;
                case 5:
                    monthlyReport(scanner);
                    break;
                case 0:
                    saveExpensesToFile();
                    System.out.println("Exiting Expense Tracker. Goodbye!");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 0);

        scanner.close();
    }

    private static void recordExpense(Scanner scanner) {
        System.out.print("Enter the amount: ");
        double amount = scanner.nextDouble();
        scanner.nextLine();  // Consume newline

        System.out.print("Enter the category: ");
        String category = scanner.nextLine();

        System.out.print("Enter the date (yyyy-MM-dd): ");
        String dateString = scanner.nextLine();
        Date date;
        try {
            date = new SimpleDateFormat("yyyy-MM-dd").parse(dateString);
        } catch (ParseException e) {
            System.out.println("Invalid date format. Expense not recorded.");
            return;
        }

        Expense expense = new Expense(amount, category, date);
        expenses.add(expense);
        System.out.println("Expense recorded successfully!");
    }

    private static void viewExpenses() {
        System.out.println("\nList of Expenses:");
        for (Expense expense : expenses) {
            System.out.println(expense);
        }
    }

    private static void displayExpenseSummary() {
        double totalExpenses = 0;
        for (Expense expense : expenses) {
            totalExpenses += expense.getAmount();
        }
        System.out.println("\nExpense Summary:");
        System.out.println("Total Expenses: " + totalExpenses);
        System.out.println("Average Expense: " + (totalExpenses / expenses.size()));
    }

    private static void categoryWiseAnalysis() {
        // Implement logic to analyze expenses by category
    }

    private static void monthlyReport(Scanner scanner) {
        System.out.print("Enter the month (MM-yyyy): ");
        String monthYear = scanner.nextLine();

        SimpleDateFormat dateFormat = new SimpleDateFormat("MM-yyyy");
        try {
            Date selectedMonth = dateFormat.parse(monthYear);

            System.out.println("\nMonthly Report for " + monthYear + ":");
            for (Expense expense : expenses) {
                if (isSameMonth(expense.getDate(), selectedMonth)) {
                    System.out.println(expense);
                }
            }
        } catch (ParseException e) {
            System.out.println("Invalid date format. Monthly report not generated.");
        }
    }

    private static boolean isSameMonth(Date date1, Date date2) {
        SimpleDateFormat monthYearFormat = new SimpleDateFormat("MM-yyyy");
        return monthYearFormat.format(date1).equals(monthYearFormat.format(date2));
    }

    private static void loadExpensesFromFile() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(FILENAME))) {
            expenses = (ArrayList<Expense>) ois.readObject();
        } catch (FileNotFoundException e) {
            System.out.println("No previous data found. Starting fresh.");
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    private static void saveExpensesToFile() {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(FILENAME))) {
            oos.writeObject(expenses);
            System.out.println("Expenses saved to file successfully.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

class Expense implements Serializable {
    private double amount;
    private String category;
    private Date date;

    public Expense(double amount, String category, Date date) {
        this.amount = amount;
        this.category = category;
        this.date = date;
    }

    public double getAmount() {
        return amount;
    }

    public String getCategory() {
        return category;
    }

    public Date getDate() {
        return date;
    }

    @Override
    public String toString() {
        return "Expense{" +
                "amount=" + amount +
                ", category='" + category + '\'' +
                ", date=" + date +
                '}';
    }
}
