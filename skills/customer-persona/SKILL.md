---
name: customer-persona
description: Generate a rich, detailed customer persona for a saved company using makemedia's persona workflow. Use when the user asks for a customer persona, buyer persona or ideal customer description for one of their products, services or customer problems. Requires the makemedia connector and a saved company profile.
---

# Customer persona

Generates a detailed customer persona from a saved company profile, a subset
of the company's services, and the problem the customer is trying to solve.
The generation runs on the makemedia server; your job is to collect the
inputs, relay any questions, and present the result.

## Steps

1. **Use the project's company.** Use the `makemedia company_id` pinned in
   the project instructions or memory (see the makemedia skill). If none is
   pinned, ask which customer this project is for and pin it. Call
   `get_company`. If its `profile` has no `services`, run the
   company-profile skill first.

2. **Pick the services.** Show the user the services from
   `profile.services.items` and ask which ones suit this customer. Pass
   them exactly as saved.

3. **Get the problem.** Ask for a short description of the problem this
   customer is trying to solve (one or two sentences is enough).

4. **Start.** Call `start_workflow` with:
   - `workflow`: `"customer_persona"`
   - `company_id`
   - `inputs`: `{"services": [...], "problem": "..."}`

5. **Follow the response.** Every response has a `status` and
   `agent_instructions`. Always follow `agent_instructions`:
   - `needs_input`: the server needs a clarification. Ask the user
     `prompt_to_user` exactly as written (listing `input.options` if there
     are any), then call `respond` with the `session_id`, the `turn`, and the
     user's reply word for word. Never answer for the user.
   - `working`: tell the user it's in progress and call `get_session`.
   - `complete`: go to step 6.
   - `failed`: tell the user and offer to try again.

6. **Present the persona.** Format `result` for reading: name and headline
   first, then profile, goals, pain points, current solutions, objections,
   buying process, channels, messaging, a day in the life, and quotes.
   Mention that it's saved (`artifact_id`) and can be used in later work.
