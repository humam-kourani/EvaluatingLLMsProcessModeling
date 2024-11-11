from utils.model_generation import ModelGenerator
gen = ModelGenerator()

# Define activities
submit_application = gen.activity('Submit application online')
review_application = gen.activity('Review application and documents')
notify_missing_documents = gen.activity('Notify applicant of missing documents')
provide_missing_documents = gen.activity('Provide missing documents')

path_missing_doc = gen.xor(None, gen.partial_order([
    (notify_missing_documents, provide_missing_documents)
]))

evaluate_application = gen.activity('Evaluate application by admissions committee')
process_fees = gen.activity('Process fees or waivers')
send_acceptance_letter = gen.activity('Send acceptance letter')
confirm_enrollment = gen.activity('Confirm enrollment')
send_orientation_materials = gen.activity('Send orientation materials')
meet_academic_advisor = gen.activity('Meet with academic advisor')
select_courses = gen.activity('Select courses')
resolve_schedule_conflicts = gen.activity('Resolve schedule conflicts')
set_up_IT_accounts = gen.activity('Set up IT accounts')
obtain_student_ID = gen.activity('Obtain student ID card')
assist_with_visa = gen.activity('Assist with visa processing')
begin_classes = gen.activity('Begin attending classes')
add_drop_courses = gen.xor('Add course', 'Drop course')
post_grades = gen.activity('Post grades')
post_grades = gen.activity('Post grades')
review_grades_online = gen.activity('Review grades online')
submit_appeal = gen.activity('Submit appeal form')
meet_appeals_committee = gen.activity('Meet with appeals committee')
await_decision = gen.activity('Await decision')
graduate = gen.activity('Graduate')
withdraw = gen.activity('Withdraw')


# Course registration subprocess
course_registration = gen.partial_order(dependencies=[
    (meet_academic_advisor, select_courses),
    (select_courses, resolve_schedule_conflicts)
])

# Appeals process
appeals_process = gen.partial_order(dependencies=[
    (submit_appeal, meet_appeals_committee),
    (meet_appeals_committee, await_decision)
])

# International student subprocess
international_student_process = gen.xor(assist_with_visa, None)

# Add/drop courses loop
add_drop_loop = gen.loop(do=None, redo=add_drop_courses)
add_drop_loop = gen.xor(None, gen.activity('Add/drop courses'))

# Semester loop
semester_loop = gen.loop(
    do=gen.partial_order(dependencies=[
        (begin_classes, add_drop_loop),
        (add_drop_loop, post_grades),
        (post_grades, review_grades_online),
        (review_grades_online, gen.xor(appeals_process, None))
    ]),
    redo=None
)

# Graduation or withdrawal decision
end_decision = gen.xor(graduate, withdraw)

enrollment = gen.partial_order([
    (confirm_enrollment, send_orientation_materials),
    (confirm_enrollment, set_up_IT_accounts),

    (set_up_IT_accounts, international_student_process),
    (send_orientation_materials, international_student_process),

    (international_student_process, obtain_student_ID),
    (international_student_process, course_registration),

    (obtain_student_ID, semester_loop),
    (course_registration, semester_loop),

    (semester_loop, end_decision)
])

decision_enrollment = gen.xor('Cancel application', enrollment)

accept_path = gen.partial_order([
    (send_acceptance_letter, decision_enrollment)
])

decision_letter = gen.xor('Send rejection letter', accept_path)

# Define dependencies
dependencies = [
    (submit_application, review_application),
    (review_application, path_missing_doc),
    (path_missing_doc, evaluate_application),
    (path_missing_doc, process_fees),
    (evaluate_application, decision_letter),
    (process_fees, decision_letter),
]

# Create the final model
final_model = gen.partial_order(dependencies=dependencies)
