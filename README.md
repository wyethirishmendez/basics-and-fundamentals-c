#include <iostream>o
#include <vector>
#include <string>
#include <iomanip>
#include <algorithm>

using namespace std;

class Product {
public:
    string productId;
    string name;
    double price;
    int quantity;

    Product(string id, string n, double p, int q) : productId(id), name(n), price(p), quantity(q) {}
};

class ShoppingCart {
public:
    int cartId;
    vector<Product> items;
    double totalAmount;  // Store total amount inside ShoppingCart

    ShoppingCart(int id) : cartId(id), totalAmount(0) {}

    void addProduct(Product p) {
        items.push_back(p);
        totalAmount += p.price * p.quantity;  // Update total amount when adding product
    }

    double calculateTotal() {
        return totalAmount;
    }
};

class Order {
public:
    int orderId;
    ShoppingCart cart;
    string status;

    Order(int id, ShoppingCart c) : orderId(id), cart(c), status("Placed") {}

    void displayOrderDetails() {
        cout << "\nOrder ID: " << orderId << endl;
        cout << "Total Amount: " << fixed << setprecision(2) << cart.calculateTotal() << endl;
        cout << "Order Details:" << endl;
        cout << left << setw(12) << "Product ID" << setw(10) << "Name" << setw(10) << "Price" << setw(10) << "Quantity" << endl;
        for (Product p : cart.items) {
            cout << left << setw(12) << p.productId << setw(10) << p.name << setw(10) << fixed << setprecision(2) << p.price << setw(10) << p.quantity << endl;
        }
        cout << endl;
    }
};

int main() {
    vector<Product> availableProducts = {
        {"ABC", "Paper", 20.00, 50},
        {"CDE", "Pencil", 10.00, 100},
        {"QWE", "Notebook", 50.00, 30},
        {"TRE", "Eraser", 5.00, 80}
    };
    
    vector<Order> orders;
    int orderIdCounter = 1;
    char choice;

    do {
        ShoppingCart cart(orderIdCounter);
        cout << "\nAvailable Products:\n";
        cout << left << setw(12) << "Product ID" << setw(10) << "Name" << setw(10) << "Price" << setw(15) << "Available Qty" << endl;
        for (const auto &product : availableProducts) {
            cout << left << setw(12) << product.productId << setw(10) << product.name << setw(10) << fixed << setprecision(2) << product.price << setw(15) << product.quantity << endl;
        }

        cout << "\nAdding products to cart:\n";
        while (true) {
            string productId;
            int quantity;
            bool found = false;

            cout << "Enter Product ID to order: ";
            cin >> productId;
            transform(productId.begin(), productId.end(), productId.begin(), ::toupper); // Convert input to uppercase

            cout << "Enter Quantity: ";
            cin >> quantity;

            for (auto &product : availableProducts) {
                if (product.productId == productId) {
                    if (quantity > product.quantity) {
                        cout << "Sorry, only " << product.quantity << " available in stock.\n";
                        break;
                    }

                    double itemTotal = product.price * quantity;
                    cart.addProduct(Product(product.productId, product.name, product.price, quantity));

                    product.quantity -= quantity;  // Reduce available stock
                    cout << "Added: " << product.name << " - Price: " << fixed << setprecision(2) << product.price
                         << " - Quantity: " << quantity << " - Item Total: " << itemTotal << endl;
                    cout << "Current Total Amount: " << cart.calculateTotal() << endl;

                    found = true;
                    break;
                }
            }

            if (!found) {
                cout << "Invalid Product ID. Please try again." << endl;
            }

            cout << "Add another product? (y/n): ";
            cin >> choice;
            while (choice != 'y' && choice != 'Y' && choice != 'n' && choice != 'N') {
                cout << "Invalid choice! Please enter 'y' or 'n': ";
                cin >> choice;
            }

            if (choice == 'n' || choice == 'N') {
                break;
            }
        }

        orders.push_back(Order(orderIdCounter, cart));
        cout << "\nOrder Summary:\n";
        orders.back().displayOrderDetails();

        orderIdCounter++;

        cout << "\nCreate another order? (y/n): ";
        cin >> choice;
        while (choice != 'y' && choice != 'Y' && choice != 'n' && choice != 'N') {
            cout << "Invalid choice! Please enter 'y' or 'n': ";
            cin >> choice;
        }

    } while (choice == 'y' || choice == 'Y');

    cout << "\nAll Orders:\n";
    for (Order &order : orders) {
        order.displayOrderDetails();
    }
    
    return 0;
}
