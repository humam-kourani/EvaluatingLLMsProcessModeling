from utils.model_generation import ModelGenerator
gen = ModelGenerator()

# Define activities
place_order = gen.activity('Place order')
record_order = gen.activity('Record order details')
process_payment = gen.activity('Process payment')
notify_payment_failure = gen.activity('Notify customer of payment failure')
check_inventory = gen.activity('Check stock availability')
initiate_back_order = gen.activity('Initiate back-order')
inform_customer_back_order = gen.activity('Inform customer about delay')
receive_back_order = gen.activity('Receive back-order')

out_of_stock = gen.xor(None, gen.partial_order(dependencies=[
    (initiate_back_order, inform_customer_back_order),
    (inform_customer_back_order, receive_back_order)
]))

pick_items = gen.activity('Pick items from warehouse')
quality_check = gen.activity('Perform quality control checks')
pack_items = gen.activity('Package items')
gift_wrap = gen.activity('Gift wrap items')
prepare_shipping_docs = gen.activity('Prepare shipping documents and labels')
prepare_customs_docs = gen.activity('Prepare customs documentation')
dispatch_order = gen.activity('Dispatch order')
send_shipping_confirmation = gen.activity('Send shipping confirmation to customer')
update_inventory = gen.activity('Update inventory levels')
schedule_follow_up = gen.activity('Schedule follow-up for feedback')
customer_reports_issue = gen.activity('Customer reports issue')
send_return_label = gen.activity('Send return shipping label')
receive_returned_items = gen.activity('Receive returned items')
inspect_returned_items = gen.activity('Inspect returned items')
process_refund_replacement = gen.xor('Process refund', 'Process replacement')

# Payment decision
payment_success = gen.activity('Payment successful')
payment_failed = gen.activity('Notify about failed payment')

# Gift wrap option
gift_wrap_decision = gen.xor(gift_wrap, None)

# Returns process loop
returns_process = gen.xor(None, gen.partial_order(dependencies=[
    (customer_reports_issue, send_return_label),
    (send_return_label, receive_returned_items),
    (receive_returned_items, inspect_returned_items),
    (inspect_returned_items, process_refund_replacement)
]))



# Define dependencies
dependencies = [
    (check_inventory, out_of_stock),
    (out_of_stock, pick_items),
    (pick_items, quality_check),
    (quality_check, prepare_shipping_docs),
    (quality_check, pack_items),
    (pack_items, gift_wrap_decision),
    (gift_wrap_decision, dispatch_order),
    (prepare_shipping_docs, dispatch_order),
    (dispatch_order, send_shipping_confirmation),
    (dispatch_order, update_inventory),
    (send_shipping_confirmation, 'Confirm delivery'),
    (update_inventory, 'Confirm delivery'),
    ('Confirm delivery', 'Send follow-up email'),
    ('Send follow-up email', returns_process),
    (update_inventory, returns_process),
]

# International shipping condition
international_shipping = gen.activity('Prepare documentation for international orders')
international_shipping_decision = gen.xor(international_shipping, None)


# Update dependencies for shipping preparations
dependencies.extend([
    (quality_check, international_shipping_decision),
    (international_shipping_decision, dispatch_order)
])

model_payment_success = gen.partial_order(dependencies = dependencies)

choice_pay = gen.xor(payment_failed, model_payment_success)

# Create the final model
final_model = gen.partial_order(dependencies=[
    (place_order, record_order),
    (record_order, 'Process payment'),
    ('Process payment', choice_pay),
])
