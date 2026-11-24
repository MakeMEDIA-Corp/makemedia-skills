---
name: company-profile
description: Set up a company in makemedia, or review and refresh its settings. Collects the company's name, website, region and target customer size, then drafts its services, customer characteristics (ideal client profile), profile topics and SEO keywords for the user to review. Use when the user wants to set up, add, update, review or refresh a company, its profile, its content topics or its SEO keywords, or when another makemedia workflow needs a company that isn't set up yet. Requires the makemedia connector.
---

# Company profile

A company's profile has two parts. Later workflows (personas, interviews,
posts) build on both.

- **Basics**, which the user provides: name, website, region and target
  customer size.
- **Settings**, which are drafted and then reviewed by the user, in this
  order, because each builds on the ones before:
  1. **Services**: what the company sells, from its website.
  2. **Customer characteristics**: its ideal client profile, from its
     website.
  3. **Profile topics**: about 15 high-level topics its content should focus
     on, from the basics and the first two settings.
  4. **Keywords**: about 50 SEO keywords, from the profile topics.

  makemedia records when each setting was last reviewed, and asks for a
  fresh review about every four months.

## Steps

1. **Check the project's company.** Follow the makemedia skill and look for
   a pinned `makemedia company_id` in the project instructions or memory.
   - **Pinned:** call `get_company`. Its `profile` holds the current basics
     and settings, and `review_due` lists settings that were never set up or
     are older than four months. If the user only wants a review or a
     refresh, go to "Review and refresh" below.
   - **Not pinned:** confirm with the user that this project is for the
     company they're asking about (one customer per project). If the
     company is already in `list_companies`, use it rather than creating a
     new one.

2. **Collect the basics.** Ask for anything the user hasn't already given.
   Use the user's own words. Don't research or guess these.
   - **Company name**: how the company is identified in every workflow.
   - **Website**: the company's main URL. For a division of a larger
     company, use the division's section of the main site (for example
     `https://example.com/cloud`).
   - **Region** (optional): the country, state or city where the business
     operates. Leave it out if the user names none, which means there's no
     restriction.
   - **Customer size**: how big the company's target customers are, by
     employees, revenue or another measure. Examples: "10-20 employees",
     "$5-$10 million ARR", "100+ employees with $50M ARR", "Fortune 1000".

3. **Save the basics.** Call `save_company` with them, and with
   `company_id` if the company already exists. If the company wasn't pinned
   yet, pin the returned `company_id` now. Don't ask whether to pin it,
   just do it:
   - Save `makemedia company_id: <company_id> (<company name>)` to memory,
     if you can save memories here.
   - Give the user this line to paste into the project's
     **Instructions**, filled in. You can't edit the instructions yourself:

     `makemedia company_id: <company_id> (<company name>). Use only this company for makemedia in this project.`

   If it says the company already exists and the user has no access, tell
   them which owner to ask for access. Don't create a second company.

4. **Work out the services.** Using your web tools, follow these
   instructions yourself, with `<website>` replaced by the saved website:

   > This is my company's website: `<website>`
   >
   > Review my Home page, About page and my Products and/or Services and/or
   > Solutions pages and summarize or list the products and/or services
   > that I sell. Give me your response as a list without any additional
   > explanation or formatting. If you cannot access my website, indicate
   > the reason and return empty list.

   For a division, stay within the division's section of the site. If you
   have no web access or the site can't be read, tell the user why and ask
   them to list their services instead.

5. **Get the customer characteristics.** Call `start_workflow` with
   `workflow: "customer_characteristics"`, the `company_id` and
   `inputs: {}`. The server reads the website using the saved website and
   customer size. Follow `agent_instructions`. If it's `working`, call
   `get_session` until it completes. Keep the result's `artifact_id`.

6. **Review services and characteristics.** Show both together:
   - Services as a list.
   - The customer characteristics text exactly as returned. If the result
     has an `error`, explain it and ask the user to describe their ideal
     client in a sentence.

   Ask the user to approve them, or to add, remove or reword anything.
   Apply their changes. Don't save until they approve.

7. **Save them.** Call `save_company_settings` with the `company_id`, the
   approved `services` list, `customer_characteristics`, and
   `from_artifact_ids` set to the workflow result's `artifact_id` (leave it
   out if the user wrote the text themselves). They must be saved before
   the next step, which reads them.

8. **Profile topics.** Call `start_workflow` with
   `workflow: "profile_topics"`, the `company_id` and `inputs: {}`. Show
   the topics as a numbered list and ask the user to approve them or to
   add, remove or reword topics. Topics are subjects to talk about, not
   article titles. Save the approved list with `save_company_settings`
   (`profile_topics`, and the result's `artifact_id` in
   `from_artifact_ids`).

9. **Keywords.** Call `start_workflow` with `workflow: "keywords"`, the
   `company_id` and `inputs: {}`. It uses the saved topics.

   **Enrich them with an SEO tool, if the user has one.** Check whether you
   have a skill or connector for an SEO service such as Semrush, Moz or
   Ahrefs. If you do, use it to look up each keyword's monthly search
   volume, keyword difficulty (0-100), the company website's current
   ranking and the search intent (informational, navigational, commercial
   or transactional; Semrush abbreviates them I, N, C and T), in the
   company's region if it has one. The tool may also
   suggest closely related keywords with better volume; offer those as
   additions. Only record numbers and intents the tool returned, never estimates, and
   name the tool in each keyword's `source`. If you have no SEO tool, skip
   this part, and mention once that connecting one (for example Semrush or
   Moz) would add search volume, rankings and search intent.

   Show the keywords as a table (keyword, then volume, difficulty, rank
   and intent if you have them), sorted by volume when known. Ask the user to approve
   the list or to add or remove keywords. Then save it with
   `save_company_settings`: `keywords` as a list of objects, each with
   `keyword` and any of `search_volume`, `difficulty`, `rank`, `intent`
   (comma-separated when there are several) and `source`,
   and the result's `artifact_id` in `from_artifact_ids`.

10. **Report.** Tell the user the company is set up, that they're its owner
    if they created it, and that they can share it with colleagues. If you
    gave them the project instructions line in step 3, remind them to paste
    it in if they haven't yet. Suggest
    a next step, such as a customer persona or a LinkedIn post.

## Review and refresh

Users can review or refresh the settings at any time. When `get_company`
shows settings in `review_due`, or a workflow's `agent_instructions` say
they're due, offer a review.

- **Review:** show the current settings from `get_company`'s `profile`.
  If the user confirms them unchanged, still call `save_company_settings`
  with the same values, because that records the review. Save any edits
  they make.
- **Refresh:** redo the step for each setting the user picks (step 4 for
  services, 5 for customer characteristics, 8 for topics, 9 for keywords).
  Show the new version next to the current one, then save what they
  approve. Save each one before refreshing a setting that builds on it.
- **Knock-on refreshes:** if `save_company` or `save_company_settings`
  returns `refresh_suggested`, those settings were built from something
  that just changed (for example, new services make the topics and
  keywords out of date). Offer to refresh them, in the order listed.

Pass only the settings the user reviewed. A setting you leave out keeps its
previous review date.
