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

```dart
class DioClient {
  final Dio _dio = Dio();
}
```

- Define your `_baseUrl`.

```dart
class DioClient {
  final Dio _dio = Dio();

  final _baseUrl = 'http://10.0.2.2:5000';
}
```

- Create your first request that return a future list of pets and name it `getPets`.

```dart
  Future<List<Pet>> getPets() async {}
```

- Our endpoint is:

```
Get, http://http://10.0.2.2:5000/pets
```

- Store the response of the request in a `Response` object.

```dart
  Future<List<Pet>> getPets() async {
    List<Pet> pets = [];
    Response response = await _dio.get(_baseUrl + '/pets');
  }
```

- Map your response to `Pet` objects using the `Pet.fromJson` constructor.

```dart
  Future<List<Pet>> getPets() async {
    List<Pet> pets = [];
      Response response = await _dio.get(_baseUrl + '/pets');
      pets = (response.data as List).map((pet) => Pet.fromJson(pet)).toList();
  }
```

- Return your new `List` of `Pet`s.

```dart
  Future<List<Pet>> getPets() async {
    List<Pet> pets = [];
      Response response = await _dio.get(_baseUrl + '/pets');
      pets = (response.data as List).map((pet) => Pet.fromJson(pet)).toList();
    return pets;
  }
```

- Don't forget to wrap your request in a `try-catch` block.

```dart
  Future<List<Pet>> getPets() async {
    List<Pet> pets = [];
    try {
      Response response = await _dio.get(_baseUrl + '/pets');
      pets = (response.data as List).map((pet) => Pet.fromJson(pet)).toList();
    } on DioError catch (error) {
      print(error);
    }
    return pets;
  }
```

- In your `PetsProvider`, create a new function that returns a future void.

```dart
  Future<void> getPets() async {}
```

- Import `services/pets.dart` file, and call `DioClient().getPets()` function, don't forget to `await`.

```dart
  Future<void> getPets() async {
    await DioClient().getPets();
  }
```

- Lastly assign the result of `DioClient().getPets()` to the `pets` variable in the provider.

```dart
  Future<void> getPets() async {
    pets = await DioClient().getPets();
  }
```

- Back to `pages/home_page.dart`, wrap your `Consumer` widget with a `FutureBuilder` widget.

```dart
FutureBuilder()
```

- Assign the `future` property to the `getPets` function we created in our provider.

```dart
FutureBuilder(
     future: Provider.of<PetsProvider>(context, listen: false).getPets(),)
```

- And in your `builder` property, return the `Consumer` widget.

```dart
 future: Provider.of<PetsProvider>(context, listen: false).getPets(),
 builder: (context, dataSnapshot) => Consumer[...]
```

- Use the `dataSnapshot` argument to check if the data is still loading, and in this case, return a spinner. Or if there's an error, show the error in a `Text` widget.

```dart
FutureBuilder(
              future:
                  Provider.of<PetsProvider>(context, listen: false).getPets(),
              builder: (context, dataSnapshot) {
                if (dataSnapshot.connectionState == ConnectionState.waiting) {
                  return const Center(
                    child: CircularProgressIndicator(),
                  );
                } else {
                  if (dataSnapshot.error != null) {
                    return const Center(
                      child: Text('An error occurred'),
                    );
                  } else {
                    return Consumer[...]
```

### Part 2: Post Data

- In `pages` folder, create a new page called `add_page.dart`.

```dart
class AddPage extends StatelessWidget {
  AddPage({Key? key}) : super(key: key);
  final titleController = TextEditingController();
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Add a pet"),
      ),
      resizeToAvoidBottomInset: false,
      body: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          children: [
            const Text("Fill those field to add a pet"),
          ],
        ),
      ),
    );
  }
}
```

- Include this page within your routes in `main.dart`.

```dart
  final _router = GoRouter(
    routes: [
      GoRoute(
        path: '/',
        builder: (context, state) => const HomePage(),
      ),
      GoRoute(
        path: '/add',
        builder: (context, state) => AddPage(),
      ),
    ])
```

- In your `home_page.dart` point the `add a new pet` button to the page we just created.

```dart
    onPressed: () {
        GoRouter.of(context).push('/add');
            },
```

- In your widgets folder, create `add_form.dart` widget and inside it initialize a stateFul widget.

```dart
class AddPetForm extends StatefulWidget {
  @override
  AddPetFormState createState() {
    return AddPetFormState();
  }
}

class AddPetFormState extends State<AddPetForm> {
}
```

- Create a global key for your form.

```dart
  final _formKey = GlobalKey<FormState>();
```

- Create a `Form` widget and assign it the key we just created.

```dart
    return Form(
      key: _formKey,
    )
```

- Create `TextFormField`s for our properties except `adopted`, `image` and `id`.

```dart
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet name',
            ),
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet Gender',
            ),
            maxLines: null,
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet age',
            ),
          ),
        ],
      ),
    );
```

- Create a button to submit our data.

```dart
Center(
            child: ElevatedButton(
              onPressed: () {},
              child: const Text("Add Pet"),
            ),
          )
```

- Import your widget in the `add_page.dart` and render it.

```dart
child: Column(
          children: [
            const Text("Fill those field to add a book"),
            AddPetForm(),
          ],
        ),
```

- Back to `add_form.dart`, add a validator to each field as following:

```
All fields should not be empty.
Age should be of type int.
Gender should be either female or male
```

- In your submit button `onPressed`, run your `_formKey.currentState!.validate()` and test your form.

```dart
ElevatedButton(
              onPressed: () {
                _formKey.currentState!.validate()
              },
              child: const Text("Add Pet"),
            ),
```

- On top of your form widget, create variables to hold our data.

```dart
  String name = "";
  String gender = "";
  int age = 0;
```

- In your `TextFormField`s `onSaved` property, assign each value to a the variable you created for it.

```dart
children: <Widget>[
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet name',
            ),
            validator: (value) {
              if (value!.isEmpty) {
                return "please fill out this field";
              } else {
                return null;
              }
            },
            onSaved: (value) {
              name = value!;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet Gender',
            ),
            maxLines: null,
            validator: (value) {
              if (value!.isEmpty) {
                return "please fill out this field";
              } else {
                return null;
              }
            },
            onSaved: (value) {
              gender = value!;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet age',
            ),
            validator: (value) {
              if (value == null) {
                return "please enter an age";
              } else if (int.tryParse(value) == null) {
                return "please enter a number";
              }
              return null;
            },
            onSaved: (value) {
              age = int.parse(value!);
            },
          ),
          [...]
```

- In your submit button `onPressed` check if the return of `_formKey.currentState!.validate()` is `true`, and if so, run `_formKey.currentState!.save()`.

```dart
ElevatedButton(
              onPressed: () {
                if (_formKey.currentState!.validate()) {
                  _formKey.currentState!.save();
                }
              },
              child: const Text("Add Pet"),
            ),
```

- Install the `image_picker` package.

```shell
flutter pub add image_picker
```

- Import the package in `add_form.dart`.

```dart
import 'package:image_picker/image_picker.dart';
```

- On the top of your widget, create a variable to hold our image, and initialize an `ImagePicker` instance.

```dart
  var _image;
  final _picker = ImagePicker();
```

- Within your form after the text fields, Create a `Row` with a `GestureDetector` widget inside and a `Text` widget.

```dart
Row(
            children: [
              GestureDetector(
                onTap: () {
                },
                child: Container(
                  width: 100,
                  height: 100,
                  margin: const EdgeInsets.only(top: 20),
                  decoration: BoxDecoration(color: Colors.blue[200]),
                  child: Container(
                          decoration: BoxDecoration(color: Colors.blue[200]),
                          width: 200,
                          height: 200,
                          child: Icon(
                            Icons.camera_alt,
                            color: Colors.grey[800],
                          ),
                    ),
                ),
              ),
              const Padding(
                padding: EdgeInsets.all(8.0),
                child: Text("Image"),
              )
            ],
          ),
```

- Within the `onTap` method, change it to `async` and call the `ImagePicker` instance to pick an image from the gallery.

```dart
onTap: () async {
                  final XFile? image =
                      await _picker.pickImage(source: ImageSource.gallery);
                },
```

- Call `setState` and assign the result of the image picker to your `_image` variable your created earlier.

```dart
onTap: () async {
                  final XFile? image =
                      await _picker.pickImage(source: ImageSource.gallery);
                  setState(() {
                    _image = File(image!.path);
                  });
                },
```

- Inside your `Container` check if the user selected an image and if so, display this image using the `Image.File` widget.

```dart
 child: Container(
                  width: 100,
                  height: 100,
                  margin: const EdgeInsets.only(top: 20),
                  decoration: BoxDecoration(color: Colors.blue[200]),
                  child: _image != null
                      ? Image.file(
                          _image,
                          width: 200.0,
                          height: 200.0,
                          fit: BoxFit.fitHeight,
                        )
                      : Container(
                          decoration: BoxDecoration(color: Colors.blue[200]),
                          width: 200,
                          height: 200,
                          child: Icon(
                            Icons.camera_alt,
                            color: Colors.grey[800],
                          ),
                        ),
                ),
```

- Back to `services/pets.dart`, create a new function that returns a future `Pet` object and takes a `Pet` argument.

```dart
  Future<Pet> createPet({required Pet pet}) async {
  }
```

- Inside, Create a variable of type `Pet` and mark it as `late`.

```dart
  Future<Pet> createPet({required Pet pet}) async {
    late Pet retrievedPet;
  }
```

- Create variable `data` of type `FormData` and assign it to `FormData.fromMap` and map your pet data inside it.

```dart
      FormData data = FormData.fromMap({
        "name": pet.name,
        "age": pet.age,
        "gender": pet.gender,
        "adopted": pet.adopted,
        "image": await MultipartFile.fromFile(
          pet.image,
        ),
      });
```

- Create a request of type post to your endpoint and pass it the `data` variable we created:

```
Post, http://http://10.0.2.2:5000/pets
```

```dart
Response response = await _dio.post(_baseUrl + '/pets', data: data);
```

- Assign the `late` variable we created to the response, and return that variable.

```dart
Response response = await _dio.post(_baseUrl + '/pets', data: data);
retrievedPet = Pet.fromJson(response.data);
```

- Don't forget to wrap your call with a `try-catch` block.

```dart
 try {
      FormData data = FormData.fromMap({
        "name": pet.name,
        "age": pet.age,
        "gender": pet.gender,
        "image": await MultipartFile.fromFile(
          pet.image,
        ),
      });
      Response response = await _dio.post(_baseUrl + '/pets', data: data);
      retrievedPet = Pet.fromJson(response.data);
    } on DioError catch (error) {
      print(error);
    }
return retrievedPet;
```

- Back to your `providers/pets.dart`, create a void function called `createPet` that takes an argument of type `Pet`.

```dart
  void createPet(Pet pet) async {}
```

- Inside it, call `DioClient().createPet()` and pass to it the argument.

```dart
    await DioClient().createPet(pet: pet);
```

- Store the result in a variable called `newPet`.

```dart
    Pet newPet = await DioClient().createPet(pet: pet);
```

- Insert this `newPet` in your `List` of pets in the provider.

```dart
    Pet newPet = await DioClient().createPet(pet: pet);
    pets.insert(0, newPet);
```

- Don't forget to call `notifyListeners`.

```dart
    notifyListeners();
```

- Lastly, Go to your submit button, and call the provider `createPet` function with `listen` set to `false` and pass it the data of our form.
- Pop the screen so the user get's back to the home page.

```dart
onPressed: () {
                if (_formKey.currentState!.validate()) {
                  _formKey.currentState!.save();
                  print(age);
                  Provider.of<PetsProvider>(context, listen: false).createPet(
                      Pet(
                          name: name,
                          gender: gender,
                          image: _image.path,
                          age: age));
                  GoRouter.of(context).pop();
                }
              },
```

### Part 3: Put Data

- As you did with the `add_page.dart`, create a page for updating a pet, as well as a form for that, both expects a `Pet` argument.

```dart
class UpdatePage extends StatelessWidget {
  final Pet pet;
  const UpdatePage({Key? key, required this.pet}) : super(key: key);
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Update a pet"),
      ),
      resizeToAvoidBottomInset: false,
      body: Padding(
        padding: const EdgeInsets.all(20.0),
        child: Column(
          children: [
            const Text("Fill those field to update a book"),
            UpdatePetForm(
              pet: pet,
            ),
          ],
        ),
      ),
    );
  }
}
```

```dart
class UpdatePetForm extends StatefulWidget {
  final Pet pet;
  UpdatePetForm({required this.pet});
  @override
  UpdateFormState createState() {
    return UpdateFormState();
  }
}

class UpdateFormState extends State<UpdatePetForm> {
}
```

- Add your new page to your routes in `main.dart`, this time create a route param called `petId`.

```dart
      GoRoute(
        path: '/update/:petId',
        builder: (context, state) {
          return UpdatePage(pet: pet);
        },
      ),
```

- Call your provider, look for the pet using the `petId` param, and pass it to the update page.

```dart
      GoRoute(
        path: '/update/:petId',
        builder: (context, state) {
          final pet = Provider.of<PetsProvider>(context).pets.firstWhere(
              (pet) => pet.id.toString() == (state.params['petId']!));
          return UpdatePage(pet: pet);
        },
      ),
```

- In the `pet_card.dart` you have an edit icon, clicking on it should take the user to the update page and pass the pet id with it.

```dart
    IconButton(
        onPressed: () {
            GoRouter.of(context).push('/update/${pet.id}');
                        ,
        icon: const Icon(Icons.edit)),
```

- In your form, access the pet argument using `widget.pet`.
- Using this, add an initial value to your field.

```dart
  Widget build(BuildContext context) {
    Pet pet = widget.pet;
    return Form(
      key: _formKey,
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: <Widget>[
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet name',
            ),
            initialValue: pet.name,
            validator: (value) {
              if (value!.isEmpty) {
                return "please fill out this field";
              } else {
                return null;
              }
            },
            onSaved: (value) {
              name = value!;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet Gender',
            ),
            initialValue: pet.gender,
            maxLines: null,
            validator: (value) {
              if (value!.isEmpty) {
                return "please fill out this field";
              } else {
                return null;
              }
            },
            onSaved: (value) {
              gender = value!;
            },
          ),
          TextFormField(
            decoration: const InputDecoration(
              hintText: 'Pet age',
            ),
            initialValue: pet.age.toString(),
            validator: (value) {
              if (value == null) {
                return "please enter an age";
              } else if (int.tryParse(value) == null) {
                return "please enter a number";
              }
              return null;
            },
            onSaved: (value) {
              age = int.parse(value!);
            },
          ),
[...]
```

- Back to `services/pets.dart`, create a function that returns future `Pet`, requires an argument of type `Pet` and complete this function similarly to the `createPet` function you did.

```dart
  Future<Pet> updatePet({required Pet pet}) async {
    late Pet retrievedPet;
    try {
      FormData data = FormData.fromMap({
        "name": pet.name,
        "age": pet.age,
        "adopted": pet.adopted,
        "gender": pet.gender,
        "image": await MultipartFile.fromFile(
          pet.image,
        ),
      });

      Response response =
          await _dio.put(_baseUrl + '/pets/${pet.id}', data: data);
      retrievedPet = Pet.fromJson(response.data);
    } on DioError catch (error) {
      print(error);
    }
    return retrievedPet;
  }
```

- In your provider, create a function `updatePet` that takes a `Pet` argument and call the `DioClient().updatePet(pet: pet)`

```dart
  void updatePet(Pet pet) async {
    Pet newPet = await DioClient().updatePet(pet: pet);
  }
```

- Finally, find the index of the pet we want to replace, and replace the pet with the response we got and call `notifyListeners`.

```dart
  void updatePet(Pet pet) async {
    Pet newPet = await DioClient().updatePet(pet: pet);
    int index = pets.indexWhere((pet) => pet.id == newPet.id);
    pets[index] = newPet;
    notifyListeners();
  }
```

- In your update form submit button, call the provider function we just created and pass the form data.

```dart
              onPressed: () {
                if (_formKey.currentState!.validate()) {
                  // If the form is valid, display a Snackbar.
                  _formKey.currentState!.save();
                  Provider.of<PetsProvider>(context, listen: false).updatePet(
                      Pet(
                          id: pet.id,
                          name: name,
                          gender: gender,
                          image: _image.path,
                          age: age));
                  GoRouter.of(context).pop();
                }
              },
```

### Part 4: Delete Data

- In your `services/pets.dart`, create a function that returns future void, takes an argument of type `int` `petId`.

```dart
  Future<void> deletePet({required int petId}) async {}
```

- Call your delete endpoint and pass the `petId` in the url.

```dart
  Future<void> deletePet({required int petId}) async {
    try {
      await _dio.delete(_baseUrl + '/pets/${petId}');
    } on DioError catch (error) {
      print(error);
    }
  }
```

- In your Provider, create a function `deletePet` that takes an argument of type `int` `petId`.

```dart
  void deletePet(int petId) async {}
```

- Call `DioClient().deleteBook()` and pass it the `petId`.

```dart
  void deletePet(int petId) async {
    await DioClient().deletePet(petId: petId);
  }
```

- Search for the pet with the same `id` and remove it from the list then call `notifyListeners`.

```dart
  void deletePet(int petId) async {
    await DioClient().deletePet(petId: petId);
    pets.removeWhere((pet) => pet.id == petId);
    notifyListeners();
  }
```

- In your `pet_card.dart` delete icon button, call this function and pass it the `pet.id`.

```dart
    IconButton(
        onPressed: () {
            Provider.of<PetsProvider>(context, listen: false).deletePet(pet.id!);
                        },
        icon: const Icon(
                Icons.delete,
                color: Colors.red,
        ))
```

### Part 5: Adopting A Pet.

- In your `services/pets.dart`, create a function that returns future void, takes an argument of type `int` `petId`.

```dart
  Future<void> adoptPet({required int petId}) async {}
```

- Call your adopt endpoint and pass the `petId` in the url.

```dart
  Future<void> adoptPet({required int petId}) async {
    try {
      Response response = await _dio.post(_baseUrl + '/pets/adopt/${petId}');
      retrievedPet = Pet.fromJson(response.data);
    } on DioError catch (error) {
      print(error);
    }
  }
```

- In your Provider, create a function `adoptPet` that takes an argument of type `int` `petId`.

```dart
  void adoptPet(int petId) async {}
```

- Call `DioClient().adoptPet();` and pass it the `petId`.

```dart
  void adoptPet(int petId) async {
    await DioClient().adoptPet(petId: petId);
  }
```

- Search for the pet with the same `id` and change the `adopted` property to true and call `notifyListeners`.

```dart
  void adoptPet(int petId) async {
    await DioClient().adoptPet(petId: petId);
    int index = pets.indexWhere((pet) => pet.id == newPet.id);
    pets[index].adopted = true;
    notifyListeners();
  }
```

- In your `pet_card.dart` adopt button, call this function and pass it the `pet.id`.

```dart
ElevatedButton(
    onPressed: () {
            Provider.of<PetsProvider>(context, listen: false).adoptPet(pet.id!);
            },
    child: const Text("Adopt"),
),
```

- Make the button disabled if the pet `adopted` property is `true`.

```dart
ElevatedButton(
    onPressed: pet.adopted
            ? null
            : () {
        Provider.of<PetsProvider>(context, listen: false).adoptPet(pet.id!);
                },
    child: const Text("Adopt"),
    ),
```
