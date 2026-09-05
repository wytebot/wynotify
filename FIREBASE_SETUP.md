# WyNotify Firebase one-time setup

The app includes `firestore.indexes.json` and `firestore.rules`. Vercel does **not** deploy Firestore indexes when it deploys the Vite/API application, so Firebase setup is a separate one-time step.

## 1. Make sure Vercel and Firebase are the same project

The Vercel server variable `FIREBASE_SERVICE_ACCOUNT_JSON` must belong to the Firebase project that owns the Firestore database used by WyNotify.

## 2. Deploy the indexes and rules

From the project root, run:

```bash
npm install
npm run firebase:setup -- --project YOUR_FIREBASE_PROJECT_ID
```

If you prefer the Firebase CLI directly:

```bash
npx firebase-tools login
npx firebase-tools deploy --only firestore:indexes,firestore:rules --project YOUR_FIREBASE_PROJECT_ID
```

Wait for the index deployment to finish before testing the dashboard.

## 3. Important: sending no longer depends on the composite frequency index

The notification send path previously ran a query combining `workspaceId` and a `createdAt` range. That query requires a composite index. It now queries by `workspaceId` only and applies the 24-hour frequency filter in the API, so a missing composite index cannot block a normal send.

The optimized composite indexes remain in `firestore.indexes.json` for dashboard/history queries and scheduled/segmentation features.

## 4. Verify the API

Open:

`/api?action=health`

A successful response should include `ok: true` and the WyNotify API version.
