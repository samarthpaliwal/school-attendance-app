# School Attendance App

A React Native app, built with Expo, for marking a small class present or absent for the day and then viewing a summary. Student records live in a Firebase Realtime Database; the app reads them on start, writes one attendance entry per student per day, and shows who was present and who was absent. It was created in Expo Snack and exported, so the project sits one folder down in `school-attendance-app/`.

## Requirements

- Node.js and npm (or yarn)
- The Expo CLI, run through `npx expo`
- Expo Go on a phone, or a browser / simulator for local testing
- A Firebase project with the Realtime Database enabled. `config.js` already points at a specific project (`school-attendance-app-d7ce0`); to use your own, replace the `firebaseConfig` object with the one from your Firebase console.

The database is expected to hold one object per student at the root, each with at least a `roll_no` and a `name`. The code pads roll numbers 1 through 5 to `01` ... `05` when it writes, so keys like `01`, `02`, ... are what it assumes. Attendance is stored on the student record under a `DD-MM-YYYY` key with the value `present` or `absent`.

## Setup

```sh
cd school-attendance-app
npm install
```

## Usage

```sh
npm start          # Expo dev server with QR code
npm run web
npm run android
npm run ios
```

The home screen lists students by roll number with Present and Absent buttons. Tapping a button highlights it (green or red) and writes today's status to that student's record. Submit navigates to the summary screen, which lists the names of today's present and absent students and shows counts. If the database returns nothing, the screen says "No Student Found".

## How it works

`config.js` initialises Firebase (SDK 8) and exports the Realtime Database handle. `App.js` shows `AppHeader` above a `react-navigation` switch navigator with two routes, `HomeScreen` and `SummaryScreen`.

`HomeScreen` subscribes to the database root with `on('value')`, sorts the students by `roll_no`, and on each button press calls `updateAttendence`, which builds today's date string and runs `db.ref(id).update({ [today]: status })`. `SummaryScreen` subscribes to the same root and filters students whose value for today's date is `present` or `absent`.

Things to be aware of: the student label on the home screen is the literal word "student" rather than the name; the summary's "Total: 5 students" is hard-coded; and once a button has been pressed it stays highlighted, so changing a mark only updates the database, not the colour. `components/Summary.js` and `components/AssetExample.js` are not used.

## Project layout

```
school-attendance-app/
  App.js                    header plus switch navigator
  config.js                 Firebase initialisation and database export
  screens/HomeScreen.js     student list with Present / Absent buttons
  screens/SummaryScreen.js  today's present and absent lists
  components/AppHeader.js   black title bar
  components/Summary.js     unused
  components/AssetExample.js  Snack sample, unused
  assets/                   icon, splash, favicon
  app.json, package.json, babel.config.js
```
