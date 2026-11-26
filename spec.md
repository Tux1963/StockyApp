# Stocky – Product Specification

Version 0.1 (draft for review) · 2026-11-26

## 1. Overview

Stocky is a mobile-first web app for tracking what you eat. Users log the food they eat at breakfast, lunch, dinner and snacks, and Stocky adds up their calories, protein, carbs and fat for the day and compares them with their daily goals.

Users can search a public food database or create their own food items. Foods they create or use are saved to their account so they can find them quickly next time.

The app is a website built for phones first. It also works on tablets and desktop browsers, but every screen is designed for a narrow touch screen before anything else.

## 2. Goals for version 1

1. A user can sign up and log in with email and password or with Google.
2. A user can log food to a meal on any day, by searching the public database or their own saved foods.
3. A user can create a custom food item when they can't find it.
4. A user can see their calories and macros for the day against their goals.
5. A user can come back on any day, see past days, and add to or change what they logged.

### Out of scope for version 1

These may come later but are not part of the first release:

- Barcode scanning
- Recipes (combining several foods into one item)
- Exercise tracking, weight, sleep and water
- Charts and trends over weeks or months
- Sharing foods between users (a community database)
- Native iOS or Android apps, and working offline
- Apple sign-in
- Other languages than English

## 3. Users and accounts

### Sign-up and log-in

Authentication uses Firebase Authentication with two methods:

| Method | Details |
| --- | --- |
| Email and password | Sign up with email and password (at least 8 characters). Password reset by email. Email verification is sent but not required to use the app. |
| Google | One-tap "Continue with Google". |

If someone signs in with Google using an email that already has a password account, Firebase links both methods to the same account.

### First-time setup (onboarding)

After a user's first sign-in, they set their daily goals before they reach the diary:

- Display name (prefilled from Google if available)
- Daily calorie goal (default 2,000 kcal)
- Macro split as percentages of calories (default 50% carbs, 20% protein, 30% fat), shown as grams as well

The user can skip this step and keep the defaults. Goals can be changed later in Settings.

### Signed-out users

Signed-out users only see the landing page and the log-in/sign-up screens. Every other page sends them to log in, then back to where they were going.

## 4. Main user flow

1. User opens the app and logs in (or signs up and sets goals).
2. User lands on **Today's diary**.
3. User taps **Add food** under a meal (e.g. Breakfast).
4. User searches for a food. Results show their own saved foods first, then foods from the public database.
5. User picks a food, enters how much they ate (grams or servings), and confirms.
6. The entry appears under the meal and the day's totals update.
7. If the food isn't found, the user taps **Create food**, enters its nutrition, and it is saved to their foods and logged.
8. The next day, the user opens the app, sees a new empty day, and can switch back to earlier days with the date picker.

## 5. Screens

All screens are designed for a phone screen first (360–430 px wide). On wider screens the content stays in a centred column (max about 600 px).

### 5.1 Landing page (`/`)

What Stocky is, with **Sign up** and **Log in** buttons. Signed-in users are sent straight to the diary.

### 5.2 Log in (`/login`) and Sign up (`/signup`)

- Email and password fields, and a **Continue with Google** button
- "Forgot password?" link on log-in, which sends a reset email
- Clear error messages (wrong password, email already used, etc.)

### 5.3 Onboarding (`/setup`)

The goal-setting form from section 3, shown once after first sign-in.

### 5.4 Diary (`/diary` and `/diary/:date`)

The main screen.

- **Date bar** at the top: previous day, the date (tap to open a calendar), next day, and a "Today" shortcut. Future dates can be viewed but show a note that they are in the future.
- **Daily summary**: calories eaten, goal and calories remaining, with a progress bar. Below it, protein, carbs and fat in grams against their goals.
- **Four meal sections**: Breakfast, Lunch, Dinner, Snacks. Each shows its entries (food name, amount, calories) and its own calorie subtotal, and has an **Add food** button.
- Tapping an entry opens it to change the amount, move it to another meal, or delete it.
- Going over a goal is shown clearly (e.g. remaining turns red and shows "over by …").

### 5.5 Add food (`/diary/:date/add?meal=breakfast`)

- Search field, focused when the screen opens
- Before typing: a **Recent** list of foods the user logged lately, and a tab for **My foods**
- While typing (after 2 characters, with a short pause before searching): results in two groups, **My foods** first, then **Food database**
- Each result shows the name, brand (if any), and calories per serving or per 100 g
- **Create food** button always visible at the bottom

### 5.6 Food details and amount (`/food/...` as a sheet over Add food)

- Name, brand, and nutrition (calories, protein, carbs, fat)
- Amount field with a unit choice: grams, or the food's serving sizes (e.g. "1 slice (30 g)")
- Meal choice (prefilled from where the user came from)
- Nutrition updates live as the amount changes
- **Add to diary** button

### 5.7 Create / edit food (`/foods/new`, `/foods/:id/edit`)

Fields:

| Field | Required | Notes |
| --- | --- | --- |
| Name | Yes | e.g. "Homemade granola" |
| Brand | No | |
| Nutrition basis | Yes | Per 100 g, or per serving |
| Serving name and size in grams | If per serving | e.g. "1 bowl", 60 g |
| Calories (kcal) | Yes | |
| Protein, carbs, fat (g) | Yes | Can be 0 |

When the user saves, the food is stored in their foods. If they came from Add food, it then goes on to the amount screen to log it.

### 5.8 My foods (`/foods`)

A list of the user's saved foods with search, edit and delete. Deleting a food does not remove past diary entries that used it (see section 6).

### 5.9 Settings (`/settings`)

- Change display name and goals
- Change password (email accounts only)
- Log out
- Delete account (removes the account and all its data after a confirmation)

### Navigation

A bottom tab bar on phones with **Diary**, **My foods** and **Settings**.

## 6. Data model (Cloud Firestore)

All user data is stored under the user's own document, so users can only ever see their own data.

```
users/{uid}
users/{uid}/foods/{foodId}
users/{uid}/entries/{entryId}
```

### `users/{uid}` – profile and goals

| Field | Type | Notes |
| --- | --- | --- |
| displayName | string | |
| email | string | |
| goals.calories | number | kcal per day |
| goals.proteinG | number | grams per day |
| goals.carbsG | number | grams per day |
| goals.fatG | number | grams per day |
| onboardingDone | boolean | |
| createdAt | timestamp | |

### `users/{uid}/foods/{foodId}` – the user's saved foods

Holds both foods the user created and public-database foods they have logged (saved so they show up in **My foods** and **Recent**, and so the app does not depend on the public database being available).

| Field | Type | Notes |
| --- | --- | --- |
| name | string | |
| nameLower | string | lowercase name, used for searching |
| brand | string or null | |
| source | `"custom"` or `"openfoodfacts"` | |
| sourceId | string or null | the product code in Open Food Facts |
| per100g | map | `{ calories, proteinG, carbsG, fatG }` – null for foods entered per serving with no weight |
| servings | array | `[{ name: "1 slice", grams: 30 }]` |
| perServing | map or null | for custom foods entered per serving |
| lastUsedAt | timestamp | for the Recent list |
| createdAt, updatedAt | timestamp | |

### `users/{uid}/entries/{entryId}` – diary entries

One document per food logged.

| Field | Type | Notes |
| --- | --- | --- |
| date | string | the user's local date, `YYYY-MM-DD` |
| meal | `"breakfast"`, `"lunch"`, `"dinner"` or `"snacks"` | |
| foodId | string | link to the saved food |
| foodName, brand | string | copied from the food at the time it was logged |
| amount | number | how much was eaten |
| unit | string | `"g"` or a serving name |
| grams | number or null | amount converted to grams |
| calories, proteinG, carbsG, fatG | number | calculated when logged |
| createdAt | timestamp | |

Each entry keeps its own copy of the name and nutrition. That way, editing or deleting a food later does not change what was logged on earlier days. The day's totals are added up in the app from that day's entries; they are not stored separately.

### Dates and time zones

Entries are stored under the user's **local** calendar date (`YYYY-MM-DD`), worked out on the device. A meal eaten at 11 pm Monday stays on Monday even if the user later travels to another time zone.

### Security rules

Firestore security rules allow a signed-in user to read and write only documents under `users/{their uid}`. The rules also check field types and that numbers are not negative.

## 7. Food search

Search looks in two places at once and shows the results together:

1. **My foods** – a prefix search on `nameLower` in the user's own foods in Firestore.
2. **Public database** – the [Open Food Facts](https://world.openfoodfacts.org/) search API. It is free, needs no API key, and has millions of packaged foods. Its nutrition values are per 100 g.

Rules:

- Search starts after 2 characters, about 300 ms after the user stops typing.
- Show up to 20 public results; load more when the user scrolls.
- Skip public results that are missing calories.
- Results from Open Food Facts are only saved to the user's foods when the user actually logs one.
- If the public database is slow or down, still show **My foods** results and a short message that the database is unavailable.

Open Food Facts asks apps to send a `User-Agent` naming the app (e.g. `Stocky/0.1`). Data quality varies because the database is filled in by volunteers, so users can always edit a saved food's values.

**Alternative considered:** the USDA FoodData Central API has better data for unpackaged foods (fruit, vegetables, meat) but needs an API key and is US-focused. It could be added later as a second source.

## 8. Nutrition calculations

- For per-100 g foods: `value = per100g.value × grams / 100`
- For serving amounts: `grams = number of servings × serving grams`, then as above
- For custom foods entered per serving with no weight: `value = perServing.value × number of servings`
- Macro goals in grams come from the calorie goal and percentages: protein and carbs 4 kcal per gram, fat 9 kcal per gram
- Show calories as whole numbers and macros to one decimal place

## 9. Technical design

| Part | Choice |
| --- | --- |
| Front end | Vue 3 with `<script setup>`, plain JavaScript, built with Vite (the existing `StockyApp` project) |
| Routing | Vue Router, with a guard that sends signed-out users to `/login` |
| State | Pinia stores: `auth`, `profile`, `diary`, `foods` |
| Back end | Firebase: Authentication and Cloud Firestore (no custom server) |
| Food database | Open Food Facts API, called from the browser |
| Hosting | Firebase Hosting |
| Styling | Plain CSS with the existing CSS variables, mobile-first; light and dark mode follow the phone's setting |

### Firebase setup

- Firebase settings go in a `.env.local` file (`VITE_FIREBASE_API_KEY` etc.), which is not committed to git.
- Firestore indexes needed: `entries` by `date`; `foods` by `nameLower`; `foods` by `lastUsedAt`.
- Use the Firebase emulators for local development.

### Mobile-first requirements

- Touch targets at least 44 × 44 px
- No sideways scrolling at 360 px wide
- Number fields open the number keyboard on phones
- The diary should load in under 2 seconds on a normal 4G connection

## 10. Build plan

1. **Foundation** – remove the starter components, add Vue Router, Pinia and Firebase, and set up the layout and bottom navigation.
2. **Accounts** – sign up, log in, Google sign-in, password reset, route guards, onboarding and settings.
3. **Custom foods** – create, edit, delete and list the user's own foods.
4. **Diary** – date bar, meal sections, logging and editing entries, daily totals against goals.
5. **Public food search** – Open Food Facts search, combined results, saving public foods on first use, Recent list.
6. **Finishing** – security rules, empty and error states, loading states, testing on real phones, deploy to Firebase Hosting.

## 11. Open questions

1. **Units:** is metric only (grams, kcal) fine for version 1, or do you need ounces?
2. **Goals:** should the app suggest a calorie goal from age, height, weight and activity level, or is entering it by hand enough?
3. **Landing page:** the current landing page lists activity, sleep, water and weight tracking. Should it be changed to describe food and calorie tracking only, to match this spec?
4. **Meals:** are the four fixed meals (breakfast, lunch, dinner, snacks) enough, or should users be able to rename or add meals?
5. **Copying meals:** is a "copy yesterday's breakfast" shortcut wanted in version 1?
