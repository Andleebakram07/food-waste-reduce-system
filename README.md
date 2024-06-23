# food-waste-reduce-system
package sectiona.foodwastereductionsystem;

import java.time.LocalDate;
import java.time.format.DateTimeParseException;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

// Define the FoodWasteReductionSystem class
public class FoodWasteReductionSystem {
    // Main method
    public static void main(String[] args) {
        System.out.println("Welcome to the Food Waste Reduction System");

        // Example usage
        FoodItemService foodItemService = new FoodItemService();
        DonationService donationService = new DonationService();
        UserService userService = new UserService();

        // Create users
        User alice = new User(1, "Alice", "alice@example.com");
        User bob = new User(2, "Bob", "bob@example.com");
        userService.addUser(alice);
        userService.addUser(bob);

        // Display all users
        System.out.println("All Users:");
        List<User> users = userService.getAllUsers();
        for (User user : users) {
            System.out.println(user);
        }

        // Create food items
        FoodItem apple = new FoodItem(1, "Apple", "2024-06-01", 10);
        FoodItem banana = new FoodItem(2, "Banana", "2024-05-20", 5);
        foodItemService.addFoodItem(apple);
        foodItemService.addFoodItem(banana);

        // Display all food items
        System.out.println("All Food Items:");
        List<FoodItem> items = foodItemService.getAllFoodItems();
        for (FoodItem item : items) {
            System.out.println(item);
        }

        // Update food item
        foodItemService.updateFoodItem(1, "Green Apple", "2024-06-15", 15);

        // Display updated food items
        System.out.println("\nUpdated Food Items:");
        items = foodItemService.getAllFoodItems();
        for (FoodItem item : items) {
            System.out.println(item);
        }

        // Check for expired items
        System.out.println("\nExpired Food Items:");
        List<FoodItem> expiredItems = foodItemService.getExpiredFoodItems();
        for (FoodItem item : expiredItems) {
            System.out.println(item);
        }

        // Donate food items nearing expiration
        System.out.println("\nFood Items Needing Donation:");
        List<FoodItem> itemsForDonation = donationService.getItemsForDonation(foodItemService, 10);
        for (FoodItem item : itemsForDonation) {
            System.out.println(item);
        }

        // Donate a food item
        donationService.donateFoodItem(2, foodItemService);

        // Display remaining food items after donation
        System.out.println("\nRemaining Food Items after donation:");
        items = foodItemService.getAllFoodItems();
        for (FoodItem item : items) {
            System.out.println(item);
        }
    }
}

// Define the FoodItem class
class FoodItem {
    private int id;
    private String name;
    private LocalDate expiryDate;
    private int quantity;

    public FoodItem(int id, String name, String expiryDate, int quantity) {
        this.id = id;
        this.name = name;
        setExpiryDate(expiryDate);
        this.quantity = quantity;
    }

    // Getters and Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public LocalDate getExpiryDate() {
        return expiryDate;
    }

    public void setExpiryDate(String expiryDate) {
        try {
            this.expiryDate = LocalDate.parse(expiryDate);
        } catch (DateTimeParseException e) {
            throw new IllegalArgumentException("Invalid date format. Please use YYYY-MM-DD.");
        }
    }

    public int getQuantity() {
        return quantity;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }

    @Override
    public String toString() {
        return "FoodItem{id=" + id + ", name='" + name + '\'' + ", expiryDate=" + expiryDate + ", quantity=" + quantity + '}';
    }
}

// Define the FoodItemService class
class FoodItemService {
    private List<FoodItem> foodItems = new ArrayList<>();

    // Method to add a food item
    public void addFoodItem(FoodItem item) {
        foodItems.add(item);
    }

    // Method to get all food items
    public List<FoodItem> getAllFoodItems() {
        return foodItems;
    }

    // Method to update a food item
    public void updateFoodItem(int id, String name, String expiryDate, int quantity) {
        for (FoodItem item : foodItems) {
            if (item.getId() == id) {
                item.setName(name);
                item.setExpiryDate(expiryDate);
                item.setQuantity(quantity);
                return;
            }
        }
        throw new IllegalArgumentException("Food item with id " + id + " not found.");
    }

    // Method to delete a food item
    public void deleteFoodItem(int id) {
        foodItems.removeIf(item -> item.getId() == id);
    }

    // Method to get expired food items
    public List<FoodItem> getExpiredFoodItems() {
        LocalDate today = LocalDate.now();
        return foodItems.stream()
                .filter(item -> item.getExpiryDate().isBefore(today))
                .collect(Collectors.toList());
    }
}

// Define the DonationService class
class DonationService {
    // Method to get items for donation (nearing expiration)
    public List<FoodItem> getItemsForDonation(FoodItemService foodItemService, int daysToExpire) {
        LocalDate today = LocalDate.now();
        return foodItemService.getAllFoodItems().stream()
                .filter(item -> item.getExpiryDate().isBefore(today.plusDays(daysToExpire)))
                .collect(Collectors.toList());
    }

    // Method to donate a food item
    public void donateFoodItem(int id, FoodItemService foodItemService) {
        FoodItem item = foodItemService.getAllFoodItems().stream()
                .filter(foodItem -> foodItem.getId() == id)
                .findFirst()
                .orElse(null);
        if (item != null) {
            System.out.println("Donating Food Item: " + item);
            foodItemService.deleteFoodItem(id);
        } else {
            System.out.println("Food item with id " + id + " not found for donation.");
        }
    }
}

// Define the User class
class User {
    private int id;
    private String name;
    private String email;

    public User(int id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
    }

    // Getters and Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + '\'' + ", email='" + email + '\'' + '}';
    }
}

// Define the UserService class
class UserService {
    private List<User> users = new ArrayList<>();

    // Method to add a user
    public void addUser(User user) {
        users.add(user);
    }

    // Method to get all users
    public List<User> getAllUsers() {
        return users;
    }

    // Method to update a user
    public void updateUser(int id, String name, String email) {
        for (User user : users) {
            if (user.getId() == id) {
                user.setName(name);
                user.setEmail(email);
                return;
            }
        }
        throw new IllegalArgumentException("User with id " + id + " not found.");
    }

    // Method to delete a user
    public void deleteUser(int id) {
        users.removeIf(user -> user.getId() == id);
    }
}
