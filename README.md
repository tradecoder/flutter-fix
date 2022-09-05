# Flutter Fix
Practical Flutter App development code problems and solutions



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
