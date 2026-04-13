# Authentication Setup Reference

This guide covers how authentication works in Fireact and how to configure auth providers and permissions.

## Supported Auth Providers

Fireact supports three authentication providers out of the box:

1. **Email/Password** — enabled by default, no extra configuration needed
2. **Google OAuth** — enable in Firebase Console + set a config flag
3. **GitHub OAuth** — create a GitHub OAuth App + enable in Firebase Console + set a config flag

## Enabling Each Provider

### Email/Password (Default)

Email/password authentication is enabled by default when you scaffold a project. No additional setup is required beyond having enabled Email/Password in the Firebase Console during initial project setup.

### Google OAuth

**Step 1: Enable in Firebase Console**

1. Firebase Console → Authentication → Sign-in method tab
2. Click "Google" → toggle "Enable"
3. Set a project support email
4. Save

**Step 2: Update app config**

Open `src/config/app.config.json` and set:

```json
{
  "socialLogin": {
    "google": true,
    "github": false
  }
}
```

The Google sign-in button will appear on the sign-in and sign-up pages automatically.

### GitHub OAuth

**Step 1: Create a GitHub OAuth App**

1. Go to [github.com/settings/developers](https://github.com/settings/developers)
2. Click "New OAuth App"
3. Fill in:
   - Application name: your app name
   - Homepage URL: your app URL (or `http://localhost:5002` for dev)
   - Authorization callback URL: get this from Firebase Console (next step)
4. Click "Register application"
5. Copy the **Client ID** and generate a **Client Secret**

**Step 2: Enable in Firebase Console**

1. Firebase Console → Authentication → Sign-in method
2. Click "GitHub" → toggle "Enable"
3. Paste the Client ID and Client Secret from GitHub
4. Copy the "Authorization callback URL" that Firebase shows — go back to your GitHub OAuth App settings and paste it as the callback URL
5. Save

**Step 3: Update app config**

Open `src/config/app.config.json` and set:

```json
{
  "socialLogin": {
    "google": false,
    "github": true
  }
}
```

Or enable both:

```json
{
  "socialLogin": {
    "google": true,
    "github": true
  }
}
```

## Configuration Files

### `src/config/app.config.json` (Frontend)

Controls which social login buttons appear in the UI:

```json
{
  "socialLogin": {
    "google": true,
    "github": false
  }
}
```

### `functions/src/config/app.config.json` (Backend)

Defines the permission structure enforced by Cloud Functions and Firestore rules:

```json
{
  "permissions": {
    "access": {
      "label": "Access",
      "default": true
    },
    "editor": {
      "label": "Editor",
      "default": false
    },
    "admin": {
      "label": "Admin",
      "default": false
    }
  }
}
```

Each key is a permission name. The `default` field determines whether new members get this permission when invited without an explicit setting. You can add custom permission keys here — they will be available in the role assignment UI when inviting team members.

After changing this file, rebuild functions:

```bash
cd functions && npm run build && cd ..
```

## Role-Based Access Control

### Permission Levels

Fireact uses a permission-based model stored per-user per-subscription. The default three permission levels are:

- **access**: Can view the subscription workspace. The baseline — all members who can log in have this.
- **editor**: Can create and edit content/data within the subscription. Add this for non-admin contributors.
- **admin**: Full control including managing team members, inviting/removing users, and managing billing.

The **owner** is the user who created the subscription. Owners have all permissions and cannot be removed.

### How Permissions Are Stored

Permissions are stored on both the subscription's user subcollection and the user's subscription subcollection:

```
subscriptions/{subscriptionId}/users/{userId}
  role: "owner" | "admin" | "member"
  permissions: { access: true, editor: true, admin: false }

users/{userId}/subscriptions/{subscriptionId}
  role: "owner" | "admin" | "member"
  permissions: { access: true, editor: true, admin: false }
```

`ProtectedSubscriptionRoute` reads from `SubscriptionContext`, which reads the user's permissions from Firestore when they navigate into a subscription.

### Checking Permissions in Components

Use the `useSubscription` hook to access the current user's permissions:

```typescript
const { subscription, permissions } = useSubscription();

if (permissions?.admin) {
  // Show admin-only UI
}
```

## Authentication Flow

1. **Sign-up**: User creates an account with email/password or OAuth. Firebase creates the user. A Firestore user profile is created with their display name and email.

2. **Email verification** (email/password only): After sign-up, Firebase sends a verification email. The app shows an email verification prompt until the user verifies.

3. **Sign-in**: User enters credentials or clicks OAuth button. Firebase validates credentials and returns an ID token. The app fetches the user's profile and subscription list from Firestore.

4. **Session management**: Firebase SDK manages token refresh automatically. The ID token is sent with every Cloud Function call. Sessions persist across browser reloads (Firebase stores tokens in localStorage by default).

5. **Sign-out**: Firebase clears the local session. The app redirects to `/sign-in`.

## Key Auth Components

All auth components live in `src/components/auth/`:

| Component | Route | Purpose |
|---|---|---|
| `SignIn` | `/sign-in` | Email/password + social login |
| `SignUp` | `/sign-up` | Registration form + social sign-up |
| `ResetPassword` | `/reset-password` | Send password reset email |
| `EmailVerification` | (shown inline) | Prompt + resend verification email |
| `ChangePassword` | `/profile/change-password` | Update password for signed-in user |
| `DeleteAccount` | `/profile/delete-account` | Permanently delete user account |

## Route Protection

### PrivateRoute

Wraps all pages that require authentication. Checks `AuthContext` for a signed-in user. If no user is found, redirects to `/sign-in` with a `?redirect=` parameter so the user returns to the intended page after signing in.

```typescript
// How PrivateRoute is used in the router
<Route element={<PrivateRoute />}>
  <Route element={<AuthenticatedLayout />}>
    <Route path="/profile" element={<Profile />} />
    {/* ... other authenticated routes */}
  </Route>
</Route>
```

### ProtectedSubscriptionRoute

Sits inside `PrivateRoute`. Additionally checks:
1. The subscription ID from the URL exists in the user's subscription list
2. The user has at minimum `access: true` in their permissions for that subscription

If either check fails, the user is redirected to their subscription list or the sign-in page.

```typescript
<Route element={<ProtectedSubscriptionRoute />}>
  <Route element={<SubscriptionLayout />}>
    <Route path="/:subscriptionId/dashboard" element={<SubscriptionDashboard />} />
  </Route>
</Route>
```

## Authorized Domains for OAuth

For OAuth providers (Google, GitHub), the redirect must come from an authorized domain. Firebase automatically includes `localhost` for development. For production, add your domain in:

Firebase Console → Authentication → Settings → Authorized domains

Add your production domain (e.g., `myapp.web.app` or `myapp.com`).
