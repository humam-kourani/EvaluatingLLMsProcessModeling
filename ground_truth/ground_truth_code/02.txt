from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
identify_need = gen.activity('Identify need for new hire')
create_job_description = gen.activity('Create job description')
post_job_description = gen.activity('Post job description')
collect_resumes = gen.activity('Collect resumes')
screen_resumes = gen.activity('Screen resumes')
conduct_phone_interviews = gen.activity('Conduct initial phone interviews')
invite_candidates = gen.activity('Invite candidates for interviews')
conduct_in_person_interview = gen.activity('Conduct an in-person interview')
conduct_virtual_interview = gen.activity('Conduct a virtual interview')
choose_candidate = gen.activity('Choose candidate')
extend_offer = gen.activity('Extend offer')
salary_negotiations = gen.xor(gen.loop(gen.activity('Negotiate salary'), None), None)
begin_onboarding = gen.activity('Begin onboarding process')
paperwork = gen.activity('Complete paperwork')
orientation = gen.activity('Complete orientation')
training = gen.activity('Complete training')


# Define exclusive choice between in-person and virtual interviews
interviews = gen.loop(gen.xor(conduct_in_person_interview, conduct_virtual_interview), None)

# Define dependencies
dependencies = [
    (identify_need, create_job_description),
    (create_job_description, post_job_description),
    (post_job_description, collect_resumes),
    (collect_resumes, screen_resumes),
    (screen_resumes, conduct_phone_interviews),
    (conduct_phone_interviews, invite_candidates),
    (invite_candidates, interviews),
    (interviews, choose_candidate),
    (choose_candidate, extend_offer),
    (extend_offer, salary_negotiations),
    (salary_negotiations, begin_onboarding),
    (begin_onboarding, paperwork),
    (begin_onboarding, orientation),
    (begin_onboarding, training)
]

# Create the final model
final_model = gen.partial_order(dependencies=dependencies)
