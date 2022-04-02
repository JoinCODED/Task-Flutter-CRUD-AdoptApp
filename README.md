# Pets Adoption App 🦄

## Instructions

- Fork and clone [this repository](https://github.com/JoinCODED/Task-Flutter-CRUD-AdoptApp) to your `Development` folder.
- Endpoints:

```
Get pets, type: Get, http://http://10.0.2.2:5000/pets
Create a new pet, type: Post, http://http://10.0.2.2:5000/pets
Update a pet, type: Put, http://http://10.0.2.2:5000/pets/{petId}
Delete a pet, type: Delete, http://http://10.0.2.2:5000/pets/{petId}
Adopt a pet, type: Post, http://http://10.0.2.2:5000/pets/adopt/{petId}
```

### Part 1: Get Data

- Install `dio` into your project:

```shell
flutter pub add dio
```

- Create a folder named `services`, inside it create a file called `pets.dart`.
- Import `dio` package in `pets.dart`.

```dart
import "package:dio/dio.dart";
```

- Create a new `DioClient` class and initialize a new dio instance.
- Define your `_baseUrl`.
- Create your first request that return a future list of pets and name it `getPets`.
- Our endpoint is:

```
Get, http://http://10.0.2.2:5000/pets
```

- Store the response of the request in a `Response` object.
- Map your response to `Pet` objects using the `Pet.fromJson` constructor.
- Don't forget to convert the result to a `List`.
- Return your new `List` of `Pet`s.
- Don't forget to wrap your request in a `try-catch` block.

- In your `PetsProvider`, create a new function that returns a future void.
- Import `services/pets.dart` file, and call `DioClient().getPets()` function, don't forget to `await`.
- Lastly assign the result of `DioClient().getPets()` to the `pets` variable in the provider.

- Back to `pages/home_page.dart`, wrap your `Consumer` widget with a `FutureBuilder` widget.
- Assign the `future` property to the `getPets` function we created in our provider.
- And in your `builder` property, return the `Consumer` widget.
- Use the `dataSnapshot` argument to check if the data is still loading, and in this case, return a spinner. Or if there's an error, show the error in a `Text` widget.

### Part 2: Post Data

- In `pages` folder, create a new page called `add_page.dart`.
- Include this page within your routes in `main.dart`.
- In your `home_page.dart` point the `add a new pet` button to the page we just created.

- In your widgets folder, create `add_form.dart` widget and inside it initialize a stateFul widget.
- Create a global key for your form.
- Create a `Form` widget and assign it the key we just created.
- Create `TextFormField`s for our properties except `adopted`, `image` and `id`.
- Create a button to submit our data.
- Import your widget in the `add_page.dart` and render it.
- Back to `add_form.dart`, add a validator to each field as following:

```
All fields should not be empty.
Age should be of type int.
Gender should be either female or male
```

- In your submit button `onPressed`, run your `_formKey.currentState!.validate()` and test your form.
- On top of your form widget, create variables to hold our data.
- In your `TextFormField`s `onSaved` property, assign each value to a the variable you created for it.
- In your submit button `onPressed` check if the return of `_formKey.currentState!.validate()` is `true`, and if so, run `_formKey.currentState!.save()`.

- Install the `image_picker` package.

```shell
flutter pub add image_picker
```

- Import the package in `add_form.dart`.
- On the top of your widget, create a variable to hold our image, and initialize an `ImagePicker` instance.
- Within your form after the text fields, Create a `Row` with a `GestureDetector` widget inside and a `Text` widget.
- Within the `onTap` method, change it to `async` and call the `ImagePicker` instance to pick an image from the gallery.
- Call `setState` and assign the result of the image picker to your `_image` variable your created earlier.
- Inside your `Container` check if the user selected an image and if so, display this image using the `Image.File` widget.
- Back to `services/pets.dart`, create a new function that returns a future `Pet` object and takes a `Pet` argument.
- Inside, Create a variable of type `Pet` and mark it as `late`.
- Create variable `data` of type `FormData` and assign it to `FormData.fromMap` and map your pet data inside it.
- Create a request of type post to your endpoint and pass it the `data` variable we created:

```
Post, http://http://10.0.2.2:5000/pets
```

- Assign the `late` variable we created to the response, and return that variable.
- Don't forget to wrap your call with a `try-catch` block.

- Back to your `providers/pets.dart`, create a void function called `createPet` that takes an argument of type `Pet`.
- Inside it, call `DioClient().createPet()` and pass to it the argument.
- Store the result in a variable called `newPet`.
- Insert this `newPet` in your `List` of pets in the provider.
- Don't forget to call `notifyListeners`.

- Lastly, Go to your submit button, and call the provider `createPet` function with `listen` set to `false` and pass it the data of our form.
- Pop the screen so the user get's back to the home page.

### Part 3: Put Data

- As you did with the `add_page.dart`, create a page for updating a pet, as well as a form for that, both expects a `Pet` argument.
- Add your new page to your routes in `main.dart`, this time create a route param called `petId`.
- Call your provider, look for the pet using the `petId` param, and pass it to the update page.

- In the `pet_card.dart` you have an edit icon, clicking on it should take the user to the update page and pass the pet id with it.
- In your form, access the pet argument using `widget.pet`.
- Using this, add an initial value to your field.

- Back to `services/pets.dart`, create a function that returns future `Pet`, requires an argument of type `Pet` and complete this function similarly to the `createPet` function you did.
- Your endpoint is:

```
Put, http://http://10.0.2.2:5000/pets/{petId}
```

- Using string interpolation, inject the `pet.id` value within the url, and pass the `data` as a second argument.
- In your provider, create a function `updatePet` that takes a `Pet` argument and call the `DioClient().updatePet(pet: pet)`
- Finally, find the index of the pet we want to replace, and replace the pet with the response we got and call `notifyListeners`.

- In your update form submit button, call the provider function we just created and pass the form data.
- Pop your screen so the user gets back to the `home_page`.

### Part 4: Delete Data

- In your `services/pets.dart`, create a function that returns future void, takes an argument of type `int` `petId`.
- Call your delete endpoint and pass the `petId` in the url.
- In your Provider, create a function `deletePet` that takes an argument of type `int` `petId`.
- Call `DioClient().deleteBook()` and pass it the `petId`.
- Search for the pet with the same `id` and remove it from the list then call `notifyListeners`.
- In your `pet_card.dart` delete icon button, call this function and pass it the `pet.id`.

### Part 5: Adopting A Pet.

- In your `services/pets.dart`, create a function that returns future void, takes an argument of type `int` `petId`.
- Call your adopt endpoint and pass the `petId` in the url.
- In your Provider, create a function `adoptPet` that takes an argument of type `int` `petId`.
- Call `DioClient().adoptPet();` and pass it the `petId`.
- Search for the pet with the same `id` and change the `adopted` property to true and call `notifyListeners`.
- In your `pet_card.dart` adopt button, call this function and pass it the `pet.id`.
- Make the button disabled if the pet `adopted` property is `true`.
