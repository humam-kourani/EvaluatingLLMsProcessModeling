from utils.model_generation import ModelGenerator
gen = ModelGenerator()

# Define activities
guest_calls = gen.activity('Guest calls room service')
manager_takes_order = gen.activity('Manager takes down the order')
manager_submits_kitchen = gen.activity('Manager submits order ticket to kitchen')
manager_gives_sommelier = gen.activity('Manager gives order to sommelier')
manager_assigns_waiter = gen.activity('Manager assigns order to waiter')
waiter_readies_cart = gen.activity('Waiter readies cart')
waiter_prepares_drinks = gen.activity('Waiter prepares nonalcoholic drinks')
kitchen_prepares_food = gen.activity('Kitchen prepares food')
sommelier_fetches_wine = gen.activity('Sommelier fetches wine and prepare alcoholic beverages')
waiter_delivers = gen.activity("Waiter delivers order to guest's room")
waiter_returns = gen.activity('Waiter returns to room-service station')
waiter_debits_account = gen.activity("Waiter debits guest's account")
wait = gen.xor(None, "Waiter waits/delays debiting")

# Optional activities
# Manager gives order to sommelier (optional, 80% of orders)
# Waiter debits account (may be delayed if another order is pending)

alcohol_path = gen.xor(gen.partial_order(dependencies = [
    (manager_gives_sommelier, sommelier_fetches_wine),
]), None)

# Define dependencies
dependencies = [
    (guest_calls, manager_takes_order),
    (manager_takes_order, manager_submits_kitchen),
    (manager_takes_order, alcohol_path),
    (manager_takes_order, manager_assigns_waiter),
    (manager_submits_kitchen, kitchen_prepares_food),
    (manager_assigns_waiter, waiter_readies_cart),
    (manager_assigns_waiter, waiter_prepares_drinks),
    (waiter_readies_cart, waiter_delivers),
    (waiter_prepares_drinks, waiter_delivers),
    (kitchen_prepares_food, waiter_delivers),
    (alcohol_path, waiter_delivers),
    (waiter_delivers, waiter_returns),
    (waiter_returns, wait),
    (wait, waiter_debits_account)
]

# Create the final model
final_model = gen.partial_order(dependencies=dependencies)