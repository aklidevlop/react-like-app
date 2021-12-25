## Installation
### Create a firebase account
go to https://console.firebase.google.com/ and create an account and create a project.

while testing configure your rules for public access
This does make your database open to anyone, even people not using your app, so be sure to restrict your database again when you set up authentication.

#### Database Rules
to keep your data a little more secure you can use these rules:
(Keep in mind this is not production ready, for that you may need stricter permissions)
```
{
  "rules": {
    "comments": {
      ".read": true,
      ".write": "auth != null",
      ".indexOn": ["post_id"]
    },
    "posts": {
      ".read": true,
      ".write": "auth != null",
      ".indexOn": ["_id"]
    },
    "users": {
      ".read": true,
      "$uid": {
        ".write": "$uid === auth.uid"
      }
    }
  }
}
```

### Storage rules
```
service firebase.storage {
  match /b/{bucket}/o {
    match /images {
      // Anyone can view any image (no auth, publicly readable)
      match /{allImages=**} {
        allow read;
      }

      // Only authenticated users can write to "public" images
      match /{imageId} {
        allow write: if request.auth != null;
      }

      // Only an individual user can write to "their" images
      match /{userId}/{imageId} {
        allow write: if request.auth.uid == userId;
      }

    }
  }
}
```


## Firebase Config
In your firebase project go to:
- Authentication
and on the top right click:
- WEB SETUP

Copy the info and paste in the config-sample.js in your root directory and save it as config.js

## Add dummy data to your firebase database (optional)
You can import db.json into you firebase database if you want to startup with some data

## Install the app
`npm install` or `yarn install`

## Running the app
`yarn start`

## Firebase Deployment
`yarn build` creates a `build` directory with a production build of your app. Set up your favourite HTTP server so that a visitor to your site is served `index.html`, and requests to static paths like `/static/js/main.<hash>.js` are served with the contents of the `/static/js/main.<hash>.js` file.

Now to use firebase hosting follow these steps:

1. `npm install -g firebase-tools`
2. `firebase login`
3. `firebase init`
4. if the init generates an empty file you can paste this inside `firebase.json`:
`{
  "hosting": {
    "public": "build",
    "ignore": [
      "firebase.json",
      "package.json",
      "yarn.lock",
      "readme.md",
      "src",
      "node_modules",
      "public"
    ]
  }
}`
5. `yarn build`
6. `firebase serve` - and check if everything is in place. If you're happy it's time to deploy
7. `firebase deploy`
