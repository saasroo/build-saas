# Troubleshooting Reference

This guide covers common issues when building, running, and deploying a Fireact application.

## Installation Issues

### `create-fireact-app` command not found

**Problem:** After installing globally, the command is not recognized.

**Solutions:**

1. Check global npm path:
   ```bash
   npm config get prefix
   ```

2. Ensure the path is in your PATH variable:
   ```bash
   # On macOS/Linux
   echo $PATH

   # On Windows
   echo %PATH%
   ```

3. Reinstall globally:
   ```bash
   npm uninstall -g create-fireact-app
   npm install -g create-fireact-app
   ```

4. Use npx instead:
   ```bash
   npx create-fireact-app my-app
   ```

### Node version incompatibility

**Problem:** Errors about unsupported Node.js version.

**Solution:** Fireact requires Node.js v18 or higher.

```bash
# Check your Node version
node -v

# Upgrade Node.js using nvm (recommended)
nvm install 18
nvm use 18

# Or download from nodejs.org
```

### npm install fails with permission errors

**Problem:** Permission denied errors during `npm install`.

**Solution:**

```bash
# Do NOT use sudo. Fix npm permissions instead:
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'

# Add to your PATH (add to ~/.bashrc or ~/.zshrc):
export PATH=~/.npm-global/bin:$PATH

# Reload your shell
source ~/.bashrc  # or source ~/.zshrc
```

### npm installation hangs during scaffolding

**Problem:** The CLI hangs during dependency installation.

**Solution:**

```bash
# Cancel with Ctrl+C and retry with cache cleared
npm cache clean --force
npx create-fireact-app my-app
```

---

## Firebase Setup Issues

### Firebase login fails

**Problem:** `firebase login` command fails or hangs.

**Solutions:**

1. Try CI mode:
   ```bash
   firebase login --no-localhost
   ```

2. Clear Firebase cache:
   ```bash
   rm -rf ~/.config/firebase
   firebase login
   ```

3. Check firewall/proxy settings — ensure ports 9005, 9099, 8080, and 5001 are available.

### Firebase project not found

**Problem:** "Failed to get Firebase project."

**Solutions:**

1. Verify the Firebase project exists at [Firebase Console](https://console.firebase.google.com/)

2. Check `.firebaserc` file:
   ```json
   {
     "projects": {
       "default": "your-project-id"
     }
   }
   ```

3. Reinitialize Firebase:
   ```bash
   firebase use --add
   # Select your project from the list
   ```

### Missing Firebase web app

**Problem:** "No web apps found in Firebase project."

**Solutions:**

1. Create a web app in Firebase Console:
   - Go to Project Settings → Your apps
   - Click "Add app" → Web
   - Register app with a nickname

2. Update configuration:
   - Copy Firebase config from the registered app
   - Update `src/config/firebase.config.json`

---

## Build and Development Issues

### TypeScript compilation errors

**Problem:** TypeScript errors during build.

**Solutions:**

1. Clean and rebuild:
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   rm -rf dist
   npm run build
   ```

2. Check TypeScript version:
   ```bash
   npx tsc --version
   # Should be 5.x
   ```

3. Verify `tsconfig.json` exists and has no syntax errors.

### Vite build fails

**Problem:** Vite build errors or warnings.

**Solutions:**

1. Clear Vite cache:
   ```bash
   rm -rf node_modules/.vite
   npm run build
   ```

2. Check for port conflicts (Vite uses port 5173 by default):
   ```bash
   lsof -i :5173
   # Kill process if needed
   ```

3. Update dependencies:
   ```bash
   npm update
   ```

### Module not found errors

**Problem:** "Cannot find module" errors.

**Solutions:**

1. Check symlinks (in the source repository only):
   ```bash
   ls -la packages/app/src/
   # Should show symlinks to ../../../src/

   # Recreate if needed (macOS/Linux)
   cd packages/app/src
   ln -sf ../../../src/components components
   # Repeat for other directories
   ```

2. Install missing dependencies:
   ```bash
   npm install
   cd functions && npm install
   ```

---

## Firebase Emulator Issues

### Emulators fail to start

**Problem:** `firebase emulators:start` fails.

**Solutions:**

1. Check port availability:
   ```bash
   lsof -i :5173  # Vite dev server
   lsof -i :9099  # Auth emulator
   lsof -i :8080  # Firestore emulator
   lsof -i :5001  # Functions emulator
   lsof -i :5002  # Hosting emulator

   kill -9 <PID>  # Kill conflicting processes
   ```

2. Build functions first — emulators require compiled function code:
   ```bash
   cd functions
   npm run build
   cd ..
   ```

3. Check Java installation (required for Firestore emulator):
   ```bash
   java -version
   # Install Java if missing
   ```

4. Clear emulator cache:
   ```bash
   rm -rf ~/.cache/firebase/emulators
   firebase emulators:start
   ```

### Emulator data not persisting

**Problem:** Data is lost when emulators restart.

**Solution:** Use export/import to persist emulator data between sessions:

```bash
# Export data before stopping
firebase emulators:export ./emulator-data

# Start with exported data
firebase emulators:start --import=./emulator-data

# Or export automatically on exit
firebase emulators:start --export-on-exit=./emulator-data --import=./emulator-data
```

### Functions emulator hot reload not working

**Problem:** Changes to function code are not reflected without restarting.

**Solutions:**

1. Run functions in watch mode (in the `functions/` directory):
   ```bash
   npm run build -- --watch
   ```

2. Or just restart emulators after each function change:
   ```bash
   # Ctrl+C to stop
   firebase emulators:start
   ```

---

## Stripe Integration Issues

### Stripe CLI not found

**Problem:** `stripe` command not recognized.

**Solutions:**

1. Install Stripe CLI:
   ```bash
   # macOS
   brew install stripe/stripe-cli/stripe

   # Windows
   scoop bucket add stripe https://github.com/stripe/scoop-stripe-cli.git
   scoop install stripe

   # Linux
   # Download from https://github.com/stripe/stripe-cli/releases
   ```

2. Login to Stripe:
   ```bash
   stripe login
   ```

### Webhook endpoint secret mismatch

**Problem:** Stripe webhooks failing with signature verification errors.

**Solution:**

1. Get new webhook secret:
   ```bash
   stripe listen --forward-to http://127.0.0.1:5001/YOUR_PROJECT_ID/us-central1/stripeWebhook
   # Copy the webhook signing secret (whsec_...)
   ```

2. Update configuration:
   ```json
   // functions/src/config/stripe.config.json
   {
     "endpointSecret": "whsec_YOUR_NEW_SECRET_HERE"
   }
   ```

3. Rebuild functions:
   ```bash
   cd functions
   npm run build
   cd ..
   ```

4. Restart emulators:
   ```bash
   firebase emulators:start
   ```

### Stripe test mode vs live mode confusion

**Problem:** Using wrong Stripe keys (test vs live).

**Solution:**

Always use **test mode** keys for development:
- Test keys start with `pk_test_` and `sk_test_`
- Live keys start with `pk_live_` and `sk_live_`

```json
// functions/src/config/stripe.config.json
{
  "secretKey": "sk_test_...",
  "publishableKey": "pk_test_..."
}
```

### Payment requires authentication

**Problem:** Test payments fail with "authentication required."

**Solution:** Use the correct Stripe test cards:

```
Card Numbers:
- Successful payment:          4242 4242 4242 4242
- Requires 3DS authentication: 4000 0025 0000 3155
- Declined:                    4000 0000 0000 9995

Expiry: Any future date (e.g., 12/29)
CVC: Any 3 digits (e.g., 123)
ZIP: Any valid ZIP code (e.g., 10001)
```

---

## Authentication Issues

### User not redirected after sign-in

**Problem:** User stays on the sign-in page after successful authentication.

**Solutions:**

1. Check routing — verify `PrivateRoute` is set up correctly and the redirect logic in the sign-in component works.

2. Clear browser cache:
   - Hard refresh: `Cmd+Shift+R` (Mac) or `Ctrl+Shift+R` (Windows)
   - Clear cookies and local storage

3. Check authorized domains in Firebase Console:
   - Authentication → Settings → Authorized domains
   - Ensure `localhost` and your domain are listed

### "Auth/network-request-failed" error

**Problem:** Network errors when attempting authentication.

**Solutions:**

1. Verify emulator configuration in `firebase.config.json`:
   ```json
   {
     "emulators": {
       "enabled": true,
       "auth": {
         "host": "localhost",
         "port": 9099
       }
     }
   }
   ```

2. Ensure emulators are actually running:
   ```bash
   firebase emulators:start
   ```

3. Check firewall/antivirus — temporarily disable to test, or add exceptions for Firebase ports (9099, 8080, 5001, 5002).

### Password reset email not sending

**Problem:** Password reset emails are not received.

**Solutions:**

1. In emulator mode — check the emulator UI at http://localhost:4000. Look in the Auth emulator for the reset link (emulators don't send real emails).

2. In production:
   - Check spam folder
   - Verify email template in Firebase Console → Authentication → Templates
   - Check Firebase email quota limits

---

## Firestore Issues

### Permission denied errors

**Problem:** "FirebaseError: Permission denied" when accessing Firestore.

**Solutions:**

1. View current rules:
   ```bash
   cat firestore.rules
   ```

2. Deploy security rules:
   ```bash
   firebase deploy --only firestore:rules
   ```

3. In emulator mode, emulators use the local `firestore.rules` file. Restart emulators after changing rules.

4. Verify the user is signed in and the authentication token is valid.

### Data not syncing in real-time

**Problem:** Firestore real-time updates not working.

**Solutions:**

1. Ensure you're using `onSnapshot` for real-time subscriptions:
   ```javascript
   onSnapshot(docRef, (snapshot) => {
     // Handle updates
   });
   ```

2. Check network connection — Firestore requires an active network for real-time updates.

3. Clear cache during development:
   ```javascript
   const settings = { cacheSizeBytes: 0 };
   firestore.settings(settings);
   ```

### Indexes missing

**Problem:** "The query requires an index" error.

**Solutions:**

1. Click the index creation link embedded in the error message — it goes directly to the Firebase Console to create the needed index.

2. Deploy indexes from `firestore.indexes.json`:
   ```bash
   # Update firestore.indexes.json first, then:
   firebase deploy --only firestore:indexes
   ```

---

## Cloud Functions Issues

### Functions not deploying

**Problem:** Deployment hangs or fails.

**Solutions:**

1. Build functions first:
   ```bash
   cd functions
   npm run build
   cd ..
   ```

2. Check Node.js version in `functions/package.json`:
   ```json
   {
     "engines": {
       "node": "18"
     }
   }
   ```

3. Deploy a specific function to isolate issues:
   ```bash
   firebase deploy --only functions:functionName
   ```

4. Check function logs:
   ```bash
   firebase functions:log
   ```

### CORS errors when calling functions

**Problem:** CORS errors in the browser console when calling Cloud Functions.

**Solutions:**

1. Use callable functions (recommended) — the Firebase SDK's `httpsCallable` handles CORS automatically:
   ```typescript
   const fn = httpsCallable(functions, 'myFunction');
   const result = await fn(data);
   ```

2. For HTTP functions, add CORS middleware:
   ```typescript
   import * as cors from 'cors';
   const corsHandler = cors({ origin: true });

   export const myFunction = functions.https.onRequest((req, res) => {
     corsHandler(req, res, () => {
       // Function logic
     });
   });
   ```

### Function timeout

**Problem:** Functions timing out.

**Solutions:**

1. Increase the timeout (max 9 minutes for v1 functions):
   ```typescript
   export const myFunction = functions
     .runWith({ timeoutSeconds: 540 })
     .https.onCall(async (data, context) => {
       // Function logic
     });
   ```

2. Optimize function code:
   - Reduce external API calls
   - Use batch Firestore operations
   - Implement pagination for large datasets

---

## Deployment Issues

### Build fails in production / CI

**Problem:** Production build succeeds locally but fails in CI/CD.

**Solutions:**

1. Ensure CI uses the same Node version as local development:
   ```yaml
   # In GitHub Actions workflow
   - uses: actions/setup-node@v3
     with:
       node-version: '18'
   ```

2. Verify all required environment variables and config files are present in CI.

3. Use `npm ci` instead of `npm install` for reproducible installs:
   ```bash
   npm ci
   ```

### Functions not working after deployment

**Problem:** Functions work locally but fail in production.

**Solutions:**

1. Check function logs:
   ```bash
   firebase functions:log --only functionName
   ```

2. Verify environment configuration (if using Firebase environment config):
   ```bash
   firebase functions:config:get
   ```

3. Check IAM permissions — ensure the Cloud Functions service account has the required Firebase and Stripe access.

### Site not loading after deployment

**Problem:** Deployed site shows a blank page or errors.

**Solutions:**

1. Verify the build output directory has content:
   ```bash
   ls -la dist/
   ```

2. Check `firebase.json` hosting config:
   ```json
   {
     "hosting": {
       "public": "dist",
       "ignore": [
         "firebase.json",
         "**/.*",
         "**/node_modules/**"
       ],
       "rewrites": [
         {
           "source": "**",
           "destination": "/index.html"
         }
       ]
     }
   }
   ```
   The `rewrites` rule is required for React Router to handle client-side routing correctly. Without it, direct URL access to routes other than `/` will 404.

3. Wait 10-15 minutes for CDN propagation, then hard refresh:
   - Mac: `Cmd+Shift+R`
   - Windows: `Ctrl+Shift+R`

---

## Quick Reference Commands

```bash
# Development
npm run dev                          # Start Vite dev server
npm run build                        # Build React app for production
firebase emulators:start             # Start all Firebase emulators
stripe listen --forward-to ...       # Forward Stripe webhooks to local emulator

# Troubleshooting
rm -rf node_modules dist             # Clean build artifacts
npm install                          # Reinstall dependencies
rm -rf node_modules/.vite            # Clear Vite cache
rm -rf ~/.cache/firebase/emulators   # Clear emulator cache

# Emulator data
firebase emulators:export ./emulator-data            # Export data
firebase emulators:start --import=./emulator-data    # Start with data

# Functions
cd functions && npm run build && cd ..    # Build functions
firebase functions:log                   # View function logs
firebase deploy --only functions         # Deploy functions only

# Firebase project
firebase use --add                   # Add project alias
firebase use staging                 # Switch to staging project
firebase deploy                      # Deploy everything
```
