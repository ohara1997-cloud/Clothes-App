# Requirements

Status: draft. Nothing here is confirmed yet. Items marked **(proposed)** are suggestions waiting for the owner's decision.

Priority uses MoSCoW: Must, Should, Could, Won't (for v0.1).

## 1. Purpose

Store photos of every clothing item the owner has, and use an LLM to suggest what to wear on a given day based on:

- items the owner is already wearing, and/or
- a mood or style described in plain language.

## 2. Users

- Single user: the owner only. **(confirmed 2026-11-09)**

## 3. Functional requirements

### 3.1 Wardrobe

| ID | Requirement | Priority |
|---|---|---|
| W1 | Add an item by taking a photo with the phone camera | Must (proposed) |
| W2 | Add an item by uploading an existing photo | Must (proposed) |
| W3 | LLM suggests tags for a new item from its photo, user confirms or edits | Must (proposed) |
| W4 | Edit an item's tags and notes | Must (proposed) |
| W5 | Delete an item | Must (proposed) |
| W6 | Browse the wardrobe as a photo grid | Must (proposed) |
| W7 | Filter the wardrobe by category, colour, season | Should (proposed) |
| W8 | Mark an item as "in wash" or unavailable so it is not suggested | Should (proposed) |
| W9 | Add several items in one go (bulk upload) | Could (proposed) |
| W10 | Remove photo backgrounds automatically | Could (proposed) |

### 3.2 Recommendations

| ID | Requirement | Priority |
|---|---|---|
| R1 | Type a free text prompt describing mood, style or occasion | Must (proposed) |
| R2 | Pick one or more items "I'm already wearing" from the wardrobe | Must (proposed) |
| R3 | Receive at least one complete outfit made only from owned items | Must (proposed) |
| R4 | Each outfit shows a short reason | Should (proposed) |
| R5 | Ask for another suggestion with the same inputs | Should (proposed) |
| R6 | Swap a single item in a suggested outfit | Could (proposed) |
| R7 | Use the local weather forecast | Could (proposed) |
| R8 | Avoid repeating recently worn outfits | Could (proposed) |

### 3.3 Outfits and history

| ID | Requirement | Priority |
|---|---|---|
| O1 | Save a suggested outfit | Should (proposed) |
| O2 | Mark an outfit as worn on a date | Could (proposed) |
| O3 | Rate an outfit, used to improve future suggestions | Could (proposed) |

### 3.4 Account

| ID | Requirement | Priority |
|---|---|---|
| A1 | Sign in so data is private and syncs between devices | Must (proposed) |
| A2 | Export all data and photos | Should (proposed) |
| A3 | Delete account and all data | Should (proposed) |

## 4. Non functional requirements

| ID | Requirement | Target |
|---|---|---|
| N1 | Time to add one item, photo to saved | Under 15 seconds (proposed) |
| N2 | Time to get a recommendation | Under 10 seconds (proposed) |
| N3 | Wardrobe size supported | Up to 150 items expected, design for 300 (confirmed size 50 to 150) |
| N4 | Phone first web app (PWA), installable to home screen | Confirmed. Browser versions still proposed: iOS Safari and Android Chrome, latest two |
| N5 | Running cost | Open, see Q6 |
| N6 | Privacy | Photos stored privately, only visible to the owner. Photos sent to the LLM provider once, for tagging only (confirmed) |
| N7 | Accessibility | WCAG 2.2 AA (proposed) |

## 5. Acceptance criteria for the core flows (draft)

**Add item**
- Given I am signed in, when I take a photo and accept the suggested tags, then the item appears in my wardrobe grid.
- If the LLM call fails, I can still save the item and enter tags by hand.

**Get outfit**
- Given my wardrobe has at least one top, one bottom and one pair of shoes, when I enter a prompt, then I see an outfit using only my items.
- Any item I selected as "already wearing" is part of every outfit shown.
- No item marked "in wash" appears.
- If the LLM returns an item that does not exist, it is not shown and the app retries or tells me.

## 6. Open questions

| # | Question | Why it matters |
|---|---|---|
| Q1 | Is this only for you, or will others use it (partner, friends, public)? | Answered: just the owner. |
| Q2 | Phone first, desktop first, or both? | Answered: phone, as a web app. |
| Q3 | Which LLM provider, and are you happy for photos to be sent to it? | Partly answered: photos may be sent for tagging. Provider not yet chosen. |
| Q4 | Roughly how many items are in your wardrobe? | Answered: 50 to 150 items. |
| Q5 | How much effort will you put into adding items? One by one, or photograph everything in one session? | Decides whether bulk upload is a must |
| Q6 | What monthly running cost are you comfortable with? | Hosting tier and LLM call limits |
| Q7 | Should weather be taken into account from v0.1? | Adds an external API and location permission |
| Q8 | Do you want the app to learn your taste over time (ratings, history)? | Data model and prompt design |
| Q9 | Do you have any coding experience, and will you maintain this yourself? | Affects how simple the stack must be |
| Q10 | Any accessories to include: shoes, bags, jewellery, coats? | Category list and outfit rules |
| Q11 | Is there a deadline or target date for a first usable version? | Release plan |

## 7. Decisions log

| Date | Decision | Reason |
|---|---|---|
| 2026-11-09 | Single user | Owner only, keeps auth and cost simple |
| 2026-11-09 | Phone first PWA | No app store, camera access via browser |
| 2026-11-09 | Wardrobe of 50 to 150 items | Whole wardrobe can be sent as text tags in one request |
| 2026-11-09 | Photos sent to LLM for tagging | Makes adding items fast |
