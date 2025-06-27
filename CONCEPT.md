# Shafee's Personal Notes:

## Intro to LangGraph(LG):
1. LG is a low-level orchestration framework used to bring more control over a complex agentic system/LLM-powered app
2. Using LG, we can bring non-linear workflow in the system.
3. Using LG, we can model/map the system's workflows into a Graph.
4. A Graph here is called a StateGraph/Statefull graph (a graph that always maintains a state/status/present condition
at each and every point (Node) in the graph )
5. So, LG can help us create a complex agentic system where we can introduce looping between the nodes/steps (step1->step2->step1)
until the desired goal is achieved, branching (letting the node to decide which edge/path to go), human-in-the-loop (allowing us to 
interfere for crucial decision making)

## Why LG?
Even without LG, we can build agentic system but here is the catch:
1. An agentic system to which we give control to more workflows (workflow is like some task like calling an API
, accessing DB or searching the web) is called as an 'Autonomous Agent'.
2. System With few workflow controls, is called as a 'Router'.
3. In an Autonomous Agent (agent with control/access to many workflows), the reliability would be so poor.
i.e., Increase in the num of workflow controls to agent Decrease the reliability. 
4. So LG is used to raise this reliability curve so that even an autonomous sys can have better reliability.

## Why not LangChain (LC)?
LangChain & LangGraph - Chain: Linear; Graph: None-linear
1. LangChain is good for sequencial/linear workflow.
2. Good when dealing with simple and some high-level problems.
3. But can't be a good choice if want to build a complex agentic sys.

## LC in LG?
Ofcourse, some of the core components (like models, messages variants(like AI, Human, System) from langchain_core)
and some abstraction are still used with LG.

Let's break-down the Graph:

## Graph:
1. A stateful graph (StateGraph) has Nodes and Edges.
2. The 3 core components in a graph are: State, Nodes, Edges.

### a) State:
1. State is just shared data structure that represents the current status/condition/snapshot of the graph (representing the
workflows of our whole agentic sys).
2. State can be built typically as a TypedDict or Pydantic BaseModel.

### b) Nodes:
1. A Node does the actual work. It can be built as a Func that encodes a logic.
2. It takes the current state as input, perform computation, and returns an updated state.
3. Just imagine a Node as just py func that takes State as a parameter and returns the updated State (replacing/overwriting
the state of the graph, like updating the status/condition of whole graph/system).

### c) Edges:
1. An Edge connects two Nodes. 
2. Edge too is implemented as a func if conditional edge or just using the LG's built-in func.
3. It can be an Unconditional edge, like insisting to go from Node 1 to Node 2 without any
other choices/conditions.
3. Conditional edge: A func that determines which Node to execute next based on the current State. 
4. LG has built-in Edges too.

## Deep into Practical use cases:
MessagesState: Message as the State for the graph.

## How MessagesState is built-in by LG:

class MessagesState(TypedDict):
    messages: Annotated[list[AnyMessage], add_messages]
    
1. MessagesState is TypedDict that has one Key called 'messages'. And in this key new messages (AnyMessae) will be appended.
2. asically, the Nature of a Node is that it takes a State as input and returns an Updated state, overwriting the state in the whole group.
3. But, here we are passing Messages as the state, so thinking logically, we should not overwrite the 
message.
4. Overwritting message state means that we're actually forgetting to preserve the previous message. So whenever
the system/graph/agent gets a new message, it would forget the previous message.
5. But as all we know, the system should always remember the context by preserving the prev messages/history. 
6. That's why in the MessagesState's key 'messages', we keep a list of all prev messages.
7. 'add_messages' acts as a reducer that appends new messages to the existing list, rather than replacing it.