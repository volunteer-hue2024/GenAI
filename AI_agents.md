# AGENTS DON'T ASK - THEY JUST DO IT

  
```https://www.geeksforgeeks.org/batch/twenty-projects-in-twenty-days-batch-5?tab=Live


AIM : ASKING THE MODEL TO GENERATE THE RECENT TRENDS IN AI.SO WE NEED to do a market research.Unlike in gemini LLM here we include market research capability as well (ie not from trained data alone, but internet matter as well) We need 
2 TOOLS
1.RESEARCHER AGENT :-it does the google search and extracts the information from internet
2.WRITER AGENT :- Basjd on the info provided by search agent we use the gemini api to write  good blog.
(serper is a tool which helps in performing market research (google search).Provides data it can fetch data from internet.Google gemini based on this data makes a proer written content)

>!pip install crewai crewai-tools langchain-google-genai
>
### BASIC IMPORTS

>import os
>from creai import Agent,Task,Crew,Process,LLM
>from crewai_tools import SerperDevTool
>from google.colab import userdata
>
### INITIALIZE THE GEMINI MODEL (using google api key)
gemini_llm = LLM(model="gemini/gemini-2.5-flash",api_key=userdata.get('GOOGLE_API_KEY')

### INITIALIZE THE SEARCH TOOL
visit https://serper.dev/api-keys  --- create a key
Login, goto API keys , there is a default key .A new API key can also be created.It then gives a free tier account allowing a particular number of requests sent per month.(appox 250 requests per month for free).Now paste it along with  colab's listed keys

Other options are Tavily etc

### load the key from 
serper_api_key=userdata.get('SERPER_API_KEY')
#### set it as an environment variable
os.environ["SERPER_API_KEY"]=serper_api_key




#### the search tool class
search_tool=SerperDevTool()


















https://www.geeksforgeeks.org/batch/twenty-projects-in-twenty-days-batch-5?tab=Live

















This GitHub-formatted README is designed with clear sectioning and structured code blocks to make the project easy for peers to understand.

🤖 Agentic Research & Content Crew
"Agents don't ask — they just do it." 
A multi-agent system built with CrewAI, Google Gemini, and Serper to automate market research and professional content generation. This project shifts from static LLM data to real-time internet intelligence.

🏗️ Architecture
This system orchestrates a collaborative workflow between two specialized agents to perform deep market research and high-quality writing.
Agent
Tool
Responsibility
Researcher Agent
SerperDevTool
Performs live Google searches, extracts technical trends, and identifies market shifts.
Writer Agent
Gemini 2.5 Flash
Transforms raw research data into a polished, SEO-friendly professional blog.


🛠️ Setup & Installation
1. Install Dependencies
Bash
pip install crewai crewai-tools langchain-google-genai

2. Environment Configuration
You will need API keys for both Google Gemini (LLM) and Serper.dev (Search).
Python
import os
from google.colab import userdata

# Load keys from Colab Secret Manager or Environment Variables
os.environ["SERPER_API_KEY"] = userdata.get('SERPER_API_KEY')
os.environ["GOOGLE_API_KEY"] = userdata.get('GOOGLE_API_KEY')


💻 Core Implementation
Initialize the Brain (Gemini 2.5)
We use the latest Flash model for high-speed reasoning and cost-efficiency.
Python
from crewai import LLM

# Initialize the Gemini model
gemini_llm = LLM(
    model="gemini/gemini-2.5-flash", 
    api_key=os.environ["GOOGLE_API_KEY"]
)

Initialize the Tools
The SerperDevTool allows our agents to step out of their "training box" and see the 2026 internet.
Python
from crewai_tools import SerperDevTool

# Initialize the Google Search capability
search_tool = SerperDevTool()

Define the Agentic Workflow
Unlike standard LLMs, these agents have specific Backstories and Goals.
Python
from crewai import Agent, Task, Crew

# 1. THE RESEARCHER
researcher = Agent(
    role='Market Research Analyst',
    goal='Uncover latest AI trends for 2026',
    backstory='You are an expert at finding high-signal data in a sea of noise.',
    tools=[search_tool],
    llm=gemini_llm
)

# 2. THE WRITER
writer = Agent(
    role='Tech Content Strategist',
    goal='Write a viral blog post on AI trends',
    backstory='You translate complex technical data into engaging narratives.',
    llm=gemini_llm
)


🚀 How to Run
To execute the agentic loop, define the tasks and "kick off" the crew:
Python
# Create the crew and start the process
crew = Crew(
    agents=[researcher, writer],
    tasks=[task1, task2], # Define your tasks here
    verbose=True
)

result = crew.kickoff()
print(result)


📈 Why this works
Real-time Intelligence: Bypasses LLM knowledge cutoffs by using live Serper data.
Specialization: The Researcher focuses on data accuracy while the Writer focuses on tone and engagement.
Autonomous: Once the goal is set, the agents handle the "how" without further user prompts.




Here are the Task definitions . In CrewAI, tasks act as the bridge between your agents and the final output.
It demonstrates how the Researcher passes data to the Writer.

📝 Task Definitions & Execution Logic
defining what the agents should actually do. Note the use of the context parameter, which ensures the Writer sees exactly what the Researcher found.
Python
from crewai import Task

# 1. Research Task: Search and Extract
research_task = Task(
    description=(
        "Search the internet for the top 5 most significant AI trends emerging in 2026. "
        "Focus on agentic workflows, multi-modal models, and real-world industrial applications. "
        "Provide a detailed summary of each trend with supporting facts."
    ),
    expected_output="A structured markdown report listing 5 key AI trends with bulleted insights.",
    agent=researcher,
    tools=[search_tool]
)

# 2. Writing Task: Analyze and Blog
# We pass research_task into context so the writer builds on the researcher's output
writing_task = Task(
    description=(
        "Using the research provided, write an engaging and professional blog post. "
        "The blog should be titled 'The Future of AI in 2026: From Chatbots to Autonomous Agents'. "
        "Include an introduction, one section for each trend, and a forward-looking conclusion."
    ),
    expected_output="A full blog post in markdown format, ready for publication on LinkedIn or a tech blog.",
    agent=writer,
    context=[research_task] 
)

# Assemble the Crew
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    process=Process.sequential, # Tasks execute one after the other
    verbose=True
)

# Start the execution
result = crew.kickoff()

# Print the final result (the blog post)
print("\n\n########################")
print("## FINAL BLOG OUTPUT ##")
print("########################\n")
print(result)


📌 Summary of Parameters used:
description: The specific instructions for the agent (The "Prompt").
expected_output: Tells the agent exactly what the successful result should look like.
context: This is the "secret sauce." It explicitly tells the Writer to use the output from the Researcher.
Process.sequential: Ensures the Researcher finishes and gives the data to the Writer before the writing begins.
 
Adding a Reviewer Agent is  often called a "Guardrail" or "Quality Assurance" step.
The Reviewer doesn't just read the text; it looks for hallucinations, logic gaps, and formatting errors.
1. Define the Reviewer Agent
This agent needs a "skeptical" backstory. Its goal is not to be nice, but to be accurate.
Python
# 3. THE REVIEWER (The "Chief Editor")
reviewer = Agent(
    role='Fact-Checker and Quality Auditor',
    goal='Ensure the final blog post is 100% accurate, avoids hype, and is formatted perfectly.',
    backstory=(
        "You are a cynical senior editor. You cross-reference claims made in the blog "
        "against the raw research data provided. You look for AI hallucinations and "
        "ensure the tone remains professional yet engaging."
    ),
    llm=gemini_llm,
    verbose=True,
    allow_delegation=False # Reviewers should focus solely on the audit
)

2. Define the Review Task
This task takes the output of the Writer as its primary input.
Python
# 3. Review Task: The Final Audit
review_task = Task(
    description=(
        "Review the blog post generated by the Writer. "
        "1. Cross-check all 2026 trends against the researcher's findings. "
        "2. Identify any exaggerated claims or technically incorrect statements. "
        "3. Ensure the Markdown formatting is clean and professional. "
        "If everything is perfect, provide the final version. If not, list the required edits."
    ),
    expected_output="A final, polished, and verified version of the blog post in Markdown format.",
    agent=reviewer,
    context=[research_task, writing_task] # Reviewer sees both the raw data and the draft
)

3. Update the Crew
Finally, add the new agent and task to your Crew and ensure process=Process.sequential.
Python
# Assemble the complete Crew
crew = Crew(
    agents=[researcher, writer, reviewer],
    tasks=[research_task, writing_task, review_task],
    process=Process.sequential,
    verbose=True
)

result = crew.kickoff()


💡 Pro-Tip: Human-in-the-Loop (HITL)
If this is for a high-stakes professional site, you might want to review it yourself before it posts. You can add a human review step by setting human_input=True in the final task:
Python
# Optional: Let a human have the final say
final_human_review = Task(
    description="Review the final blog post and approve it for publication.",
    expected_output="The final approved blog post.",
    agent=reviewer,
    human_input=True # The crew will pause and wait for YOUR input in the console
)
A Deployment Script allows you to package your entire multi-agent system into a single, professional command. This moves your project from a "notebook experiment" to a structured application.

1. Project Directory Structure
First, organize your files like a pro. This ensures your agents can find their configurations and saves your outputs in the right place.
Plaintext
ai-agent-project/
├── .env                # Secret API Keys (Git ignored!)
├── main.py             # The Deployment Script
├── requirements.txt    # Library dependencies
├── output/             # Where final blogs are saved
└── README.md           # The documentation we built


2. The Deployment Script (main.py)
This script handles the environment setup, initializes the agents, and executes the workflow in one go.
Python
import os
from dotenv import load_dotenv
from crewai import Agent, Task, Crew, Process, LLM
from crewai_tools import SerperDevTool

# 1. LOAD CONFIGURATION
load_dotenv() # Automatically loads keys from your .env file

def run_research_crew(topic):
    print(f"🚀 Starting Agentic Workflow for: {topic}...")
    
    # 2. INITIALIZE TOOLS & MODELS
    search_tool = SerperDevTool()
    gemini_llm = LLM(model="gemini/gemini-2.5-flash")

    # 3. DEFINE THE TEAM (Agents)
    researcher = Agent(
        role='Market Analyst',
        goal=f'Find 2026 trends for {topic}',
        backstory='Expert in digital archaeology and trend spotting.',
        tools=[search_tool],
        llm=gemini_llm
    )

    writer = Agent(
        role='Content Strategist',
        goal='Create a high-impact blog post',
        backstory='Master of technical storytelling.',
        llm=gemini_llm
    )

    reviewer = Agent(
        role='Editor-in-Chief',
        goal='Ensure 100% accuracy and SEO quality',
        backstory='High-standards auditor who hates fluff.',
        llm=gemini_llm
    )

    # 4. DEFINE THE WORK (Tasks)
    t1 = Task(description=f"Research {topic}", expected_output="Report", agent=researcher)
    t2 = Task(description="Write blog", expected_output="Draft", agent=writer, context=[t1])
    t3 = Task(
        description="Review and finalize", 
        expected_output="Final Markdown", 
        agent=reviewer, 
        context=[t1, t2],
        output_file=f"output/{topic.replace(' ', '_')}_blog.md"
    )

    # 5. EXECUTION
    crew = Crew(
        agents=[researcher, writer, reviewer],
        tasks=[t1, t2, t3],
        process=Process.sequential,
        verbose=True
    )

    return crew.kickoff()

if __name__ == "__main__":
    # You can change this topic to anything!
    run_research_crew("Autonomous AI Agents in Recruitment")


3. Creating the .env File
Never hard-code your keys. Create a file named .env in your root folder:
Code snippet
GOOGLE_API_KEY=your_gemini_key_here
SERPER_API_KEY=your_serper_key_here


4. How to Run It
Once you've set up the files, your deployment process is just two steps:
Install dependencies:
pip install -r requirements.txt
Run the application:
python main.py
Why use this over a Notebook?
Automation: You can set this up to run every Monday morning at 9:00 AM (using a Cron job or GitHub Actions) to generate a "Weekly AI Trends" newsletter.
Reliability: The output/ folder keeps a history of everything your agents have created.
Scalability: You can easily add a "Social Media Agent" to this script to automatically post the final blog to Twitter or LinkedIn.


Create the "Social Media Agent" and the task to auto-generate LinkedIn posts from your blog?

To complete the automated engine, we will add a Social Media Manager Agent. In 2026, social media agents don't just "summarize"; they repurpose content for platform-specific algorithms (e.g., using "hook" sentences for LinkedIn).
1. Define the Social Media Agent
This agent's expertise is in engagement engineering. It takes the long-form blog and breaks it into "snackable" viral content.

Python


# 4. THE SOCIAL MEDIA MANAGER
social_media_manager = Agent(
    role='Social Media Content Strategist',
    goal='Repurpose long-form blogs into high-engagement LinkedIn posts and Twitter threads.',
    backstory=(
        "You are a master of digital hooks and professional storytelling. "
        "You know exactly how to structure a LinkedIn post to trigger the 'See More' click. "
        "You use emojis sparingly, maintain a professional yet approachable tone, "
        "and always include relevant hashtags and a call-to-action."
    ),
    llm=gemini_llm,
    verbose=True
)


2. Define the Social Media Task
This task specifically references the writing_task output to ensure the social posts are consistent with the blog.

Python


# 4. Social Media Task: Platform Repurposing
social_media_task = Task(
    description=(
        "Analyze the final blog post provided by the Reviewer. "
        "1. Create one high-impact LinkedIn post (max 300 words). Use a strong hook at the start. "
        "2. Create a Twitter (X) thread of 5-7 tweets summarizing the key 2026 trends. "
        "3. Ensure both include a call-to-action (CTA) to read the full blog."
    ),
    expected_output="A text file containing the LinkedIn post and the Twitter thread, formatted for immediate posting.",
    agent=social_media_manager,
    context=[review_task], # Built directly from the verified blog
    output_file="output/social_promo_pack.md"
)


3. The Fully Integrated "Master Crew"
Update your main.py to include the full chain. The workflow now flows as follows:
Research → Write → Review/Fact-Check → Social Media Promotion.

Python


# Updated Crew with all 4 Agents
crew = Crew(
    agents=[researcher, writer, reviewer, social_media_manager],
    tasks=[research_task, writing_task, review_task, social_media_task],
    process=Process.sequential,
    verbose=True
)

result = crew.kickoff()


📁 Updated Project Output Structure
After running this, your output/ folder will now look like this:
File
Content
Use Case
AI_Recruitment_blog.md
The full, fact-checked technical article.
Website / Medium / Substack
social_promo_pack.md
The LinkedIn post and Twitter thread.
Daily social promotion

🛡️This setup is "Professional Grade" bcoz:
Context Chaining: The Social Media agent doesn't need to search the web again; it uses the review_task as its "source of truth," saving you API tokens and ensuring zero contradictions.
Multi-Platform Ready: By changing the social_media_task description, you can easily add tasks for Instagram scripts, Newsletter summaries, or even YouTube video outlines.

How to add a "Graphic Designer Agent" that suggests specific image prompts for your blog's cover photo.

In 2026, we don't just "write" about images—we use a Visual Design Agent to bridge the gap between text and pixels. Since you are using Gemini 2.5 Flash, you can actually use it to generate the image directly or craft high-fidelity prompts for models like DALL-E 3 or Midjourney v7.

1. Define the Visual Design Agent
This agent's job is to translate the core themes of the blog into "Cinematic" or "Technical" visual prompts.
Python
# 5. THE VISUAL DESIGNER
visual_designer = Agent(
    role='AI Visual Prompt Engineer',
    goal='Create high-fidelity image generation prompts for blog cover art.',
    backstory=(
        "You specialize in converting abstract tech concepts into vivid visual descriptions. "
        "You understand lighting, composition, and artistic styles (like 'Cyberpunk 2026' or 'Minimalist Vector'). "
        "You ensure every prompt avoids 'uncanny' features and focuses on high-impact hero shots."
    ),
    llm=gemini_llm,
    verbose=True
)

2. Define the Design Task
This task ensures the image perfectly matches the blog’s specific tone.
Python
# 5. Design Task: Hero Image Generation
design_task = Task(
    description=(
        "Review the final blog post and the social media pack. "
        "1. Create 3 distinct image generation prompts for the blog's hero image. "
        "2. One prompt should be 'Photorealistic Tech', one 'Abstract Digital Art', and one 'Minimalist 3D Render'. "
        "3. Ensure the prompts are compatible with DALL-E 3 and Midjourney v7 syntax."
    ),
    expected_output="A Markdown section containing 3 detailed image prompts with style descriptions.",
    agent=visual_designer,
    context=[review_task], 
    output_file="output/visual_design_brief.md"
)


3. Pro Tip: Automated Image Generation
If you have an OpenAI API key or a Midjourney API, you can even give this agent a "Tool" to actually create the image file and save it to your output/ folder automatically.
The "Dall-E Tool" Integration:
Python
from crewai_tools import DallETool

# Initialize the Tool
image_gen_tool = DallETool(model="dall-e-3", size="1024x1024", quality="hd")

# Add it to your Visual Designer
visual_designer = Agent(
    role='Visual Designer',
    goal='Generate the actual cover image for the blog',
    tools=[image_gen_tool], # The agent can now "draw"!
    llm=gemini_llm
)


🏗️ The Final 2026 "Full-Stack" Agentic Flow
Your system is now a complete content factory. Here is the final sequence in your main.py:
Researcher: Finds the 2026 facts (via Serper).
Writer: Drafts the 2,000-word blog (via Gemini).
Reviewer: Polishes the text and verifies facts.
Social Media Manager: Creates the LinkedIn/Twitter promotion.
Visual Designer: Generates the cover art prompts (or the image itself).
What you have built:
You’ve moved from "Using AI" to "Managing a Workforce."
Create a "Project Manager Agent" that coordinates all these agents and provides you with a single status report at the end?

To wrap up your 2026 AI Agent project, we are adding the Project Manager (PM) Agent. This agent acts as the "orchestrator," moving your workflow from a simple sequential chain to a Hierarchical Process.
The PM doesn't do the research or the writing—it manages the other agents, validates their work, and ensures the final project meets your high-level goals.

1. Define the Project Manager Agent
In CrewAI, you can set an agent as the manager_agent. This agent needs a high-level "Executive" backstory.
Python
# 6. THE PROJECT MANAGER (The Orchestrator)
project_manager = Agent(
    role='Agile Project Manager',
    goal='Coordinate the AI content team to deliver a high-quality, verified media pack.',
    backstory=(
        "You are an expert in Agile methodologies and content quality control. "
        "Your job is to oversee the workflow between the Researcher, Writer, Reviewer, "
        "and Designer. You ensure that no steps are skipped, deadlines are met, "
        "and the final output is a cohesive, ready-to-publish package."
    ),
    allow_delegation=True, # Critical: The PM must be able to talk to other agents
    llm=gemini_llm,
    verbose=True
)

2. The Final Management Task
This task is the "Grand Finale." It compiles the work of all other agents into a single Project Status Report for you.
Python
# 6. Management Task: Final Delivery & Status Report
management_task = Task(
    description=(
        "1. Supervise the completion of the research, blog, social posts, and visual prompts. "
        "2. Compile a 'Project Delivery Report' that includes: "
        "   - A summary of the key findings. "
        "   - Links/Paths to the generated files. "
        "   - A confidence score (1-10) for the final blog's accuracy."
    ),
    expected_output="A structured Project Status Report in Markdown format.",
    agent=project_manager,
    context=[research_task, writing_task, review_task, social_media_task, design_task],
    output_file="output/FINAL_PROJECT_REPORT.md"
)


3. The Hierarchical Crew Configuration
To make the PM truly in charge, you update your Crew to use Process.hierarchical. This tells CrewAI that the PM agent is the boss.
Python
# The Master Crew Integration
crew = Crew(
    agents=[researcher, writer, reviewer, social_media_manager, visual_designer], # Workers
    manager_agent=project_manager, # The Boss
    tasks=[
        research_task, 
        writing_task, 
        review_task, 
        social_media_task, 
        design_task, 
        management_task
    ],
    process=Process.hierarchical, # Changes the flow from sequential to managed
    verbose=True
)

result = crew.kickoff()


🏆 Your Completed 2026 AI Workforce
You have successfully built a full-scale AI Content Factory. Here is the final team roster:
The Researcher: Scours the live web via Serper.
The Writer: Crafts the technical narrative via Gemini 2.5.
The Reviewer: Fact-checks and audits the text for quality.
The Social Media Lead: Repurposes the blog for viral growth.
The Visual Designer: Generates professional image prompts.
The Project Manager: Coordinates the team and delivers your final report.
📁 Final File Output
When you run main.py, your output/ folder will be fully populated:
AI_Trends_2026_blog.md (The Main Article)
social_promo_pack.md (LinkedIn/Twitter Content)
visual_design_brief.md (Image Prompts)
FINAL_PROJECT_REPORT.md (Your executive summary)

 Create  "Requirements.txt" file so you can install all the necessary libraries to run this entire system now!


