# Google-AI-Builder-
Ai Agent Builder 
AgileVision — AI Agile Sprint & Story Visualizer
AgileVision Demo

AgileVision is an AI-powered Agile Coach and Product Owner assistant built with the Google Agent Development Kit (ADK) and powered by Gemini. It helps software development teams manage sprint backlogs, write user stories, calculate sprint capacity, fetch engineering wisdom, generate UI concept wireframes, and display structured cards via Google A2UI.

🎯 Features & Capabilities
📋 Sprint Backlog Management: Stream live user stories from Firestore, create new stories with acceptance criteria, and update story status (Backlog, In Progress, Done).
🧮 Sprint Capacity & Velocity Math: Calculate net working hours, focus factor adjustments, and recommended story point targets. Sum completed story points across sprints.
🎨 UI Wireframe & Visual Asset Generation: Generate visual UI concept sketches for user stories and domain graphics (badges, milestone logos) using Google's Gemini multimodal models.
🎥 Domain Video Clip Generation: Generate short video animations for team retrospective celebrations and feature walkthroughs using Google's Gemini Omni model.
🧠 Cross-Session Memory: Long-term memory extraction for team velocity history, story point estimates, and working preferences across conversations.
🗺️ Team Offsite & Venue Finder: Geocode team addresses and discover nearby venues and restaurants for team sprint retrospectives via Google Maps APIs.
💡 Engineering Principles: Fetch software design wisdom for standups and team retrospectives.
📱 A2UI Rich Cards: Natively render structured cards, backlog lists, and image previews inside the web interface.
☁️ Google Cloud Services & Tools Wired Up
The agent code in app/agent.py directly integrates the following Google Cloud services and APIs:

Service / Integration	Implementation Details
Vertex AI Memory Bank	Managed long-term cross-session memory (VertexAiMemoryBankService) integrated via PreloadMemoryTool and post-turn session memory callbacks.
Google Cloud Firestore	NoSQL database storing user story collections (user_stories) for real-time backlog queries, story creation, and velocity tracking.
Google Cloud Storage	Public storage bucket for hosting generated UI wireframes, domain images, and video clips with public HTTPS URLs.
Vertex AI Image Generation	Multimodal image generation using gemini-3.1-flash-lite-image in the global region to produce wireframe sketches and agile visual assets.
Vertex AI Interactions API Video Generation	Video generation using gemini-omni-flash-preview in the global region to create short video clips.
Agent Engine Code Sandbox	Isolated execution environment (AgentEngineSandboxCodeExecutor) for running Python math for velocity and capacity calculations.
Google A2UI (Agent to User Interface)	Schema manager and catalog renderer (a2ui_utils.py / BasicCatalog) emitting structured UI cards.
Google Maps APIs	Geocoding API (geocode_address) and Places API New (find_nearby_places) for location lookup and offsite planning.
GitHub Zen API	Web API integration (get_engineering_wisdom) fetching engineering design principles.
🛠️ Implemented Tool Reference
All tools are defined in 
app/agent.py
 and registered with the root agent:

get_sprint_backlog(status_filter): Streams user stories from Firestore filtered by status (Backlog, In Progress, Done, or All).
create_user_story(title, description, acceptance_criteria, story_points, priority, assignee): Creates a new user story in Firestore with an auto-assigned ID (STORY-10x).
update_story_status(story_id, new_status): Updates the status of a story in Firestore.
calculate_sprint_capacity(team_members, sprint_days, daily_hours_per_member, focus_factor, hours_per_story_point): Calculates gross/net hours and target story point capacity.
calculate_sprint_velocity(): Sums total story points for completed (Done) stories in Firestore.
generate_story_wireframe(prompt): Generates a UI concept sketch using gemini-3.1-flash-lite-image and uploads it to Cloud Storage.
generate_domain_image(prompt, tool_context): Generates an agile visual asset, saves an ADK Playground artifact, and uploads to Cloud Storage.
generate_domain_video(prompt, tool_context): Generates a video clip using gemini-omni-flash-preview, saves an ADK Playground artifact, and uploads to Cloud Storage.
get_engineering_wisdom(): Fetches agile principles from GitHub Zen API.
geocode_address(address): Converts addresses to latitude/longitude coordinates via Google Geocoding API.
find_nearby_places(latitude, longitude, place_type, radius_meters): Finds nearby venues via Google Places API (New).
📁 Repository Structure

agilevision/
├── app/
│   ├── __init__.py
│   ├── a2ui_utils.py           # A2UI callback and schema manager setup
│   └── agent.py                # ADK agent definition, Memory Bank, and tool implementations
├── frontend/
│   ├── server.py               # FastAPI proxy server (A2A protocol)
│   └── static/
│       ├── index.html          # AgileVision web UI layout, custom styles, and A2UI renderer
│       └── favicon.ico
├── demo.gif                    # Animated demonstration preview
├── agents-cli-manifest.yaml    # Agents CLI project deployment manifest
├── seed_database.py            # Firestore database seed script for initial user stories
├── pyproject.toml              # Project dependencies and configuration
└── README.md                   # Project documentation
🚀 Local Setup & Run Instructions
Follow these steps to run AgileVision locally on your machine.

1. Prerequisites
Python 3.11+
Google Cloud SDK (gcloud) authenticated with a project containing Firestore and Vertex AI access.
2. Environment Setup
Clone the repository and create a virtual environment:

bash

cd agilevision
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
3. Configure Environment Variables
Create a .env file in the project root:

bash

cp .env.example .env
Ensure your .env contains:

env

GOOGLE_GENAI_USE_VERTEXAI=true
GOOGLE_CLOUD_PROJECT=your-gcp-project-id
GOOGLE_CLOUD_LOCATION=us-central1
GOOGLE_MAPS_API_KEY=your-google-maps-api-key
4. Seed Firestore Backlog (Optional)
Populate sample user stories into your Firestore database:

bash

python3 seed_database.py
5. Run the Agent Locally
Launch the Agent Development Kit (ADK) web developer playground:

bash

adk web app
Or run the custom FastAPI frontend server:

bash

python3 -m frontend.server
