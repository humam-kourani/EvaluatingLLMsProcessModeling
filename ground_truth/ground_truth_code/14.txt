from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
outline_objectives = gen.activity('Outline objectives')
draft_plan = gen.activity('Draft plan')
conduct_meeting = gen.activity('Conduct strategic alignment meeting')
review_plan = gen.activity('Review budget feasibility')
approve_budget = gen.activity('Approve final budget')
distribute_budget = gen.activity('Distribute budget')
implement_plan = gen.activity('Implement plan')

do_part = gen.partial_order(dependencies=[
    (review_plan, "Provide feedback")
])

redo_part = gen.partial_order(dependencies=[
    ('Adjust Plan', "Documented and approve adjustment")
])

# Optional negotiation and adjustment
optional_negotiate_adjust = gen.loop(do_part, redo_part)

# Define the partial order with dependencies
final_model = gen.partial_order(dependencies=[
    (outline_objectives, draft_plan),
    (draft_plan, conduct_meeting),
    (conduct_meeting, optional_negotiate_adjust),
    (optional_negotiate_adjust, approve_budget),
    (approve_budget, distribute_budget),
    (distribute_budget, implement_plan)
])
