# AXIOM-ONE
### A Research-Grade Reasoning System (Not a Chatbot)

![Axiom-One Hero](assets/axiom-one-hero.png)

> **"Build a thinking-first research agent that decomposes problems, runs multi-step reasoning loops, validates its own outputs, and exposes the entire thought pipeline to the user."**

This project is about **epistemic rigor**, not vibes.

---

## ⚡ Quick Start (Get Running in 2 Minutes)

```bash
# 1. Clone and install
git clone https://github.com/bbfcfmars/AXiOM.git
cd AXiOM
npm install

# 2. Set up your API key
cp .env.example .env
# Edit .env and add your Gemini API key from https://aistudio.google.com/apikey

# 3. Run the development server
npm run dev
```

Then open **http://localhost:3000** in your browser. That's it! 🚀

---

## 1. Product Definition

### What Axiom-One Does
Axiom-One is a **research execution engine** that:
- Accepts a complex question or goal.
- Breaks it into **atomic research tasks**.
- Executes them in parallel and sequentially.
- Cross-validates results.
- Produces:
    - A final answer.
    - A transparent reasoning trace.
    - Structured artifacts (notes, citations, assumptions).

### What It Is NOT
- Not a chat UI with memory slapped on.
- Not a “personal assistant”.
- Not a prompt wrapper.
*If it can’t explain why it believes something, it shuts up.*

---

## 2. Technical Architecture

### Blueprint (Subagent-Driven Research)
The architecture follows a strict "Hub-and-Spoke" model where a central Orchestrator manages specialized modules. This ensures context cleanliness and prevents hallucination cascades.

#### High-Level Sequence
```mermaid
sequenceDiagram
    participant U as User UI
    participant O as Orchestrator
    participant TD as Task Decomposer
    participant S as Subagents (Pool)
    participant V as Validator
    participant SE as Synthesis Engine

    U->>O: Submits Query
    O->>TD: Decompose Goal
    TD-->>O: Task Graph (JSON)

    loop Parallel Execution
        O->>S: Spawn Subagent (Fresh Context)
        S->>S: Execute Research
        S-->>O: Return Result + Metadata
    end

    O->>V: Validate Outputs (Hostile Peer Review)
    V-->>O: Flags, Revisions, Confidence Scores

    O->>SE: Synthesize Final Answer
    SE-->>U: Final Output + Reasoning Trace
```

#### System Component Blueprint (The "FigJam" Model)

```mermaid
graph TD
    %% Styling
    classDef gray fill:#f0f0f0,stroke:#333,stroke-width:2px,color:#000;
    classDef blue fill:#e6f3ff,stroke:#0066cc,stroke-width:2px,color:#000;
    classDef green fill:#e6fffa,stroke:#00cc99,stroke-width:2px,color:#000;
    classDef orange fill:#fff0e6,stroke:#ff6600,stroke-width:2px,color:#000;
    classDef purple fill:#f3e6ff,stroke:#6600cc,stroke-width:2px,color:#000;

    %% Frame 1: User + Entry Point
    subgraph Frame1 ["Frame 1: User & Input"]
        direction LR
        User[User / Researcher] --> Query[Research Query]
    end
    class Frame1 gray

    %% Frame 2: Orchestrator Layer
    subgraph Frame2 ["Frame 2: Main Orchestrator (The Brain)"]
        Orchestrator["Axiom-One Orchestrator<br/>(Global Objective Only)"]
        Decomposer["Task Decomposer<br/>(Atomic JSON Graph)"]
        Manager["Subagent Manager<br/>(Spawns Isolated Agents)"]

        Orchestrator --> Decomposer
        Decomposer --> Manager
    end
    class Frame2 blue

    %% Frame 3: Subagent Execution Layer
    subgraph Frame3 ["Frame 3: Isolated Research Subagents"]
        direction LR
        subgraph S1 [Subagent A]
            TaskA["Task: Survey Techniques"]
        end
        subgraph S2 [Subagent B]
            TaskB["Task: Validate Claims"]
        end
        subgraph S3 [Subagent C]
            TaskC["Task: Extract Tradeoffs"]
        end

        %% Note: Context = Ephemeral
    end
    class Frame3 green

    %% Frame 4: Validation + Synthesis
    subgraph Frame4 ["Frame 4: Validation & Synthesis"]
        Validator["Reasoning Validator<br/>(Hostile Peer Review)"]
        Synthesizer["Synthesis Engine<br/>(Merge & Conflict Resolution)"]

        Validator --> Synthesizer
    end
    class Frame4 orange

    %% Frame 5: Outputs + Persistence
    subgraph Frame5 ["Frame 5: Artifacts"]
        FinalAnswer["Final Answer<br/>(With Uncertainty)"]
        Trace["Reasoning Trace<br/>(Tree + Validation)"]
        Evidence["Evidence Store<br/>(Vector + Metadata)"]
    end
    class Frame5 purple

    %% Connections between Frames
    Query --> Orchestrator
    Manager -- Spawns --> S1
    Manager -- Spawns --> S2
    Manager -- Spawns --> S3

    S1 -- Clean Result --> Orchestrator
    S2 -- Clean Result --> Orchestrator
    S3 -- Clean Result --> Orchestrator

    Orchestrator -- All Results --> Validator
    Synthesizer --> FinalAnswer
    Synthesizer --> Trace
    Synthesizer --> Evidence

    %% Notes
    Note1["Note: Failures die in Frame 3.<br/>Noise never reaches Orchestrator."]
    Note1 -.- Frame3
```

---

## 3. Core Modules (The Real Meat)

### 1️⃣ Task Decomposer (Brain Stem)
*   **Input:** User query.
*   **Output:** Structured task graph (JSON).
*   **Implementation:** Gemini 3 Pro in *Thinking Mode* with deterministic temperature.

### 2️⃣ Research Executor Pool
*   **Concept:** Each task becomes an **independent execution unit**.
*   **Architecture:** Stateless workers. Each worker receives a task + context slice, calls the LLM, and stores the result + confidence score.
*   **Why:** No long chats. No rambling. Context is ephemeral.

### 3️⃣ Evidence Store (Truth Backbone)
*   **Vector Store:** Task outputs and semantic chunks.
*   **Metadata DB:** Task ID, Assumptions, Confidence, Source type.
*   *Prevents hallucinations from pretending to be facts.*

### 4️⃣ Reasoning Validator (The Adult in the Room)
*   **Checks:** Contradictions, unsupported claims, circular reasoning.
*   **Implementation:** Secondary Gemini pass prompted as a "hostile peer reviewer".

### 5️⃣ Synthesis Engine
*   **Responsibilities:** Merge task outputs, resolve conflicts, and produce the final answer with a "What could be wrong" section.

---

## 4. MVP Scope & Tech Stack

### Tech Stack
*   **Current MVP (This Repo):** React + Vite (Frontend Focus)
*   **Target Architecture:** Next.js + FastAPI + Python (Full Agentic System)
*   **LLM:** Gemini 3 Pro (Thinking enabled)
*   **Storage:** Vector DB (Pinecone/FAISS) + Postgres/SQLite

### MVP Features (Locked)
*   ✅ **Input:** Single complex research question.
*   ✅ **Output:** Final synthesized answer, task breakdown, reasoning trace.
*   ✅ **UI:** Simple web interface (Task Tree left, Outputs right).

### NOT in MVP
*   Multi-user collaboration
*   Long-term memory (beyond current session)
*   Real-time streaming
*   Agent self-improvement loops

---

## 5. Development Setup

This repository contains the **Frontend** implementation using React and Vite.

### Prerequisites
*   Node.js (v18+)
*   Google Gemini API Key

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/bbfcfmars/AXiOM.git
    cd AXiOM
    ```

2.  **Install dependencies:**
    ```bash
    npm install
    ```

3.  **Configure Environment:**
    Copy the example environment file and add your Gemini API key:
    ```bash
    cp .env.example .env
    ```
    Then edit `.env` and replace `your_gemini_api_key_here` with your actual API key.
    
    > 💡 **Get your free API key:** https://aistudio.google.com/apikey

4.  **Run the Development Server:**
    ```bash
    npm run dev
    ```
    
    The app will be available at **http://localhost:3000**

---

## 6. Why Axiom-One Matters

Most “AI agents” optimize for fluency, hide uncertainty, and collapse under scrutiny.
**Axiom-One optimizes for correctness under pressure**, makes uncertainty explicit, and treats reasoning as a first-class artifact.
