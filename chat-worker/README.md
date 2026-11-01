# Daniel's AI project assistant

The small server behind the AI chat at https://temmy762.github.io/start/.
It keeps the Gemini API key secret, talks to Gemini, and hands the finished project brief back to the chat page, which emails it to Daniel.

If this server is not set up or is down, the chat page automatically switches to the question-by-question chat, so no visitor is lost.

## Set it up (about 10 minutes, no coding)

### 1. Get a Gemini API key
1. Go to https://aistudio.google.com/apikey and sign in with your Google account.
2. Click **Create API key** and copy it. Keep it private.

### 2. Create the Worker on Cloudflare
1. Sign up for free at https://dash.cloudflare.com/sign-up.
2. In the left menu open **Compute (Workers)** → **Workers & Pages** → **Create** → **Create Worker**.
3. Name it `daniel-project-assistant` and click **Deploy**.
4. Click **Edit code**. Delete everything in the editor, paste the whole contents of [`worker.js`](./worker.js), then click **Deploy**.

### 3. Add the key
1. Go back to the Worker and open **Settings** → **Variables and Secrets** → **Add**.
2. Type: **Secret**. Name: `GEMINI_API_KEY`. Value: the key from step 1. Click **Deploy**.

### 4. Check it works
Open `https://daniel-project-assistant.<your-subdomain>.workers.dev/health` in your browser.
You should see `{"ok":true,"configured":true}`.

### 5. Switch the chat page to AI
Send the Worker address to Claude, or edit `start/index.html` and set:

```js
const AI_ENDPOINT = "https://daniel-project-assistant.<your-subdomain>.workers.dev";
```

## Optional settings
Add these under **Settings → Variables and Secrets** as type **Text**:

| Name | What it does | Default |
| --- | --- | --- |
| `GEMINI_MODEL` | Which Gemini model to use | `gemini-2.5-flash` |
| `ALLOWED_ORIGINS` | Websites allowed to use this server, comma-separated | `https://temmy762.github.io` |

## For developers
```bash
npm i -g wrangler
wrangler login
wrangler secret put GEMINI_API_KEY
wrangler deploy
```
