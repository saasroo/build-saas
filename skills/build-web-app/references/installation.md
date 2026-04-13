# Fireact Installation Reference

This guide covers everything needed to install and run a Fireact SaaS application locally from scratch.

## Prerequisites

### Node.js v18+

Fireact requires Node.js v18 or higher.

```bash
# Check your version
node --version

# Install via nvm (recommended)
nvm install 18
nvm use 18

# Or download from nodejs.org
```

### Firebase CLI

```bash
npm install -g firebase-tools
firebase login
```

### Stripe CLI (for local webhook testing)

```bash
# macOS
brew install stripe/stripe-cli/stripe

# Windows
scoop bucket add stripe https://github.com/stripe/scoop-stripe-cli.git
scoop install stripe

# Linux
# Download from https://github.com/stripe/stripe-cli/releases

# Login after install
stripe login
```

## Firebase Project Setup

Before running the CLI you need a Firebase project configured with the correct services.

### 1. Create a Firebase project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project"
3. Give it a name, follow the prompts
4. Note the project ID (you'll need it)

### 2. Enable Firebase services

In the Firebase Console for your project:

**Authentication:**
- Go to Build → Authentication → Get started
- Sign-in method tab → Enable "Email/Password"

**Firestore:**
- Go to Build → Firestore Database → Create database
- Choose "Start in test mode" for development (tighten rules before production)
- Pick a region close to your users

**Cloud Functions:**
- Go to Build → Functions → Get started
- You'll need to upgrade to Blaze (pay-as-you-go) plan — Functions require Blaze

### 3. Register a web app and get the config

1. In Firebase Console → Project Settings (gear icon)
2. Scroll to "Your apps" → click "Add app" → Web
3. Give it a nickname (e.g., "My SaaS App"), click "Register app"
4. Copy the `firebaseConfig` object — the CLI will fetch this automatically

## Stripe Account Setup

### 1. Create or log in to a Stripe account

Go to [dashboard.stripe.com](https://dashboard.stripe.com). Start in test mode (toggle in the top left).

### 2. Create subscription products and prices

1. Stripe Dashboard → Products → Add product
2. Give it a name (e.g., "Pro Plan")
3. Under Pricing: choose "Recurring", set the price and interval (monthly/yearly)
4. Save — you'll see a Price ID like `price_1ABC...`
5. Repeat for each plan you want to offer

### 3. Get API keys

Stripe Dashboard → Developers → API keys:
- **Publishable key**: starts with `pk_test_...`
- **Secret key**: starts with `sk_test_...`

Keep these handy — the CLI will ask for them.

## Running `create-fireact-app`

### Option A: Global install (recommended for repeated use)

```bash
npm install -g create-fireact-app
create-fireact-app my-saas-app
```

### Option B: npx (no install required)

```bash
npx create-fireact-app my-saas-app
```

### What the CLI does

The CLI will prompt you through:

1. **Firebase project selection** — lists your projects, pick one
2. **Firebase web app selection** — lists web apps in the project, pick one; the CLI fetches SDK config automatically
3. **Stripe configuration** — enter publishable key, secret key, then configure subscription plans with Price IDs
4. **Dependency installation** — installs npm packages for both the React app and Cloud Functions automatically

The scaffolded project includes a Claude Code skill at `.claude/skills/fireact-builder/` for AI-assisted customization.

## Post-Installation Build

After the CLI finishes, navigate into the project and build everything:

```bash
cd my-saas-app

# Build the React app
npm run build

# Build Cloud Functions
cd functions && npm run build && cd ..
```

Both builds must succeed before starting the emulators.

## Starting Local Development

### Start Firebase emulators

```bash
firebase emulators:start
```

This starts the following services:
- **React app (Hosting)**: http://localhost:5002
- **Auth emulator**: http://localhost:9099
- **Firestore emulator**: http://localhost:8080
- **Functions emulator**: http://localhost:5001
- **Emulator UI**: http://localhost:4000

### Start Stripe webhook forwarding (separate terminal)

```bash
stripe listen --forward-to http://127.0.0.1:5001/<your-firebase-project-id>/us-central1/stripeWebhook
```

The CLI output shows a webhook signing secret starting with `whsec_`. Copy it.

### Update the webhook secret

1. Open `functions/src/config/stripe.config.json`
2. Update `endpointSecret` with the `whsec_` value from the Stripe CLI
3. Rebuild functions and restart emulators:
   ```bash
   cd functions && npm run build && cd ..
   firebase emulators:start
   ```

Open http://localhost:5002 in your browser to see the app.

## Directory Structure After Scaffolding

```
my-saas-app/
├── .claude/                    # Claude Code skill
│   └── skills/
│       └── fireact-builder/   # AI-assisted customization
├── src/                        # React application source
│   ├── components/            # UI components
│   ├── contexts/              # React contexts
│   ├── hooks/                 # Custom hooks
│   ├── config/                # Config files (firebase, app)
│   └── i18n/                  # Translations
├── functions/                  # Cloud Functions backend
│   └── src/
│       ├── functions/         # Individual function modules
│       └── config/            # stripe.config.json, plans config
├── public/                     # Static assets
├── firebase.json              # Firebase configuration
├── firestore.rules            # Firestore security rules
├── firestore.indexes.json     # Firestore indexes
└── package.json               # Root dependencies
```

Key config files:
- `src/config/firebase.config.json` — Firebase SDK credentials + emulator settings
- `src/config/app.config.json` — Social login flags and app settings
- `functions/src/config/stripe.config.json` — Stripe keys and webhook secret
- `functions/src/config/app.config.json` — Permission definitions

## Troubleshooting Common Installation Issues

### `create-fireact-app` command not found

```bash
# Check global npm path
npm config get prefix

# Ensure it's in PATH (add to ~/.bashrc or ~/.zshrc)
export PATH=~/.npm-global/bin:$PATH

# Or use npx instead
npx create-fireact-app my-app
```

### Node version incompatibility

```bash
node -v  # Must be v18+
nvm install 18 && nvm use 18
```

### npm install permission errors

```bash
# Do NOT use sudo. Fix npm permissions instead:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
source ~/.zshrc  # or ~/.bashrc
```

### Firebase login fails or hangs

```bash
# Try CI mode (avoids browser redirect)
firebase login --no-localhost

# Or clear cache and retry
rm -rf ~/.config/firebase
firebase login
```

### No web apps found in Firebase project

The CLI requires at least one web app registered in the Firebase project. Go to Firebase Console → Project Settings → Your apps → Add app → Web.

### npm install hangs during scaffolding

```bash
# Cancel with Ctrl+C and retry with cache cleared
npm cache clean --force
npx create-fireact-app my-app
```

### Emulators fail to start (port conflicts)

Check for processes using emulator ports:

```bash
lsof -i :5173  # Vite dev server
lsof -i :9099  # Auth emulator
lsof -i :8080  # Firestore emulator
lsof -i :5001  # Functions emulator
lsof -i :5002  # Hosting emulator

kill -9 <PID>  # Kill conflicting process
```

Firestore emulator also requires Java:

```bash
java -version  # Install if missing
```
