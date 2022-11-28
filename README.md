# Flutter Fix
Practical Flutter App development code problems and solutions

## Flutter Bottom Navigation Bar with Tabs
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
        items: const [
          BottomNavigationBarItem(
              icon: Icon(Icons.category), label: 'Category'),
          BottomNavigationBarItem(icon: Icon(Icons.star), label: 'Favorite')
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
