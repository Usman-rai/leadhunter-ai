# LeadHunter AI — Setup Guide

This guide walks you through setting up LeadHunter AI from scratch.

**Estimated time:** 45 minutes
**Difficulty:** Beginner-friendly

---

## What You'll Need

Before starting, create accounts for these (all have free tiers):

| Service | Purpose | Link |
|---------|---------|------|
| n8n | Runs the workflow | [n8n.io](https://n8n.io) |
| Apify | Scrapes Google Maps | [apify.com](https://apify.com) |
| Groq | AI brain (free) | [console.groq.com](https://console.groq.com) |
| Google account | Stores leads in Sheets | [sheets.google.com](https://sheets.google.com) |

---

## Step 1 — Get Your API Keys (15 minutes)

### Apify Token
1. Sign up at [apify.com](https://apify.com)
2. Go to **Settings → Integrations → API tokens**
3. Click **Create new token**
4. Name it `n8n-leadhunter`
5. Copy the token (starts with `apify_api_...`)
6. Save it somewhere safe

### Add the Google Maps Scraper Actor
1. Go to [console.apify.com/store](https://console.apify.com/store)
2. Search **"google maps scraper"**
3. Find the one by **compass**
4. Click **Try for free** / **Add to my actors**

### Groq API Key
1. Sign up at [console.groq.com](https://console.groq.com)
2. Go to **API Keys**
3. Create a key
4. Copy it (starts with `gsk_...`)
5. Save it safely

---

## Step 2 — Create the Google Sheet (5 minutes)

1. Open [sheets.google.com](https://sheets.google.com)
2. Create a new blank sheet
3. Name it: **LeadHunter AI - Leads Database**
4. In **row 1**, add these column headers (one per cell, A1 to K1):

```
Business Name | Phone | Email | Website | Address | Rating | Reviews | Opportunity Score | Priority | Opportunities | Date Added
```

5. Make row 1 **bold** (select row → Ctrl+B)
6. Freeze row 1: **View → Freeze → 1 row**
7. Copy your Sheet ID from the URL:
   - URL looks like: `https://docs.google.com/spreadsheets/d/`**`THIS_PART`**`/edit`
   - Save that ID

---

## Step 3 — Import the Workflow (5 minutes)

1. Open n8n
2. Click **Workflows → Import from File**
3. Select `leadhunter-ai-workflow.json` from this repo
4. The workflow appears with all nodes

---

## Step 4 — Connect Your Credentials (15 minutes)

### Connect Apify
1. Click the **search_google_maps** tool node
2. Under credentials, click **Create New**
3. Paste your Apify token
4. Save

### Connect Groq
1. Click the **Groq Chat Model** node
2. Create new credential
3. Paste your Groq API key
4. Save

### Connect Google Sheets
1. Click the **save_lead_to_sheet** tool node
2. Create new credential → sign in with Google
3. Authorize access
4. Set the **Document ID** to your Sheet ID from Step 2
5. Set **Sheet Name** to `Sheet1`
6. Save

---

## Step 5 — Test It (5 minutes)

1. **Activate** the workflow (toggle top-right)
2. Click **Open Chat**
3. Type: `Find 3 dentists in Lahore`
4. Wait ~60 seconds
5. Check your Google Sheet — 3 rows should appear with scores

### Expected Result

The chat should reply with something like:

```
🎯 Found 3 dentists in Lahore

📊 Quick Stats:
🔥 High priority: 1
⚡ Medium: 1
💡 Low: 1

🏆 Top opportunity: [Business Name] (Score: 75)

📋 Saved to Google Sheets!
```

And your Google Sheet should have 3 new rows with all the data filled in.

---

## Troubleshooting

### "Request too large for model"
Groq free tier has token limits. Fix:
- In the **analyze_and_score_website** tool → Options → turn **Truncate Response** ON → set to 3000
- Reduce search batch size (try "find 3" instead of "find 20")

### AI searches when you just say "hi"
The system prompt needs stricter rules. Make sure the AI Agent's system message has clear "DO NOT use tools for greetings" instructions.

### "Actor not found" (Apify)
You didn't add the Google Maps Scraper actor. Go to [console.apify.com/store](https://console.apify.com/store), search "google maps scraper", add it.

### Google Sheet not filling
- Check the Sheet ID is correct
- Check column names match exactly
- Check Google credential is authorized

### Workflow runs but no results
- Check your Apify free credits aren't exhausted
- Test the Apify actor directly in the Apify dashboard first

---

## Cost Notes

- **Apify:** ~$0.005 per business. Free tier gives ~$5 credit (~1000 leads).
- **Groq:** Free tier (has rate limits — keep batches small).
- **n8n:** Free tier works for testing.
- **Google Sheets:** Free.

**Real cost per search of 5 leads:** about $0.03–0.05.

---

## Customization

### Change default number of results
In the AI Agent system prompt, change "default 5" to your preferred number (keep under 10 for free Groq tier).

### Adjust the scoring rules
In the AI Agent system prompt, find the scoring section and adjust point values to match what matters for your use case.

### Change the search location format
The AI extracts location from natural language. Works with cities ("Lahore"), areas ("DHA Lahore"), or regions.

---

## Questions?

Open an issue on this repo, or reach out:
- Email: raiusmanr517@gmail.com
- LinkedIn: [linkedin.com/in/usman-rai](https://linkedin.com/in/usman-rai)
