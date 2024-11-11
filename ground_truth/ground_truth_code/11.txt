from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
sign_up = gen.activity('Customer signs up')
generate_account = gen.activity('Generate account')
assign_access = gen.activity('Assign access')
trigger_billing = gen.activity('Set automatic triggers for billing cycles')

# Initial dependencies
initial_dependencies = [
    (sign_up, generate_account),
    (generate_account, assign_access),
    (generate_account, trigger_billing)
]

initial_poset = gen.partial_order(dependencies=initial_dependencies)

# Define subscription activities
receive_updates = gen.activity('Send regular updates')
receive_enhancements = gen.activity('Send product enhancements')
receive_notifications = gen.activity('Send renewal notifications')

subscription_activities = gen.partial_order(dependencies=[
    (receive_updates,),
    (receive_enhancements,),
    (receive_notifications,)
])

# Subscription loop: activities can be repeated indefinitely
subscription_loop = gen.loop(do=subscription_activities, redo=None)

# Cancellation process
submit_cancellation = gen.activity('Customer submits cancellation request')
apply_refunds_charges = gen.xor('apply refund', 'apply charges', None)
deactivate_subscription = gen.activity('Deactivate subscription')
settle_balance = gen.activity('Settle final account balance')

cancellation_dependencies = [
    (submit_cancellation, deactivate_subscription),
    (submit_cancellation, apply_refunds_charges),
    (apply_refunds_charges, settle_balance)
]

cancellation_process = gen.xor(None, gen.partial_order(dependencies=cancellation_dependencies))

# Combine the overall process
final_model = gen.partial_order(dependencies=[
    (initial_poset, subscription_loop),
    (subscription_loop, cancellation_process)
])
