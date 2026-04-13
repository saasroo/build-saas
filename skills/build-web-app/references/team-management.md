# Team Management Reference

This guide explains how team/multi-user workspaces work in Fireact and how to configure and extend them.

## How Teams Work in Fireact

In Fireact, a **subscription is a team workspace**. When a user creates a subscription (i.e., signs up for a plan), they become the owner of that subscription and can invite other users to join it.

Every user can belong to multiple subscriptions, and each subscription can have multiple members with different roles and permissions. The subscription ID is used as a namespace — all team data lives under or is keyed to the `subscriptionId`.

## Creating a Team (Starting a Subscription)

1. A signed-in user navigates to "Create subscription" (typically `/create-subscription`)
2. They select a plan and complete payment if needed
3. A Cloud Function (`createSubscription`) runs, which:
   - Creates a Stripe customer and (if paid) a Stripe subscription
   - Creates a `subscriptions/{subscriptionId}` document in Firestore
   - Adds the user to the subscription's user subcollection as `owner`
   - Adds the subscription to the user's `users/{userId}/subscriptions` subcollection
4. The user is redirected to `/{subscriptionId}/dashboard`

The subscription creator is permanently the owner. Ownership cannot be transferred.

## Team Data Model

### subscriptions collection

```
subscriptions/{subscriptionId}
  name: string          # Team/workspace name
  ownerId: string       # Firebase UID of the creator
  planId: string        # References the plans config
  stripeCustomerId: string
  stripeSubscriptionId: string
  status: string        # "active" | "past_due" | "canceled" | "unpaid" | "incomplete"
  createdAt: timestamp
  updatedAt: timestamp
```

### Per-subscription user list

Each subscription has a `users` subcollection tracking membership:

```
subscriptions/{subscriptionId}/users/{userId}
  role: string          # "owner" | "admin" | "member"
  permissions: object   # { access: true, editor: false, admin: false }
```

### Per-user subscription list

Each user has a `subscriptions` subcollection so the app can list all workspaces the user belongs to without a collection-group query:

```
users/{userId}/subscriptions/{subscriptionId}
  id: string            # same as the subscriptionId
  role: string          # "owner" | "admin" | "member"
  permissions: object   # mirrors the entry in subscriptions/{id}/users/{uid}
```

## Inviting Members

Team owners and admins can invite new members using the `InviteUser` component (typically on the team members page at `/{subscriptionId}/users`).

### Invitation flow

1. Admin fills in the invitee's email address and selects their role/permissions in the `InviteUser` form
2. The `createInvite` Cloud Function runs, creating a record in the `invites` collection:
   ```
   invites/{inviteId}
     subscriptionId: string
     email: string
     role: string
     permissions: object
     status: "pending"
     createdAt: timestamp
   ```
3. An invitation email is sent to the invitee (or you can build a custom email flow)
4. The invitee clicks the invite link, is taken to the app, and sees the invitation

### Accepting an invitation

The `acceptInvite` Cloud Function:
1. Validates the invite is still `pending` and the current user's email matches
2. Creates the user's membership record in `subscriptions/{subscriptionId}/users/{userId}`
3. Creates the subscription entry in `users/{userId}/subscriptions/{subscriptionId}`
4. Updates the invite `status` to `"accepted"`

### Rejecting an invitation

The `rejectInvite` Cloud Function updates the invite `status` to `"rejected"` and removes any partial membership records.

## Permission Levels

Permissions are defined in `functions/src/config/app.config.json`. The defaults are:

| Permission | What it grants |
|---|---|
| `access` | Can log in to the subscription workspace and view content |
| `editor` | Can create and modify data/content within the workspace |
| `admin` | Full control: manage members, manage billing, all editor permissions |

**Owner** is a special role (not just a permission set) — the owner has all permissions plus cannot be removed from the subscription.

Permission checks happen in two places:
1. **Cloud Functions**: validate `permissions` from the user's Firestore subscription record before executing sensitive operations
2. **Frontend**: `ProtectedSubscriptionRoute` checks for `access: true` on route entry; components use `useSubscription()` to conditionally render admin-only UI

### Checking permissions in your components

```typescript
const { permissions } = useSubscription();

// Show only to admins
if (permissions?.admin) {
  return <AdminPanel />;
}

// Show only to editors or admins
if (permissions?.editor || permissions?.admin) {
  return <EditButton />;
}
```

## Removing Team Members

Admins (and owners) can remove members via the `UserTable` component on the team members page. When a member is removed:

1. Their entry is deleted from `subscriptions/{subscriptionId}/users/{userId}`
2. The subscription entry is deleted from `users/{userId}/subscriptions/{subscriptionId}`

The removed user loses access immediately — on their next route navigation, `ProtectedSubscriptionRoute` will redirect them away from the subscription workspace.

## Data Isolation

Each subscription's data is scoped to its `subscriptionId`. When you build custom features, store all subscription-specific data under a subcollection of the subscription document or use the `subscriptionId` as a field to filter on:

```
# Recommended: subcollection
subscriptions/{subscriptionId}/yourData/{docId}

# Alternative: field-based
yourCollection/{docId}
  subscriptionId: string
  ...
```

Subcollections are simpler to secure with Firestore rules because you can inherit access from the parent document.

## Firestore Security Rules for Team Data

Rules enforce that only subscription members can read subscription data, and only admins/owners can modify membership. The pattern looks like:

```javascript
// Only subscription members can read
function isSubscriptionMember(subscriptionId) {
  return exists(/databases/$(database)/documents/subscriptions/$(subscriptionId)/users/$(request.auth.uid));
}

// Only owners/admins can manage members
function isSubscriptionAdmin(subscriptionId) {
  let member = get(/databases/$(database)/documents/subscriptions/$(subscriptionId)/users/$(request.auth.uid)).data;
  return member.role == "owner" || member.permissions.admin == true;
}

match /subscriptions/{subscriptionId} {
  allow read: if isSubscriptionMember(subscriptionId);
  allow create: if request.auth != null;
  allow update: if isSubscriptionAdmin(subscriptionId);
}
```

## Customizing Permissions

To add a custom permission (e.g., `"billing_viewer"`):

1. Edit `functions/src/config/app.config.json`:
   ```json
   {
     "permissions": {
       "access": { "label": "Access", "default": true },
       "editor": { "label": "Editor", "default": false },
       "admin": { "label": "Admin", "default": false },
       "billing_viewer": { "label": "View Billing", "default": false }
     }
   }
   ```

2. Rebuild functions:
   ```bash
   cd functions && npm run build && cd ..
   ```

3. The new permission will appear in the invite UI and be stored in Firestore. Check it in your components:
   ```typescript
   if (permissions?.billing_viewer || permissions?.admin) {
     // Show billing info
   }
   ```
