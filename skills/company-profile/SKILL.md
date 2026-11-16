---
name: company-profile
description: Research a company from its website and save its ideal customer profile (ICP) and product catalog to makemedia. Use when the user wants to set up, add, research or update a company, or when another makemedia workflow needs a company profile that doesn't exist yet. Requires the makemedia connector.
---

# Company profile

Builds a company profile by researching the company's website yourself, then
saves it with the makemedia connector so later workflows (personas,
interviews) can use it.

## Steps

1. **Check the project's company.** Follow the makemedia skill: look for a
   pinned `makemedia company_id` in the project instructions or memory.
   - Pinned: this run refreshes that company's profile. Pass its
     `company_id` when saving, and skip asking for the basics you can get
     from `get_company`.
   - Not pinned: confirm with the user that this project is for the company
     they're asking about (one customer per project). If the company is
     already in `list_companies`, ask whether to use it as is or refresh it.

2. **Collect the basics.** Ask for anything the user hasn't already given:
   - Company name
   - Website URL
   - Size: number of employees **or** annual recurring revenue (ARR). Either
     is fine; an estimate is fine.

3. **Research.** Search and read the company's website (home page, product or
   solutions pages, pricing, customers or case studies, about page) and other
   reputable sources if needed. Work out:
   - What the company does, in one or two sentences.
   - Its **ideal customer profile**: industries, company sizes, regions,
     buyer roles, the pain points it solves, and what triggers a purchase.
   - Its **products**: name, short description and target customer for
     each. Give each product a short, unique slug as its `id` (for example
     `analytics-pro`).
   - The URLs you used.

   Only state what the sources support. If something isn't clear, leave the
   list empty or the number null rather than guessing.

4. **Confirm with the user.** Show a short summary: the ICP summary, the
   products (with their IDs), and anything you couldn't determine. Apply any
   corrections they give.

5. **Save.** Call `save_company_profile` with the profile. Its input schema
   lists every field. If it returns a validation error, fix the named fields
   and call it again.

   If it says the company already exists and the user has no access, tell
   them which owner to ask for access. Don't create a second company.

6. **Pin and report.** If the company wasn't pinned yet, pin the returned
   `company_id` to this project as the makemedia skill describes: save it
   to memory and give the user the line for the project instructions.
   Tell the user the profile is saved. Mention that they're its owner and
   can share it with colleagues. Suggest a next step, such as a customer
   persona (customer-persona skill) or a discovery interview
   (discovery-interview skill).
