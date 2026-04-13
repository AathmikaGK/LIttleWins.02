# Little Wins
Financial budgetting app designed to analyse the user's past transactions and create personalised savings goals / challenges.

## What It Does

- Shows savings goals, daily quests, weekly quests, and a profile screen.
- Fetches rows from the Supabase `transactions` table.
- Sends the transaction details and savings goals to OpenAI for recurring spending analysis.
- Asks OpenAI to categorize every individual transaction.
- Writes the finalized transaction category back into the Supabase `category` column.
- Saves generated daily and weekly quests to the Supabase `quest` table.
- Lets users tick quest completion checkboxes, updating `completion` in Supabase.
- Supports local sign up and login with JWT tokens.
- Saves each user's dashboard goals in Supabase when `SUPABASE_SERVICE_ROLE_KEY` is set, with a local JSON fallback for laptop development.
- Displays the AI response in this JSON shape:


The backend also asks OpenAI for an internal `categorized_transactions` array so it can update Supabase.

## Setup

1. Copy the placeholder environment file:

```bash
cp .env.example .env
```

2. Fill in the Supabase and OpenAI values in `.env`.

3. Run the app:

```bash
npm run dev
```

4. Open:

```text
http://localhost:3000
```


