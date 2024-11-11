from utils.model_generation import ModelGenerator
gen = ModelGenerator()

# Define activities
notify_regional_offices = gen.activity('Send notification')
prepare_statements = gen.activity('Prepare financial statements')
gather_documents = gen.activity('Gather necessary documents')
check_regulatory_updates = gen.activity('Check regulatory updates')
submit_documents = gen.activity('Submit documents')
review_submissions = gen.activity('Review submission')
complete_risk_assessment = gen.activity('Complete risk assessment/mitigation')
evaluate_financial_risks = gen.activity('Evaluate financial risks')
evaluate_operational_risks = gen.activity('Evaluate operational risks')
evaluate_compliance_risks = gen.activity('Evaluate compliance risks')
launch_investigation = gen.activity('Launch detailed investigation')
data_analysis = gen.activity('Conduct data analysis')
interviews = gen.activity('Conduct interviews')
site_visits = gen.activity('Perform site visits')
compile_report = gen.activity('Compile audit report')
review_report = gen.activity('Review report by audit director')
approve_report = gen.activity('Approve report')
request_revisions = gen.activity('Request revisions')
update_report = gen.activity('Update audit report')
distribute_report = gen.activity('Distribute final report')
archive_report = gen.activity('Archive report')
close_audit = gen.activity('Close audit process')

discrepancies = gen.xor(None, gen.partial_order(dependencies=[
    ('Request clarifications of discrepancies', 'Receive clarifications')
]))

# Regional office subprocess
regional_office_preparation = gen.partial_order(dependencies=[
    (prepare_statements, submit_documents),
    (gather_documents, submit_documents),
    (check_regulatory_updates, submit_documents)
])

# Risk assessment subprocess
risk_evaluations = gen.partial_order(dependencies=[
    (evaluate_financial_risks,),
    (evaluate_operational_risks,),
    (evaluate_compliance_risks,)
])

# Investigation loop
investigation_activities = gen.partial_order(dependencies=[
    (data_analysis,),
    (interviews,),
    (gen.xor(site_visits, None),)
])

investigation_path = gen.partial_order(dependencies=[
    (launch_investigation,investigation_activities)
])

investigation_xor = gen.xor(investigation_path, None)

revision_path = gen.partial_order(dependencies=[
    ('Send report for revision', 'Update and resubmit report')
])

report_revision_loop = gen.loop(do=review_report, redo=revision_path)

# Define dependencies
dependencies = [
    (notify_regional_offices, regional_office_preparation),
    (regional_office_preparation, review_submissions),
    (review_submissions, discrepancies),
    (discrepancies, risk_evaluations),
    (risk_evaluations, investigation_xor),
    (investigation_xor, complete_risk_assessment),
    (complete_risk_assessment, compile_report),
    (compile_report, report_revision_loop),
    (report_revision_loop, approve_report),
    (approve_report, distribute_report),
    (distribute_report, archive_report),
    (archive_report, close_audit)
]

# Create the final model
final_model = gen.partial_order(dependencies=dependencies)
