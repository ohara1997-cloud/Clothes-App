# Project plan

Status: draft v0.1. Several decisions are still open. These are tracked in [REQUIREMENTS.md](REQUIREMENTS.md#open-questions).

## 1. The main risk

The LLM part is the easy part. The hard part is getting every item into the app. If adding a single item takes more than about 15 seconds, most people stop before the wardrobe is complete, and recommendations from an incomplete wardrobe are poor. The plan below treats fast item capture and automatic tagging as the core of the product.

## 2. Phases

| Phase | Output | Exit condition |
|---|---|---|
| 1. Requirements | `REQUIREMENTS.md` agreed | All "must" requirements confirmed, open questions answered |
| 2. Design | Wireframes, then clickable mockups | Every must requirement maps to a screen |
| 3. Technical design | Architecture, data model, prompt design | Decisions recorded in `docs/DECISIONS.md` |
| 4. Build MVP | Working app behind login | Must requirements pass acceptance tests |
| 5. Test | Automated suite plus LLM evaluation set | Test protocol in section 5 passes |
| 6. Release | v0.1.0 tagged and deployed | Running on hosting, used for one real week |
| 7. Iterate | v0.2 onwards | Driven by notes from real use |

Mockups come after requirements on purpose. Designing screens first tends to lock in features nobody has agreed on.

## 3. Proposed approach (to be confirmed)

These are recommendations, not decisions. Each depends on answers in the requirements doc.

### 3.1 App type

Recommendation: a Progressive Web App (PWA). It runs in the phone browser, can be added to the home screen, and can use the phone camera. It avoids App Store and Play Store review and fees. A native app is only worth it if offline use or deep camera features turn out to be essential.

### 3.2 Stack

| Layer | Recommendation | Why |
|---|---|---|
| Frontend | React with Vite, TypeScript | Widely documented, easy to test |
| Backend and database | Supabase (Postgres, auth, file storage) | One service covers login, data and images, with a free tier |
| LLM calls | A small server function (Supabase Edge Function or Vercel function) calling the Claude API | The API key must never be in the browser |
| Hosting | Vercel or Netlify for the frontend | Free tier, deploys from GitHub, preview link per pull request |

Pricing and free tier limits for these services change. They need checking at the technical design phase.

### 3.3 How the LLM is used

There are two separate LLM jobs.

1. **Tagging on upload.** When a photo is added, a vision capable model reads it once and suggests tags: category, colours, pattern, formality, season, warmth. The user confirms or edits. This is what makes capture fast.
2. **Recommending outfits.** The request sends the text tags of the wardrobe, not the photos, along with the user's prompt and any items they are already wearing. The model returns item IDs plus a short reason. Sending text rather than images keeps each request cheaper and faster.

The app must check that every returned item ID exists in the user's wardrobe before showing a result. LLMs can return items that do not exist.

### 3.4 Data model (first sketch)

- **User**: id, email, settings
- **Item**: id, user id, photo path, category, colours, pattern, formality, seasons, warmth, notes, status (available, in wash, retired), created date
- **Outfit**: id, user id, item ids, prompt used, reason given, rating, worn date
- **Request log**: prompt, items sent, response, cost estimate, time taken (useful for debugging and cost tracking)

## 4. Design approach

1. Low fidelity wireframes in Markdown. Started in [WIREFRAMES.md](WIREFRAMES.md).
2. After requirements are agreed, clickable HTML mockups of the main flows.
3. Design for phone width first. Desktop is a stretched version of the same layout.
4. Accessibility baseline: WCAG 2.2 AA colour contrast, labelled controls, usable with a screen reader.

## 5. Testing protocol

| Level | Tool | What it covers | When it runs |
|---|---|---|---|
| Unit | Vitest | Tag parsing, ID validation, prompt building | Every push |
| Component | React Testing Library | Forms, item grid, outfit card | Every push |
| End to end | Playwright | Sign in, add item, get outfit, save outfit | Every pull request |
| Accessibility | axe with Playwright | Each main screen | Every pull request |
| LLM evaluation | Script with fixed test wardrobes | See below | Before each release and after any prompt change |
| Manual | Real phone, iOS Safari and Android Chrome | Camera capture, home screen install | Before each release |

### 5.1 LLM evaluation set

LLM output varies between runs, so normal pass or fail tests are not enough on their own. The plan is:

- Three fixture wardrobes of about 20 to 40 items each, stored as JSON with test photos.
- About 30 fixed prompts, for example "I'm wearing the navy chinos, something smart casual", "cosy rainy day", "first date, nothing too formal".
- Hard checks on every response, which must pass 100 percent of the time: valid JSON, all item IDs exist, no duplicate categories where that makes no sense (two pairs of trousers), any "already wearing" items included, items marked "in wash" excluded.
- Soft checks, scored by a person or a second model: does the outfit match the mood, is the reason sensible. Target score to be set after the first run.
- In automated CI the LLM is replaced with recorded responses, so tests are fast, free and repeatable. Live LLM evaluation is run by hand.

### 5.2 Definition of done for a feature

- Acceptance criteria from the requirements doc pass
- Tests added and green in CI
- Works on phone width
- No new accessibility violations

## 6. Release planning

### 6.1 GitHub

- `main` is always deployable. Work happens on short lived branches with pull requests.
- Branch protection on `main`: CI must pass before merge.
- GitHub Actions workflow: install, lint, type check, unit and component tests, Playwright tests.
- GitHub Issues for each requirement, grouped into milestones (v0.1, v0.2).
- Secrets (Claude API key, Supabase keys) stored in hosting and GitHub Actions secrets. Never committed.

### 6.2 Hosting

- Frontend: Vercel or Netlify connected to the repo. Every pull request gets a preview URL. Merging to `main` deploys production.
- Backend: Supabase project. Separate projects for development and production if the free tier allows.
- Database changes kept as migration files in the repo.

### 6.3 Versions

Semantic versioning. Each release gets a Git tag and a GitHub Release with notes.

| Version | Scope |
|---|---|
| v0.1.0 | MVP: sign in, add items with auto tags, browse wardrobe, get outfit from prompt and "already wearing" |
| v0.2.0 | Saved outfits, ratings, "in wash" status, history so recent outfits are not repeated |
| v0.3.0 | Weather aware suggestions, calendar or occasion input |
| Later | Background removal on photos, sharing, packing lists |

The v0.2 and v0.3 scopes are placeholders until requirements are agreed.

### 6.4 Cost control

- Spending limit set on the Claude API account.
- Per user daily request cap in the server function.
- Request log records cost estimate per call.

## 7. Next steps

1. Answer the open questions in [REQUIREMENTS.md](REQUIREMENTS.md#open-questions).
2. Mark each requirement as must, should, could or won't for v0.1.
3. Turn wireframes into clickable mockups.
