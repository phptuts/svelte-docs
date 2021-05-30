1\. Install Svelte Form Libary

```
npm i svelte-forms-lib yup
```

2\. Clean up the index.svelte page

3\. Add Bootstrap app.html

```
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.1/dist/css/bootstrap.min.css" />
<link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"
      integrity="sha512-iBBXm8fW90+nuLcSKlbmrPcLa0OT92xO1BIsZ+ywDWZCvqsWgccV3gFoRBv0z+8dLJgyAHIhR35VZc2oM/gI1w=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    />
```

4\. Create the starter form in our add-recipe.svelte pageg.

```typescript
import * as yup from "yup";
import { createForm } from "svelte-forms-lib";
import { Row, Col, Button, FormGroup, Input, Label } from "sveltestrap/src";

const schema = yup.object().shape({
  title: yup.string().required().min(4).max(50),
  description: yup.string().required().min(10).max(1000),
});

const { form, errors, handleChange, handleSubmit } = createForm({
  initialValues: {
    title: "",
    description: "",
  },
  validationSchema: schema,
  onSubmit: (values) => {
    alert(JSON.stringify(values));
  },
});
```

5\. Create the HTML

```html
<Row>
  <Col>
    <FormGroup>
      <Label for="title">Title</Label>
      <Input
        on:change={handleChange}
        bind:value={$form.title}
        invalid={$errors.title.length > 0}
        type="text"
        name="title"
        id="title"
        placeholder="Recipe Title"
      />
      {#if $errors.title}
        <div class="invalid-feedback">{$errors.title}</div>
      {/if}
    </FormGroup>
  </Col>
</Row>

<Row>
  <Col>
    <FormGroup>
      <Label for="title">Description</Label>
      <Input
        on:change={handleChange}
        bind:value={$form.description}
        invalid={$errors.description.length > 0}
        type="textarea"
        name="description"
        id="description"
        placeholder="Recipe Description"
      />
      {#if $errors.description}
        <div class="invalid-feedback">{$errors.description}</div>
      {/if}
    </FormGroup>
  </Col>
</Row>
<Row>
  <Col>
    <Button on:click={handleSubmit} class="w-100" color="success">Submit</Button
    >
  </Col>
</Row>
```

6\. Add the list of ingredients to our schema.

```svelte
const schema = yup.object().shape({
  title: yup.string().required().min(4).max(50),
  description: yup.string().required().min(10).max(1000),
  ingredients: yup
    .array()
    .min(1)
    .max(10)
    .of(
      yup.object().shape({
        name: yup.string().required().min(2).max(10),
        unit: yup.mixed().oneOf(["n/a", "ounces", "cups", "pounds"]),
        amount: yup.number().min(1).max(30000),
      })
    ),
});
```

7\. Add the default for the create form.

```js
const { form, errors, handleChange, handleSubmit } = createForm({
  initialValues: {
    title: "",
    description: "",
    ingredients: [
      {
        name: "",
        units: "none",
        amount: 1,
      },
    ],
  },
  validationSchema: schema,
  onSubmit: (values) => {
    alert(JSON.stringify(values));
  },
});
```

7\. Add the HTML For Ingredients and the button.

```svelte
<Row class="mb-4 mt-4">
  <Col>
    <h2>Ingredients</h2>
  </Col>
  <Col>
    <Button class="float-end" color="primary">Add Ingredient</Button>
  </Col>
</Row>
```

8\. Add the loop HTML For the ingredients

```svelte
{#each $form.ingredients as ingredient, i}
  <Row>
    <Col sm={5}>
      <FormGroup>
        <Label for={`ingredients_${i}_name`}>Name</Label>
        <Input
          on:change={handleChange}
          bind:value={$form.ingredients[i]["name"]}
          invalid={$errors.ingredients[i]["name"] &&
            $errors.ingredients[i]["name"].length > 0}
          type="text"
          name={`ingredients[${i}].name`}
          id={`ingredients_${i}_name`}
          placeholder="Name"
        />
        {#if $errors.ingredients[i]["name"]}
          <div class="invalid-feedback">{$errors.ingredients[i]["name"]}</div>
        {/if}
      </FormGroup>
    </Col>
    <Col sm={4}>
      <FormGroup>
        <Label for={`ingredients_${i}_units`}>Units</Label>
        <Input
          on:change={handleChange}
          bind:value={$form.ingredients[i]["units"]}
          type="select"
          name={`ingredients[${i}].units`}
          id={`ingredients_${i}_units`}
        >
          <option value="none">None</option>
          <option value="pounds">Pounds</option>
          <option value="ounces">Ounces</option>
          <option value="cups">Cups</option>
        </Input>
      </FormGroup>
    </Col>

    <Col sm={2}>
      <FormGroup>
        <Label for={`ingredients_${i}_amount`}>Amount</Label>
        <Input
          on:change={handleChange}
          bind:value={$form.ingredients[i]["amount"]}
          invalid={$errors.ingredients[i]["amount"] &&
            $errors.ingredients[i]["amount"].length > 0}
          type="number"
          min="1"
          max="300000"
          name={`ingredients[${i}]amount`}
          id={`ingredients_${i}_amount`}
        />
        {#if $errors.ingredients[i]["amount"]}
          <div class="invalid-feedback">{$errors.ingredients[i]["amount"]}</div>
        {/if}
      </FormGroup>
    </Col>
    <Col sm={1}>
      <i class="fas fa-trash" on:click={() => removeIngredient(i)} />
    </Col>
  </Row>
{/each}

<style>
.fa-trash {
    margin-top: 35px;
    color: rgb(254, 131, 131);
    cursor: pointer;
    font-size: 25px;
  }
</style>
```

9\. Add an add function to add ingredients and attach it to the button.

```js
const addIngredient = () => {
  $form.ingredients = $form.ingredients.concat({
    name: "",
    units: "none",
    amount: 1,
  });

  $errors.ingredients = $errors.ingredients.concat({
    name: "",
    units: "",
    amount: "",
  });
};
```

```html
<button class="float-end" on:click="{addIngredient}" color="primary">
  Add Ingredient
</button>
```

10\. Add a remove column button.

```js
const removeIngredient = (index: number) => {
  $form.ingredients = $form.ingredients.filter((i, j) => j !== index);
  $errors.ingredients = $errors.ingredients.filter((i, j) => j !== index);
};
```

```html
<i class="fas fa-trash" on:click={() => removeIngredient(i)} />
```

11\. Add the error message for when no ingredients are present.

```svelte
{#if typeof $errors.ingredients === "string" && !$errors.ingredients.includes("[object")}
  <Alert color="danger">{$errors.ingredients}</Alert>
{/if}
```

12\. Create a file called db. Here we'll add types and save db function.

```typescript
import firebase from "firebase/app";
import { firestore } from "./firestore";

export type Ingredient = {
  name: string;
  units: string;
  amount: number;
};

export type Recipe = {
  title: string;
  description: string;
  userId: string;
  createdAt: firebase.firestore.Timestamp | firebase.firestore.FieldValue;
  updatedAt: firebase.firestore.Timestamp | firebase.firestore.FieldValue;
  ingredients: Ingredient[];
};

export type RecipeForm = {
  title: string;
  description: string;
  ingredients: Ingredient[];
};

export const createRecipe = async (recipeForm: RecipeForm, userId: string) => {
  const recipe: Recipe = {
    ...recipeForm,
    userId,
    createdAt: firebase.firestore.FieldValue.serverTimestamp(),
    updatedAt: firebase.firestore.FieldValue.serverTimestamp(),
  };
  const db = await firestore();
  await db.collection("recipes").add(recipe);
};
```

13\. Edit the submit function to save.

```js
  import { createRecipe } from "../db";

...
onSubmit: async (values) => {
      try {
        await createRecipe(values, $authStore.user.uid);
        alert("Saved Recipe");
      } catch (e) {
        alert("error saving");
        console.log(e);
      }
    },
```

14\. Firestore Rules

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
