# PDF Viewer Flutter

A plugin/package for Flutter for Android and iOS, Web, Mac, Linux and Windows app!

[![pub package](https://img.shields.io/pub/v/pdf_viewer_flutter.svg)](https://pub.dev/packages/pdf_viewer_flutter)

### 1. Installation

Add this to your project's pubspec.yaml file:


```
dependencies:
  pdf_viewer_flutter: ^1.0.0
```

and run

```
$ flutter pub get
```

in the terminal

or

write 

``` flutter pub add pdf_viewer_flutter ```

in the terminal inside your project and hit enter

### 2. Configuration for Android release

You have to follow first these steps: https://flutter.io/docs/deployment/android
After that you have to add ndk filters to your release config:

```
    buildTypes {

        release {
            signingConfig signingConfigs.release
            minifyEnabled true
            useProguard true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'

            ndk {
                abiFilters 'armeabi-v7a'
            }
        }

        debug {
            minifyEnabled false
            useProguard false
        }
    }

```

### 3. Usage

```
import 'dart:async';
import 'dart:io';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:path_provider/path_provider.dart';
import 'package:pdf_viewer_flutter/pdf_viewer_flutter.dart';

void main() {
  runApp(MaterialApp(
    title: 'PDF Viewer for Flutter',
    home: MyApp(),
  ));
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => new _MyAppState();
}

class _MyAppState extends State<MyApp> {
  String _pefFilePath = "";

  @override
  void initState() {
    super.initState();
    createFileOfPdfUrl().then((file) {
      setState(() {
        _pefFilePath = file.path;
        print(_pefFilePath);
      });
    });
  }

  Future<File> createFileOfPdfUrl() async {
    final url =
        "https://github.com/contactlutforrahman/files/blob/main/pdf_viewer.pdf";
    final filename = url.substring(url.lastIndexOf("/") + 1);
    var request = await HttpClient().getUrl(Uri.parse(url));
    var response = await request.close();
    var bytes = await consolidateHttpClientResponseBytes(response);
    String dir = (await getApplicationDocumentsDirectory()).path;
    File file = new File('$dir/$filename');
    await file.writeAsBytes(bytes);
    return file;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('PDF Viewer for Flutter')),
      body: Center(
        child: ElevatedButton(
          child: Text("Open a PDF file"),
          onPressed: () => Navigator.push(
            context,
            MaterialPageRoute(
                builder: (context) => PDFViewerScreen(_pefFilePath)),
          ),
        ),
      ),
    );
  }
}

class PDFViewerScreen extends StatelessWidget {
  final String? pefFilePath;
  PDFViewerScreen(this.pefFilePath);

  @override
  Widget build(BuildContext context) {
    return PDFViewerScaffold(
        appBar: AppBar(
          title: Text("PDF Document"),
          actions: <Widget>[
            IconButton(
              icon: Icon(Icons.share),
              onPressed: () {},
            ),
          ],
        ),
        path: pefFilePath);
  }
}

```