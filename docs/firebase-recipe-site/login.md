# Sveltekit Firebase Authenication Recipe Site

<iframe width="560" height="315" src="https://www.youtube.com/embed/OTxIcU_2Qos" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

- [Code](https://github.com/phptuts/firebase-sveltekit-recipe-site)
- Documentation for [SvelteKit](https://kit.svelte.dev/)
- Documentation for [Firebase Auth](https://firebase.google.com/docs/auth/web/start)
- Documentation for [Sveltestrap](https://sveltestrap.js.org)

## SvelteKit Notes

- app.html in the source folder contains the html wrapper. If you are coming from sapper think of it as template.html
- layout files look like this \_\_layout.svelte and must contain a slot to render out content from the pages.
- To create a page put it under the src -> routes folder.

## SvelteStrap Notes

- When importing components sveltestrap/src

## Firebase Initialization

- Import Firebase from 'firebase/app' for security reasons.
- It must happen in an on mount
- The firebase config is publicly available

```html
<script>
	import firebase from 'firebase/app';
	import 'firebase/auth';
	onMount(() => {
		const firebaseConfig = {
			// fill in your config
		};

		firebase.initializeApp(firebaseConfig);
	})

```

## Firebase Login with google

- Wrap in a try catch
- Error will occur if the user leaves the pageg so don't respond to all errors.

```html
<script lang="ts">
  import { Row, Col } from "sveltestrap";
  import firebase from "firebase/app";

  async function loginWithGoogle() {
    try {
      const provider = new firebase.auth.GoogleAuthProvider();

      await firebase.auth().signInWithPopup(provider);
    } catch (e) {
      console.log(e);
    }
  }
</script>
```

## Firebase Authentication Listener and AuthStore

- AuthStore stores the state of the user using the site.
- Listener sets the authStore
- AuthStore uses typescript which helps with documentation.

authStore.ts

```ts
import { writable } from "svelte/store";
import type firebase from "firebase/app";

const authStore = writable<{
  isLoggedIn: boolean;
  user?: firebase.UserInfo;
  firebaseControlled: boolean;
}>({
  isLoggedIn: false,
  firebaseControlled: false,
});

export default {
  subscribe: authStore.subscribe,
  set: authStore.set,
};
```

firebase listener

```js
firebase.auth().onAuthStateChanged((user) => {
  authStore.set({
    isLoggedIn: user !== null,
    user,
    firebaseControlled: true,
  });
});
```

## Protected Pages

- Use the authStore to boot people out of protected pageg.
- You don't need to wrap it in an onMount so it's ssr safe.

```ts
import authStore from "../stores/authStore";

authStore.subscribe(async ({ isLoggedIn, firebaseControlled }) => {
  if (!isLoggedIn && firebaseControlled) {
    await goto("/login");
  }
});
```
