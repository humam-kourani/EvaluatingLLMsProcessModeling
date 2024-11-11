from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
check_inventory_level = gen.activity('Check current inventory level')
signal_reorder_automated = gen.activity('Send an automated alert')
signal_reorder_manual = gen.activity('Send a manual alert')
place_order = gen.activity('Place order with suppliers')
update_expected_delivery = gen.activity('Update inventory system with expected delivery dates')
receive_stock = gen.activity('Receive stock')
inspect_quality = gen.activity('Inspect stock for quality')
record_in_system = gen.activity('Record stock in system')
place_on_shelves = gen.activity('Place stock on shelves')
place_in_storage = gen.activity('Place stock in storage')
update_inventory_levels = gen.activity('Update inventory levels')

monitor_inventory = gen.loop(check_inventory_level, None)

store = gen.xor(place_on_shelves, place_in_storage)

# Define exclusive choice between automated alert and manual check
signal_reorder_choice = gen.xor(signal_reorder_automated, signal_reorder_manual)

# Define the final partial order with dependencies
final_model = gen.partial_order(dependencies=[
    (monitor_inventory, signal_reorder_choice),
    (signal_reorder_choice, place_order),
    (place_order, update_expected_delivery),
    (update_expected_delivery, receive_stock),
    (receive_stock, inspect_quality),
    (inspect_quality, store),
    (receive_stock, record_in_system),
    (store, update_inventory_levels),
    (record_in_system, update_inventory_levels)
])
