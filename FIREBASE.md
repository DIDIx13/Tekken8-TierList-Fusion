## Firebase Setup

To enable Firebase services for **Tekken8 TierList Fusion**, follow the steps below. This setup will allow you to use Firebase Authentication and Firestore for managing user data and tier lists.

### 1. Create a Firebase Project

1. **Sign in to Firebase Console:**
   - Navigate to the [Firebase Console](https://console.firebase.google.com/) and sign in with your Google account.

2. **Create a New Project:**
   - Click on **"Add project"**.
   - Enter a **Project Name** (e.g., `Tekken8TierListFusion`).
   - (Optional) Enable **Google Analytics** for your project.
   - Click **"Create Project"** and wait for the setup to complete.

### 2. Set Up Firestore

1. **Navigate to Firestore:**
   - In the Firebase Console, select your project.
   - From the left sidebar, click on **"Firestore Database"**.

2. **Create a Firestore Database:**
   - Click on **"Create database"**.
   - Choose **"Start in production mode"** for secure access or **"Start in test mode"** for easier initial development (remember to update security rules later).
   - Select a **Cloud Firestore location** close to your user base.
   - Click **"Enable"** to create the database.

### 3. Set Up Firebase Authentication

1. **Navigate to Authentication:**
   - In the Firebase Console, select your project.
   - From the left sidebar, click on **"Authentication"**.

2. **Enable Sign-In Methods:**
   - Click on the **"Sign-in method"** tab.
   - Enable **Email/Password** authentication:
     - Click on **"Email/Password"**.
     - Toggle the **"Enable"** switch to **ON**.
     - Click **"Save"**.
   - (Optional) Enable additional authentication providers (e.g., Google, Facebook) as needed.

### 4. Obtain Firebase Configuration

1. **Navigate to Project Settings:**
   - In the Firebase Console, click on the gear icon next to **"Project Overview"** and select **"Project settings"**.

2. **Add a Web App:**
   - Under the **"General"** tab, scroll down to **"Your apps"**.
   - Click on the **"</>"** icon to add a new web app.
   - Enter a nickname for your app (e.g., `Tekken8TierListFusionApp`).
   - (Optional) Set up Firebase Hosting now or skip it if you've already configured it.
   - Click **"Register app"**.

3. **Copy Firebase Config:**
   - After registering, you'll see your Firebase SDK configuration. It looks like this:
     ```javascript
     // Your web app's Firebase configuration
     const firebaseConfig = {
       apiKey: "YOUR_API_KEY",
       authDomain: "YOUR_AUTH_DOMAIN",
       projectId: "YOUR_PROJECT_ID",
       storageBucket: "YOUR_STORAGE_BUCKET",
       messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
       appId: "YOUR_APP_ID"
     };
     ```
   - **Do not share** these credentials publicly.

### 5. Configure Environment Variables

1. **Create a `.env` File:**
   - In the root of your `frontend` directory, create a file named `.env`.
   - Add your Firebase configuration to the `.env` file with the `REACT_APP_` prefix:
     ```env
     REACT_APP_FIREBASE_API_KEY=your_api_key
     REACT_APP_FIREBASE_AUTH_DOMAIN=your_auth_domain
     REACT_APP_FIREBASE_PROJECT_ID=your_project_id
     REACT_APP_FIREBASE_STORAGE_BUCKET=your_storage_bucket
     REACT_APP_FIREBASE_MESSAGING_SENDER_ID=your_messaging_sender_id
     REACT_APP_FIREBASE_APP_ID=your_app_id
     ```

2. **Secure the `.env` File:**
   - Ensure that the `.env` file is listed in your `.gitignore` to prevent sensitive information from being pushed to GitHub.
     ```bash
     echo ".env" >> .gitignore
     ```

### 6. Initialize Firebase in React

1. **Install Firebase SDK:**
   - If you haven't already installed Firebase, navigate to the `frontend` directory and install it:
     ```bash
     cd frontend
     npm install firebase
     ```

2. **Create Firebase Configuration File:**
   - In the `frontend/src` directory, create a file named `firebase.js` and add the following code:
     ```javascript
     // frontend/src/firebase.js
     import { initializeApp } from "firebase/app";
     import { getFirestore } from "firebase/firestore";
     import { getAuth } from "firebase/auth";

     const firebaseConfig = {
       apiKey: process.env.REACT_APP_FIREBASE_API_KEY,
       authDomain: process.env.REACT_APP_FIREBASE_AUTH_DOMAIN,
       projectId: process.env.REACT_APP_FIREBASE_PROJECT_ID,
       storageBucket: process.env.REACT_APP_FIREBASE_STORAGE_BUCKET,
       messagingSenderId: process.env.REACT_APP_FIREBASE_MESSAGING_SENDER_ID,
       appId: process.env.REACT_APP_FIREBASE_APP_ID,
     };

     // Initialize Firebase
     const app = initializeApp(firebaseConfig);

     // Initialize Firestore
     const db = getFirestore(app);

     // Initialize Authentication
     const auth = getAuth(app);

     export { db, auth };
     ```

### 7. Set Up Firebase Firestore Rules (Production Mode)

If you initialized Firestore in production mode, you need to set up security rules to protect your data.

1. **Navigate to Firestore Rules:**
   - In the Firebase Console, go to **"Firestore Database"**.
   - Click on the **"Rules"** tab.

2. **Define Security Rules:**
   - Below is a basic example of security rules that allow authenticated users to read and write their own tier lists and manage groups:
     ```javascript
     rules_version = '2';
     service cloud.firestore {
       match /databases/{database}/documents {
         
         // Allow read access to all users for tier lists
         match /tierlists/{tierlistId} {
           allow read: if request.auth != null;
           allow write: if request.auth != null && request.auth.uid == resource.data.userId;
         }
         
         // Allow users to manage their groups
         match /groups/{groupId} {
           allow read, write: if request.auth != null && request.auth.uid in resource.data.members;
         }
         
         // Additional rules as needed
       }
     }
     ```
   - **Customize** these rules based on your application's specific needs.

3. **Publish Rules:**
   - After defining the rules, click **"Publish"** to apply them.

### 8. Test Firebase Integration

1. **Run the Application Locally:**
   - Start your React application to ensure Firebase is correctly configured.
     ```bash
     npm start
     ```
   - The app should be running at `http://localhost:3000`.

2. **Verify Authentication:**
   - Attempt to sign up and log in using the authentication methods you've enabled.
   - Check the Firebase Console under **"Authentication"** to see if new users are being registered.

3. **Verify Firestore:**
   - Create a test tier list or group in the application.
   - Navigate to **"Firestore Database"** in the Firebase Console to ensure data is being stored correctly.

### 9. Deploying Firebase Functions (Optional)

If your project requires server-side functions (e.g., for aggregating tier lists), you can set up Firebase Functions.

1. **Initialize Functions:**
   - In the root of your project, run:
     ```bash
     firebase init functions
     ```
   - Select **JavaScript** or **TypeScript** based on your preference.
   - Install dependencies when prompted.

2. **Write Your Functions:**
   - Implement necessary cloud functions in the `functions` directory.

3. **Deploy Functions:**
   - Deploy your functions to Firebase:
     ```bash
     firebase deploy --only functions
     ```

### 10. Additional Resources

- **Firebase Documentation:**
  - [Firestore Documentation](https://firebase.google.com/docs/firestore)
  - [Authentication Documentation](https://firebase.google.com/docs/auth)
  - [Firebase CLI Documentation](https://firebase.google.com/docs/cli)
- **React Firebase Tutorials:**
  - [React Firebase Authentication](https://firebase.google.com/docs/auth/web/start)
  - [React Firebase Firestore](https://firebase.google.com/docs/firestore/quickstart)
- **Security Best Practices:**
  - Always keep your `.env` file secure and never expose sensitive credentials.
  - Regularly review and update Firestore security rules to protect your data.

---

By following these steps, you and your colleagues can set up Firebase for the **Tekken8 TierList Fusion** project, enabling authentication and data management functionalities. If you encounter any issues or have questions, feel free to reach out through the [Issues](https://github.com/DIDIx13/Tekken8-TierList-Fusion/issues) section of the repository.

