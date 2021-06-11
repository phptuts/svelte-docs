## Uplaoding Pictures to Firebase

## Video

## Code

## Steps


1\. Add File validator with yup.ts and import into add-recipe.svelte page.

```typescript
import * as yup from "yup";

yup.addMethod(yup.mixed, "fileMax", function (args) {
  const { maxBytes, message } = args;
  return this.test("fileMax", message, function (value) {
    if (!value || value instanceof File === false) {
      return true;
    }

    return Math.round(value.size / 1024) < maxBytes;
  });
});

yup.addMethod(yup.mixed, "fileFormat", function (args) {
  const { formats, message } = args;
  return this.test("fileFormat", message, function (value) {
    if (!value || value instanceof File === false) {
      return true;
    }
    return formats.includes(value.type);
  });
});
```

2\. Add it to our form schema.

```typescript
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
      mainPicture: yup
      .mixed()
      .required("Picture Required")
      .fileMax({
        maxBytes: 500000,
        message: "Max Image size is 50MB",
      })
      .fileFormat({
        formats: ["image/gif", "image/jpeg", "image/png"],
        message: "Images can only be png, gif, jpg",
      }),
  });

```

and default values and import yup.ts

```ts
import '../yup.ts.';


// ...
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
      mainPicture: null
    },
```

3a\. Fix Units mistake

Add an s to units in the form schema.


3\. Add a form field.  We updating the field with the file and not the just the value because we want the validation to validate the file.

```svelte
<Row>
  <Col>
    <FormGroup>
      <Label for="title">Main Picture</Label>
      <Input
        on:change={(e) => updateValidateField("mainPicture", e.target.files[0])}
        invalid={$errors.mainPicture.length > 0}
        type="file"
        name="mainPicture"
        id="mainPicture"
      />
      {#if $errors.mainPicture}
        <div class="invalid-feedback">{$errors.mainPicture}</div>
      {/if}
    </FormGroup>
  </Col>
</Row>

```

4\. Import the firebase storage and extra protection for initialize the app a second time.

```
await import("firebase/storage");
    if (fb.apps.length == 0) {
      fb.initializeApp(firebaseConfig);
    }
```

5\. Change the recipe form type to accept and file

```ts
export type RecipeForm = {
  title: string;
  mainPicture: File,
  description: string;
  ingredients: Ingredient[];
};
```

6\.  Add the uploadFile and getUrl functions


```ts
const uploadFile = async (
  recipeId: string,
  userId: string,
  pic: File,
) => {

  const mainPicturePath = `/${userId}/${recipeId}.${pic.name
    .split(".")
    .pop()}`;
  const storage = firebase.storage();
  const ref = storage.ref(mainPicturePath);
  await ref.put(pic);
  return mainPicturePath;
};

export const getUrl = async (path: string) => {
  const storage = firebase.storage();
  return await storage.ref(path).getDownloadURL();
};
```

7\. Modify the createRecipe function to save the picture.

```ts
export const createRecipe = async (recipeForm: RecipeForm, userId: string) => {
  
  const recipe: Recipe = {
    title: recipeForm.title,
    description: recipeForm.description,
    ingredients: recipeForm.ingredients,
    userId,
    createdAt: firebase.firestore.FieldValue.serverTimestamp(),
    updatedAt: firebase.firestore.FieldValue.serverTimestamp(),
  };
  const db = await firestore();
  const recipeRef = await db.collection("recipes").add(recipe);
  const path = await uploadFile(recipeRef.id, userId, recipeForm.mainPicture);
  const url = await getUrl(path);
  recipeRef.update("picture", url);

  return recipeRef;
};
```

8\. Try saving and notice it fails.  It fails because we don't all the collection to be updated.  Update your firebase storage rule and firestore rules to fix this.

Firestore
```
match /recipes/{document=**} {
      allow read: if true;
      allow create: if request.auth != null;
      allow update: if request.auth != null && request.auth.uid == resource.data.userId;
    }
```
Firestorage
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read: if true;
    }
    match/{userId}/{allPaths=**} {
    	 allow write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```