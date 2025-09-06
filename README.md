👇

🌪 CrisisSignal AI – Intelligent Disaster Response Hub
🚨 Problem Statement

Natural disasters (floods, earthquakes, cyclones, etc.) spread chaos in seconds. Thousands of SOS tweets flood X (Twitter), but manual monitoring is slow, scattered, and often misses critical help requests. Relief agencies lack a real-time, structured, and prioritized alert system.

💡 Our Solution

CrisisSignal AI automates disaster signal detection and triage using n8n + OpenAI APIs:

Fetches live tweets from X related to disasters.

Classifies urgency (Critical – Life Threatening, High – Resource Needs, Info – General Updates).

Extracts key entities: location, type of disaster, resources needed.

Routes alerts automatically:

Critical → Telegram/Slack for first responders.

High → Google Sheet for coordination.

Info → Dashboard for analysis.

⚙️ Tech Workflow

Built using n8n workflow automation.

Trigger → Cron Node (fetches tweets every 10 mins)

X Node → Search tweets by disaster keywords

OpenAI Node → Classify + Extract data

Switch Node → Route based on severity

Google Sheet / Telegram / Slack Nodes → Deliver alerts to right teams

🔑 How We Use OpenAI APIs

Prompt Engineering:

Classify the urgency of this tweet as Critical, High, or Info.  
Extract: {location, disaster type, resources needed}  
Output in JSON only.  


Classification – urgency detection.

Entity Extraction – identify place, need, disaster.

Summarization – clean structured alerts.

🚀 Feasibility & Scaling

Phase 1: MVP with Tweets → Google Sheet.

Phase 2: Add Telegram/Slack alerts.

Phase 3: Scale to 24/7 auto-refresh, integrate with Maps API + NGO dashboards.

🛠️ Setup Instructions

Clone this repo

Import the JSON workflow into n8n

Setup credentials:

X (Twitter) API

OpenAI API Key

Google Sheets / Telegram (Optional)

Execute workflow manually or via cron trigger.

📌 Example Use Case

Tweet:

"Urgent help! 5 people trapped in Chennai floods, no food, need boat rescue."

AI Output:

{
  "urgency": "Critical",
  "location": "Chennai",
  "disaster_type": "Flood",
  "resources_needed": "Rescue boat, food"
}


System Routes → Telegram Alert for Rescue Teams 🚑

🏆 Why This Matters

Impact: Faster disaster response = more lives saved.

Innovation: Combines real-time social signals + AI triage.

Scalable: From local community alerts → national disaster response hub.
