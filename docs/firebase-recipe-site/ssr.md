# Sveltekit Firebase Authenication Recipe Site

<iframe width="560" height="315" src="https://www.youtube.com/embed/pQyXjoPVOs4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- [Code](https://github.com/phptuts/firebase-sveltekit-recipe-site/tree/ssr-example)

- [Firestore Docs](https://firebase.google.com/docs/firestore/quickstart)

## Notes

- SSR means that all the html is rendered by the server.
- Benefits SEO and Social Media Sharing.
- This example does not work with admin becuase it does not account for login.
- If you want it to work with login you will have to validate the firebase jwt token and recreate your firebase firestore rules in code.

## Firebase Rules

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /recipes/{document=**} {
      allow read: if true;
      allow create: if request.auth != null;
    }

    match /private/{document=**} {
      allow read, write: if request.auth != null;
    }

    match /public/{document=**} {
      allow read: if true;
    }
  }
}
```
