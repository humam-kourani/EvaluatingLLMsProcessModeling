from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
place_order_online = gen.activity('Customer places order online')
place_order_phone = gen.activity('Customer places order over the phone')
generate_order_confirmation = gen.activity('Generate and send order confirmation')
pick_and_pack = gen.activity('Pick and pack items')
generate_shipping_label = gen.activity('Generate shipping label')
handover_to_logistics = gen.activity('Hand over order to logistics provider')
send_tracking_info = gen.activity('Send tracking information to customer')
monitor_shipment = gen.activity('Monitor shipment')
successful_delivery = gen.activity('Successful delivery')
process_feedback_or_returns = gen.activity('Process customer feedback or returns')

# Choice between placing order online or over the phone
choice_ordering = gen.xor(place_order_online, place_order_phone)

# Loop for monitoring shipment
monitor_loop = gen.loop(do=monitor_shipment, redo=None)

# Optional processing of customer feedback or returns
feedback_or_returns_optional = gen.xor(process_feedback_or_returns, None)

# Final partial order with dependencies
final_model = gen.partial_order(dependencies=[
    (choice_ordering, generate_order_confirmation),
    (generate_order_confirmation, pick_and_pack),
    (generate_order_confirmation, generate_shipping_label),
    (pick_and_pack, handover_to_logistics),
    (generate_shipping_label, handover_to_logistics),
    (handover_to_logistics, send_tracking_info),
    (send_tracking_info, monitor_loop),
    (monitor_loop, successful_delivery),
    (successful_delivery, feedback_or_returns_optional)
])
