# Day 1 – Building a Bakery Assistant with Groq and LangChain

## Objective
Create a basic AI customer-support assistant for **Sweet Crumbs Home Bakery** using a Groq-hosted language model through LangChain. The assistant answers customer questions using a fixed set of bakery facts and follows response rules.

## Task
- Install and import `langchain_groq`.
- Read the Groq API key from Google Colab Secrets.
- Initialize a chat model.
- Define the assistant's role, business facts, and response constraints.
- Create a helper function to send a customer question to the model.
- Make a sample request.
- Explore a separate prompt-guard model call.

## Implementation

### 1. Install and import the integration
```python
!pip install langchain_groq
from langchain_groq import ChatGroq
```
The package provides LangChain integration with models served by Groq.

### 2. Configure the API key and model
```python
import os
from google.colab import userdata

os.environ["GROQ_API_KEY"] = userdata.get("GROQ_API_KEY")

llm = ChatGroq(
    model="openai/gpt-oss-120b",
    temperature=0
)
```
The API key is retrieved from Colab Secrets rather than being written directly in the notebook. The model is initialized with temperature `0` to keep answers relatively consistent.

### 3. Define the assistant instructions
The system prompt gives the assistant the bakery's context:
- Orders are made to order.
- Orders require 24 hours' notice.
- Eggless versions are available for all cakes.

It also sets response rules:
- Do not promise delivery times.
- Admit when information is not known.
- Be polite and keep replies under 60 words.

### 4. Create the question-answer function
```python
def ask(question):
    return llm.invoke([
        ("system", SYSTEM),
        ("human", question)
    ]).content
```
The function sends the system instructions and the customer's question together. It returns the model's text response.

### 5. Try a sample question
```python
print(ask("can i get my order tommorrow"))
```
This tests whether the assistant applies the 24-hour notice rule when answering a customer.

### 6. Explore prompt-guard
The notebook also makes a direct Groq SDK request to the `meta-llama/llama-prompt-guard-2-22m` model with an example prompt-injection-style message. This is a separate experiment to inspect prompt-guard behavior; it is not connected to the `ask()` bakery-chat function.

## Result
A basic single-turn bakery assistant is created. It uses a system prompt to keep responses within the supplied business facts and communication rules.

## Notes
- Add `GROQ_API_KEY` to the Google Colab **Secrets** panel before running the notebook.
- The prompt-guard example is an exploratory standalone call, not a complete security layer around the bakery assistant.
- The notebook's main assistant does not maintain conversation history; each `ask()` call is independent.
