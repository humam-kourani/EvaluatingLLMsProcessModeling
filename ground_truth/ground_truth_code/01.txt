from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
receive_inquiry = gen.activity('Receive customer inquiry')
collect_info = gen.activity('Collect customer information')
address_concerns = gen.activity('Address customer concerns or questions')
guide_selection = gen.activity('Guide customer in selecting product/service')
provide_quote = gen.activity('Provide quote')
place_order = gen.activity('Place order')
record_order = gen.activity('Record order in system')
send_confirmation = gen.activity('Send order confirmation to customer')

# Define proceeding after quote approval
proceeding_after_quote = gen.partial_order(dependencies=[
    (place_order, record_order),
    (record_order, send_confirmation)
])

# Choice after quote approval (customer approves or not)
choice_after_quote = gen.xor(proceeding_after_quote, None)

# Continuation submodel after initial customer interest
continuation_submodel = gen.partial_order(dependencies=[
    (guide_selection, provide_quote),
    (provide_quote, choice_after_quote)
])

# Choice after initial interactions (customer interested or not)
choice_after_initial = gen.xor(continuation_submodel, None)

# Initial partial order with dependencies
initial_poset = gen.partial_order(dependencies=[
    (receive_inquiry, collect_info),
    (receive_inquiry, address_concerns),
    (collect_info, choice_after_initial),
    (address_concerns, choice_after_initial)
])

final_model = initial_poset
