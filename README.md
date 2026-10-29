# ArenaX Admin Website

Mobile-friendly tournament management dashboard for Firebase Realtime Database and Firebase Storage.

## Setup
1. Open Firebase Console and select/create your project.
2. Project settings → General → Your apps → add a Web app; copy its config into `app.js`, replacing every `PASTE_...` value.
3. Authentication → Sign-in method: enable Email/Password. Create your admin user in Authentication → Users and copy its UID.
4. Create Realtime Database. In its Rules tab, use `database.rules.json`, replacing `REPLACE_WITH_ADMIN_UID` with your admin UID. Publish rules.
5. Enable Firebase Storage. Add Storage rules (replace UID):
```javascript
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /tournament-banners/{path=**} {
      allow read: if true;
      allow write: if request.auth != null
        && request.auth.uid == 'REPLACE_WITH_ADMIN_UID'
        && request.resource.size < 5 * 1024 * 1024
        && request.resource.contentType.matches('image/.*');
    }
  }
}
```
6. Host these files over HTTPS (Firebase Hosting or another static host). Do not open as a local `file://` file.

## Database
Records are stored at `/tournaments/{id}`. The website supports add/edit/delete, real-time listing, search, banner uploads, game, mode, map/map type, entry fee, prize pool, rupees per kill, max slots, status, date/time, room ID, and rules/description.

## Security warning
The included database rules allow public reads for tournament cards and admin-only writes. They are a starting point, not the full player-registration security model. Do not let players directly change `registeredCount` from the app. Enforce registrations, slot limits, wallet deductions, refunds, kill rewards and prize payments using trusted server-side logic (such as Cloud Functions/transactions) before handling real money.
