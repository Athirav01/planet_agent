## 🪐 Planet Agent

> An intelligent multi-agent AI system built with **Google ADK** that answers questions about planets, moons, and space missions in our solar system — deployed live on **Google Cloud Run**.

[![Live Demo](https://img.shields.io/badge/🚀_Live_Demo-Cloud_Run-4285F4?style=for-the-badge&logo=googlecloud&logoColor=white)](https://planet-guide-1013476993343.europe-west1.run.app)
[![Built with ADK](https://img.shields.io/badge/Built_with-Google_ADK_1.14-34A853?style=for-the-badge&logo=google&logoColor=white)](https://google.github.io/adk-docs/)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)

---

## 🌌 What It Does

**Planet Agent** is a conversational AI agent that acts as your personal Mission Control. Ask it anything about the solar system — from Mars rover landing sites to Jupiter's atmospheric composition — and it responds like an expert astrophysicist backed by real-time Wikipedia data.

The system uses a **multi-agent pipeline** powered by Google's Agent Development Kit (ADK):

1. **Mission Control Greeter** welcomes the user and captures their planet inquiry
2. **Planetary Researcher** gathers data using Wikipedia search tools
3. **Response Formatter** synthesizes everything into an inspiring, professional mission briefing

---

## 🏗️ Architecture

```
User Input
    │
    ▼
┌─────────────────────────────┐
│   mission_control_greeter   │  ← Root Agent (entry point)
│   Captures user's query     │
└────────────┬────────────────┘
             │ transfers to
             ▼
┌─────────────────────────────────────────────────┐
│         planet_explorer_workflow                │  ← SequentialAgent
│                                                 │
│  ┌─────────────────────┐  ┌──────────────────┐  │
│  │ planetary_researcher│→ │response_formatter│  │
│  │ Wikipedia + tools   │  │ Mission briefing │  │
│  └─────────────────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────┘
             │
             ▼
    Professional Answer to User
```

### Agents

| Agent | Role | Tools |
|---|---|---|
| `mission_control_greeter` | Entry point, captures user intent | `add_prompt_to_state` |
| `planetary_researcher` | Fetches planetary data | `WikipediaQueryRun` (via LangchainTool) |
| `response_formatter` | Formats a mission-style briefing | — (synthesizer only) |

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| Agent Framework | [Google ADK](https://google.github.io/adk-docs/) `1.14.0` |
| LLM | Gemini (via `MODEL` env variable) |
| Knowledge Source | Wikipedia API (via `langchain-community`) |
| Logging | Google Cloud Logging |
| Deployment | Google Cloud Run |
| Runtime | Python 3.10+ |

---

## 📁 Project Structure

```
planet_agent-main/
├── agent.py          # All agent definitions, tools, and workflow
├── __init__.py       # Package entry point
├── requirement.txt   # Python dependencies
└── README.md         # This file
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- Google Cloud account (for Cloud Logging and deployment)
- A Gemini API key or Vertex AI setup

### 1. Clone the Repository

```bash
git clone https://github.com/Athirav01/planet_agent.git
cd planet_agent
```

### 2. Install Dependencies

```bash
pip install -r requirement.txt
```

### 3. Configure Environment Variables

Create a `.env` file in the project root:

```env
MODEL=gemini-2.0-flash
```

### 4. Run Locally with ADK

```bash
adk web
```

Visit `http://localhost:8000` and start chatting with Mission Control!

---

## ☁️ Deployment (Google Cloud Run)

This project is deployed on **Cloud Run** in the `europe-west1` region.

```bash
# Authenticate
gcloud auth login

# Build and deploy
gcloud run deploy planet-guide \
  --source . \
  --region europe-west1 \
  --allow-unauthenticated
```

**Live URL:** https://planet-guide-1013476993343.europe-west1.run.app

---

## 💬 Example Conversations

**User:** Tell me about Mars  
**Agent:** 🚀 *Welcome to the Interstellar Exploration Bureau...* Greetings, Explorer! Mars, the Red Planet, is currently home to NASA's Perseverance rover, which landed in Jezero Crater in February 2021...

**User:** What do we know about Jupiter's moons?  
**Agent:** *Mission Control Briefing:* Jupiter hosts 95 known moons. The four largest — Io, Europa, Ganymede, and Callisto — were discovered by Galileo in 1610...

---

## 🔧 How It Works — Code Walkthrough

### State Management

The root agent uses a custom tool to store the user's query in shared agent state:

```python
def add_prompt_to_state(tool_context: ToolContext, prompt: str) -> dict:
    """Saves the user's planetary inquiry to the state."""
    tool_context.state["PROMPT"] = prompt
    return {"status": "success"}
```

### Wikipedia Integration via LangChain

```python
wikipedia_tool = LangchainTool(
    tool=WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper())
)
```

ADK's `LangchainTool` wrapper lets us drop any LangChain tool directly into a Google ADK agent.

### Sequential Workflow

```python
planet_explorer_workflow = SequentialAgent(
    name="planet_explorer_workflow",
    sub_agents=[comprehensive_researcher, response_formatter]
)
```

The `SequentialAgent` runs agents in order, passing the `output_key` from one agent as context to the next.

---

## 📦 Dependencies

```
google-adk==1.14.0
langchain-community==0.3.27
wikipedia==1.4.0
```

---

## 🛠️ Future Improvements

- [ ] Add NASA Open APIs for real mission data (rovers, NEO tracking)
- [ ] Support image generation of planets
- [ ] Add voice interface via Web Speech API
- [ ] Expand to exoplanet data from the NASA Exoplanet Archive
- [ ] Add a planet comparison tool

---

## 👩‍💻 Author

Built with ❤️ using Google ADK — *Because every explorer needs a Mission Control.*

---

## 📄 License

This project is open source. Feel free to fork and extend it!
