# Sveltekit Bootstrap Nav bar

<!-- Timestamps

00:00 NavBar Creation
00:50 NavBar include in layout
01:41 NavBar Mobile Nav Toggle
03:03 Dynamic Login & Logout Links
06:00 Login Page Redirect to home
08:00 Unsubscribing from authstore
08:48 Remove Logout Button
09:56 Outro -->

1\. Create the navbar in component in components/nav.svelte.  You will need to create a folder

```html
<script lang="ts">
  import {
    Collapse,
    Navbar,
    NavbarToggler,
    NavbarBrand,
    Nav,
    NavItem,
    NavLink,
  } from 'sveltestrap/src';
</script>

<Navbar color="light" light expand="md">
  <NavbarBrand href="/">Recipe Site</NavbarBrand>
  <NavbarToggler />
  <Collapse navbar expand="md">
    <Nav class="ms-auto" navbar>
      <NavItem>
        <NavLink href="/login">Login</NavLink>
      </NavItem>
      <NavItem>
        <NavLink>Logout</NavLink>
      </NavItem>
    </Nav>
  </Collapse>
</Navbar>

```

2\. Include the Navbar in \_\_layout.svelte

```html
<script lang="ts">
  import Nav from '../components/nav.svelte';
</script>

...
<Nav />
```

3\. Add the Handle function

```html hl_lines="12-13 18-19"
<script lang="ts">
  import {
    Collapse,
    Navbar,
    NavbarToggler,
    NavbarBrand,
    Nav,
    NavItem,
    NavLink,
  } from 'sveltestrap/src';

  // variable that control whether mobile menu is open or closed
  let isOpen = false;
</script>

<Navbar color="light" light expand="md">
  <NavbarBrand href="/">Recipe Site</NavbarBrand>
  <NavbarToggler on:click={() => (isOpen = !isOpen)} />
  <Collapse {isOpen} navbar expand="md" on:update="{handleUpdate}">
    <Nav class="ms-auto" navbar>
      <NavItem>
        <NavLink href="/login">Login</NavLink>
      </NavItem>
      <NavItem>
        <NavLink>Logout</NavLink>
      </NavItem>
    </Nav>
  </Collapse>
</Navbar>
```

4\. Add logic to dynamically display the login and logout

```html hl_lines="11 23-33"
<script lang="ts">
  import {
    Collapse,
    Navbar,
    NavbarToggler,
    NavbarBrand,
    Nav,
    NavItem,
    NavLink,
  } from 'sveltestrap/src';
  import authStore from '../stores/authStore';

  // variable that control whether mobile menu is open or closed
  let isOpen = false;

</script>

<Navbar color="light" light expand="md">
  <NavbarBrand href="/">Recipe Site</NavbarBrand>
  <NavbarToggler on:click={() => (isOpen = !isOpen)} />
  <Collapse {isOpen} navbar expand="md">
    <Nav class="ms-auto" navbar>
      {#if $authStore.firebaseControlled} 
        {#if !$authStore.isLoggedIn}
        <NavItem>
            <NavLink href="/login">Login</NavLink>
        </NavItem>
        {:else}
        <NavItem>
            <NavLink>Logout</NavLink>
        </NavItem>
        {/if} 
      {/if}
    </Nav>
  </Collapse>
</Navbar>
```

5\. Add the logout function

```html hl_lines="12 18-20 35"
<script lang="ts">
  import {
    Collapse,
    Navbar,
    NavbarToggler,
    NavbarBrand,
    Nav,
    NavItem,
    NavLink,
  } from 'sveltestrap/src';
  import authStore from '../stores/authStore';
  import firebase from 'firebase/app';

  // variable that control whether mobile menu is open or closed
  let isOpen = false;

  // handles the logout of
  async function logout() {
    await firebase.auth().signOut();
  }
</script>

<Navbar color="light" light expand="md">
  <NavbarBrand href="/">Recipe Site</NavbarBrand>
  <NavbarToggler on:click={() => (isOpen = !isOpen)} />
  <Collapse {isOpen} navbar expand="md">
    <Nav class="ms-auto" navbar>
      {#if $authStore.firebaseControlled}
        {#if !$authStore.isLoggedIn}
          <NavItem>
            <NavLink href="/login">Login</NavLink>
          </NavItem>
        {:else}
          <NavItem>
            <NavLink on:click={logout}>Logout</NavLink>
          </NavItem>
        {/if}
      {/if}
    </Nav>
  </Collapse>
</Navbar>

```

6\. Add Redirect to Login Page

```html hl_lines="5-6 17-25"
<script lang="ts">
  import { Row, Col } from 'sveltestrap';
  import firebase from 'firebase/app';
  import authStore from '../stores/authStore';
  import { goto } from '$app/navigation';
  import { onDestroy } from 'svelte';
  async function loginWithGoogle() {
    try {
      const provider = new firebase.auth.GoogleAuthProvider();

      await firebase.auth().signInWithPopup(provider);
    } catch (e) {
      console.log(e);
    }
  }

 authStore.subscribe(async (info) => {
    if (info.isLoggedIn) {
        try {
            await goto('/');
        } catch(e) {
            console.log(e)
        }
    }
  });
</script>

<Row>
  <Col>
    <h1>Login with Google</h1>
  </Col>
</Row>

<Row>
  <Col>
    <img
      on:click={loginWithGoogle}
      src="/login-with-google.png"
      alt="Login With Google"
    />
  </Col>
</Row>

<style>
  img {
    cursor: pointer;
  }
</style>

```

7\. Unsubscribe from authStore when page goes away

```html hl_lines="17 27-29"
<script lang="ts">
  import { Row, Col } from 'sveltestrap';
  import firebase from 'firebase/app';
  import authStore from '../stores/authStore';
  import { goto } from '$app/navigation';
  import { onDestroy } from 'svelte';
  async function loginWithGoogle() {
    try {
      const provider = new firebase.auth.GoogleAuthProvider();

      await firebase.auth().signInWithPopup(provider);
    } catch (e) {
      console.log(e);
    }
  }

  const sub =  authStore.subscribe(async (info) => {
    if (info.isLoggedIn) {
        try {
            await goto('/');
        } catch(e) {
            console.log(e)
        }
    }
  });

  onDestroy(() => {
    sub();
  });
</script>

<Row>
  <Col>
    <h1>Login with Google</h1>
  </Col>
</Row>

<Row>
  <Col>
    <img
      on:click={loginWithGoogle}
      src="/login-with-google.png"
      alt="Login With Google"
    />
  </Col>
</Row>

<style>
  img {
    cursor: pointer;
  }
</style>

```


8\. Cleanup index.svelte file

```html
<script lang="ts">
  import { Row, Col, Button } from 'sveltestrap';
</script>

<Row>
  <Col>
    <h1>Welcome to the recipe site</h1>
  </Col>
</Row>

```
