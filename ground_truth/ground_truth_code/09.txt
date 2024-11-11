from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
identify_idea = gen.activity('Identify idea for new product or improvement')
conduct_initial_research = gen.activity('Conduct initial research')
conduct_feasibility_studies = gen.activity('Conduct feasibility studies')
draft_design_concepts = gen.activity('Draft design concepts')
select_promising_design = gen.activity('Select promising design')
build_prototype = gen.activity('Build prototype')
test_functionality = gen.activity('Test functionality')
test_safety = gen.activity('Test safety')
test_market_potential = gen.activity('Test market potential')
collect_feedback = gen.activity('Collect feedback from testing phase')
refine_prototype = gen.activity('Refine prototype')
approve_prototype = gen.activity('Approve prototype for further development')
discard_prototype = gen.activity('Discard prototype')

# Define tests and feedback as a partial order
tests_and_feedback = gen.partial_order(dependencies=[
    (test_functionality, collect_feedback),
    (test_safety, collect_feedback),
    (test_market_potential, collect_feedback)
])

# Define loop over tests and refinement
loop_tests_and_refine = gen.loop(do=tests_and_feedback, redo=refine_prototype)

# Define final decision
final_decision = gen.xor(approve_prototype, discard_prototype)

# Combine all dependencies into a final partial order
final_model = gen.partial_order(dependencies=[
    (identify_idea, conduct_initial_research),
    (identify_idea, conduct_feasibility_studies),
    (conduct_initial_research, draft_design_concepts),
    (conduct_feasibility_studies, draft_design_concepts),
    (draft_design_concepts, select_promising_design),
    (select_promising_design, build_prototype),
    (build_prototype, loop_tests_and_refine),
    (loop_tests_and_refine, final_decision)
])
