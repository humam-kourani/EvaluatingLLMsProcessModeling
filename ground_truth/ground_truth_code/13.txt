from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
file_complaint = gen.activity('File complaint')
log_complaint = gen.activity('Log complaint')
assign_department = gen.activity('Assign complaint to relevant department')
review_details = gen.activity('Review complaint details')
approve_notify_customer = gen.activity('Approve and notify customer')
reject_notify_customer = gen.activity('Reject and notify customer')
process_reimbursement = gen.activity('Process reimbursement')
complaint_resolved = gen.activity('Resolve complaint')
close_case = gen.xor('Provide feedback', None)

choice = gen.xor(gen.partial_order(dependencies=[(approve_notify_customer, process_reimbursement)]), reject_notify_customer)

# Build the final process model
final_model = gen.partial_order(dependencies=[
    (file_complaint, log_complaint),
    (log_complaint, assign_department),
    (assign_department, review_details),
    (review_details, choice),
    (choice, complaint_resolved),
    (complaint_resolved, close_case)
])
