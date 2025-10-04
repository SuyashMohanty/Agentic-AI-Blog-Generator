
# Agentic AI Blog Generator 🤖📝

[![Python Version](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100.0-green.svg)](https://fastapi.tiangolo.com/)
[![LangChain](https://img.shields.io/badge/LangChain-blueviolet)](https://www.langchain.com/)
[![LangGraph](https://img.shields.io/badge/LangGraph-black)](https://langchain-ai.github.io/langgraph/)

An intelligent, agentic system designed to automatically generate high-quality, SEO-friendly blog posts. This project leverages the power of **LangGraph** to create dynamic, stateful workflows and **Groq's Llama 3.1 LLM** for lightning-fast content generation. It also includes a feature to translate the generated content into multiple languages.

## ✨ Features

* **SEO-Optimized Titles**: Automatically generates catchy and search-engine-optimized headlines for your blog posts.
* **Markdown Content Generation**: Creates well-structured and formatted blog content using Markdown.
* **Multi-Language Translation**: Seamlessly translates blog content into specified languages (e.g., Hindi, French).
* **Agentic Workflows**: Built with LangGraph to manage complex, multi-step generation and translation processes.
* **High-Speed Inference**: Utilizes the Groq API for near-instant responses from the Llama 3.1 language model.
* **Simple API**: Exposed via a clean and easy-to-use FastAPI endpoint.

---

## 🛠️ Technologies Used

* **Framework**: FastAPI
* **Orchestration**: LangChain, LangGraph
* **LLM Provider**: Groq (Llama 3.1 8B)
* **Server**: Uvicorn
* **Data Validation**: Pydantic
* **Core**: Python

---

## 📂 Project Structure

Here is an overview of the project's directory structure.

![Project Directory Structure](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/Project%20Structure.png)

---

## 💻 Codebase Overview

This section provides a detailed breakdown of each file in the project and its role.

### `app.py`

This is the main entry point for the application. It sets up the FastAPI server and defines the API endpoints.

* **Responsibilities**:
    * Initializes the FastAPI application.
    * Loads environment variables (like API keys) using `dotenv`.
    * Defines the `POST /blogs` endpoint, which is the core of the service.
    * Parses the incoming request to get the `topic` and optional `language`.
    * Selects the appropriate LangGraph workflow (`topic` or `language`) based on the input.
    * Invokes the graph and returns the final generated and/or translated blog post as a JSON response.
    * Uses `uvicorn` to run the web server.

### `graph_builder.py`

This file is the architect of the agentic workflows. It uses LangGraph to define the structure, nodes, and edges for both the blog generation and translation processes.

* **Responsibilities**:
    * Defines the `GraphBuilder` class, which constructs the computation graphs.
    * `build_topic_graph()`: Creates a simple, linear graph that first creates a title and then generates content.
    * `build_language_graph()`: Creates a more complex graph with conditional logic. After generating content, it routes the workflow to a specific translation node (`hindi` or `french`) based on the input language.
    * Uses a state object (`BlogState`) to pass data between nodes.
    * The `setup_graph()` method compiles the defined graph, making it ready for execution.

### `blog_node.py`

This file contains the core logic for each individual step (or "node") in the LangGraph workflows. Each function in the `BlogNode` class represents an action that the agent can perform.

* **Responsibilities**:
    * `title_creation()`: Prompts the LLM to generate an SEO-friendly title for the given topic.
    * `content_generation()`: Prompts the LLM to write the full blog content in Markdown format.
    * `translation()`: Prompts the LLM to translate the generated content into a specified language.
    * `route_decision()`: Implements the logic for conditional routing. It checks the target language in the state and decides which translation node the graph should proceed to next.

### `blogstate.py`

This file defines the data structure for the state that is passed between nodes in the LangGraph. A consistent state is crucial for managing the flow of data through the graph.

* **Responsibilities**:
    * Defines a Pydantic `BaseModel` called `Blog` to structure the `title` and `content`.
    * Defines a `TypedDict` called `BlogState`, which is the main state object containing the `topic`, the `blog` object, and the `current_language`.

### `groqllm.py`

This is a utility module for initializing the connection to the Groq API and configuring the language model.

* **Responsibilities**:
    * Defines the `GroqLLM` class to encapsulate the LLM setup.
    * The `get_llm()` method retrieves the Groq API key from environment variables.
    * It initializes the `ChatGroq` instance with the correct model name (`llama-3.1-8b-instant`) and returns the configured LLM object, ready to be used by the nodes.

### `requirements.txt`

This is the standard Python dependency file. It lists all the external packages that the project needs to run.

* **Purpose**:
    * Ensures a reproducible environment by listing exact dependencies like `fastapi`, `uvicorn`, `langchain`, `langgraph`, and `langchain_groq`.
    * The command `pip install -r requirements.txt` uses this file to install everything needed for the project.

### `main.py` and `langgraph.json`

These files are primarily for development and deployment.

* **`main.py`**: A simple, placeholder entry point script.
* **`langgraph.json`**: A configuration file used by the `langgraph-cli` tool, which can be used to deploy, serve, and interact with LangGraph agents in a production environment.

---

## 🚀 Getting Started

Follow these instructions to get the project up and running on your local machine.

### Prerequisites

* Python 3.9 or higher
* A Groq API Key. You can get one from the [Groq Console](https://console.groq.com/keys).
* A LangSmith API Key (optional, for tracing). You can get one from the [LangSmith Console](https://smith.langchain.com/).

### Installation

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/your-username/agentic-ai-blog-generator.git](https://github.com/your-username/agentic-ai-blog-generator.git)
    cd agentic-ai-blog-generator
    ```

2.  **Create a virtual environment and activate it:**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
    ```

3.  **Install the required dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Create a `.env` file** in the root directory and add your API keys:
    ```env
    GROQ_API_KEY="gsk_YourGroqApiKey"
    LANGCHAIN_API_KEY="ls__YourLangSmithApiKey"
    ```

---

## 🏃‍♀️ How to Run

To start the FastAPI application, run the following command from the root directory:

```bash
uvicorn app:app --host 0.0.0.0 --port 8000 --reload
```
---

## ⚙️ API Usage Examples

The application exposes a single endpoint `/blogs` that accepts `POST` requests.

### Example 1: Generate a Blog Post (English)

This example shows how to generate a blog post by providing only a topic.

**Postman Request & Response:**

![Postman request for a blog post without language specification](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/PostMan%20without%20Language.png)

### Example 2: Generate and Translate a Blog Post

This example shows how to generate a blog post and translate it by providing a topic and a language.

**Postman Request & Response (with Translation):**

![Postman request for a blog post with language translation to Hindi](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/PostMan%20With%20Language.png)

---

## 🔄 Workflow Explanation

The project uses LangGraph to define and execute two distinct agentic workflows, which can be visualized and traced in LangSmith.

### Workflow 1: `build_topic_graph` (Topic Only)

This graph is triggered when only a `topic` is provided. It follows a simple path: generating a title and then the content.

**LangGraph Visualization:**

![Diagram of the LangGraph workflow for generating a blog post without translation](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/LangGraph%20Without%20Language.png)

**Execution Trace:**

![Trace log of the LangGraph workflow running for a blog post without translation](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/LangGraph%20Without%20Language%20Running.png)

### Workflow 2: `build_language_graph` (Topic + Language)

This graph is triggered when both a `topic` and a `language` are provided. It uses conditional routing to translate the content after it has been generated.

**LangGraph Visualization:**

![Diagram of the LangGraph workflow for generating and translating a blog post](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/LangGraph%20Language%20Blog%20Workkflow.png)

**Execution Trace:**

![Trace log of the LangGraph workflow running for a blog post with translation](https://github.com/SuyashMohanty/Agentic-AI-Blog-Generator/blob/main/Images/LangGraph%20Running.png)

