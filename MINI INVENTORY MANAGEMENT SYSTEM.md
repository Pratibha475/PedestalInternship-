import csv
import os
from datetime import datetime

class Product:
    def __init__(self, product_id, name, quantity, price, category):
        self.product_id = product_id
        self.name = name
        self.quantity = int(quantity)
        self.price = float(price)
        self.category = category

    def to_list(self):
        return [self.product_id, self.name, self.quantity, self.price, self.category]

class Inventory:
    def __init__(self, filename='inventory.csv'):
        self.filename = filename
        self.products = []
        self.load_data()

    def load_data(self):
        if os.path.exists(self.filename):
            with open(self.filename, 'r', newline='') as file:
                reader = csv.reader(file)
                self.products = [Product(*row) for row in reader]

    def save_data(self):
        with open(self.filename, 'w', newline='') as file:
            writer = csv.writer(file)
            for product in self.products:
                writer.writerow(product.to_list())

    def add_product(self, product):
        self.products.append(product)
        self.save_data()

    def view_products(self):
        for p in self.products:
            print(f"ID: {p.product_id}, Name: {p.name}, Qty: {p.quantity}, Price: {p.price}, Category: {p.category}")

    def search_product(self, keyword):
        found = [p for p in self.products if p.product_id == keyword or p.name.lower() == keyword.lower()]
        return found

    def update_product(self, product_id, **kwargs):
        for p in self.products:
            if p.product_id == product_id:
                p.name = kwargs.get('name', p.name)
                p.quantity = int(kwargs.get('quantity', p.quantity))
                p.price = float(kwargs.get('price', p.price))
                p.category = kwargs.get('category', p.category)
                self.save_data()
                return True
        return False

    def delete_product(self, product_id):
        self.products = [p for p in self.products if p.product_id != product_id]
        self.save_data()

    def generate_report(self):
        total_value = sum(p.quantity * p.price for p in self.products)
        low_stock = [p for p in self.products if p.quantity < 10]
        print(f"\nTotal Inventory Value: ₹{total_value:.2f}")
        print("Low Stock Items (Qty < 10):")
        for p in low_stock:
            print(f"- {p.name} (Qty: {p.quantity})")

def menu():
    inv = Inventory()

    while True:
        print("\nInventory Management System")
        print("1. Add Product")
        print("2. View All Products")
        print("3. Search Product")
        print("4. Update Product")
        print("5. Delete Product")
        print("6. Generate Report")
        print("7. Exit")
        choice = input("Enter choice: ")

        try:
            if choice == '1':
                pid = input("Product ID: ")
                name = input("Name: ")
                qty = int(input("Quantity: "))
                price = float(input("Price: "))
                cat = input("Category: ")
                inv.add_product(Product(pid, name, qty, price, cat))

            elif choice == '2':
                inv.view_products()

            elif choice == '3':
                keyword = input("Enter ID or Name to search: ")
                results = inv.search_product(keyword)
                for p in results:
                    print(f"Found: {p.product_id} - {p.name}")

            elif choice == '4':
                pid = input("Enter Product ID to update: ")
                name = input("New Name (leave blank to keep same): ")
                qty = input("New Quantity: ")
                price = input("New Price: ")
                cat = input("New Category: ")
                updated = inv.update_product(pid, name=name or None, quantity=qty or None, price=price or None, category=cat or None)
                print("Updated" if updated else "Product not found")

            elif choice == '5':
                pid = input("Enter Product ID to delete: ")
                inv.delete_product(pid)
                print("Deleted")

            elif choice == '6':
                inv.generate_report()

            elif choice == '7':
                break

            else:
                print("Invalid choice. Try again.")

        except Exception as e:
            print("Error:", e)

if __name__ == "__main__":
    menu()
