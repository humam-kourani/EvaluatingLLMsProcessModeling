from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
identify_needs = gen.activity('Identify development needs or career aspirations')
create_plan = gen.activity('Create personal development plan')
skill_enhancement = gen.loop(gen.activity('Work on skill enhancement'), None)
feedback_evaluation = gen.loop(gen.activity('Receive feedback and evaluation from supervisors'), None)
consider_promotion = gen.activity('Consider employee for promotion or new role')
performance_review = gen.activity('Conducts formal performance review')
approve_promotion = gen.activity('Approve promotion')


new_res = gen.activity('Set new responsibilities')
adjust_salary = gen.activity('Adjust compensation')
transition_role = gen.activity('Transition into new role')


promotion = gen.xor(gen.partial_order(dependencies=[
    (approve_promotion, new_res),
    (approve_promotion, adjust_salary),
    (new_res,transition_role),
    (adjust_salary,transition_role)
]), None)


# Main process dependencies
dependencies = [
    (identify_needs, create_plan),
    (create_plan, skill_enhancement),
    (create_plan, feedback_evaluation),
    (skill_enhancement, consider_promotion),
    (feedback_evaluation, consider_promotion),
    (consider_promotion, performance_review),
    (performance_review, promotion)
]

final_model = gen.partial_order(dependencies=dependencies)
