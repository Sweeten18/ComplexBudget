# ComplexBudget
#budget program using python
import json
import os
from datetime import datetime
import matplotlib.pyplot as plt

DATA_FILE = "budget_data.json"

class BudgetManager:
    def __init__(self):
        self.data = {
            "income": [],
            "expenses": [],
            "budgets": {},
            "savings_goal": 0.0
        }
        self.load_data()

    def load_data(self):
        if os.path.exists(DATA_FILE):
            with open(DATA_FILE, 'r') as f:
                self.data = json.load(f)

    def save_data(self):
        with open(DATA_FILE, 'w') as f:
            json.dump(self.data, f, indent=4)

    def add_income(self, amount, source):
        entry = {
            "amount": amount,
            "source": source,
            "date": datetime.now().isoformat()
        }
        self.data['income'].append(entry)
        self.save_data()

    def add_expense(self, amount, category):
        entry = {
            "amount": amount,
            "category": category,
            "date": datetime.now().isoformat()
        }
        self.data['expenses'].append(entry)
        self.save_data()

    def set_budget(self, category, amount):
        self.data['budgets'][category] = amount
        self.save_data()

    def set_savings_goal(self, amount):
        self.data['savings_goal'] = amount
        self.save_data()

    def get_summary(self):
        income_total = sum(item['amount'] for item in self.data['income'])
        expense_total = sum(item['amount'] for item in self.data['expenses'])
        savings = income_total - expense_total
        return {
            "Total Income": income_total,
            "Total Expenses": expense_total,
            "Savings": savings,
            "Goal Met": savings >= self.data['savings_goal']
        }

    def category_expense_summary(self):
        summary = {}
        for item in self.data['expenses']:
            category = item['category']
            summary[category] = summary.get(category, 0) + item['amount']
        return summary

    def visualize_expenses(self):
        summary = self.category_expense_summary()
        categories = list(summary.keys())
        amounts = list(summary.values())

        plt.figure(figsize=(10, 6))
        plt.pie(amounts, labels=categories, autopct='%1.1f%%', startangle=140)
        plt.title('Expenses by Category')
        plt.axis('equal')
        plt.show()

    def print_summary(self):
        summary = self.get_summary()
        print("\n--- Monthly Summary ---")
        for key, value in summary.items():
            print(f"{key}: ${value:.2f}")

        print("\n--- Budget vs. Actual ---")
        actuals = self.category_expense_summary()
        for category, budget in self.data['budgets'].items():
            actual = actuals.get(category, 0)
            print(f"{category} -> Budgeted: ${budget:.2f}, Actual: ${actual:.2f}")


# Example usage:
if __name__ == "__main__":
    bm = BudgetManager()

    # Simulate some actions
    bm.add_income(3000, "Salary")
    bm.add_expense(500, "Rent")
    bm.add_expense(150, "Groceries")
    bm.add_expense(60, "Utilities")
    bm.set_budget("Rent", 600)
    bm.set_budget("Groceries", 200)
    bm.set_budget("Utilities", 100)
    bm.set_savings_goal(500)

    bm.print_summary()
    bm.visualize_expenses()
