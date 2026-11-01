# Daniel's AI project assistant

The small server behind the AI chat at https://temmy762.github.io/start/.
It keeps the AI key secret, talks to OpenAI (with Mistral or Gemini as optional backups), and hands the finished project brief back to the chat page, which emails it to Daniel.

If this server is not set up or is down, the chat page automatically switches to the question-by-question chat, so no visitor is lost.

## Set it up (about 10 minutes, no coding)

### 1. Get an OpenAI API key
1. Sign in at https://platform.openai.com.
2. Open **Settings → Billing** and add a payment method with some credit (for example $5). The API has no free tier, so without credit every request fails with error 429 `insufficient_quota`. A project chat like this costs a fraction of a cent per conversation.
3. Open **API keys** (https://platform.openai.com/api-keys), click **Create new secret key** and copy it. Keep it private; it is only shown once.

### 2. Create the Worker on Cloudflare
1. Sign up for free at https://dash.cloudflare.com/sign-up.
2. In the left menu open **Compute (Workers)** → **Workers & Pages** → **Create** → **Create Worker**.
3. Name it `daniel-project-assistant` and click **Deploy**.
4. Click **Edit code**. Delete everything in the editor, paste the whole contents of [`worker.js`](./worker.js), then click **Deploy**.

### 3. Add the key
1. Go back to the Worker and open **Settings** → **Variables and Secrets** → **Add**.
2. Type: **Secret**. Name: `OPENAI_API_KEY`. Value: the key from step 1. Click **Deploy**.

### 4. Check it works
Open `https://daniel-project-assistant.<your-subdomain>.workers.dev/health?test=1` in your browser.
Under `"tests"`, `"openai"` should say `"result": "passed"`. If it says `failed`, the `error` text explains why (401 = wrong key, 429 = no credit or rate limit).

### 5. Switch the chat page to AI
Send the Worker address to Claude, or edit `start/index.html` and set:

```js
const AI_ENDPOINT = "https://daniel-project-assistant.<your-subdomain>.workers.dev";
```

## Backup AIs (optional)
You can also add `MISTRAL_API_KEY` (https://console.mistral.ai/api-keys) and/or `GEMINI_API_KEY` (https://aistudio.google.com/apikey) as Secrets.
The server tries OpenAI first, then Mistral, then Gemini, and moves to the next one automatically if one fails.

## Optional settings
Add these under **Settings → Variables and Secrets** as type **Text**:

| Name | What it does | Default |
| --- | --- | --- |
| `OPENAI_MODEL` | Which OpenAI model to use | `gpt-4.1-mini` |
| `MISTRAL_MODEL` | Which Mistral model to use | `mistral-small-latest` |
| `GEMINI_MODEL` | Which Gemini model to use | `gemini-2.5-flash` |
| `ALLOWED_ORIGINS` | Websites allowed to use this server, comma-separated | `https://temmy762.github.io` |

## For developers
```bash
npm i -g wrangler
wrangler login
wrangler secret put OPENAI_API_KEY
wrangler deploy
```
