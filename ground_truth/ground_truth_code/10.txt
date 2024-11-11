from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
submit_request = gen.activity('Submit IT solution request')
assess_request = gen.activity('Assess compatibility')
evaluate_request = gen.activity('Evaluate cost and resource needs')
procure_tools = gen.activity('Procure necessary tools or licenses')
install_solution = gen.activity('Install the solution')
test_solution = gen.activity('Test solution')
roll_out_solution = gen.activity('Roll out solution to requesting department')
provide_training = gen.activity('Provide training')
provide_it_support = gen.xor(None, gen.loop(gen.activity('Provide support for troubleshooting'), None))

# Optional training after rollout
optional_training = gen.xor(provide_training, None)

# Approved subprocess
approved_subprocess = gen.partial_order(dependencies=[
    (procure_tools, install_solution),
    (install_solution, test_solution),
    (test_solution, roll_out_solution),
    (roll_out_solution, optional_training),
    (roll_out_solution, provide_it_support)
])

# Choice after assessment (approved or process ends)
choice_after_assessment = gen.xor(approved_subprocess, None)

# Initial process with dependencies
final_model = gen.partial_order(dependencies=[
    (submit_request, assess_request),
    (submit_request, evaluate_request),
    (assess_request, choice_after_assessment),
    (evaluate_request, choice_after_assessment)
])
