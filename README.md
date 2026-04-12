# Little Wins
Contributers: Aathmika Gokula Krishna, Koko Mclaughlin, Fatima Rana, Anika Nagarajan

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

```json
{
  "excessive spending": {
    "summary": "short explanation",
    "categories": [
      {
        "category": "Coffee",
        "amount": 24.5,
        "reason": "short reason"
      }
    ]
  },
  "daily goals": [
    {
      "title": "No coffee today",
      "saving": 5,
      "category": "Coffee",
      "reason": "short reason"
    }
  ],
  "weekly goals": [
    {
      "title": "No eating out",
      "saving": 50,
      "category": "Eating out",
      "reason": "short reason"
    }
  ]
}
```

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

## Supabase Notes

Authentication is local and uses JWT tokens. In local development, users can be stored in `local_users.json`. In Vercel, set `SUPABASE_SERVICE_ROLE_KEY` so users and goals are persisted in the Supabase `user` table instead of the temporary serverless filesystem.

The default table and column names are:

- Table: `transactions`
- Quest table: `quest`
- ID column: `id`
- Category column: `category`

If your schema uses different names, update these values in `.env`:

```text
SUPABASE_TRANSACTIONS_TABLE=transactions
SUPABASE_QUESTS_TABLE=quest
SUPABASE_TRANSACTION_ID_COLUMN=id
SUPABASE_TRANSACTION_CATEGORY_COLUMN=category
APP_USER_ID=00000000-0000-0000-0000-000000000000
SITE_URL=http://localhost:3000
JWT_SECRET=change-this-local-jwt-secret
LOCAL_USERS_FILE=local_users.json
```

Use `SUPABASE_SERVICE_ROLE_KEY` only on the server. It is intentionally not exposed to the browser.

## Vercel Notes

This repo includes `vercel.json`, which routes requests to `server.js` as a Vercel serverless function. Locally, `server.js` still runs as a normal Node server with `npm run dev`.

Add these environment variables in Vercel Project Settings:

```text
SITE_URL=https://your-vercel-app.vercel.app
JWT_SECRET=use-a-long-random-secret
SUPABASE_URL=https://your-project-ref.supabase.co
SUPABASE_ANON_KEY=your-supabase-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-supabase-service-role-key
SUPABASE_USERS_TABLE=user
SUPABASE_USER_ID_COLUMN=user_id
SUPABASE_TRANSACTIONS_TABLE=transactions
SUPABASE_QUESTS_TABLE=quest
OPENAI_API_KEY=your-openai-api-key
OPENAI_MODEL=gpt-4o-mini
```

Do not add `LOCAL_USERS_FILE` on Vercel. The serverless filesystem is temporary, so production user data must live in Supabase.

## OpenAI Notes

The app uses the OpenAI Responses API from the server only. Your API key is never sent to the browser.

The default model is:

```text
OPENAI_MODEL=gpt-4o-mini
```

To keep testing costs low, the server sends only the most recent configured number of transactions per analysis:

```text
MAX_TRANSACTIONS_FOR_ANALYSIS=75
```

Lower that number while tuning prompts if you want to spend less per click.
