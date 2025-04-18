/// File: app/UnlaceApp.java
package app;
// Importing classes from other packages for modular design and functionality
import core.InventoryManager;// Handles viewing, adding, deleting, and searching inventory
import core.SalesManager;// Handles recording sales and showing sales history
import data.DataManager;// Handles saving and loading data from file
import models.User;// Represents the current logged-in user
import models.ShoeItem;// Represents shoe inventory items
import models.SaleRecord;// Represents individual sales transactions
import util.InputHelper;// Provides validated user input methods
import util.ReportGenerator;// Handles generating inventory and sales reports

import java.time.format.DateTimeFormatter;// For formatting login session time
import java.util.ArrayList;// Dynamic list for storing inventory and sales
import java.util.Scanner;// For reading user input

public class UnlaceApp {
    // Formatter for displaying timestamps in menus and reports
    private static final DateTimeFormatter DATE_TIME_FORMAT = DateTimeFormatter.ofPattern("MM/dd/yyyy HH:mm");
    // Entry point of the application
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);// Create a Scanner for user input
        // Prompt user for name and email; create a User object
        User currentUser = InputHelper.authenticateUser(input);
        // Load inventory and sales data from file (or create new lists if not found)
        ArrayList<ShoeItem> inventory = DataManager.loadInventory();
        ArrayList<SaleRecord> sales = DataManager.loadSales();
        // Main loop: repeatedly show menu and process user commands
        while (true) {
            displayMainMenu(currentUser);// Display menu header and options
            String choice = input.nextLine().trim().toLowerCase();// Get user input
            // Handle menu choice using modern switch-case with lambda-style branches
            switch (choice) {
                case "1", "view" -> InventoryManager.showInventory(inventory);// View inventory
                case "2", "add" -> InventoryManager.addInventoryItem(inventory, input);// Add new shoe
                case "3", "delete" -> InventoryManager.deleteInventoryItem(inventory, input);// Delete shoe
                case "4", "sale" -> SalesManager.processSale(inventory, sales, input);// Record sale
                case "5", "history" -> SalesManager.showSalesHistory(sales);// Show sales history
                case "6", "report" -> ReportGenerator.generateReports(inventory, sales, input);// Generate reports
                case "7", "search" -> InventoryManager.searchInventory(inventory, input);// Search inventory
                case "8", "exit" -> {// Save and exit
                    DataManager.saveData(inventory, sales);// Save data to file
                    System.out.println("Goodbye, " + currentUser.username + "!");
                    input.close();// Close input stream
                    System.exit(0);// Exit program
                }
                case "help" -> showHelp();// Show help menu
                default -> System.out.println("Invalid option. Try again or type 'help'.");// Handle invalid input
            }
            System.out.println();// Add spacing after each loop iteration
        }
    }
    // Displays the main menu header and list of options
    private static void displayMainMenu(User user) {
        System.out.println("=== Welcome to Unlace, " + user.username + " ===");
        System.out.println("Session started: " + user.loginTime.format(DATE_TIME_FORMAT));
        System.out.println("\nMain Menu:");
        System.out.println("1. View Inventory (view)");
        System.out.println("2. Add New Shoe (add)");
        System.out.println("3. Delete Shoe (delete)");
        System.out.println("4. Record a Sale (sale)");
        System.out.println("5. View Sales History (history)");
        System.out.println("6. Generate Reports (report)");
        System.out.println("7. Search Inventory (search)");
        System.out.println("8. Exit (exit)");
        System.out.println("Type 'help' for command descriptions");
        System.out.print("Choose an option: ");
    }
    // Displays explanation of each command
    private static void showHelp() {
        System.out.println("\n=== Unlace Help ===");
        System.out.println("1/view   - Show current inventory");
        System.out.println("2/add    - Add new shoes to inventory");
        System.out.println("3/delete - Remove shoes from inventory");
        System.out.println("4/sale   - Record a shoe sale");
        System.out.println("6/report - Generate inventory and sales reports");
        System.out.println("7/search - Search inventory by various criteria");
        System.out.println("8/exit   - Save data and exit the system");
        System.out.println("help     - Show this help message");
    }
}
