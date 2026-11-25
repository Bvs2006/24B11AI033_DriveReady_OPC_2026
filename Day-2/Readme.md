# Day 2 – OpenAI-Compatible Groq API, Python Data Handling, and Chat Memory

## Objective
Build on the bakery assistant by calling Groq through the OpenAI Python SDK, representing menu and order data in Python, calculating prices, and creating a command-line chatbot that retains recent conversation history.

## Tasks
- Install and configure the OpenAI Python client to use Groq's API endpoint.
- Send chat-completion requests with system and user messages.
- Add bakery facts, menu prices, and response constraints to the system prompt.
- Practise Python dictionaries, safe dictionary lookup, JSON conversion, functions, and exception handling.
- Implement a chat loop with conversation history and basic API error handling.

## Implementation

### 1. Install and configure the client
```python
!pip install -q openai

from google.colab import userdata
from openai import OpenAI

client = OpenAI(
    api_key=userdata.get("GROQ_API_KEY"),
    base_url="https://api.groq.com/openai/v1"
)
```
The OpenAI SDK is used as a compatible client, but requests are sent to Groq through its OpenAI-compatible base URL.

### 2. Send a basic chat-completion request
A sample request uses `openai/gpt-oss-120b`, with a system message asking the model to tell a joke and a user message requesting one. The response is read from:
```python
response.choices[0].message.content
```

### 3. Create the bakery system prompt
The bakery prompt provides:
- 24-hour advance-notice requirement.
- Eggless cake availability.
- Plum cake: ₹500.
- Chocolate cake: ₹650.
- Cupcake: ₹80.

It instructs the assistant not to promise delivery times, to say **"I'll check with the owner."** when information is unavailable, to respond in fewer than 50 words, and to match the customer's English or Telugu.

### 4. Represent menu and order information
A dictionary stores product prices:
```python
MENU = {
    "plum cake": 500,
    "chocolate cake": 650,
    "cupcake": 80
}
```
A sample order is represented as a dictionary with the customer's name, item, quantity, and eggless preference:
```python
order = {
    "name": "Ravi",
    "item": "plum cake",
    "qty": 2,
    "eggless": True
}
```
The total is calculated by multiplying the quantity by the selected menu price.

The notebook compares direct dictionary indexing with `.get()`. Direct indexing raises a `KeyError` for a missing key, while `.get()` can return a fallback message.

### 5. Convert order data to and from JSON
```python
import json

text = json.dumps(order)
back = json.loads(text)
```
`json.dumps()` converts the Python dictionary into a JSON string, and `json.loads()` converts it back into a Python object. This is useful when order data needs to be transmitted or stored in JSON format.

### 6. Write a reusable price function
```python
def get_price(item: str, qty: int = 1) -> int:
    return MENU[item.lower().strip()] * qty
```
The function normalizes the item name by converting it to lowercase and removing surrounding spaces. It then multiplies the menu price by the requested quantity. The notebook demonstrates calls such as six cupcakes and one plum cake.

### 7. Practise exception handling
A small example intentionally references an undefined variable and then demonstrates using `try`/`except` to handle the resulting error. This illustrates why error handling is useful when a program encounters unexpected conditions.

### 8. Build a conversational command-line chatbot
The final implementation:
- Defines the model name, menu, and bakery system prompt.
- Uses a `log_call` decorator to print the arguments and result of the price function when it is called.
- Initializes `history` with the system message.
- Keeps a maximum of eight recent message entries in the request context.
- Appends each user message and assistant reply to the conversation history.
- Handles `RateLimitError` and `APIConnectionError` with friendly fallback responses.
- Runs an input loop until the user enters `bye`, `exit`, or `quit`.

The `chat()` function sends the recent history to the model, receives a response, stores that response in history, and returns it to the command-line loop.

## Result
The notebook progresses from a one-request API example to a stateful command-line bakery chatbot. It also practises the Python data structures and utility functions needed to represent customer orders and calculate totals.

## Notes
- Add `GROQ_API_KEY` to Colab Secrets.
- The chatbot keeps history in memory for the current Python session; it does not save conversations to a database or file.
- The `get_price()` function is demonstrated separately; the final `chat()` loop does not expose it as a model tool.
- The notebook includes a basic exception-handling exercise. In production code, catch specific exceptions and validate menu items and quantities.
