from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
identify_need = gen.activity('Identify need for new supplier or vendor')
issue_rfp = gen.activity('Issue request for proposals (RFP)')
receive_proposals = gen.activity('Receive supplier proposals')
evaluate_proposal = gen.activity('Evaluate proposal')
conduct_site_visit = gen.activity('Conduct site visit')
conduct_interview = gen.activity('Conduct interview')
optional_site_visit = gen.xor(conduct_site_visit, conduct_interview, None)
evaluation_subprocess = gen.partial_order(dependencies=[
    (evaluate_proposal, optional_site_visit)
])
evaluation_loop = gen.loop(do=evaluation_subprocess, redo=None)
select_supplier = gen.activity('Select supplier')
begin_negotiations = gen.activity('Begin contract negotiations')
sign_contract = gen.activity('Sign contract')
onboard_supplier = gen.activity('Onboard supplier')
execute_contract = gen.activity('Execute contract')

# Define process flow
final_model = gen.partial_order(dependencies=[
    (identify_need, issue_rfp),
    (issue_rfp, receive_proposals),
    (receive_proposals, evaluation_loop),
    (evaluation_loop, select_supplier),
    (select_supplier, begin_negotiations),
    (begin_negotiations, sign_contract),
    (sign_contract, onboard_supplier),
    (onboard_supplier, execute_contract)
])
