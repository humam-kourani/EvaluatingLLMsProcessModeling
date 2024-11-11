from utils.model_generation import ModelGenerator

gen = ModelGenerator()

# Define activities
define_objectives = gen.activity('Define campaign objectives')
create_content = gen.activity('Create content')
design_visuals = gen.activity('Design visuals')
select_channels = gen.activity('Select promotion channels')
launch_campaign = gen.activity('Launch campaign')
track_performance = gen.activity('Track performance in real-time')
collect_leads = gen.activity('Collect leads in CRM system')
sales_follow_up = gen.xor(gen.activity('Sales teams follows up on leads'), None)
campaign_ends = gen.activity('Campaign period ends')
analyze_performance = gen.activity('Analyze performance for future optimization')

# Define dependencies
dependencies = [
    (define_objectives, create_content),
    (define_objectives, design_visuals),
    (define_objectives, select_channels),
    (create_content, launch_campaign),
    (design_visuals, launch_campaign),
    (select_channels, launch_campaign),
    (launch_campaign, track_performance),
    (launch_campaign, collect_leads),
    (collect_leads, sales_follow_up),
    (track_performance, analyze_performance),
    (track_performance, campaign_ends),
    (sales_follow_up, campaign_ends),
]

# Create final model
final_model = gen.partial_order(dependencies=dependencies)
