# AI Phone Agent with RAG and Tools

An intelligent, conversational voice agent that answers phone calls, provides information from a knowledge base using Retrieval Augmented Generation (RAG), and performs actions like booking appointments and hotel rooms.

This agent runs the core language model (Llama 3.2) locally using Ollama, ensuring privacy and control. It uses Twilio for telephony, Eleven Labs for high-quality text-to-speech, and ChromaDB for an efficient local vector store.

## Core Features

-   **Conversational Voice Interface:** Handles incoming phone calls in real-time.
-   **Retrieval Augmented Generation (RAG):** Answers questions accurately by retrieving information from a private knowledge base (e.g., text files, PDFs).
-   **Tool-Based Actions:** Can perform specific tasks like booking appointments or hotel rooms by calling predefined functions.
-   **Local & Private LLM:** Uses Llama 3.2 running on your local machine via Ollama, so conversations and data remain private.
-   **High-Quality Voice:** Leverages Eleven Labs for natural-sounding, realistic speech synthesis.
-   **Modular Tech Stack:** Built with industry-standard tools for easy extension and modification.

## Tech Stack

-   **LLM:** Llama 3.2 (via `Ollama`)
-   **Agent Framework:** LangChain
-   **Embedding Model:** `mxbai-embed-large`
-   **Vector Database:** ChromaDB
-   **Telephony:** Twilio
-   **Text-to-Speech (TTS):** Eleven Labs
-   **Web Framework:** Flask
-   **Language:** Python

## How It Works

1.  A customer calls the designated Twilio phone number.
2.  Twilio triggers the `/incoming_call` webhook on the Flask server.
3.  The server greets the caller and uses `<Gather>` to capture their speech.
4.  Twilio transcribes the speech and sends the text to the `/process_speech` webhook.
5.  The text is passed to the core LangChain agent.
6.  The agent, powered by Llama 3.2, decides whether to:
    -   Use the **RAG chain** to answer an informational question.
    -   Use a **tool** to handle a booking or scheduling request.
7.  The agent generates a text response.
8.  The response is sent to Eleven Labs to be converted into a high-quality audio stream.
9.  The Flask server provides Twilio with a URL to the audio file, which is then played to the caller.
10. The loop continues with another `<Gather>` to listen for the user's next response.

## Setup and Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/ebosoh/phone_agent.git
    cd phone_agent
    ```

2.  **Install Ollama and Llama 3.2:**
    -   Download and install [Ollama](https://ollama.com/).
    -   Pull the Llama 3.2 model by running:
        ```bash
        ollama pull llama3.2:8b-instruct
        ```

3.  **Set up environment variables:**
    -   Rename the `.env` file.
    -   Open `.env` and fill in your actual credentials for `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_PHONE_NUMBER`, and `ELEVEN_LABS_API_KEY`.

4.  **Install Python dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

5.  **Ingest your data:**
    -   Add your business documents (`.txt`, `.pdf`, `.md`) to the `knowledge_base` folder.
    -   Run the ingestion script to build your vector database:
        ```bash
        python ingest.py
        ```

## Running the Agent

1.  **Start the Ollama server:**
    -   Ensure the Ollama application is running in the background.

2.  **Start the Flask web server:**
    ```bash
    flask run --port=5001
    ```

3.  **Expose your server with ngrok:**
    -   In a new terminal, run:
        ```bash
        ngrok http 5001
        ```
    -   Copy the `https://...ngrok-free.app` forwarding URL.

4.  **Configure Twilio:**
    -   Go to your Twilio phone number's settings in the console.
    -   Under "A CALL COMES IN", set the webhook to your ngrok URL, appending `/incoming_call` (e.g., `https://<your-id>.ngrok-free.app/incoming_call`).
    -   Set the HTTP method to `POST`.
    -   Save the changes.

5.  **Make a call!**
    -   Call your Twilio number to start a conversation with your AI agent.
