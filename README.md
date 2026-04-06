
# 🦈 EcoFin AI

> **Smarter Fisheries, Greener Future**

EcoFin AI is a mobile-first web application built for Filipino fishers. It combines real-time catch logging with GPS, live weather and sea condition forecasting, catch history management, and automated notifications via Facebook Messenger and WhatsApp.

---

## 📱 Features

- **Dashboard** — Live weather display, sea condition warnings (Safe / Moderate / Danger), and catch summary
- **Log Catch** — Record catches with species, weight, size, source, depth, and GPS location via an interactive map
- **Catch History** — Searchable and filterable list of all logged catches, with option to clear history
- **Weather & Forecast** — Real-time weather and hourly fishing quality ratings (Excellent / Good / Fair / Poor)
- **Email / Password Auth** — Sign up and log in with email and password via Supabase Auth (email verification required)
- **Facebook Login** — OAuth 2.0 authentication via Facebook
- **Messenger Notifications** — Automated catch summaries and login alerts via Facebook Messenger
- **WhatsApp Notifications** — Catch summaries and login alerts via WhatsApp Cloud API
- **Profile Management** — Update name, email, and connected messaging accounts

---

## 🛠️ Tech Stack

### Frontend

| Technology | Purpose |
|---|---|
| HTML / CSS / JavaScript | UI and page logic (no framework) |
| Leaflet.js | Interactive GPS map on the Log Catch page |
| Open-Meteo API | Free weather and forecast data (no API key required) |
| Nominatim (OpenStreetMap) | Reverse geocoding GPS coordinates to location name |
| Font Awesome | Icons |

### Backend

| Technology | Purpose |
|---|---|
| Node.js + Express v5 | HTTP server and routing |
| express-session | Server-side session management |
| @supabase/supabase-js | Supabase Auth and database client |
| axios | HTTP requests to Meta APIs |
| body-parser | Request body parsing |
| dotenv | Environment variable management |
| Meta Graph API | Facebook OAuth + Messenger Send API |
| WhatsApp Cloud API | WhatsApp message delivery |

### Database & Deployment

| Service | Purpose |
|---|---|
| Supabase | PostgreSQL database + Auth (users, catches) |
| Railway / Render | Node.js server hosting |

---

## 📁 Project Structure

```
EcoFin-AI/
├── src/
│   ├── routes/
│   │   └── webhook.js              # Meta webhook handler
│   └── services/
│       ├── supabase.js             # Supabase helper functions (saveUser, saveCatch, etc.)
│       └── messengerService.js     # Messenger & WhatsApp send logic
├── images/                         # App icons and logos
├── auth.js                         # Frontend session check
├── server.js                       # Main Express app + all API routes
├── seedSupabase.js                 # Script to seed 20 test users and sample catches
├── dashboard.html                  # Home screen
├── login.html                      # Login (email/password + Facebook OAuth)
├── signup.html                     # Sign-up with email verification
├── verify.html                     # Email verification landing page
├── chat.html                       # Weather, forecast, and catch overview
├── log-catch.html                  # Log a new catch
├── history.html                    # Catch history
├── profile.html                    # User profile + connect messaging accounts
├── style.css                       # Global stylesheet
├── .env                            # Environment variables (not committed)
└── package.json
```

---

## 🗄️ Database Schema

Two tables are required in your Supabase project:

**`users`**

| Column | Type | Notes |
|---|---|---|
| `id` | text | Primary key (e.g. `fb_123`, `email_abc`, `user_001`) |
| `name` | text | |
| `email` | text | |
| `facebook_id` | text | Nullable |
| `psid` | text | Messenger Page-Scoped ID, nullable |
| `whatsapp` | text | Phone number, nullable |
| `waba_id` | text | WhatsApp Business Account ID, nullable |
| `location` | text | |
| `total_catches` | int | |
| `fishing_hours` | int | |
| `achievements` | int | |
| `success_rate` | int | |
| `member_since` | text | e.g. `"April 2026"` |
| `messenger_connected` | boolean | |
| `whatsapp_connected` | boolean | |

**`catches`**

| Column | Type | Notes |
|---|---|---|
| `id` | text | Primary key (e.g. `catch_user_001_1`) |
| `user_id` | text | Foreign key → `users.id` |
| `fish` | text | Species name |
| `weight` | text | In kg |
| `size` | text | e.g. `"45cm - 60cm"` |
| `source` | text | Sea / Lake / River / Pond |
| `location` | text | Location name |
| `depth` | text | In meters |
| `date` | text | e.g. `"Jan 15, 2026"` |

---

## 🚀 Setup Guide

Follow these steps **in order**.

---

### ✅ Prerequisites

- [Node.js v18+](https://nodejs.org/) — LTS version recommended
- A [Supabase account](https://supabase.com) — free tier is fine
- A [Facebook account](https://facebook.com) — for Meta Developer access
- A [Railway](https://railway.app) or [Render](https://render.com) account — for deployment

---

### Step 1 — Clone the Repository

```bash
git clone https://github.com/Awgori/EcoFin-AI.git
cd EcoFin-AI
npm install
```

---

### Step 2 — Set Up Supabase

#### 2A — Create a Supabase Project

1. Go to [https://supabase.com](https://supabase.com) and sign in
2. Click **New project**, give it a name (e.g. `ecofin-ai`), set a database password, and click **Create new project**

#### 2B — Create the Database Tables

1. In the Supabase dashboard, go to **Table Editor** → **New table**
2. Create the `users` and `catches` tables using the schema above
3. Disable **Row Level Security (RLS)** for now, or add policies that allow your server to read and write freely

#### 2C — Enable Email Auth

1. Go to **Authentication** → **Providers**
2. Make sure **Email** is enabled
3. Email confirmation is required by default — users will receive a verification link before they can log in

#### 2D — Get Your Supabase Credentials

1. Go to **Project Settings** → **API**
2. Copy the **Project URL** → `SUPABASE_URL`
3. Copy the **service_role secret** key → `SUPABASE_SERVICE_KEY`

> ⚠️ Use the `service_role` key on the server only. Never expose it in frontend code.

---

### Step 3 — Set Up the Meta Developer App

#### 3A — Create a Meta Developer Account

1. Go to [https://developers.facebook.com](https://developers.facebook.com) and log in
2. Click **Get Started** and complete registration

#### 3B — Create a New App

1. Go to **My Apps** → **Create App**
2. Select **Other** → **Next** → **Business** → **Next**
3. Enter a name (e.g. `EcoFin AI`) and click **Create app**

#### 3C — Add the Messenger Product

1. On the app dashboard, find **Messenger** and click **Set up**
2. Under **Access Tokens**, connect your Facebook Page and click **Generate token**
3. Copy the token → `PAGE_ACCESS_TOKEN`

> ℹ️ If you don't have a Facebook Page, create one at [facebook.com/pages/create](https://facebook.com/pages/create).

#### 3D — Get App Credentials

1. Go to **Settings** → **Basic**
2. Copy the **App ID** → `APP_ID`
3. Copy the **App Secret** → `APP_SECRET`

#### 3E — Add WhatsApp Product

1. On the app dashboard, find **WhatsApp** and click **Set up**
2. Copy the **Phone number ID** → `PHONE_NUMBER_ID`
3. Copy the **Temporary access token** → `WHATSAPP_TOKEN`

> ⚠️ The temporary WhatsApp token expires every 24 hours. Generate a permanent System User token from Meta Business Manager for production.

#### 3F — Add Test Users

1. Go to **App Roles** → **Roles** → **Add Testers**
2. Enter the Facebook usernames of anyone who needs to test the app

---

### Step 4 — Deploy to Railway or Render

#### Railway

1. Push your code to GitHub
2. Go to [https://railway.app](https://railway.app) → **New Project** → **Deploy from GitHub repo**
3. Select your repo — Railway will auto-detect Node.js
4. Go to your project → **Variables** and add all environment variables from Step 5
5. Your app URL will look like `https://ecofin-ai-production.up.railway.app`

#### Render

1. Push your code to GitHub
2. Go to [https://render.com](https://render.com) → **New** → **Web Service**
3. Connect your repo, set **Build Command** to `npm install` and **Start Command** to `npm start`
4. Add all environment variables from Step 5
5. Your app URL will look like `https://ecofin-ai.onrender.com`

---

### Step 5 — Configure Environment Variables

Add these in your Railway/Render dashboard, or create a `.env` file locally:

```env
# Supabase
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_SERVICE_KEY=your_service_role_key

# Facebook / Messenger
PAGE_ACCESS_TOKEN=your_page_access_token
APP_ID=your_app_id
APP_SECRET=your_app_secret
REDIRECT_URI=https://your-app-url/auth/messenger/callback

# WhatsApp
WHATSAPP_TOKEN=your_whatsapp_token
PHONE_NUMBER_ID=your_phone_number_id

# Webhook
VERIFY_TOKEN=EcoFinVerify123

# App
APP_URL=https://your-app-url
PORT=3000
```

---

### Step 6 — Set Facebook OAuth Redirect URI

1. Meta App dashboard → **Facebook Login** → **Settings**
2. Under **Valid OAuth Redirect URIs**, add: `https://your-app-url/auth/messenger/callback`
3. Click **Save changes**

---

### Step 7 — Set the Messenger Webhook

1. Meta App → **Messenger** → **Settings** → **Webhooks** → **Add Callback URL**
2. Callback URL: `https://your-app-url/webhook`
3. Verify Token: `EcoFinVerify123`
4. Subscribe to `messages` and `messaging_postbacks`

---

### Step 8 — Seed the Database (Optional)

To populate Supabase with 20 test users and sample catches:

```bash
node seedSupabase.js
```

This creates 20 accounts (all with password `EcoFin2025!`) and seeds 2–4 catches per user. All credentials are printed to the console when the script finishes.

---

## 💬 Notification Format

When a catch is logged, a notification is sent to the user's connected Messenger and/or WhatsApp:

```
New Catch Logged 🎣
Tuna — 25.5 kg (45–60 cm)
📍 South China Sea
📅 Jan 15, 2026
```

When a user logs in, they also receive a welcome message with quick-action buttons on their connected accounts.

---

## ⚠️ Important Notes

### Meta App Review

This app runs in **Development Mode** on Meta. Only users added as Testers, Developers, or Admins can use Facebook Login and receive messages. For public access, the app must go through **Meta App Review**, which requires business verification.

### WhatsApp Token Expiry

The temporary WhatsApp access token expires every 24 hours. For production, generate a permanent System User token from Meta Business Manager.

### Supabase RLS

For production, enable Row Level Security on your Supabase tables and configure appropriate policies so only authenticated users can access their own data.

---

## 📄 License

ISC
