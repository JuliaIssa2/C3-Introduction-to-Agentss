# Cedar & Crumb Business Assistant

This repository contains the complete C3 agent assignment: a fictional business profile, a clearly separated persona and instruction set, a three-way message router, two OpenAI tool functions, short-term chat memory, a Gradio interface, and the optional reflection step.

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
2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Open `.env` and add your OpenAI API key:

   ```text
   OPENAI_API_KEY=your_real_key_here
   OPENAI_MODEL=gpt-4o-mini
   ENABLE_REFLECTION=true
   ```

4. Start the app:

   ```bash
   python app.py
   ```

The app starts even when the key is missing; the chat will display a configuration reminder until a valid key is added and the app is restarted.

## Router behavior

The router deliberately uses simple, explainable heuristics:

- General question: business-information questions such as hours, services, or delivery.
- Lead collection: order, quote, booking, custom-cake, corporate-order, contact, or email signals.
- Feedback/unanswered question: complaint, issue, suggestion, or feedback signals. If a general question is not covered by the business sources, the model is instructed to call the same feedback tool.

## Tool logs

- `record_customer_interest(email, name, message)` appends to `data/leads.jsonl`.
- `record_feedback(question)` appends to `data/feedback.jsonl`.

Both tools also print their activity in the terminal. The JSONL files are ignored by Git so customer data is not committed.

## Suggested video demo

1. Run the router demo cell in the notebook.
2. Ask: `What are your opening hours?`
3. Ask: `I need a custom cake for 20 people.` Then provide a name and email when prompted, and show the lead tool log.
4. Ask: `I have a suggestion about your packaging.` Show the feedback tool log.
5. Ask an unknown question such as `Do you offer a loyalty card?` Show that it is logged for review instead of invented.
6. Show the terminal's `[REFLECTION]` original and revised reply lines.

## Hugging Face Spaces bonus

Create a Gradio Space, upload the repository without `.env`, and add `OPENAI_API_KEY`, `OPENAI_MODEL`, and `ENABLE_REFLECTION` as Space secrets/variables. The Space will use `app.py` automatically.
