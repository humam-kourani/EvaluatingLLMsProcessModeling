from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
schedule_compliance_audit = gen.activity('Schedule compliance audit')
prepare_documentation = gen.activity('Prepare documentation')
gather_evidence = gen.activity('Gather evidence')
conduct_self_assessment = gen.activity('Conduct self-assessment')
external_audit = gen.activity('Conduct external audit')
identify_gaps_or_issues = gen.activity('Identify gaps or issues')
make_corrections_or_improvements = gen.activity('Make necessary corrections or improvements')
final_audit = gen.activity('Conduct final audit')
award_certification = gen.activity('Award certification')
issue_official_documents = gen.activity('Issue official documents')

# Define proceeding after final audit
award_and_issue = gen.partial_order(dependencies=[
    (award_certification, issue_official_documents)
])

# Choice after final audit: award certification or not
choice_after_final_audit = gen.xor(award_and_issue, None)

# Define initial partial order with dependencies
initial_poset = gen.partial_order(dependencies=[
    (schedule_compliance_audit, prepare_documentation),
    (schedule_compliance_audit, gather_evidence),
    (schedule_compliance_audit, conduct_self_assessment),
    (prepare_documentation, external_audit),
    (gather_evidence, external_audit),
    (conduct_self_assessment, external_audit),
    (external_audit, identify_gaps_or_issues),
    (identify_gaps_or_issues, make_corrections_or_improvements),
    (make_corrections_or_improvements, final_audit),
    (final_audit, choice_after_final_audit)
])

final_model = initial_poset
