# Flutter Fix
Practical Flutter App development code problems and solutions

## Automatic flutter app icon launcher 

* Add flutter_launcher_icons as dev_dependencies
* Put your icons in a folder and configure it as shown below with the icon path
```yaml

dev_dependencies:

  flutter_launcher_icons: ^0.11.0

flutter_icons:
  android: true
  ios: true
  image_path: "dev_assets/flatchat.png"
  adaptive_icon_background: "#ffffff"
  adaptive_icon_foreground: "dev_assets/flatchat_adaptive.png"

```
* run this command `flutter pub run flutter_launcher_icons`


## Flutter form validation (login / signup screen)

```dart
import 'package:flutter/material.dart';

class AuthForm extends StatefulWidget {
  const AuthForm({super.key});

  @override
  State<AuthForm> createState() => _AuthFormState();
}

class _AuthFormState extends State<AuthForm> {
  final _formKey = GlobalKey<FormState>();
  var _isLogin = true;
  var _userEmail = '';
  var _userName = '';
  var _userPassword = '';
  void _trySubmit() {
    final isValid = _formKey.currentState!.validate();
    FocusScope.of(context).unfocus();
    if (isValid) {
      _formKey.currentState!.save();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Card(
        margin: const EdgeInsets.all(20),
        child: SingleChildScrollView(
          child: Padding(
            padding: const EdgeInsets.all(14),
            child: Form(
              key: _formKey,
              child: Column(
                mainAxisSize: MainAxisSize.min,
                children: [
                  TextFormField(
                    key: const ValueKey('email'),
                    validator: (value) {
                      if (value!.isEmpty ||
                          !value.contains('@') ||
                          !value.contains('.') ||
                          value.length < 6) {
                        return 'Please enter a valid email address';
                      }
                      return null;
                    },
                    keyboardType: TextInputType.emailAddress,
                    decoration:
                        const InputDecoration(labelText: 'Email address'),
                    onSaved: (newValue) {
                      _userEmail = newValue!;
                    },
                  ),
                  if (!_isLogin)
                    TextFormField(
                      key: const ValueKey('username'),
                      validator: (value) {
                        if (value!.isEmpty || value.length < 5) {
                          return 'Username should be min 6 chars long';
                        }
                        return null;
                      },
                      decoration: const InputDecoration(labelText: 'Username'),
                      onSaved: (newValue) {
                        _userName = newValue!;
                      },
                    ),
                  TextFormField(
                    key: const ValueKey('password'),
                    validator: (value) {
                      if (value!.isEmpty || value.length < 7) {
                        return 'Please enter min 8 char long password';
                      }
                      return null;
                    },
                    decoration: const InputDecoration(labelText: 'Password'),
                    obscureText: true,
                    onSaved: (newValue) {
                      _userPassword = newValue!;
                    },
                  ),
                  const SizedBox(height: 14),
                  TextButton(
                    onPressed: _trySubmit,
                    child: Text(_isLogin ? 'Login' : 'Signup'),
                  ),
                  TextButton(
                    onPressed: () {
                      setState(() {
                        _isLogin = !_isLogin;
                      });
                    },
                    child: Text(_isLogin
                        ? 'Create a new account'
                        : 'I already have an account'),
                  )
                ],
              ),
            ),
          ),
        ),
      ),
    );
  }
}

```

## Getting data from firestore

```dart
 floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: () async {
          await Firebase.initializeApp();
          FirebaseFirestore.instance
              .collection('/chat/5eUgK6qd6YABtD2Pt723/messages/')
              .snapshots()
              .listen((QuerySnapshot querySnapshot) {
            print('I am here');
            print(querySnapshot.docs.map((e) => e['text']));
            // 'text' is a document field stored in database
          });
        },
      ),
```

## Use of Camera in flutter 
```dart
import 'dart:io';
import 'package:flutter/material.dart';
import 'package:image_picker/image_picker.dart';

class ImageInput extends StatefulWidget {
  const ImageInput({super.key});

  @override
  State<ImageInput> createState() => _ImageInputState();
}

class _ImageInputState extends State<ImageInput> {
  File? _storedImage;
  Future<void> _takePicture() async {
    final imagePicker = ImagePicker();
    final imageFile = await imagePicker.pickImage(
      source: ImageSource.camera,
      maxWidth: 600,
    );

    // this will prevent app crash when you'll cancel the camera
    // without taking image
    if (imageFile == null) {
      return;
    }

    // update _storedImage immediately after taking a photo
    setState(() {
      _storedImage = File(imageFile.path);
    });
  }

  @override
  Widget build(BuildContext context) {
    return Row(
      children: [
        Container(
          width: 150,
          height: 100,
          decoration: BoxDecoration(
            border: Border.all(width: 1, color: Colors.grey),
          ),
          alignment: Alignment.center,
          child: _storedImage != null
              ? Image.file(
                  _storedImage!,
                  fit: BoxFit.cover,
                  width: double.infinity,
                )
              : const Text(
                  'No image taken',
                  textAlign: TextAlign.center,
                ),
        ),
        const SizedBox(
          height: 12.0,
        ),
        Expanded(
          child: TextButton.icon(
            onPressed: _takePicture,
            icon: const Icon(Icons.camera_alt),
            label: const Text('Take Photo'),
          ),
        ),
      ],
    );
  }
}

```

## Signup with email and password Firebase api endpoint
```dart
final url = Uri.parse('https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=here_is_your_project_api_key');

// final url = Uri.parse('https://www.googleapis.com/identitytoolkit/v3/relyingparty/signupNewUser?key=here_is_your_project_api_key');
```

## Firebase Database Rules only authenticated users can read- write

```javascript{
  "rules": {
    ".read": "auth !=null",  
    ".write": "auth !=null",  
  }
}
```

## Update only required field in Firebase realtimedatabase with flutter
```dart
import 'dart:convert';
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

class Product with ChangeNotifier {
  final String id;
  final String title;
  final String description;
  final double price;
  final String imageUrl;
  bool isFavorite;

  Product({
    required this.id,
    required this.title,
    required this.description,
    required this.price,
    required this.imageUrl,
    this.isFavorite = false,
  });

  void _setFavoriteValue(bool newValue) {
    isFavorite = newValue;
    notifyListeners();
  }

  Future<void> toggleFavoriteStatus() async {
    final oldStatus = isFavorite;
    isFavorite = !isFavorite;
    notifyListeners();
    try {
      final url =
          'https://your_own_url_here.firebaseio.com/products/$id.json';

      final response = await http.patch(Uri.parse(url),
          body: json.encode({
            'isFavorite': isFavorite,
          }));

      if (response.statusCode != 200) {
        _setFavoriteValue(oldStatus);
      }
    } catch (error) {
      _setFavoriteValue(oldStatus);
    }
  }
}


```


## Handle invalid network image loading in flutter (404)

```dart
Image.network(
            product.imageUrl,
            fit: BoxFit.cover,

            // if image url does not work, load a sample image from app assets
            errorBuilder:
                (BuildContext context, Object error, StackTrace? stackTrace) {
              return Image.asset(
                'assets/images/image_not_found.jpg',
                fit: BoxFit.cover,
              );
            },
          ),

```


## Rebuild only required or update data consumed widget with Provider
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../pages/product_details.page.dart';
import '../providers/product.model.dart';

class ProductItem extends StatelessWidget {
  const ProductItem({super.key});

  @override
  Widget build(BuildContext context) {
  
  //don't listen for whole widgets - listen false
  // but it will listen where required through Consumer
    final product = Provider.of<Product>(context, listen: false);
    return ClipRRect(
      borderRadius: BorderRadius.circular(10),
      child: GridTile(
        header: Text(
          'Product ID: ${product.id.toUpperCase()}',
        ),
        footer: GridTileBar(
            backgroundColor: Colors.black87,
            
            // consume updates only where you want 
            //and only this widget will be rebuilt
            
            leading: Consumer<Product>(
              builder: (ctx, prod, child) => IconButton(
                onPressed: () {
                  product.toggleFavoriteStatus();
                },
                color: Theme.of(context).primaryColor,
                icon: Icon(product.isFavorite
                    ? Icons.favorite
                    : Icons.favorite_border),
              ),
            ),
            trailing: IconButton(
                onPressed: () {},
                color: Theme.of(context).primaryColor,
                icon: const Icon(Icons.shopping_cart)),
            title: Text(
              product.title,
              textAlign: TextAlign.center,
            )),
        child: GestureDetector(
          onTap: () {
            Navigator.of(context).pushNamed(
              ProductDetailsPage.routeName,
              arguments: product.id,
            );
          },
          child: Image.network(
            product.imageUrl,
            fit: BoxFit.cover,
          ),
        ),
      ),
    );
  }
}

```

## Use of LinearGradient in flutter

```dart
Card(
        elevation: 5,
        child: Container(
          padding: const EdgeInsets.all(10),
          decoration: BoxDecoration(
            borderRadius: BorderRadius.circular(5),
            gradient: LinearGradient(
              colors: [color.withOpacity(0.7), color],
              begin: Alignment.topLeft,
              end: Alignment.bottomLeft,
            ),
          ),
          child: Center(
            child: Text(
              title,
              style:
                  const TextStyle(fontSize: 18.0, fontWeight: FontWeight.bold),
            ),
          ),
        ),
      )
```

## Use of flutter InkWell

```dart
Widget build(BuildContext context) {
    return InkWell(
      onTap: () => selectCategory(context),
      splashColor: Theme.of(context).primaryColor,
      borderRadius: BorderRadius.circular(20),
      child: Card(
        elevation: 5,
        child: Container(
          padding: const EdgeInsets.all(10),
          decoration: BoxDecoration(
            borderRadius: BorderRadius.circular(5),
            gradient: LinearGradient(
              colors: [color.withOpacity(0.7), color],
              begin: Alignment.topLeft,
              end: Alignment.bottomLeft,
            ),
          ),
          child: Center(
            child: Text(
              title,
              style:
                  const TextStyle(fontSize: 18.0, fontWeight: FontWeight.bold),
            ),
          ),
        ),
      ),
    );
  }
}
```

## Use of flutter didChangeDependencies
* widget. call does not work in state class, so to use in initState is ok, but
* ModalRoute does not work in initState(), so to use all in didChangeDependencies

```dart
void didChangeDependencies() {
    if (!_loadedInitData) {
      final routeArgs =
          ModalRoute.of(context)?.settings.arguments as Map<String, String>;
      categoryTitle = routeArgs['title']!;
      final categoryId = routeArgs['id'];
      displayedMeals = widget.availableMeals.where((meal) {
        return meal.categories.contains(categoryId);
      }).toList();

      _loadedInitData = true;
    }

    super.didChangeDependencies();
  }


```

## Use of flutter GridView

```dart
  Widget build(BuildContext context) {
    return SafeArea(
      child: GridView(
        padding: const EdgeInsets.all(16),
        gridDelegate: const SliverGridDelegateWithMaxCrossAxisExtent(
          maxCrossAxisExtent: 180,
          childAspectRatio: 1,
          crossAxisSpacing: 12,
          mainAxisSpacing: 12,
          mainAxisExtent: 75,
        ),
        children: listOfCategories
            .map((data) => CategoryItem(data.id, data.title, data.color))
            .toList(),
      ),
    );
  }

```

## Use of flutter SwitchListTile
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app) in filter_screen.dart

```dart

import 'package:flutter/material.dart';
import '../widgets/main_drawer.dart';

class FilterScreen extends StatefulWidget {
  static const routeName = '/filters';
  const FilterScreen({super.key});

  @override
  State<FilterScreen> createState() => _FilterScreenState();
}

class _FilterScreenState extends State<FilterScreen> {
  bool _glutenFree = false;
  bool _vegetarian = false;
  bool _vegan = false;
  bool _lactoseFree = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Filtered'),
      ),
      drawer: const MainDrawer(),
      body: Column(
        children: [
          Container(
            padding: const EdgeInsets.all(14),
            child: const Text('Filtered meals'),
          ),
          Expanded(
            child: ListView(
              children: [
                SwitchListTile(
                  title: const Text('Gluten free'),
                  subtitle: const Text('Show gluten free meal'),
                  value: _glutenFree,
                  onChanged: ((newValue) {
                    setState(() {
                      _glutenFree = newValue;
                    });
                  }),
                ),
                SwitchListTile(
                  title: const Text('Vegetarian'),
                  subtitle: const Text('Show vegetarian meals'),
                  value: _vegetarian,
                  onChanged: ((newValue) {
                    setState(() {
                      _vegetarian = newValue;
                    });
                  }),
                ),
                SwitchListTile(
                  title: const Text('Vegan'),
                  subtitle: const Text('Show Vegan meals'),
                  value: _vegan,
                  onChanged: ((newValue) {
                    setState(() {
                      _vegan = newValue;
                    });
                  }),
                ),
                SwitchListTile(
                  title: const Text('Lactose free'),
                  subtitle: const Text('Show Lactose free meals'),
                  value: _lactoseFree,
                  onChanged: ((newValue) {
                    setState(() {
                      _lactoseFree = newValue;
                    });
                  }),
                ),
              ],
            ),
          )
        ],
      ),
    );
  }
}

```


## Use of flutter pushReplacementNamed() and Drawer()
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app) in main_drawer.dart 
```dart
import 'package:flutter/material.dart';
import '../screens/filter_screen.dart';

class MainDrawer extends StatelessWidget {
  Widget buildListTile(String title, IconData icon, VoidCallback tapHandler) {
    return ListTile(
      leading: Icon(
        icon,
        size: 24,
      ),
      title: Text(
        title,
        style: const TextStyle(fontWeight: FontWeight.bold),
      ),
      onTap: tapHandler,
    );
  }

  const MainDrawer({super.key});

  @override
  Widget build(BuildContext context) {
    return Drawer(
      child: Column(
        children: [
          Container(
            height: 100,
            width: double.infinity,
            padding: const EdgeInsets.all(14),
            alignment: Alignment.centerLeft,
            child: const Text(
              'Cooking up!',
              style: TextStyle(fontSize: 20),
            ),
          ),
          const SizedBox(height: 16),
          buildListTile(
            'Meals',
            Icons.restaurant,
            () {
              Navigator.of(context).pushReplacementNamed('/');
            },
          ),
          buildListTile(
            'Filters',
            Icons.settings,
            () {
              Navigator.of(context)
                  .pushReplacementNamed(FilterScreen.routeName);
            },
          ),
        ],
      ),
    );
  }
}


```

## Flutter Drawer()
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app) in TabScreen
```dart
Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(_pages[_selectedPageIndex]['title']),
      ),
      drawer: const Drawer(
        child: Text('Drawer'),
      ),
      body: _pages[_selectedPageIndex]['page'],
      bottomNavigationBar: BottomNavigationBar(
        onTap: _selectPage,
        unselectedItemColor: Colors.white,
        selectedItemColor: Colors.yellow,
        currentIndex: _selectedPageIndex,
        type: BottomNavigationBarType.shifting,
        items: [
          BottomNavigationBarItem(
            backgroundColor: Theme.of(context).primaryColor,
            icon: const Icon(Icons.category),
            label: 'Category',
          ),
          BottomNavigationBarItem(
            backgroundColor: Theme.of(context).primaryColor,
            icon: const Icon(Icons.star),
            label: 'Favorite',
          ),
        ],
      ),
    );
  }

```

## Flutter dynamic BottomNavigationBar 
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app)

```dart
import 'package:flutter/material.dart';
import '../screens/categories_screen.dart';
import '../screens/favorite_screen.dart';

class TabScreen extends StatefulWidget {
  const TabScreen({super.key});

  @override
  State<TabScreen> createState() => _TabScreenState();
}

class _TabScreenState extends State<TabScreen> {
  final List<Map<String, dynamic>> _pages = [
    {
      'page': const CategoriesScreen(),
      'title': 'Categories',
    },
    {
      'page': const FavoriteScreen(),
      'title': 'Your favorite meal',
    },
  ];
  int _selectedPageIndex = 0;
  void _selectPage(int index) {
    setState(() {
      _selectedPageIndex = index;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(_pages[_selectedPageIndex]['title']),
      ),
      body: _pages[_selectedPageIndex]['page'],
      bottomNavigationBar: BottomNavigationBar(
        onTap: _selectPage,
        unselectedItemColor: Colors.white,
        selectedItemColor: Colors.yellow,
        currentIndex: _selectedPageIndex,
        type: BottomNavigationBarType.shifting,
        items: [
          BottomNavigationBarItem(
            backgroundColor: Theme.of(context).primaryColor,
            icon: const Icon(Icons.category),
            label: 'Category',
          ),
          BottomNavigationBarItem(
            backgroundColor: Theme.of(context).primaryColor,
            icon: const Icon(Icons.star),
            label: 'Favorite',
          ),
        ],
      ),
    );
  }
}

```


## Flutter Bottom Navigation Bar with active Tab color
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app)

```dart


import 'package:flutter/material.dart';
import '../screens/categories_screen.dart';
import '../screens/favorite_screen.dart';

class TabScreen extends StatefulWidget {
  const TabScreen({super.key});

  @override
  State<TabScreen> createState() => _TabScreenState();
}

class _TabScreenState extends State<TabScreen> {
  final List<Widget> _pages = const [
    CategoriesScreen(),
    FavoriteScreen(),
  ];
  int _selectedPageIndex = 0;
  void _selectPage(int index) {
    setState(() {
      _selectedPageIndex = index;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Meals'),
      ),
      body: _pages[_selectedPageIndex],
      bottomNavigationBar: BottomNavigationBar(
        onTap: _selectPage,
        unselectedItemColor: Colors.grey,
        selectedItemColor: Theme.of(context).primaryColor,
        currentIndex: _selectedPageIndex,
        items: const [
          BottomNavigationBarItem(
            icon: Icon(
              Icons.category,
            ),
            label: 'Category',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.star),
            label: 'Favorite',
          ),
        ],
      ),
    );
  }
}



```


## Flutter Tab Screen ViewTabBar
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app)

```dart
import 'package:flutter/material.dart';
import '../screens/categories_screen.dart';
import '../screens/favorite_screen.dart';

class TabScreen extends StatefulWidget {
  const TabScreen({super.key});

  @override
  State<TabScreen> createState() => _TabScreenState();
}

class _TabScreenState extends State<TabScreen> {
  @override
  Widget build(BuildContext context) {
    return DefaultTabController(
      length: 2,
      initialIndex: 0,
      child: Scaffold(
        appBar: AppBar(
          title: const Text('Meals'),
          bottom: const TabBar(
            tabs: <Widget>[
              Tab(
                icon: Icon(Icons.category),
                text: 'Categories',
              ),
              Tab(
                icon: Icon(Icons.star),
                text: 'Favorite',
              ),
            ],
          ),
        ),
        body: const TabBarView(
          children: <Widget>[
            CategoriesScreen(),
            FavoriteScreen(),
          ],
        ),
      ),
    );
  }
}


```

## Options of registering flutter page routes/ navigation, fall back routes for unregistered or unknown routes
Full code in [flutter-meals-app](https://github.com/tradecoder/flutter-meals-app/commit/888580c12e7334090f03ebf33c5e88417b352f02#diff-e61eb31d013d12616f5532636a88cfa63631dda8f7829e5424e68542214d1608)
```dart

import 'package:flutter/material.dart';
import '../screens/meal_detail_screen.dart';
import './screens/category_meals_screen.dart';
import './screens/categories_screen.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Meals App",
      theme: ThemeData(primarySwatch: Colors.pink),
      initialRoute: '/',
      routes: {
        '/': (context) => const CategoriesScreen(),
        CategoryMealsScreen.routeName: (context) => const CategoryMealsScreen(),
        MealDetailScreen.routeName: (context) => const MealDetailScreen(),
      },
      onGenerateRoute: (settings) {
        return MaterialPageRoute(builder: (ctx) => const CategoriesScreen());
      },
      onUnknownRoute: (settings) {
        return MaterialPageRoute(builder: (ctx) => const CategoriesScreen());
      },
    );
  }
}

```




## Allow device only portrait mode- flutter
```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  SystemChrome.setPreferredOrientations([
    DeviceOrientation.portraitUp,
    DeviceOrientation.portraitDown,
  ]);
  runApp(const MyApp());
}


```


## Fix double.parse() / int.parse() problems when string contains both numbers and text, and remove leading whitespace from a text

```dart
import 'package:flutter/material.dart';

class NewTransaction extends StatefulWidget {
  final Function txnDetails;
  const NewTransaction({super.key, required this.txnDetails});

  @override
  State<NewTransaction> createState() => _NewTransactionState();
}

class _NewTransactionState extends State<NewTransaction> {
  final TextEditingController titleController = TextEditingController();
  final TextEditingController amountController = TextEditingController();

  void submitData() {
    // .trim() will remove leading whitespace if any

    final enteredTitle = titleController.text.trim();
    double? enteredAmount = 0;

    // before applying double.parse(), first check if it really has numbers
    // then remove everything from the string but keep only numbers and a dot
    if (amountController.text.contains(RegExp('[1-9]'))) {
      enteredAmount = double.parse(
          amountController.text.trim().replaceAll(RegExp('[^0-9.]'), ''));
    }

    if (!titleController.text.contains(RegExp('[a-zA-Z0-9]')) ||
        amountController.text.isEmpty ||
        enteredAmount <= 0 ||
        amountController.text.contains(RegExp('[a-zA-Z,]'))) {
      return;
    }

    widget.txnDetails(
      enteredTitle,
      enteredAmount,
    );
    Navigator.of(context).pop();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      color: null,
      child: Card(
        elevation: 5,
        child: Container(
          color: null,
          padding: const EdgeInsets.all(12.0),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.end,
            children: <Widget>[
              TextField(
                decoration: const InputDecoration(labelText: 'Title'),
                controller: titleController,
              ),
              TextField(
                decoration: const InputDecoration(labelText: 'Amount'),
                controller: amountController,
                keyboardType: TextInputType.number,
              ),
              TextButton(
                  onPressed: submitData, child: const Text('Add Transaction'))
            ],
          ),
        ),
      ),
    );
  }
}
```


## Add and open external link / hyperlink in flutter
```dart
import 'package:flutter/material.dart';
// add url_launcher and import
import 'package:url_launcher/url_launcher.dart';


//use _openLink("your_url_here") //
//for an action to open a link where required
Future<void> _openLink(myUrl) async {
  if (!await launchUrl(Uri.parse(myUrl))) {
    throw 'could not open link';
  }
}
//////////////////

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "my app",
      home: Scaffold(
          body: Center(
        child: ElevatedButton(
            onPressed: () {
              // use of url opener : _openLink()
              _openLink("https://flutter.dev/");
            },
            child: const Text('Test link opener')),
      )),
    );
  }
}
```


## Flutter App ListView builder

```dart
import 'package:flutter/material.dart';
import 'package:english_words/english_words.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter App List View",
      theme: ThemeData(primarySwatch: Colors.red),
      home: Scaffold(
          appBar: AppBar(title: const Text("Flutter App ListView")),
          body: const Center(
            child: RandomWords(),
          )),
    );
  }
}

class RandomWords extends StatefulWidget {
  const RandomWords({Key? key}) : super(key: key);

  @override
  State<RandomWords> createState() => _RandomWordsState();
}

class _RandomWordsState extends State<RandomWords> {
  final _suggestions = <WordPair>[];
  final _titleFont = const TextStyle(fontSize: 20);
  @override
  Widget build(BuildContext context) {
    // lazyloaded ListView builder in flutter
    return ListView.builder(itemBuilder: (context, index) {
      if (index.isOdd) {
        return const Divider();
      }
      final i = index ~/ 2;
      if (i >= _suggestions.length) {
        _suggestions.addAll(generateWordPairs().take(5));
      }

      return ListTile(
          title: Text(
        _suggestions[i].asSnakeCase,
        style: _titleFont,
      ));
    });
  }
}

```

## Flutter ListView - ListTile with trailing Icon

```dart
import 'package:flutter/material.dart';
import 'package:english_words/english_words.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: "Flutter ListView with Trailing Icon",
      home: ViewWordList(),
    );
  }
}

class ViewWordList extends StatefulWidget {
  const ViewWordList({Key? key}) : super(key: key);

  @override
  State<ViewWordList> createState() => _ViewWordListState();
}

class _ViewWordListState extends State<ViewWordList> {
  final _wordCollection = <WordPair>[];
  final _titleFont = const TextStyle(fontSize: 20, color: Colors.blue);
  final _saved = <WordPair>[];
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Flutter ListView with trailing Icon"),
      ),
      body: ListView.builder(itemBuilder: (context, index) {
        if (index.isOdd) {
          return const Divider();
        }
        final i = index ~/ 2;
        if (i >= _wordCollection.length) {
          _wordCollection.addAll(generateWordPairs().take(10));
        }

        final isSaved = _saved.contains(_wordCollection[i]);

        return ListTile(
          title: Text(
            _wordCollection[i].asSnakeCase,
            style: _titleFont,
          ),
          trailing: Icon(
            isSaved ? Icons.favorite : Icons.favorite_border,
            color: isSaved ? Colors.red : null,
            semanticLabel: isSaved ? "remove" : "save",
          ),
          onTap: () {
            setState(() {
              isSaved
                  ? _saved.remove(_wordCollection[i])
                  : _saved.add(_wordCollection[i]);
            });
          },
        );
      }),
    );
  }
}


```


## Flutter Menu - Open a new page

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: "Fluter menu- opening new page",
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Home page"),
        actions: [
          IconButton(
            onPressed: _newPage,
            icon: const Icon(Icons.list),
          ),
        ],
      ),
      body: Container(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text(
              "Press the Menu icon to open a new page. Also you can open the new page with this button.",
            ),
            const Divider(),
            const Padding(padding: EdgeInsets.all(10.0)),
            ElevatedButton(
              onPressed: _newPage,
              child: const Text("Also can use this button"),
            ),
          ],
        ),
      ),
    );
  }

  void _newPage() {
    Navigator.of(context).push(
      MaterialPageRoute(
        builder: (context) {
          return Scaffold(
            appBar: AppBar(
              title: const Text("New page"),
            ),
            body: const Center(
              child: Text("New page contents here"),
            ),
          );
        },
      ),
    );
  }
}

```

## View saved items in a new page in Flutter

```dart
import 'package:flutter/material.dart';
import 'package:english_words/english_words.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      title: "View saved items in a new page in Flutter",
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  final _wordCollection = <WordPair>[];
  final _savedItems = <WordPair>[];
  final _titleFont = const TextStyle(
    fontSize: 20,
    color: Colors.blue,
  );

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Home page: view all"),
        actions: [
          IconButton(
            onPressed: _viewSavedItems,
            icon: const Icon(Icons.list),
          ),
        ],
      ),
      body: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Padding(
            padding: const EdgeInsets.all(15.0),
            child: _savedItems.isNotEmpty
                ? const Text("Press menu icon to see all your saved items.")
                : const Text(
                    "You haven't any saved collection. To save an item, just tap it."),
          ),
          const Divider(),
          Expanded(
            child: ListView.builder(
              itemBuilder: (context, index) {
                if (index.isOdd) {
                  return const Divider();
                }
                final i = index ~/ 2;
                if (i >= _wordCollection.length) {
                  _wordCollection.addAll(generateWordPairs().take(10));
                }
                final isItemSaved = _savedItems.contains(_wordCollection[i]);

                return ListTile(
                  title: Text(
                    _wordCollection[i].asSnakeCase,
                    style: _titleFont,
                  ),
                  subtitle: isItemSaved
                      ? const Text("You like it")
                      : const Text("Like? Tap to save"),
                  trailing: Icon(
                    isItemSaved ? Icons.favorite : Icons.favorite_border,
                    color: isItemSaved ? Colors.red : null,
                  ),
                  onTap: () {
                    setState(() {
                      isItemSaved
                          ? _savedItems.remove(_wordCollection[i])
                          : _savedItems.add(_wordCollection[i]);
                    });
                  },
                );
              },
            ),
          )
        ],
      ),
    );
  }

  void _viewSavedItems() {
    Navigator.of(context).push(
      MaterialPageRoute<void>(
        builder: ((context) {
          final tiles = _savedItems.map((e) {
            return ListTile(
              title: Text(
                e.asSnakeCase,
                style: _titleFont,
              ),
            );
          });

          final viewSelectedItems = tiles.isNotEmpty
              ? ListTile.divideTiles(
                  context: context,
                  tiles: tiles,
                ).toList()
              : <Widget>[];
          return Scaffold(
            appBar: AppBar(
              title: const Text("Your saved items"),
            ),
            body: ListView(
              children: viewSelectedItems,
            ),
          );
        }),
      ),
    );
  }
}

```

## Full width button setup in Flutter

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

const _titleFont = TextStyle(fontSize: 20, color: Colors.blue);

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Full width button setup in Flutter",
      theme: ThemeData(
        appBarTheme: const AppBarTheme(
          backgroundColor: Colors.blue,
          foregroundColor: Colors.white,
        ),
      ),
      home: Scaffold(
          appBar: AppBar(
            title: const Text("Sign up"),
          ),
          body: Container(
            color: Colors.white54,
            padding: const EdgeInsets.all(16.0),
            child: Column(
              mainAxisAlignment: MainAxisAlignment.start,
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Container(
                  padding: null,
                  child: const Center(
                    child: Text(
                      "Please sign up",
                      style: _titleFont,
                    ),
                  ),
                ),
                const SizedBox(
                  height: 30.0,
                ),
                Container(
                  color: null,
                  child: TextFormField(
                    decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: "First name",
                    ),
                  ),
                ),
                const SizedBox(
                  height: 10,
                ),
                Container(
                  color: null,
                  child: TextFormField(
                    decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: "Last name",
                    ),
                  ),
                ),
                const SizedBox(height: 10),
                Container(
                  color: null,
                  child: TextFormField(
                    decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: "Email",
                    ),
                  ),
                ),
                const SizedBox(height: 10),
                Container(
                  color: null,
                  child: TextFormField(
                    decoration: const InputDecoration(
                      border: OutlineInputBorder(),
                      labelText: "Mobile number",
                    ),
                  ),
                ),
                const SizedBox(height: 10),
                const SizedBox(
                  height: 50,
                  //this will make the button full width        
                  width: double.infinity,
                  child: ElevatedButton(
                    onPressed: _onSubmitSignupData,
                    child: Text("Submit"),
                  ),
                ),
                const SizedBox(
                  height: 30.0,
                ),
                const Text(
                  "Have an account? Login here",
                  style: TextStyle(
                    color: Colors.blue,
                    decoration: TextDecoration.underline,
                  ),
                ),
              ],
            ),
          )),
    );
  }
}

void _onSubmitSignupData() {}

```

## Set full screen background image in Flutter

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Set full screen background image in Flutter",
      theme: ThemeData(primarySwatch: Colors.pink),
      home: const Homepage(),
    );
  }
}

class Homepage extends StatefulWidget {
  const Homepage({super.key});
  @override
  State<Homepage> createState() => _HomepageState();
}

class _HomepageState extends State<Homepage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Full screen background image"),
      ),
      body: Container(
        padding: const EdgeInsets.all(16.0),
        color: null,

        //this will set background image
        decoration: const BoxDecoration(
          image: DecorationImage(
            image: AssetImage("assets/images/bg-image-1.jpg"),
            // use BoxFit to size background image
            // BoxFit.fill / BoxFit.cover / and other options you can use
            // based on your requirement
            fit: BoxFit.fill,
          ),
        ),
        child: const Center(
            child: Text("To setup full screen background image, "
                "use DecorationImage and set required size with BoxFit")),
      ),
    );
  }
}

```

## Flutter page navigation create and clear routes
### Main app
```dart
import 'package:flutter/material.dart';
import 'home_page.dart';
import 'product_page.dart';
import 'cart_page.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Navigation",
      theme: ThemeData(primarySwatch: Colors.orange),
      initialRoute: '/',
      routes: {
        '/': (context) => const HomePage(),
        '/products': (context) => const ProductPage(),
        '/cart': (context) => const CartPage(),
      },
    );
  }
}

```
### Home page

```dart
import 'package:flutter/material.dart';

class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Home")),
      body: Center(
        child: Center(
          child: ElevatedButton(
            onPressed: () {
              Navigator.pushNamed(context, '/products');
            },
            child: const Text("See all products"),
          ),
        ),
      ),
    );
  }
}

```
### Product page

```dart
import 'package:flutter/material.dart';

class ProductPage extends StatelessWidget {
  const ProductPage({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Shop")),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            Navigator.pushNamed(context, '/cart');
          },
          child: const Text("View cart"),
        ),
      ),
    );
  }
}

```

### Cart page

```dart
import 'package:flutter/material.dart';

class CartPage extends StatelessWidget {
  const CartPage({super.key});
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text("Cart")),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // this will go back upto the given route name
            Navigator.of(context).popUntil(ModalRoute.withName('/'));

            // and this will go back to the first route
            // Navigator.popUntil(context, (route) => route.isFirst);
          },
          child: const Text("Clear all routes and back to Homepage"),
        ),
      ),
    );
  }
}
```
