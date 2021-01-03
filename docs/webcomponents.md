# Web Components in Svelte

## Video

## Demos

### HTML

[HTML Svelte Demo](https://phptuts.github.io/svelte-webcomponets-yt/)

[HTML Svelte Demo Code](https://github.com/phptuts/svelte-webcomponets-yt)

### Angular

[Angular Demo](https://phptuts.github.io/angular-svelte-webcomponents-yt/)

[Angular Code](https://github.com/phptuts/angular-svelte-webcomponents-yt)

### React

[React Demo](https://github.com/phptuts/react-svelte-webcomponent-yt)

[React Code](https://phptuts.github.io/react-svelte-webcomponent-yt/)

### Vue

[Vue Demo](https://phptuts.github.io/vue-svelte-webcomponent-yt/)

[Vue Code](https://phptuts.github.io/vue-svelte-webcomponent-yt/)

## How to create a web component with Svelte

1\. Go to the rollup.config.js file in the root of the project.

2\. Change the output.file to to the name of the webcomponent you want to export.

```js
output: {
    sourcemap: true,
    format: "iife",
    name: "app",
    file: "public/build/count-btn.js",
  },
```

3\. Change change compiler options to this.

```js
svelte({
      compilerOptions: {
        // enable run-time checks when not in production
        dev: !production,
        customElement: true,
      },
    }),
```

4\. Delete the css plugin below the svelte component.

5\. Go to main.js and delete all config in your app component.

```js
import App from "./App.svelte";

const app = new App();

export default app;
```

6\. Create the component you want to export in your App.svelte file. You will need to include the svelte:option component with tag prop equaling the name of your component.

```html
<script>
  import { onMount } from "svelte";
  export let count = 0;

  function onClick() {
    count = +count + 1;
  }
  onMount(() => {
    console.log("can use on mount");
  });
</script>

<style>
  button {
    width: 100px;
    height: 100px;
  }
</style>

<button on:click="{onClick}">Count {count}</button>

<svelte:options tag="count-btn" />
```

7\. Go you your index.html page and delete the css imports.

8\. Include your new component in a defered script tag.

```html
<script defer src="build/count-btn.js"></script>
```

9\. Use your component in your html

```html
<count-btn count="30"></count-btn>
```

10\. Run npm run build

11\. Run npm start to see your component.

### Note

Everytime you want to see a change you will have to run npm run build. There is probably a way you can watch the file.

## How to use svelte component in Angular

1\. Create a folder in your src called js and copy the web component.js files there.

2\. Go to your angular.json file and include the javascript. It's will be under project -> architect -> build -> scripts.

```js
"scripts": [
              "src/js/count-btn.js"
            ]
```

3\. Go to src -> app -> app.module.ts and add a custom schema to the ng module.

```typescript
import { CUSTOM_ELEMENTS_SCHEMA, NgModule } from "@angular/core";

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AppRoutingModule],
  providers: [],
  bootstrap: [AppComponent],
  schemas: [CUSTOM_ELEMENTS_SCHEMA],
})
export class AppModule {}
```

3\. Go to your app.compoenent.html file and insert your web component.

```html
<count-btn count="20"></count-btn>
```

## How to use svelte component in React

1\. Copy the component js into the public folder.

2\. In the index.html file on the public folder include the js you copied with a defer attribute.

```html
<script defer src="count-btn.js"></script>
```

3\. Use the webcomponent in your App component.

```js
import "./App.css";

function App() {
  return (
    <div className="App">
      <h1>Svelte Component in React App</h1>
      <count-btn count="3"></count-btn>
    </div>
  );
}

export default App;
```

## How to use svelte component in Vue

1\. Copy the component js into the public folder.

2\. In the index.html file on the public folder include the js you copied with a defer attribute.

```html
<script defer src="count-btn.js"></script>
```

3\. Use the component in any of the .vue component files.

```html
<template>
  <div class="hello">
    <h1>Svelte Component in Vue app</h1>
    <count-btn count="23"></count-btn>
  </div>
</template>
```
