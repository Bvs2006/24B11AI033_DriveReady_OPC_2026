# 🍰 Sweet Crumbs – AI Bakery Ordering Agent

An AI-powered customer service and cake-ordering assistant built using **LangChain, Groq LLM, Python, and Google Sheets**.

The agent helps customers check bakery opening hours, calculate cake prices, and place cake orders through natural-language conversations in English or Telugu.

## 🚀 Project Overview

Sweet Crumbs is a home bakery that uses an AI agent to automate basic customer interactions and order management.

Instead of manually answering every customer query, the AI agent understands customer messages, selects the appropriate tools, retrieves accurate information, and saves confirmed orders directly into Google Sheets.

## ✨ Features Implemented

### 1. AI-Powered Customer Assistant
- Understands natural-language customer messages.
- Supports English and Telugu conversations.
- Responds in a friendly and conversational manner.
- Maintains conversation history during the notebook session.
- Uses an LLM to understand customer requests and decide which tools to execute.

### 2. Cake Menu and Pricing

The bakery currently supports three products:

| Product | Price |
|---|---:|
| Plum Cake | ₹500 |
| Chocolate Cake | ₹650 |
| Cupcake | ₹80 |

- Calculates the total price based on quantity.
- Uses the `get_price` tool instead of guessing prices.
- Informs customers when an item is unavailable.

### 3. Bakery Opening Hours

The agent can check bakery operating hours for any day of the week.

| Day | Opening Hours |
|---|---|
| Monday – Saturday | 9 AM – 8 PM |
| Sunday | 10 AM – 6 PM |

The `is_open` tool retrieves the correct timings.

### 4. Automated Cake Ordering

Customers can place orders using natural language.

The agent collects the following information:

- Customer name
- Cake item
- Quantity
- Pickup date
- Eggless preference

The order information is validated using a Pydantic model before being saved.

### 5. Google Sheets Integration

Google Sheets is used as the order database.

When an order is confirmed, the `log_order` tool saves the following information:

| Column | Description |
|---|---|
| name | Customer name |
| item | Cake name |
| qty | Order quantity |
| date | Pickup date |
| eggless | Eggless preference |
| total | Total order amount |

Every successfully saved order is added as a new row in the Google Sheet.

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core programming language |
| LangChain | AI agent and tool orchestration |
| Groq | LLM inference |
| GPT-OSS-20B | Language model |
| Pydantic | Structured data validation |
| Google Sheets API | Order storage |
| gspread | Google Sheets integration |
| Google Colab | Development and execution environment |

## 🧠 Agent Architecture

The application follows a tool-calling agent architecture.

```text
       Customer Message
              |
              v
       LangChain AI Agent
              |
              v
         Groq LLM
        GPT-OSS-20B
              |
              v
       Understand Intent
              |
       +------+------+
       |      |      |
       v      v      v
    Pricing Hours  Ordering
       |      |      |
       v      v      v
   get_price is_open log_order
       |      |      |
       |      |      v
       |      |   Pydantic
       |      |   Validation
       |      |      |
       |      |      v
       |      |  Google Sheets
       |      |      |
       +------+------+
              |
              v
       Customer Response
```

## 🔧 Tools Implemented

Three custom tools are implemented using LangChain's `@tool` decorator.

### `get_price(item, qty)`
- Retrieves the price of a menu item.
- Calculates the total based on quantity.
- Handles unavailable menu items.

### `is_open(day)`
- Checks the bakery's opening hours.
- Returns the operating hours for the requested day.
- Handles invalid day inputs.

### `log_order(name, item, qty, date, eggless)`
- Validates order details using Pydantic.
- Calculates the total order amount.
- Saves the order to Google Sheets.
- Returns a confirmation containing the order details.

## 📂 Project Structure

```text
Sweet-Crumbs-AI-Agent/
│
├── AI_Agent_Day-4.ipynb
│
└── README.md
```

## ⚙️ Installation and Setup

### Step 1: Install Dependencies

```bash
pip install langchain langchain-groq gspread
```

### Step 2: Configure Groq API Key

Create a Groq API key and add it to Google Colab Secrets using the name:

```text
GROQ_API_KEY
```

The notebook retrieves the key securely using:

```python
from google.colab import userdata

os.environ["GROQ_API_KEY"] = userdata.get("GROQ_API_KEY")
```

### Step 3: Configure Google Sheets

Authenticate your Google account:

```python
from google.colab import auth
auth.authenticate_user()
```

Initialize the Google Sheets connection using `gspread` and Google authentication credentials.

The notebook creates a spreadsheet named:

```text
Sweet Crumbs Orders
```

### Step 4: Initialize the AI Agent

The agent uses the Groq-hosted model:

```python
from langchain_groq import ChatGroq

llm = ChatGroq(
    model="openai/gpt-oss-20b",
    temperature=0.2,
    max_retries=3
)
```

### Step 5: Run the Notebook

Execute the notebook cells in order.

The final cell initializes the agent and provides a `chat()` function for interacting with it.

## 💬 Example Conversations

**Example 1: Checking Opening Hours**

Customer:
> Are you open on Sunday?

AI Agent:
> Yes! We're open from 10 AM to 6 PM on Sunday.

**Example 2: Checking Prices**

Customer:
> How much are 2 chocolate cakes?

AI Agent:
> 2 chocolate cakes cost ₹1,300.

**Example 3: Placing an Order**

Customer:
> I'm Ravi. Please book 2 eggless chocolate cakes for 24 Dec.

AI Agent:
> Your order for 2 eggless chocolate cakes has been saved. The total is ₹1,300. Thank you, Ravi!

*Example responses illustrate the intended workflow; actual wording is generated by the model.*

## 🔒 Important Agent Rules

The system prompt defines the agent's behavior:

- Always use the pricing tool to retrieve prices.
- Always use the opening-hours tool to check timings.
- Never guess prices or opening hours.
- Collect the required customer details before saving an order.
- Read back the order details and total after saving.
- Respect the 24-hour advance-order requirement.
- Offer eggless cake options.
- Keep responses under 60 words.
- Match the customer's language (English or Telugu).

## 🔮 Future Improvements

Potential improvements for future versions:

- WhatsApp Business API integration.
- Real-time customer conversations.
- Persistent conversation history.
- Order cancellation and modification.
- Order status tracking.
- Payment gateway integration.
- Admin dashboard for bakery owners.
- Automated order notifications.
- Better date and 24-hour advance-order validation.

## 🎯 Learning Outcomes

Through this implementation, we explored:

1. Building AI agents using LangChain.
2. Connecting LLMs using Groq.
3. Creating custom tools using `@tool`.
4. Understanding tool calling and agent orchestration.
5. Validating structured data using Pydantic.
6. Integrating external services using Google Sheets.
7. Maintaining conversational history.
8. Building a practical AI-powered business automation workflow.

## 📌 Project Status

**Implemented:** Core AI agent, three custom tools, structured order validation, Google Sheets order storage, and conversational memory within the running notebook session.

**Not yet implemented:** WhatsApp integration, payment processing, customer-facing web application, and production deployment.

---

**Sweet Crumbs – Making Bakery Ordering Smarter with AI!** 🍰
