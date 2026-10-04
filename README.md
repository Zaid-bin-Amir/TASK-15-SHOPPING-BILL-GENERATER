TAX_RATE = 0.17
DISCOUNT_TIERS = [(10000, 0.15), (5000, 0.10), (2000, 0.05)]
LINE_WIDTH = 62


def format_money(amount):
    return f"Rs. {amount:,.2f}"


def read_name(prompt):
    while True:
        value = input(prompt).strip()
        if value:
            return value
        print("Name cannot be empty. Please try again.")


def read_quantity(prompt):
    while True:
        try:
            value = int(input(prompt))
            if value > 0:
                return value
            print("Quantity must be greater than zero.")
        except ValueError:
            print("Invalid quantity. Please enter a whole number.")


def read_price(prompt):
    while True:
        try:
            value = float(input(prompt))
            if value > 0:
                return value
            print("Price must be greater than zero.")
        except ValueError:
            print("Invalid price. Please enter a number.")


def read_yes_no(prompt):
    while True:
        answer = input(prompt).strip().lower()
        if answer in ("y", "yes"):
            return True
        if answer in ("n", "no"):
            return False
        print("Please enter y or n.")


def create_product(name, quantity, price):
    return {"name": name, "quantity": quantity, "price": price}


def line_total(product):
    return product["quantity"] * product["price"]


def calculate_subtotal(products):
    return sum(line_total(product) for product in products)


def get_discount_rate(subtotal):
    for threshold, rate in DISCOUNT_TIERS:
        if subtotal >= threshold:
            return rate
    return 0.0


def calculate_discount(subtotal):
    return subtotal * get_discount_rate(subtotal)


def calculate_tax(taxable_amount):
    return taxable_amount * TAX_RATE


def calculate_final_amount(subtotal, discount, tax):
    return subtotal - discount + tax


def collect_products():
    products = []
    while True:
        print(f"\nProduct #{len(products) + 1}")
        name = read_name("  Name     : ")
        quantity = read_quantity("  Quantity : ")
        price = read_price("  Price    : ")
        products.append(create_product(name, quantity, price))
        if not read_yes_no("\nAdd another product? (y/n): "):
            return products


def print_bill(products):
    subtotal = calculate_subtotal(products)
    discount_rate = get_discount_rate(subtotal)
    discount = calculate_discount(subtotal)
    taxable_amount = subtotal - discount
    tax = calculate_tax(taxable_amount)
    final_amount = calculate_final_amount(subtotal, discount, tax)

    print("\n" + "=" * LINE_WIDTH)
    print("SHOPPING BILL".center(LINE_WIDTH))
    print("=" * LINE_WIDTH)
    print(f"{'#':<4}{'Item':<20}{'Qty':>5}{'Price':>15}{'Total':>18}")
    print("-" * LINE_WIDTH)

    for index, product in enumerate(products, start=1):
        print(
            f"{index:<4}"
            f"{product['name'][:19]:<20}"
            f"{product['quantity']:>5}"
            f"{product['price']:>15,.2f}"
            f"{line_total(product):>18,.2f}"
        )

    print("-" * LINE_WIDTH)
    print(f"{'Subtotal':<30}{format_money(subtotal):>32}")
    print(f"{f'Discount ({discount_rate:.0%})':<30}{'- ' + format_money(discount):>32}")
    print(f"{f'Tax ({TAX_RATE:.0%})':<30}{'+ ' + format_money(tax):>32}")
    print("=" * LINE_WIDTH)
    print(f"{'FINAL AMOUNT':<30}{format_money(final_amount):>32}")
    print("=" * LINE_WIDTH)
    print("Thank you for shopping with us!".center(LINE_WIDTH))


def main():
    print("Welcome to the Shopping Bill Generator")
    products = collect_products()
    print_bill(products)


if __name__ == "__main__":
    main()
