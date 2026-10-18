# Cedar & Crumb Business Assistant

This repository contains the complete C3 agent assignment: a fictional business profile, separate persona and instruction blocks, a three-way router, two tool functions, short-term memory, a Gradio interface, and an optional reflection step.

The app uses **Gemini through Google's OpenAI-compatible endpoint**. The `openai` Python package is used only as a compatible client library; the app does not use an OpenAI API key or OpenAI billing.

## Project files

- `me/about_business.pdf` - detailed fictional business profile.
- `me/business_summary.txt` - short business summary used by the agent.
- `business_agent.ipynb` - main assignment notebook, organized by rubric item.
- `app.py` - runnable Gradio deployment entry point.
- `requirements.txt` - Python dependencies.
- `.env.example` - safe configuration template. The real `.env` is ignored by Git.
- `data/` - runtime JSONL logs for leads and feedback.

## Run locally

1. Create and activate a Python 3.11 or newer virtual environment.
2. Install the dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Add the following variables to your local `.env` file:

   ```text
   GEMINI_API_KEY=your_gemini_api_key
   OPENAI_MODEL=gemini-3.5-flash-lite
   ENABLE_REFLECTION=false
   ```

   `OPENAI_MODEL` is the configuration name expected by the app, but its value is a Gemini model. Never commit `.env` or place the real key in the notebook.

4. Start the Gradio app:

   ```bash
   python app.py
   ```

The interface starts without a key. General business questions still work because they are answered locally. Lead and feedback routes display a configuration reminder until `GEMINI_API_KEY` is available and the app is restarted.

## Agent behavior

The router uses simple, explainable heuristics and returns exactly one assignment category:

- `general_question` - known business questions are answered directly from verified Cedar & Crumb information, without an API call. An unknown general question is logged locally with `record_feedback` instead of being invented.
- `lead_collection` - Gemini receives the last eight user/assistant messages and may call `record_customer_interest` after the customer's name, email, and request are known.
- `feedback_unanswered` - Gemini receives the short-term history and is required to call `record_feedback`.

The Gemini client is created with the OpenAI-compatible endpoint:

```text
https://generativelanguage.googleapis.com/v1beta/openai/
```

## Tools and logs

- `record_customer_interest(email, name, message)` appends to `data/leads.jsonl`.
- `record_feedback(question)` appends to `data/feedback.jsonl`.

Both tools also print their activity in the terminal. The JSONL files are ignored by Git so customer data is not committed.

## Optional reflection bonus

The reflection code is included but disabled in the standard configuration:

```text
ENABLE_REFLECTION=false
```

Set it to `true` locally and restart the app to demonstrate the bonus. Reflection applies after Gemini prepares a lead or feedback reply, makes one additional Gemini request, and prints both the original draft and revised reply. Deterministic general-question replies do not use reflection.

## Suggested video demo

1. Run the router demonstration cell in the notebook.
2. Ask `What are your opening hours?` and show that it works locally.
3. Ask `I need a custom cake for 20 people.` Then provide a name and email and show the lead tool log.
4. Ask `I have a suggestion about your packaging.` Show the feedback tool log.
5. Ask `Do you offer a loyalty card?` Show that the unknown question is logged instead of answered with invented information.
6. Optional bonus: set `ENABLE_REFLECTION=true`, restart, repeat a lead or feedback prompt, and show the `[REFLECTION]` log lines.

## Hugging Face Spaces bonus

Create a Gradio Space and upload the repository without `.env`. Add `GEMINI_API_KEY` as a Space secret and set `OPENAI_MODEL=gemini-3.5-flash-lite` and `ENABLE_REFLECTION=false` as variables. The Space will use `app.py` as the application entry point.
