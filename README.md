# Swift Learning Journey Tracker

A web application to track your progress through Swift programming tutorials, with cross-device sync using Firebase.

## Features

- Track completion status of Swift tutorials
- Categorize tutorials by topic/series
- Cross-device progress synchronization using Firebase
- Google Sign-In for persistent progress across devices
- Offline support with local storage fallback
- Clean, responsive UI with Tailwind CSS

## How It Works

### Authentication
- Uses Firebase Authentication with Google Sign-In
- User's progress is tied to their Google account
- Progress is automatically synced across all devices where the user is signed in

### Data Storage
- Progress data is stored in Firestore under the `tutorials_state` collection
- Each user's progress is stored in a document with their Firebase UID
- Local storage is used as a fallback when offline

### State Management
- The app maintains a local state of tutorial progress
- Changes are immediately saved to Firestore and local storage
- On app load, it first checks for server state and merges with local state if needed

## Setup

1. **Firebase Configuration**
   - Create a new Firebase project at [Firebase Console](https://console.firebase.google.com/)
   - Enable Google Sign-In in Authentication → Sign-in method
   - Add your domain to Authorized domains in Authentication → Settings
   - Create a new web app in Firebase and copy the config

2. **Environment Setup**
   - Create a `.env` file in the project root with your Firebase config:
     ```
     VITE_FIREBASE_API_KEY=your_api_key
     VITE_FIREBASE_AUTH_DOMAIN=your_auth_domain
     VITE_FIREBASE_PROJECT_ID=your_project_id
     VITE_FIREBASE_STORAGE_BUCKET=your_storage_bucket
     VITE_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
     VITE_FIREBASE_APP_ID=your_app_id
     VITE_FIREBASE_MEASUREMENT_ID=your_measurement_id
     ```

3. **Running Locally**
   - Open `index.html` in a modern web browser
   - Or use a local server (e.g., `python -m http.server`)

## Security Measures

### 1. Firebase Security Rules

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /tutorials_state/{userId} {
      // Only allow read/write if user is authenticated and owns the document
      allow read, write: if request.auth != null && request.auth.uid == userId;
      
      // Validate data structure
      allow create: if request.resource.data.keys().hasAll(['tutorials']);
      allow update: if (
        request.resource.data.diff(resource.data).affectedKeys().hasOnly(['tutorials'])
      );
    }
  }
}
```

### 2. API Key Protection
- The Firebase API key is client-side but protected by:
  - App Check (recommended to enable in Firebase Console)
  - Domain restrictions in Firebase Console
  - API key restrictions in Google Cloud Console

### 3. Authentication Security
- Google Sign-In with secure token-based authentication
- Session persistence set to LOCAL (not persistent across browser sessions)
- Email verification required for sensitive operations

### 4. Rate Limiting
- Enable Firebase App Check to prevent abuse
- Set up Cloud Functions with rate limiting for sensitive operations

### 5. Data Validation
- All data is validated before being written to Firestore
- Input sanitization on all user inputs
- No sensitive user data is stored in the database

## Dependencies

- [Firebase](https://firebase.google.com/) - Backend services
- [Tailwind CSS](https://tailwindcss.com/) - Styling
- [Firebase Web SDK](https://firebase.google.com/docs/web/setup) - Client-side Firebase integration

## Contributing

1. Fork the repository
2. Create a new branch for your feature
3. Commit your changes
4. Push to the branch
5. Create a new Pull Request

## License

This project is open source and available under the [MIT License](LICENSE).

---

*Note: Make sure to keep your Firebase configuration secure and never commit sensitive information to version control.*
