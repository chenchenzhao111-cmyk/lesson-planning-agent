# Lesson Planning Agent — Version 4 (Live LLM)

This replaces V3's fixed topic-specific generator with a **real, runtime OpenAI Responses API call**. The server performs V3 rule-based `CLARIFY` / `HUMAN_REVIEW` routing before calling the model for `GENERATE`. It returns four tailored instructional phases and a ready-to-use example. **No API key is embedded in the browser.**

## Folder layout

- `site/index.html` — website (frontend).
- `netlify/functions/generate-plan.mjs` — secure, server-side validation + OpenAI API call.
- `netlify.toml` — identifies publish & functions directories.
- `tests/` — local mocked test (does not use your API quota).

## Deploy (recommended: Netlify CLI or GitHub connection)

**Important:** Uploading only `index.html` or dragging only the `site` folder to Netlify will NOT set up the live function. Use Git-connected deployment or the Netlify CLI so Netlify builds and deploys the function.

1. Download and extract the full project ZIP. Keep `site`, `netlify`, and `netlify.toml` in the same project root.
2. In your OpenAI **API platform** account, create an API key and ensure your account has API credits. ChatGPT Plus and OpenAI API billing are separate.
3. In your existing Netlify site, navigate to **Project configuration → Environment variables** (labels can vary), and add:
   - `OPENAI_API_KEY`: your private OpenAI API key; set scope including **Functions**, and **never put this key in any site HTML or Git repository**.
   - Optional `OPENAI_MODEL`: defaults to `gpt-4.1-mini` (must be available to your API account).
4. Redeploy after setting the variable.
5. **CLI deployment into your existing site** (requires Node.js and Netlify account access):
   ```bash
   npm install -g netlify-cli
   cd Lesson_Planning_Agent_V4_Live_LLM
   netlify login
   netlify link
   netlify deploy --prod
   ```
   When prompted, link to your existing Netlify site to preserve its URL. The command reads `netlify.toml` and deploys `site/` plus `netlify/functions/`. Alternatively push this project to a GitHub repository and connect the repository to Netlify.
6. Open your Netlify URL, enter lesson requirements, and click **Generate with live AI**. It should show `GENERATE` and `Live LLM-generated draft`. Then test missing duration (`CLARIFY`) and 6×15 minutes in 20 minutes (`HUMAN_REVIEW`).

### Running locally

Run `netlify dev` from the project root, with `OPENAI_API_KEY` configured in your local Netlify CLI environment or a private uncommitted `.env` file. Opening `site/index.html` directly with `file://` will **not** run the Netlify backend.

### Important limitations

- This version sends teacher-entered lesson fields to the configured OpenAI API service. **Do not submit personally identifiable student records, grades, or sensitive data.** The request sets `store:false`.
- LLM outputs can still be inaccurate, miss constraints, or include impractical activities. Teacher review is necessary. No internet research or source verification is performed.
- Every successful `GENERATE` request invokes a paid API call. Protect the public site with Netlify access controls or add authentication/rate limits before sharing widely; set API spending limits/alerts. The included starter code limits request length but is **not** a full anti-abuse system.
- The four phase durations are calculated and enforced in code, but the educational quality and appropriateness of AI-generated tasks need human evaluation.
- If the API isn't configured, the app displays a clear ERROR instead of silently falling back to rule-based generation.

### Relevance to EDUC 6192 Project

Keep the original V2/V3 copies for baseline comparison and document V4 as an additional implementation that now uses an LLM API at runtime. **If your evaluation plan explicitly describes V2 as the system under evaluation, update the architecture and repeated-testing sections before testing V4.**
