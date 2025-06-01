import json
import os

DATA_FILE = "budget_data.json"

def load_data():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, 'r') as f:
            return json.load(f)
    return {}

def save_data(data):
    with open(DATA_FILE, 'w') as f:
        json.dump(data, f, indent=4)

def get_float_input(prompt):
    while True:
        try:
            return float(input(prompt))
        except ValueError:
            print("Invalid input. Please enter a number.")

def get_category_input(title):
    print(f"\nEnter {title} (type 'done' when finished):")
    entries = {}
    while True:
        category = input("Category: ")
        if category.lower() == 'done':
            break
        amount = get_float_input(f"Amount for {category}: ")
        entries[category] = amount
    return entries

def calculate_summary(data):
    income = data.get("income", 0)
    expenses = data.get("expenses", {})
    budgets = data.get("budgets", {})
    goal = data.get("savings_goal", 0)

    total_expenses = sum(expenses.values())
    savings = income - total_expenses
    goal_met = savings >= goal

    return {
        "income": income,
        "total_expenses": total_expenses,
        "savings": savings,
        "goal": goal,
        "goal_met": goal_met,
        "expenses": expenses,
        "budgets": budgets
    }

def display_summary(summary):
    print("\n--- Budget Summary ---")
    print(f"Income: ${summary['income']:.2f}")
    print(f"Total Expenses: ${summary['total_expenses']:.2f}")
    print(f"Savings: ${summary['savings']:.2f}")
    print(f"Savings Goal: ${summary['goal']:.2f}")
    print("Goal Met: ✅" if summary['goal_met'] else "Goal Met: ❌")

    print("\n--- Budget vs. Actual Expenses ---")
    for category in set(summary["budgets"]) | set(summary["expenses"]):
        budgeted = summary["budgets"].get(category, 0)
        spent = summary["expenses"].get(category, 0)
        print(f"{category}: Budgeted ${budgeted:.2f} | Spent ${spent:.2f}")

def main():
    print("=== Monthly Budget Manager ===")
    data = {}

    data["income"] = get_float_input("Enter your monthly income: $")
    data["expenses"] = get_category_input("monthly expenses")
    data["budgets"] = get_category_input("budget per category")
    data["savings_goal"] = get_float_input("Enter your monthly savings goal: $")

    save_data(data)

    summary = calculate_summary(data)
    display_summary(summary)

if __name__ == "__main__":
    main()
    bm.set_budget("Utilities", 100)
    bm.set_savings_goal(500)

    bm.print_summary()
    bm.visualize_expenses()
