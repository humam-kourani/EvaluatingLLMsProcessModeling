from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
report_incident = gen.activity('Report incident')
log_report = gen.activity('Log report into tracking system')
assign_to_team = gen.activity('Assign report to appropriate team')
gather_info = gen.activity('Gather necessary information')
identify_cause = gen.activity('Identify cause of incident')
propose_actions = gen.activity('Propose corrective actions')
implement_fix = gen.activity('Implement fix')
conduct_training = gen.activity('Conduct training')
change_policy = gen.activity('Change policy')
follow_up = gen.activity('Conduct follow-up')
close_report = gen.activity('Close incident report')
notify_stakeholders = gen.activity('Notify all stakeholders')

# Define the implement solution choice
implement_solution = gen.xor(implement_fix, conduct_training, change_policy)

# Define dependencies in a single partial order
dependencies = [
    (report_incident, log_report),
    (log_report, assign_to_team),
    (assign_to_team, gather_info),
    (assign_to_team, identify_cause),
    (gather_info, propose_actions),
    (identify_cause, propose_actions),
    (propose_actions, implement_solution),
    (implement_solution, follow_up),
    (follow_up, close_report),
    (close_report, notify_stakeholders)
]

final_model = gen.partial_order(dependencies=dependencies)
