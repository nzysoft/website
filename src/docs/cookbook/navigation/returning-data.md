---
title: Return data from a screen
prev:
  title: Pass arguments to a named route
  path: /docs/cookbook/navigation/navigate-with-arguments
next:
  title: Send data to a new screen
  path: /docs/cookbook/navigation/passing-data
---

In some cases, you might want to return data from a new screen.
For example, say you push a new screen that presents two options to a user.
When the user taps an option, you want to inform the first screen
of the user's selection so that it can act on that information.

You can do this with the
[`Navigator.pop()`]({{site.api}}/flutter/widgets/Navigator/pop.html)
method using the following steps:

  1. Define the home screen
  2. Add a button that launches the selection screen
  3. Show the selection screen with two buttons
  4. When a button is tapped, close the selection screen
  5. Show a snackbar on the home screen with the selection

## 1. Define the home screen

The home screen displays a button. When tapped,
it launches the selection screen.

<!-- skip -->
```dart
class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Returning Data Demo'),
      ),
      // Create the SelectionButton widget in the next step.
      body: Center(child: SelectionButton()),
    );
  }
}
```

## 2. Add a button that launches the selection screen

Now, create the SelectionButton, which does the following:

  * Launches the SelectionScreen when it's tapped.
  * Waits for the SelectionScreen to return a result.

<!-- skip -->
```dart
class SelectionButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: Text('Pick an option, any option!'),
    );
  }

  // A method that launches the SelectionScreen and awaits the
  // result from Navigator.pop.
  _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push returns a Future that completes after calling
    // Navigator.pop on the Selection Screen.
    final result = await Navigator.push(
      context,
      // Create the SelectionScreen in the next step.
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );
  }
}
```

## 3. Show the selection screen with two buttons

Now, build a selection screen that contains two buttons.
When a user taps a button,
that app closes the selection screen and lets the home
screen know which button was tapped.

This step defines the UI.
The next step adds code to return data.

```dart
class SelectionScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Pick an option'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // Pop here with "Yep"...
                },
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // Pop here with "Nope"
                },
                child: Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

## 4. When a button is tapped, close the selection screen

Now, update the `onPressed()` callback for both of the buttons.
To return data to the first screen, use the
[`Navigator.pop()`]({{site.api}}/flutter/widgets/Navigator/pop.html)
method, which accepts an optional second argument called `result`.
Any result is returned to the `Future` in the SelectionButton.

### Yep button

<!-- skip -->
```dart
RaisedButton(
  onPressed: () {
    // The Yep button returns "Yep!" as the result.
    Navigator.pop(context, 'Yep!');
  },
  child: Text('Yep!'),
);
```

### Nope button

<!-- skip -->
```dart
RaisedButton(
  onPressed: () {
    // The Nope button returns "Nope!" as the result.
    Navigator.pop(context, 'Nope!');
  },
  child: Text('Nope!'),
);
```

## 5. Show a snackbar on the home screen with the selection

Now that you're launching a selection screen and awaiting the result,
you'll want to do something with the information that's returned.

In this case, show a snackbar displaying the result by using the
`_navigateAndDisplaySelection()` method in `SelectionButton`:

<!-- skip -->
```dart
_navigateAndDisplaySelection(BuildContext context) async {
  final result = await Navigator.push(
    context,
    MaterialPageRoute(builder: (context) => SelectionScreen()),
  );

  // After the Selection Screen returns a result, hide any previous snackbars
  // and show the new result.
  Scaffold.of(context)
    ..removeCurrentSnackBar()
    ..showSnackBar(SnackBar(content: Text("$result")));
}
```

## Complete example

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    title: 'Returning Data',
    home: HomeScreen(),
  ));
}

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Returning Data Demo'),
      ),
      body: Center(child: SelectionButton()),
    );
  }
}

class SelectionButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      onPressed: () {
        _navigateAndDisplaySelection(context);
      },
      child: Text('Pick an option, any option!'),
    );
  }

  // A method that launches the SelectionScreen and awaits the result from
  // Navigator.pop.
  _navigateAndDisplaySelection(BuildContext context) async {
    // Navigator.push returns a Future that completes after calling
    // Navigator.pop on the Selection Screen.
    final result = await Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SelectionScreen()),
    );

    // After the Selection Screen returns a result, hide any previous snackbars
    // and show the new result.
    Scaffold.of(context)
      ..removeCurrentSnackBar()
      ..showSnackBar(SnackBar(content: Text("$result")));
  }
}

class SelectionScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Pick an option'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // Close the screen and return "Yep!" as the result.
                  Navigator.pop(context, 'Yep!');
                },
                child: Text('Yep!'),
              ),
            ),
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: RaisedButton(
                onPressed: () {
                  // Close the screen and return "Nope!" as the result.
                  Navigator.pop(context, 'Nope.');
                },
                child: Text('Nope.'),
              ),
            )
          ],
        ),
      ),
    );
  }
}
```

![Returning data demo](/images/cookbook/returning-data.gif){:.site-mobile-screenshot}