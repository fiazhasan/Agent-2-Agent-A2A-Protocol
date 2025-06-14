## 📚 A2A Math Agents Project
This project demonstrates the A2A (Agent-to-Agent) protocol using the python-a2a library. It includes two agents: AddAgent for adding two numbers and SubtractAgent for subtracting two numbers. These agents are networked together, and queries are routed to the appropriate agent using an intelligent router. This project serves as a foundation for building advanced, modular agent-based systems.

## 👤 Author

- **Name**: `Fiaz Ul Hasan`
- **Email**: `fiazhasan4161@example.com`
- **Purpose**: This project aims to help developers understand the `A2A protocol` and create networked agents. It’s a stepping stone for building complex, scalable agent-based applications.

## 📂 Folder Structure
```
math_agents_project/
│
├── agents/
│ ├── add_agent.py
│ ├── subtract_agent.py
│
├── network/
│ ├── math_network.py
│
├── requirements.txt
├── README.md
```
## File Descriptions

- `agents/add_agent.py`: Defines the AddAgent class to handle addition tasks.
- `agents/subtract_agent.py`: Defines the SubtractAgent class to handle subtraction tasks.
- `network/math_network.py`: Configures the agent network and routes queries to the appropriate agent.
- `requirements.txt`: Lists project dependencies.
- `README.md`: This documentation file.

## 🛠 Prerequisites

- `Python 3.8+`: Ensure Python is installed (`python --version`).
- `pip`: Python’s package manager for installing dependencies.
- `Virtual Environment`: Recommended for isolating dependencies.

## 🚀 Setup Instructions

#### 1. Create the Project Directory
```
   mkdir math_agents_project
   cd math_agents_project
```
Explanation:  This creates a dedicated folder for the project and navigates into it to keep all files organized.
#### 2. Set Up a Virtual Environment
```
python -m venv .venv
# On Windows: .venv\Scripts\activate # On MacBook source .venv/bin/activate 
```
Explanation: A virtual environment isolates project dependencies, preventing conflicts with other Python projects. The `.venv` folder stores the environment, and activating it ensures commands like `pip` and `python` use the isolated setup. 
#### 3. Install Dependencies
```
pip install -r requirements.txt
```
Explanation: The `requirements.txt` file lists `python-a2a[all]`, which includes the A2A library and all optional dependencies (e.g., server, OpenAI, Anthropic). Running this command installs everything needed to run the agents and network. 

#### 4. Create the Folder Structure
```
mkdir agents network
```
Explanation: The agents folder will contain individual agent scripts (ad`d_agent.py, subtract_agent.py`), and the network folder will hold the network configuration (math_ne   ). This structure keeps the codebase modular and organized. 

#### 5. Create the Files
Below are the contents of each file with detailed explanations of their logic.
##### 📄 agents/add_agent.py
```Python
from python_a2a import A2AServer, skill, agent, run_server, TaskStatus, TaskState
import re

@agent(
name="Add Agent",
description="Performs addition of two numbers",
version="1.0.0"
)
class AddAgent(A2AServer):

    @skill(
        name="Add Numbers",
        description="Add two numbers provided in the query",
        tags=["math", "addition"]
    )
    def add_numbers(self, num1, num2):
        """Add two numbers."""
        return f"The sum of {num1} and {num2} is {num1 + num2}"

    def handle_task(self, task):
        # Extract numbers from message
        message_data = task.message or {}
        content = message_data.get("content", {})
        text = content.get("text", "") if isinstance(content, dict) else ""

        # Find numbers in the text using regex
        numbers = re.findall(r'\d+\.?\d*', text)
        if len(numbers) >= 2:
            num1, num2 = float(numbers[0]), float(numbers[1])
            result = self.add_numbers(num1, num2)
            task.artifacts = [{
                "parts": [{"type": "text", "text": result}]
            }]
            task.status = TaskStatus(state=TaskState.COMPLETED)
        else:
            task.status = TaskStatus(
                state=TaskState.INPUT_REQUIRED,
                message={"role": "agent", "content": {"type": "text",
                         "text": "Please provide two numbers to add, e.g., 'Add 5 and 3'."}}
            )
        return task

if **name** == "**main**":
agent = AddAgent()
run_server(agent, port=5001)
```

#### Logical Explanation:

- `Imports`: The python_a2a library provides classes and decorators (`A2AServer, @agent, @skill, etc`.) for building agents. re is used for regular expressions to extract numbers.
- `Agent Definition`: The @agent decorator defines the agent’s metadata (`name, description, version`). AddAgent inherits from A2AServer to handle incoming tasks.
- `Skill Definition`: The `@skill` decorator marks the add_numbers method as a skill, which takes two numbers and returns their sum as a formatted string.
- `Task Handling`: The handle_task method processes incoming tasks:
It extracts the text from the task’s message.
Uses regex `(`r'\d+\.?\d\*'``) to find numbers in the text (e.g., "5" or "5.2").
If two or more numbers are found, it calls `add_numbers` and sets the task’s result in `task.artifacts`.
If fewer than two numbers are found, it requests more input by setting `TaskState.INPUT_REQUIRED`.

- `Server`: The `run_server` function starts the agent as a server on port `5001`, making it accessible via HTTP.

##### 📄 agents/subtract_agent.py
```python
from python_a2a import A2AServer, skill, agent, run_server, TaskStatus, TaskState
import re

@agent(
name="Subtract Agent",
description="Performs subtraction of two numbers",
version="1.0.0"
)
class SubtractAgent(A2AServer):

    @skill(
        name="Subtract Numbers",
        description="Subtract one number from another",
        tags=["math", "subtraction"]
    )
    def subtract_numbers(self, num1, num2):
        """Subtract two numbers."""
        return f"The difference between {num1} and {num2} is {num1 - num2}"

    def handle_task(self, task):
        # Extract numbers from message
        message_data = task.message or {}
        content = message_data.get("content", {})
        text = content.get("text", "") if isinstance(content, dict) else ""

        # Find numbers in the text using regex
        numbers = re.findall(r'\d+\.?\d*', text)
        if len(numbers) >= 2:
            num1, num2 = float(numbers[0]), float(numbers[1])
            result = self.subtract_numbers(num1, num2)
            task.artifacts = [{
                "parts": [{"type": "text", "text": result}]
            }]
            task.status = TaskStatus(state=TaskState.COMPLETED)
        else:
            task.status = TaskStatus(
                state=TaskState.INPUT_REQUIRED,
                message={"role": "agent", "content": {"type": "text",
                         "text": "Please provide two numbers to subtract, e.g., 'Subtract 5 from 8'."}}
            )
        return task

if **name** == "**main**":
agent = SubtractAgent()
run_server(agent, port=5002)
```
#### Logical Explanation:

- `Similar Structure`: This file is nearly identical to `add_agent.py` but handles subtraction.
- `Skill`: The `subtract_numbers` method subtracts num2 from num1 and returns a formatted result.
- `Task Handling`: Like AddAgent, it extracts numbers using regex and processes the task. The only difference is the operation (subtraction) and the error message.
- `Port`: Runs on port `5002` to avoid conflicts with AddAgent.

#### 📄 network/math_network.py
```python
from python_a2a import AgentNetwork, A2AClient, AIAgentRouter

# Create an agent network

network = AgentNetwork(name="Math Assistant Network")

# Add agents to the network

network.add("add", "http://localhost:5001")
network.add("subtract", "http://localhost:5002")

# Create a router to intelligently direct queries

router = AIAgentRouter(
llm_client=A2AClient("http://localhost:5000/openai"),
agent_network=network
)

# Route a query to the appropriate agent

def run_query(query):
agent_name, confidence = router.route_query(query)
print(f"Routing to {agent_name} with {confidence:.2f} confidence")

    # Get the selected agent and ask the question
    agent = network.get_agent(agent_name)
    response = agent.ask(query)
    print(f"Response: {response}")

    # List all available agents
    print("\nAvailable Agents:")
    for agent_info in network.list_agents():
        print(f"- {agent_info['name']}: {agent_info['description']}")

if **name** == "**main**": # Example queries
run_query("Add 10 and 5")
run_query("Subtract 8 from 12")
```
#### Logical Explanation:

- `Imports`: `AgentNetwork` manages the collection of agents, `A2AClient` interacts with `LLMs`, and `AIAgentRouter` decides which agent handles a query.
- `Network Creation`: AgentNetwork is initialized with a name ("`Math Assistant Network`").
### Adding Agents:
The network.add method registers agents by name and URL. For example:
"`add`" is linked to `http://localhost:5001` (where AddAgent runs).
"`subtract`" is linked to `http://localhost:5002` (where SubtractAgent runs).
- `How It Works:` The add method stores the agent’s metadata (name, URL) in the network’s registry, allowing the router to communicate with them via HTTP.

### Router Setup:
The AIAgentRouter uses an LLM client (A2AClient) to analyze queries and decide which agent is best suited.
`llm_client=A2AClient("http://localhost:5000/openai")` points to an LLM service (e.g., OpenAI-compatible API). In practice, you’d need to set up this service or mock it for testing.
- `Purpose of Router`: The router parses the query (e.g., "Add 10 and 5") and uses the LLM to determine the operation (addition) and route it to the add agent. It returns the agent name and a confidence score.

- `Query Execution`:
The `run_query` function:
- Routes the query using `router.route_query`.
- Retrieves the chosen agent with `network.get_agent`.
- Sends the query to the agent using `agent.ask`.
- Prints the response and lists all agents.

`Example Queries`: The script runs two test queries to demonstrate the network’s functionality.

#### 📄 requirements.txt
```
python-a2a[all]
```
- `Explanation`: This specifies the `python-a2a` library with all optional dependencies (e.g., server, OpenAI, Anthropic). The` [all]` extra ensures all features (like networking and LLM integrations) are available.
## 6. Run the Project

#### Start the Agents:

Open three terminals and activate the virtual environment in each:

```
source .venv/bin/activate # On Windows: .venv\Scripts\activate
```
- Terminal 1 (AddAgent):
```
python agents/add_agent.py
```
- Terminal 2 (SubtractAgent):
```
python agents/subtract_agent.py
```
- Terminal 3 (Network):
```
python network/math_network.py
```
Explanation: Each agent runs as a server on its assigned port (`5001, 5002`). The network script communicates with them via HTTP. Running them separately allows the agents to operate concurrently.

#### Test the Agents:

The `math_network.py` script executes example queries ("Add 10 and 5", "Subtract 8 from 12").
Modify run_query calls in `math_network.py` to test other queries.

## 7. Example Output
```
INFO:python_a2a.client.network:Added agent 'add' from URL: http://localhost:5001
INFO:python_a2a.client.network:Added agent 'subtract' from URL: http://localhost:5002
Routing to add with 0.10 confidence
Response: The sum of 10.0 and 5.0 is 15.0

Available Agents:
- add: Performs addition of two numbers
- subtract: Performs subtraction of two numbers
Routing to subtract with 0.10 confidence
Response: The difference between 8.0 and 12.0 is -4.0

Available Agents:
- add: Performs addition of two numbers
- subtract: Performs subtraction of two numbers

```
`Explanation`: The router identifies the operation, sends the query to the correct agent, and displays the result. The confidence score reflects the router’s certainty in its choice.

## ➕ Adding a New Agent to the Network
To add a new agent (e.g., a MultiplyAgent for multiplication), follow these steps:
#### Step 1: Create the New Agent File

In the agents folder, create a new file, e.g., `multiply_agent.py`.
Use the following template (modeled after add_agent.py and subtract_agent.py):
```python
from python_a2a import A2AServer, skill, agent, run_server, TaskStatus, TaskState
import re

@agent(
name="Multiply Agent",
description="Performs multiplication of two numbers",
version="1.0.0"
)
class MultiplyAgent(A2AServer):

    @skill(
        name="Multiply Numbers",
        description="Multiply two numbers provided in the query",
        tags=["math", "multiplication"]
    )
    def multiply_numbers(self, num1, num2):
        """Multiply two numbers."""
        return f"The product of {num1} and {num2} is {num1 * num2}"

    def handle_task(self, task):
        # Extract numbers from message
        message_data = task.message or {}
        content = message_data.get("content", {})
        text = content.get("text", "") if isinstance(content, dict) else ""

        # Find numbers in the text using regex
        numbers = re.findall(r'\d+\.?\d*', text)
        if len(numbers) >= 2:
            num1, num2 = float(numbers[0]), float(numbers[1])
            result = self.multiply_numbers(num1, num2)
            task.artifacts = [{
                "parts": [{"type": "text", "text": result}]
            }]
            task.status = TaskStatus(state=TaskState.COMPLETED)
        else:
            task.status = TaskStatus(
                state=TaskState.INPUT_REQUIRED,
                message={"role": "agent", "content": {"type": "text",
                         "text": "Please provide two numbers to multiply, e.g., 'Multiply 4 and 5'."}}
            )
        return task

if **name** == "**main**":
agent = MultiplyAgent()
run_server(agent, port=5003)
```
`Explanation`:
The agent follows the same structure as AddAgent and SubtractAgent.
It defines a multiply_numbers skill to multiply two numbers.
It runs on a unique port (e.g., 5003) to avoid conflicts with other agents.

#### Step 2: Update the Network

- Open `network/math_network.py.`
- Add the new agent to the network by inserting the following line after the existing `network.add` calls:
```
network.add("multiply", "http://localhost:5003")
```
`Explanation`:

The network.add method registers the new agent with a unique name (multiply) and its URL (`http://localhost:5003`).
The router will use the agent’s metadata (from the @agent decorator) and the query content to decide when to route queries to this agent (e.g., for queries like "Multiply 4 and 5").

#### Step 3: Run the New Agent

#### Open a new terminal and activate the virtual environment:
```
source .venv/bin/activate # On Windows: .venv\Scripts\activate
```
#### Start the new agent:
```
python agents/multiply_agent.py
```
`Explanation`: The new agent must be running as a server on its designated port (5003) for the network to communicate with it.
### Step 4: Test the New Agent

Update `math_network.py` to include a test query for the new agent, e.g., add:
```
run_query("Multiply 4 and 5")
```
- Run the network script again:
```
python network/math_network.py
```
`Explanation`: The router will analyze the query, route it to the multiply agent, and display the result (e.g., "The product of 4 and 5 is 20").
## Why It Works

- The AgentNetwork maintains a registry of agents, mapping names to URLs.
- The AIAgentRouter uses the LLM client to parse queries and match them to agent skills based on tags and descriptions (e.g., "math", "multiplication").
- Adding a new agent involves creating the agent script, assigning a unique port, and registering it in the network.

## 🌐 About the A2A Protocol
- The A2A (Agent-to-Agent) protocol enables modular, networked agents to communicate and collaborate. Key features:

`Modularity`: Each agent handles specific tasks (e.g., addition, subtraction).
`Networking`: Agents are linked via HTTP, allowing distributed systems.
`Intelligent Routing`: The `AIAgentRouter` uses an LLM to direct queries to the best agent.This project demonstrates these concepts and can be extended for advanced use cases (e.g., integrating with real LLMs or adding complex skills).

## 🔧 Troubleshooting

`Port Conflicts`: If ports 5001, 5002, or 5003 are in use, change them in the agent scripts and `math_network.py.`
`Missing Dependencies`: Re-run pip install -r `requirements.txt`.
`LLM Client Error`: The router expects an LLM at `http://localhost:5000/openai`. 
- For testing, mock this endpoint or set up an LLM service.

## 🚀 Extending the Project

`Add More Agents`: Follow the steps above to create agents for operations like division or exponentiation.
`Enhance Input Parsing`: Improve regex to handle complex inputs (e.g., "Add five and three").
`Integrate LLMs`: Use real LLM services (e.g., OpenAI, Anthropic) for smarter routing.
`Error Handling`: Add validation for non-numeric inputs.

This project provides a solid foundation for exploring the A2A protocol. It’s ideal for learning how to build scalable, agent-based systems. Happy coding! 🎉
