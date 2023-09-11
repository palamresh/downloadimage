# downloadimage

1.page
import 'package:flutter/material.dart';

import 'home_page.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
          useMaterial3: true,
        ),
        home: HomePage());
  }
}

2.HomePage

import 'dart:io';

import 'package:flutter/material.dart';
import 'package:image_picker/image_picker.dart';
import 'package:pdf1/down.dart';

class HomePage extends StatefulWidget {
  const HomePage({super.key});

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  File? _image;
  final picker = ImagePicker();
  Future getImgae() async {
    final pickImage = await picker.pickImage(source: ImageSource.gallery);

    setState(() {
      if (pickImage != null) {
        _image = File(pickImage.path);
      } else {
        print("can't pick image");
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          getImgae();
        },
        child: Icon(Icons.camera),
      ),
      appBar: AppBar(
        actions: [
          IconButton(
              onPressed: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) => DownloadingFile()));
              },
              icon: Icon(Icons.downloading))
        ],
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text('Pick Image from Camera'),
      ),
      body: Center(
        child: _image == null
            ? const Text('NO image Selected')
            : Image.file(
                _image!.absolute,
              ),
      ),
    );
  }
}
3.download image 
import 'package:flutter/material.dart';
import 'package:flutter_file_downloader/flutter_file_downloader.dart';

class DownloadingFile extends StatefulWidget {
  const DownloadingFile({super.key});

  @override
  State<DownloadingFile> createState() => _DownloadingFileState();
}

class _DownloadingFileState extends State<DownloadingFile> {
  final urlController = TextEditingController();
  double? _progress;
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Downloding'),
      ),
      body: Column(
        children: [
          Text('Downloading Image'),
          TextField(
            controller: urlController,
            decoration: InputDecoration(hintText: 'Enter Image Url'),
          ),
          _progress != null
              ? CircularProgressIndicator()
              : ElevatedButton(
                  onPressed: () {
                    FileDownloader.downloadFile(
                      url: urlController.text.trim(),
                      onProgress: (name, progres) {
                        setState(() {
                          _progress = progres;
                        });
                      },
                      onDownloadCompleted: (path) {
                        print('path $path');
                        setState(() {
                          _progress = null;
                        });
                      },
                    );
                  },
                  child: Text("Download"))
        ],
      ),
    );
  }
}


