from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
search_ticket = gen.activity('Customer searches for ticket')
select_route = gen.activity('Select route')
select_date = gen.activity('Select date and time')
provide_personal_info = gen.activity('Provide personal information')
provide_payment_details = gen.activity('Provide payment details')
generate_ticket = gen.activity('Generate ticket')
send_ticket_email = gen.activity('Send ticket via email')
send_ticket_sms = gen.activity('Send ticket via SMS')
send_ticket_choice = gen.xor(send_ticket_email, send_ticket_sms)
update_seat_inventory = gen.activity('Update seat inventory')
send_reminder = gen.xor(gen.loop(gen.activity('Send reminder'), None), None)
send_instructions = gen.xor(gen.loop(gen.activity('Send instructions'), None), None)
complete_journey = gen.activity('Customer completes journey')
post_travel_feedback = gen.activity('Post-travel feedback or services')
post_travel_feedback_optional = gen.xor(post_travel_feedback, None)


po1 = gen.partial_order(dependencies=[
    (select_route,),
    (select_date,)
])

po2 = gen.partial_order(dependencies=[
    (provide_personal_info,),
    (provide_payment_details,)
])

po3 = gen.partial_order(dependencies=[
    (send_ticket_choice,),
    (update_seat_inventory,)
])

po4 = gen.partial_order(dependencies=[
    (send_reminder,),
    (send_instructions,)
])

# Define dependencies
dependencies = [
    (search_ticket, po1),
    (po1, po2),
    (po2, generate_ticket),
    (generate_ticket, po3),
    (po3, po4),
    (po4, complete_journey),
    (complete_journey, post_travel_feedback_optional)
]

final_model = gen.partial_order(dependencies=dependencies)
