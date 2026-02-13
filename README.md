# Generative UI: Guide to Building Dynamic Interfaces

<img width="579" height="381" alt="Generative UI Illustration" src="https://github.com/user-attachments/assets/9ec87ea4-c9af-4c5d-96ea-3d33c3f86b11" />

---

## Introduction

Generative UI is an emerging paradigm in which AI systems not only power the logic behind an application but also generate the user interface itself in real time. 

Traditional graphical user interfaces (GUIs) rely on static layouts defined by designers, but generative UI allows models to assemble custom interfaces—cards, charts, forms, maps, diagrams—on the fly based on the user's goals and context. 

This shift turns AI from a "black box" text responder into a visual collaborator that uses dynamic layouts to communicate its reasoning, request permissions and display results. By producing interactive experiences rather than walls of text, generative UIs enhance transparency, reduce cognitive load and unlock new applications such as interactive research dashboards, adaptive shopping flows and agentic workflows.

---

## Why Generative UI Matters

### **Structured Input and Feedback**
Generative UI provides the user with forms, tables or selectors, encouraging precise input and enabling the AI agent to gather the necessary parameters rather than interpreting long text descriptions.

### **Task-Specific Layouts**
The interface adapts to the task; for example, a travel query can trigger itinerary cards with flight details, while a vulnerability analysis agent can display CVE timelines and exploitability tables.

### **Visualized Reasoning**
Agents can externalize their planning and intermediate results, building trust by showing progress and tools used, rather than hiding reasoning steps in hidden chains of thought.

### **Personalization and Accessibility**
AI-generated interfaces can be personalized to a user's preferences or device characteristics. This fosters inclusive design because layouts can adjust for different screen sizes or user roles.

### **Efficient Development**
Designers and developers can specify high-level components or data schemas instead of hand-coding every layout. The AI fills in details, saves time and scales personalization across many users.

---

## Key Patterns and Specifications

Research and industry efforts have converged on three main patterns for implementing generative UI. Each pattern differs in the degree of freedom given to the agent and the responsibilities of the client (front-end).

### 1. Static Generative UI (AG-UI Pattern)

In static generative UI, the front end exposes a fixed set of UI components (cards, lists, charts). The agent chooses which component to use and provides the data; the front end handles rendering and state management. 

CopilotKit's AG-UI protocol uses this pattern: the agent streams tool calls and status updates, and the client maps them to pre-defined React components. 

**Pros:** Easy to implement and safe because the set of components is controlled  
**Cons:** Limits flexibility and may not support novel layouts

### 2. Declarative Generative UI (A2UI and Open-JSON-UI)

Declarative patterns allow the agent to return a structured description of the interface in JSON. The client reads the JSON and renders the interface using its native widgets. 

Google's A2UI specification and OpenAI's Open-JSON-UI follow this approach. A2UI emphasizes:
- Simplicity (a flat list of components)
- Progressive streaming of UI messages
- Platform-agnostic descriptions
- Separate data/update messages

It ensures that LLMs can generate components easily and that clients can map abstract descriptions (e.g., `{"type":"form","fields":[...]}`) to platform-specific widgets. 

**Pros:** Balance flexibility and safety; platform-agnostic; streamable  
**Cons:** Requires a capable renderer; may still restrict unique layouts

### 3. Open-Ended Generative UI (MCP-UI)

Open-ended generative UI gives the agent near-full control over the visual experience. Microsoft, Shopify and others propose MCP-UI, an extension of the Model Context Protocol where the agent can return resources like HTML, CSS or remote DOM definitions to be rendered within a sandboxed iframe.

This pattern enables complex experiences (product selectors, charts, interactive simulations) because the agent can write HTML and even include external resources. 

To maintain security and control, the specification introduces an **intent-based messaging system**: user actions such as "view_details" or "checkout" are reported to the agent, which approves further tool calls. 

**Pros:** Maximum expressiveness; supports rich commerce and simulation UIs  
**Cons:** Security and performance concerns; complex to implement

---

## Comparison Table

| Pattern / Spec | Core Idea | Pros | Cons |
|----------------|-----------|------|------|
| **Static Generative UI (AG-UI)** | Agent selects among predefined components; client handles rendering | Safe, easy to implement, well-defined tool flows | Limited flexibility; cannot invent new layouts |
| **Declarative Generative UI (A2UI, Open-JSON-UI)** | Agent returns JSON describing components and data | Flexible yet structured; platform-agnostic; streamable | Requires a capable renderer; may still restrict unique layouts |
| **Open-Ended Generative UI (MCP-UI)** | Agent sends HTML/CSS or remote DOM; client renders in sandbox | Maximum expressiveness; supports rich commerce and simulation UIs | Security and performance concerns; complex to implement |

---

## System Components and Workflow

Generative UI systems share common building blocks:

### 1. Tool Access
The agent must have access to functions (e.g., image generation, search, remote APIs) to gather data and produce content. Google's generative UI system uses tool endpoints to get images, maps and other content.

### 2. Prompt Design
Carefully crafted system instructions guide the model. Prompts often include goals, planning instructions, examples of UI formats and technical guidelines (such as using only supported components). This ensures the agent returns safe, consistent layouts.

### 3. Post-Processing
After the model produces a UI specification, a post-processing step checks for syntax errors, normalizes component types and handles missing fields. In MCP-UI, the post-processor might also sanitize HTML.

### 4. Renderer / Client
The front-end interprets the UI description (component list, HTML or JSON) and renders it. It streams updates as the agent sends partial results to improve responsiveness.

### 5. Intent and Event Handling
When the user interacts with a generated UI (clicking a button, submitting a form), the client captures the event as an intent and sends it back to the agent for approval and next actions. This ensures the agent remains in control of tool calls.

---

## Examples and Case Studies

### Research Dashboards
A vulnerability analysis agent can display a table of CVE scores, a timeline of patch releases and a chart of exploit probabilities, letting the user filter the dataset via a generated form. Instead of reading a long paragraph, the user interacts with dynamic charts and selects which vulnerability to investigate.

### Travel Planning
A generative UI agent asked to "plan a trip to Tokyo" can display itinerary cards for flights, hotels and attractions. Each card includes images, prices and a booking button. The user can adjust dates via a generated date picker, and the agent updates recommendations in real time.

### E-Commerce Flows
MCP-UI supports product selectors, variant pickers and checkout flows. A shopping agent might show a carousel of products, and clicking "view details" triggers the agent to fetch inventory data and update the UI. The intent system ensures that the agent approves purchases before executing them.

### Education and Simulations
Generative UI can produce interactive visualizations—like fractal explorers or step-by-step math proof explorers—by combining code execution with dynamic charts. Google's demonstration showed AI generating fractal graphics and interactive math explanations for user prompts.

---

## Getting Started: Step-by-Step Guide

### 1. Define the User Task and Data Sources
Determine what tasks your agent will support (e.g., research, shopping) and what tools it needs. For instance, a research agent might need search, table rendering and charting functions.

### 2. Choose a Generative UI Pattern
- Use **static patterns** if the tasks fit within known components
- Choose **declarative patterns** like A2UI if you want flexibility while keeping control
- Opt for **open-ended patterns** (MCP-UI) only when you need full custom UIs such as complex commerce experiences

### 3. Design System Instructions
Write a prompt that explains the goal, the allowed components and examples of the desired UI structure. Include guidelines on style (e.g., use single column cards, short titles) to maintain consistency.

### 4. Implement Tool Endpoints
Expose necessary tools (search, calculation, map retrieval, etc.) as functions accessible by the agent. In frameworks like CopilotKit, register these tools so the agent can call them and stream results.

### 5. Parse the Agent's UI Output
Write a parser or adopt a library that reads the JSON or HTML produced by the agent and maps it to your front-end components. Handle streaming messages and partial updates to keep the UI responsive.

### 6. Set Up Intent and Event Handling
When the user interacts with the generated UI, capture events (form submission, button click) as intents and send them back to the agent. The agent should decide whether to proceed (e.g., call a purchase API, run a command).

### 7. Enforce Security and Performance
For open-ended patterns, sandbox the agent's HTML in an iframe, sanitize CSS/JavaScript and limit external requests. For declarative patterns, validate component types to prevent injection. Consider generation time; streaming partial UIs can improve perceived responsiveness.

### 8. Test and Iterate
Evaluate whether the generated UI improves user satisfaction. Use metrics such as task completion time, error rate and subjective preference. Research shows that users overwhelmingly prefer generative UI pages over plain text responses, though human-designed pages still perform best.

---

## Best Practices

### Consistent Styling
Provide a style guide or CSS snippet so that generated components have consistent fonts, colors and spacing.

### Limit Component Vocabulary
In declarative patterns, restrict the set of available components (e.g., cards, lists, charts) to ensure the agent doesn't misuse UI primitives.

### Progressive Disclosure
Stream intermediate results and break complex tasks into smaller subtasks to avoid overwhelming the user. Use collapsible sections and tabs to organize information.

### Accessibility
Ensure generated UIs are accessible. Provide alt text for images, ensure sufficient contrast and support keyboard navigation.

### User Control
Always keep the agent in control of side effects (API calls, purchases). Use the intent pattern to require explicit confirmation before executing actions.

---

## Challenges and Considerations

### Generation Time and Latency
Rendering complex interfaces can be slower than returning plain text. Streaming partial UIs mitigates this issue but cannot eliminate latency entirely.

### Model Hallucination
LLMs may generate components or data that are incorrect or inconsistent. Post-processing and tool verification are essential.

### Security Risks
Open-ended generative UI can expose the client to malicious HTML/CSS. Always sandbox external code and enforce a strict content security policy.

### Tool and API Reliability
Agents rely on external tools; network failures or API changes can break UI generation. Implement retries and fallbacks.

---

## Resources and Further Reading

- **Generative UI Paper** – The foundational paper introduces generative UI as a new modality, describing emergent capabilities, system components and evaluation results
- **Google Research Blog** – Explains the generative UI approach deployed in the Gemini app, including examples and challenges
- **CopilotKit Developer's Guide** – Offers practical guidance for building generative UIs using AG-UI, A2UI and open-ended patterns
- **A2UI Specification** – Provides design requirements and streaming architecture for declarative UI generation
- **MCP-UI Articles** – Shopify and Vellum blogs detail the commerce-oriented MCP-UI protocol and its intent system

---

## Conclusion

This guide introduces generative UI as a transformative approach to building interactive applications. By leveraging AI to generate user interfaces at runtime, developers can create experiences that adapt to each user, provide richer context and bridge the gap between conversation and execution. 

Following the patterns, guidelines and best practices outlined here will help you start experimenting with generative UI in your own projects.