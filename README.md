
Android for Python Users
========================

*An unofficial Users' Guide*

Revised 2022-03-06

# Table of Contents

- [Introduction](#introduction)
- [What is Different about Android?](#what-is-different-about-android)
  * [Posix](#posix)
  * [Wheels](#wheels)
  * [Meta-information](#meta-information)
- [Android Storage](#android-storage)
  * [Private Storage](#private-storage)
  * [Shared Storage](#shared-storage)
  * [Sharing a file between apps](#sharing-a-file-between-apps)
- [Threads and Subprocesses](#threads-and-subprocesses)
- [Android Service](#android-service)
  * [Specifying a Service](#specifying-a-service)
  * [Service Lifetime](#service-lifetime)
  * [Service Performance](#service-performance)
  * [p4a Service Implementation](#p4a-service-implementation)
- [App Permissions](#app-permissions)
- [Buildozer and p4a](#buildozer-and-p4a)
  * [Install](#install)
  * [Changing buildozer.spec](#changing-buildozerspec)
  * [Some buildozer.spec options](#some-buildozerspec-options)
    + [package.domain](#packagedomain)
    + [requirements](#requirements)
      - [requirements basics](#requirements-basics)
      - [Simple requirements examples](#simple-requirements-examples)
      - [More complex requirements](#more-complex-requirements)
    + [source.include_exts](#sourceinclude-exts)
    + [android.permissions](#androidpermissions)
    + [android.api](#androidapi)
    + [android.minapi](#androidminapi)
    + [android.ndk](#androidndk)
    + [android.arch](#androidarch)
- [Debugging](#debugging)
- [Android Hardware](#android-hardware)
  * [Camera](#camera)
  * [Keyboard](#keyboard)
  * [Back Button and Gesture](#back-button-and-gesture)
- [Android Packages](#android-packages)
  * [The Android package](#the-android-package)
  * [Plyer](#plyer)
  * [Pyjnius](#pyjnius)
    + [Basic Pyjnius Usage](#basic-pyjnius-usage)
    + [Pyjnius Performance](#pyjnius-performance)
    + [Java Abstract Classes](#java-abstract-classes)
    + [Pyjnius Memory Management](#pyjnius-memory-management)
    + [Java Api Versions](#java-api-versions)
    + [Calling Python from Java](#calling-python-from-java)
  * [Android Notifications](#android-notifications)
- [Kivy Related Topics](#kivy-related-topics)
  * [Layout](#layout)
  * [KivyMD](#kivymd)
  * [Kivy Lifecycle](#kivy-lifecycle)
  * [Kivy Garden](#kivy-garden)
- [Cryptic Error Messages](#cryptic-error-messages)
  * [No module named 'msvcrt'](#no-module-named-msvcrt)
  * [Aidl not found](#aidl-not-found)
  * [64-bit instead of 32-bit](#64-bit-instead-of-32-bit)
  * [No module named '_Socket'](#no-module-named-_socket)
  * [weakly-referenced object](#weakly-referenced-object)
  * [OpenCV requires Android SDK Tools](#opencv-requires-android-sdk-tools)
  * [No such file or directory: 'ffmpeg'](#no-such-file-or-directory-ffmpeg)
- [Resources](#resources)
  * [Read the Fine Manual](#read-the-fine-manual)
  * [Android for Python](#android-for-python)
  * [KivAds](#kivads)
  * [Other Resources](#other-resources)
- [Release Builds](#release-builds)
- [Appendix A : Using adb](#appendix-a--using-adb)
- [Appendix B : Using an emulator](#appendix-b--using-an-emulator)
- [Appendix C : Locally modifying a recipe](#appendix-c--locally-modifying-a-recipe)
- [Appendix D : Debugging on WSL](#appendix-d--debugging-on-wsl)
- [Appendix E : Copying from private storage](#appendix-e--copying-from-private-storage)
- [Appendix F : Install Bundletool](#appendix-f--install-bundletool)
- [Appendix G : Modifying p4a](#appendix-g--modifying-p4a)

# Introduction

Python and Kivy are portable across operating systems because of POSIX, Python wheels, and pip. However Android is not POSIX compliant, wheels are not usually available for Android, and pip is not installed on Android. Clearly many apps won't 'just work' on Android. The document is about porting a Kivy app to Android.

For a well written app that only paints the screen, and does nothing else, building for Android will be 'push button'. One can for example build 'Hello World' with the default 'buildozer.spec' file. Of course this does not describe many apps.

This document is not a substitute for Reading The Fine Manual [(RTFM)](https://en.wikipedia.org/wiki/RTFM). Your basic resources are [Buildozer](https://github.com/kivy/buildozer/tree/master/docs/source), [Python for Android](https://github.com/kivy/python-for-android/tree/develop/doc/source), and [Android](https://developer.android.com/guide).

We assume android.api >= 29

Python-for-Android is a truly amazing achievement, but there are some details to understand. The alternative is Java and the Android api, which has been shown to induce insanity in laboratory mice. 

Lastly this document is my understanding as of the date above. I am not a developer in any of the Kivy sub projects (except for one PR in p4a). The document is guaranteed to be incomplete is some way, and may possibly be wrong in another way. But reading it will hopefully provide you with come context with which to read the official documentation and to experiment. Those two steps are the key to insight and understanding, which in turn is key to making your idea real.

# What is Different about Android?

## Posix

Android is not POSIX compliant (POSIX is so deep in your assumptions about computers you probably don't know it exists).
   
The file system model is different, the app cannot use any directory in the file system. The app has specific private storage directories that support file operations. The app also has storage shared between apps, this is implemented as a database.

Threads are available like the desktop. Subprocess is available, but the excutable called by subprocess on the desktop almost certainly is not available.

Multi-tasking, on the desktop when an app loses focus or is minimized it continues to execute - it just doesnt see UI events. Android is not multi-tasking, when an app is removed from the UI it *pauses*, and then does not execute any app code. Execution without a UI requires an Android service.

Apps have a lifecycle, keep to the [Kivy Lifcycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle) so that the app keeps to the [Android Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle#alc). Android api calls made outside of the Kivy App class or its children may cause non-deterministic behavior or crashes.

## Wheels

Some Python packages are not written in Python (are not pure-Python), they contain code that must be compiled. Pip provides pre-compiled packages for desktop OSes, but not for Android. P4a addresses this with [recipes](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes), but not all impure packages are available. AVOID DISAPPOINTMENT, check availability first. [See also.](#requirements)

## Meta-information

Unlike the desktop you must provide information *about* your Python code, this requirement causes everybody who doesn't understand it to crash and burn. This information must be supplied in the buildozer.spec file. It includes *all* the pip packages your app depends on, any file types your app depends on for data, and the Android permissions your app depends on. Meta-information may not be limited to that short list, but that list is critical.

# Android Storage

Android's view of its file system has changed a few times over the years. The result is a messy pile of jargon: local, system, internal, external, primary, secondary, scoped, all describe views of storage.

However in the storage model described here storage is either *Private Storage* or *Shared Storage*. Private storage content is only visible to the app that created it, shared storage is visible to all apps. This model is valid if the build api>=29.

Physically storage may be 'internal' or 'external', this is a somewhat historical view where 'external' represents the (on older devices possibly removable) sdcard. The apis referenced here all default to 'external', with 'internal' as an option.

## Private Storage

An app can perform Python file operations (read, write, shutil) on its private storage. Files in Private Storage are persistent over the life of the app, they are retained when the app is updated, and deleted when the app is uninstalled.

The location of an app's private storage is given by for example `app_storage_path()` imported as `from android.storage import app_storage_path`

The install directory './' is also private storage, but files do not persist between installs and updates. Only use this directory for reading data files packaged in the apk. Files saved to './' do persist between installs.

Do not confuse private with secure, on older Android versions it is possible for other apps to read private storage. In this case internal storage is more secure than external storage.

No permissions are requires to read or write an app's private storage. [More on permissions below](#app-permissions)

## Shared Storage

Shared storage is visible to all apps, is persistent after an app is uninstalled. On devices running Android 10 and later shared storage is implemented as a database, you cannot access shared storage with POSIX file operations. The Kivy and KivyMD file choosers do not work, you must use the Android file picker.

The nearby [storage example](https://github.com/Android-for-Python/Storage-Example) demonstrates the database insert(), delete(), and retrieve() operations.

Shared storage is organized based on root directories located in Android's 'Main Storage'. They are 'Music', 'Movies', 'Pictures', 'Documents', and 'Downloads'.

On devices running Android 10 and later no permissions are requires to read or write an app's own shared storage. Reading another app's shared storage requires READ_EXTERNAL_STORAGE permission.

On devices running Android 9 and less, for shared storage, both file access and database access are available, though the database implementation is perhaps too basic. The best choice depends on the application. The database approach enables a file share with other apps (see next section), the file system approach provides the user with the familiar hierarchy, and requires WRITE_EXTERNAL_STORAGE permission.

## Sharing a file between apps

Files in Shared Storage can be shared between apps. Either using the file picker, or the Share mechanism. See the examples: [Sending a Share](https://github.com/Android-for-Python/Share-Send-Example) and [Receiving a Share](https://github.com/Android-for-Python/Share-Receive-Example).

# Threads and Subprocesses

Threads are available. Kivy executes on the 'UI thread', Android requires that this thread is always responsive to UI events. As a consequence long latency operations (e.g. network access, sleep()) or computationally expensive operations must be performed in their own Python threads. Threads must be truly asynchronous to the UI thread, so do not join() in the UI thread. A non-UI thread may not write to a UI widget. [See this basics example](https://gist.github.com/el3/3c8d4e127d41e86ca3f2eae94c25c15f). A very thread safe way to return results to the UI thread is to use Clock_schedule_once().

The Python subprocess depends on having an ARM executable to run, this does not exist unless you build it and then `chmod 744`. The exception is system commands which of course are compiled for ARM; the executables for `ls`, `ps`, etc. are in Android's `/system/bin`. System commands may not have the same features as a Bash shell, and run from `subprocess.Popen` have app only permission. 

There is no `python3` executable, Python's `sys.executable` is empty. To run a Python script in a new process, we use an Android Service.

# Android Service

An [Android Service](https://developer.android.com/guide/components/services) is somewhat equivalent to a Python subprocess, in that it can perform operations in the background. An Android service has asynchronous execution and an independent memory space, and may execute on a different core. In the Python context we create an Android service that can emulate a Python subprocess, this emulation does not have the lifetime of a true Android service.

There are two Kivy examples [Kivy Service Osc](https://github.com/tshirtman/kivy_service_osc), and [Mutli-Service Example](https://github.com/Android-for-Python/Multi-Service-Example). OSC is a good package for message passing between app and service. However it is not designed for passing large datas, consider using the file system in this case. OSC requires INTERNET permission.

## Specifying a Service 

If the context of Kivy, an Android service is a python script. The script has a file name and we give the service some name; these are declared in `buildozer.spec` (here `the_service.py` is the name of the script, and `Worker` is the name we give the service *which must be a valid Java class name*). 
```
# (list) List of service to declare
services = Worker:the_service.py
```

We start the service from an app using the service name, with a standard prefix:
```
from android import mActivity
context =  mActivity.getApplicationContext()
SERVICE_NAME = str(context.getPackageName()) + '.Service' + 'Worker'
self.service = autoclass(SERVICE_NAME)
self.service.start(mActivity,'')
```

`SERVICE_NAME` is the name of a Java class implementing the service, which in turn executes the `the_service.py`.

A [foreground service](https://developer.android.com/guide/components/foreground-services) is specified in buildozer.spec with: 
```
# (list) List of service to declare
services = Worker:the_service.py:foreground

# (list) Permissions
android.permissions = FOREGROUND_SERVICE
```
A notification icon will be created in the task bar. 

## Service Lifetime

The lifetime of the service's Python script is usually determined by an infinite loop, if this is the case the lifetime of the service is determined by the lifetime of the app. A service started by a Kivy app executes while the app is either in the foreground or paused, and like a Python subprocess stops when the app stops. An app stops when it is removed from the list of currently started apps. This lifetime is different from the lifetime of a Java service which is persistent.

A service can be killed at any time by Android if it requires the resources. Generally a background service is more likely to be killed than a foreground service. Extend the lifetime using [`setAutoRestartService()`](https://github.com/kivy/python-for-android/blob/develop/doc/source/services.rst#service-auto-restart) which forces a service to restart when it stops (set to False when comanding the service to stop). 

## Service Performance

Like a desktop subprocess an Android Service can execute on a different core to the UI that started it, so the service can improve performance of computational tasks.

## p4a Service Implementation

If you want to understand the implementation of services in more detail, read the code: [PythonService.java](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/src/main/java/org/kivy/android/PythonService.java), [Service.tmpl.java](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/templates/Service.tmpl.java), and [build.py](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/build.py).

# App Permissions

Android restricts access to many features. An app must declare the permissions it requires. There are two different declarations, manifest and user. User permissions are a subset of manifest permissions.

Manifest permissions are declared in the buildozer.spec file. Common examples are  CAMERA, INTERNET, READ_EXTERNAL_STORAGE, RECORD_AUDIO. The [full list is](https://developer.android.com/reference/android/Manifest.permission). Apps that scan Bluetooth or scan Wifi may require multiple permissions. In general you must research the permissions needed, resist the temptation to blindly guess.

Python for Android always enables manifest WRITE_EXTERNAL_STORAGE permission. WRITE_EXTERNAL_STORAGE implies READ_EXTERNAL_STORAGE. [WRITE_EXTERNAL_STORAGE is never required](https://developer.android.com/training/data-storage#permissions) for devices running api >= 30.

Any app manifest permission documented in that list as having "Protection level: dangerous" additionally require a user permission. The four listed above are all "dangerous". User permissions generate the dialog that Android apps present to the user, this is initiated with a `request_permissions()` call.

In a Kivy App `request_permissions()` may be called from the `build()` method, there can **only be one** such call in the `build()` method. If there is more than one call, no permission request will be made. `request_permissions()` **must not** be called from the `on_start()` method as this will break the Kivy Lifecycle; there will be an `on_resume()` without a prior `on_pause()`. `request_permissions()` may be called after the `on_start()` timestep has completed, but only once per timestep.

See any of the nearby examples.

# Buildozer and p4a

## Install

Buildozer runs on Linux, Windows users need a Linux virtual machine such as WSL, Colab, or VirtualBox to run Buildozer. Buildozer also runs on a Mac, but the number of users is small and this is reflected in the availability of help and in software maturity.

[Install documentation](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst), RTFM, really.

That said, there has not been a release of Buildozer for a long time, sooner or later you are going to have to move to the master version, do it now:

```
pip3 uninstall buildozer
pip3 install git+https://github.com/kivy/buildozer.git
```
A side effect of this is you will have to specify the `develop` version of p4a. After typing `buildozer init`, in `buildozer.spec` set: 
```
p4a.branch = develop
```

**Test your Buildozer install by building (`buildozer android debug`) and running [Hello World](https://kivy.org/doc/stable/guide/basic.html#create-an-application) with the default `buildozer.spec` (create this with `buildozer init`). I know you just want to see your app run on Android, but this simple first step will provide you will a framework to address any future issues.**

Errors during a Buildozer build are usually because the user:

* Failed to [read the install instructions](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst), and so failed to install a dependency.

* Failed to accept the Google License Agreements.

* Attempted to build an impure Python package.

Buildozer's behavior can be non-deterministic in any of these cases:

* It is run as root

* It is run on an NTFS partition mounted on a Linux system.

* There are Python style trailing comments in the buildozer.spec

## Changing buildozer.spec

Note that Buildozer allows *specification* of build files, versions, and options; but unlike most other build tools it *does not do version management*. If buildozer.spec is changed the change probably *won't* propagate into the apk on the next build. After changing the buildozer.spec file users *must* do an appclean.
```
buildozer appclean
buildozer android debug
```
There may be some exceptions to this, the only one I know to be safe is one can add (but not change version of, or remove) a package in the [requirements](#requirements) list without the appclean.

There is no magic universal buildozer.spec, its configuration depends on the functionality of your app. 

## Some buildozer.spec options

[RTFM](https://github.com/kivy/buildozer/blob/master/docs/source/specifications.rst), really. And see the [KivyMD section](#kivymd).

### package.domain

This must contain exctly one period (.) surrounded by alpha numeric characters, do not use any other special characters. Anthing else will cause a Gradle fail.

### requirements

#### requirements basics

This is basically the list of pip packages and the Python version that your app depends on. On the desktop this is handled for you by `pip3 install`, at the cost of disk space. On Android there is no `pip3` so you have to do it by hand.

Generally you can find what packages a package depends on by looking on GitHub at the package's `requirements.txt` file (if it exists). These packages must be added to requirements in buildozer.spec.   

It is important that you understand what your app depends on. The requirements list must be complete, missing one item is the most common cause of a run time crash on Android. The next most common cause is adding a package that is not pure Python or does not have a recipe.

Do not add Python system modules, only packages you might install with pip3 on the desktop. Or for some recipes, other recipies whose name begins with 'lib'.

There are some pip3 packages that are added automatically, no need to put these in requirements: `libffi, openssl, sqlite3, setuptools, six, pyjnius, android, certifi`.

The packages you add here **must be pure Python, or have a recipe** [in this list](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes). If this is not the case, the options are to:

* Rewrite the app

* Locally modify an existing recipe [see Appendix C](#appendix-c--locally-modifying-a-recipe).

* [Create a new recipe](https://github.com/kivy/python-for-android/blob/develop/doc/source/recipes.rst).

* Import the functionality from Java.

None of these options are trivial. That is why it said AVOID DISAPPOINTMENT in [the Wheels section above](#wheels).

If you have a problem run the [debugger](#debugging).

#### Simple requirements examples

Some packages have dependencies but no requirements.txt file, the only way to resolve these is with the debugger. One example:

`import requests` needs `requirements = python3,kivy,requests,urllib3,chardet,idna`

Some Kivy widgets depend on other packages. For example:

`from kivy.uix.videoplayer import VideoPlayer` needs `requirements = python3,kivy,ffpyplayer`

`from kivy.core.audio import SoundLoader`  needs `requirements = python3,kivy,ffpyplayer,ffpyplayer_codecs` [to play .mp3](https://github.com/Sahil-pixel/kivy-with-mp3-on-android-).

Some pip3 package names are not the same as the class name. For example:

`from bs4 import BeautifulSoup` needs `requirements = python3,kivy,beautifulsoup4`

Some recipe names are not the same as the class name. For example:

`import google.protobuf` needs `requirements = python3,kivy,protobuf_cpp`

#### More complex requirements

Some imports have more than one of the above cases. To determine a package's dependencies look in requirements.txt recursively. For example for `pyrebase` start with [requirements.txt](https://github.com/thisbejim/Pyrebase/blob/master/requirements.txt) to see the dependencies, there are six. The first is `requests`, this one is easy because its dependencies are listed earlier in this section. For the others recur. If you miss one it will show up as a `ModuleNotFoundError` at run time. It is not hard, just stop whining and do the work. For example for `pyrebase` we get:

`import pyrebase` needs `requirements = python3, kivy, pyrebase, requests, urllib3, chardet, idna, gcloud, oauth2client, requests-toolbelt, protobuf_cpp, python-jwt, pycryptodome, httplib2, pyparsing, pyasn1, pyasn1_modules, rsa, jwcrypto, cryptography, deprecated, wrapt`

Anybody got any more examples I could add here?

### source.include_exts

If the app contains any data files, for example icons. Then add the file extension to this list:
```
source.include_exts = py,png,jpg,kv,atlas
```

### android.permissions

Research the Android permissions your app needs. For example
```
android.permissions = INTERNET, CAMERA, READ_EXTERNAL_STORAGE
```

### android.api

The current buildozer default is 27, but should be "as high as possible". 
```
android.api = 31
```

### android.minapi

Python for Android enables `android.minapi = 21`.

### android.ndk

Probably best not to change this from the current 19c. But if there is some reason you really need to, 21d also appears to work.

### android.arch

Currently defaults to 32-bit ARM. For performance improvement, if you have a 64 bit device, change this to:
```
android.arch = arm64-v8a
```
An install message INSTALL_FAILED_NO_MATCHING_ABIS means the apk was built for a different architecture than the phone or emulator.

# Debugging

On the desktop your friends are the Python stack trace, and logging or print statements. It is no different on Android. To get these we [run the debugger](https://kivy.org/doc/stable/guide/android.html#debugging-your-application-on-the-android-platform).

First connect the device via USB, on the Android device enable 'Developer Mode' and 'USB debugging'.

If Buildozer was run on a virtual machine such as WSL then it may not be able to use the the physical USB port and the 'deploy run logcat' options will not work. [In this case use adb instead.](#appendix-a--using-adb)

Successful setup is indicated by log output similar to:
```
List of devices attached
0A052FDE40019P  device
```
If 'List of devices attached' is followed by an empty line then the connection failed, regardless of what the Buildozer log says afterwards. Because either the device debug options are not set, or the debugger is run from a virtual machine that can't see a physical USB port.

In the logcat output look for 'Traceback', what follows is a Python stack trace, which usually indicates the cause of the issue. For example:
```
ModuleNotFoundError: No module named 'some-import-name'
```
Where 'some-import-name' is in 'some-pip-package-name', the error occurs because 'some-pip-package-name' is missing from [buildozer.spec requirements](#requirements). To get 'some-pip-package-name' look at the import statement in the Python code, it will typically be similar to `from some-pip-package-name import some-import-name`. Get the file name and line number of the import statement from the traceback.

If you don't see an error message with the Python only messages, there is probably an error message from Android. If using Buildozer's 'deploy run logcat', comment the Python only filter:
```
# (str) Android logcat filters to use
#android.logcat_filters = *:S python:D
```

It is possible to [debug using an emulator](#appendix-b--using-an-emulator) but this is not recomended initially, as it adds unknowns to the debug process. The emulator is useful for checking a debugged app on various devices and Android versions.

# Android Hardware

## Camera

It is hard to get the Kivy Camera widget to work on Android, [for example](https://github.com/kivy/kivy/issues?q=is%3Aissue+is%3Aopen+camera). There are several 3rd party alternatives. Look at the [Preview widget in Camera4Kivy](https://github.com/Android-for-Python/Camera4Kivy#camera4kivy), and try its [examples](https://github.com/Android-for-Python/Camera4Kivy#tested-examples-and-platforms).

Also try the [Xcamera widget](https://github.com/kivy-garden/xcamera) from the Kivy Garden, or [kivy-anderoid-camera](https://github.com/alecvn/kivy-android-camera). For OpenCV try [Kivy-Android-Camera](https://github.com/tibssy/Kivy-Android-Camera). Also there is [Color blind camera](https://github.com/inclement/colour-blind-camera) and [zbarcam](https://github.com/kivy-garden/zbarcam). 

## Keyboard

The relationship between the Android keyboard and the layout is somewhat configurable with [softinput_mode](https://kivy.org/doc/stable/api-kivy.core.window.html#kivy.core.window.WindowBase.softinput_mode), consider `Window.softinput_mode = 'below_target'`.

The type of keyboard can be set with `input_type` for example `TextInput(input_type = 'tel')`. Accepted values are 'text', 'number', 'url', 'mail', 'datetime', 'tel', or 'address'. 

## Back Button and Gesture

A back button/gesture can be detected with a test for key == 27, as shown in the [documentation](https://github.com/kivy/python-for-android/blob/develop/doc/source/apis.rst#handling-the-back-button). The key handler **must** return a boolean.

Android 10 and up require that the back button/gesture can return the app to the Android home screen, therefore there must be a state in the app where back button/gesture is not consumed by the app. For example if the back button/gesture transitions between Kivy screens, from the "main" screen the app can on a back event go to the Android home screen:
```
class Main(ScreenManager):
    def __init__(self, **kwargs): 
        super().__init__(**kwargs)
        Window.bind(on_keyboard = self.keyboard)
        
    def keyboard(self,window,key,*args):
        if key == 27 and self.sm.current != "main":
            self.current = some_previous_screen
            return True   # key event consumed by app
        else:           
            return False  # key event passed to Android
```

# Android Packages

## The Android package

P4a provides Android specific utilities in the android package, this is only available on Android. It is as they say 'self documenting', which really means there isn't any documentation. [Read the code](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes/android/src/android).

## Plyer

Plyer is an OS independent api for some non-POSIX OS features. See [available features](https://github.com/kivy/plyer#supported-apis).

The [Plyer examples](https://github.com/kivy/plyer/tree/master/examples) are the documentation. Some Plyer examples work on Android but not all Android versions, for example Camera, Speech to text, Storage.

The following Plyer modules require Manifest and run time [permissions](#app-permissions):

Module | Permissions
-------|------------
audio | RECORD_AUDIO
battery | BATTERY_STATS
brightness | WRITE_SETTINGS
call | CALL_PHONE 
flash | CAMERA, FLASHLIGHT
gps | ACCESS_COARSE_LOCATION, ACCESS_FINE_LOCATION
sms | SEND_SMS
stt | RECORD_AUDIO
vibrator | VIBRATE

If you plan to use Plyer, and the idea of it is very appealing, first try a small test case on your target Android versions. If it does what you expect, check that all the features you need are available; as Plyer has a platform lowest common denominator design.


## Pyjnius

### Basic Pyjnius Usage

[Pyjnus](https://github.com/kivy/pyjnius/tree/master/docs/source) allows import of Java code into Python code. It is an interface to the Java api and the Android api. The Android api is only available on Android devices, Android api calls must be debugged on Android.

```python
from jnius import autoclass
# declare a Java Class
DownloadManager = autoclass('android.app.DownloadManager')
# Java sub classes are delimited by a '$' in place of a '.'
DownloadManagerRequest = autoclass('android.app.DownloadManager$Request')  

   # get a class constant
   visibility = DownloadManagerRequest.VISIBILITY_VISIBLE_NOTIFY_COMPLETED
   # instance a Java class
   self.request = DownloadManagerRequest(uri)
   # call a method in that class
   self.request.setNotificationVisibility(visibility)
```

Then use this to write code with Python syntax and semantics, and Java class semantics added. Some basic knowledge of Java semantics is required, get over it. Android classes will require (possibly extensive) reading of the [Android Developer Guide](https://developer.android.com/guide) and [Android Reference](https://developer.android.com/reference).

In addition `android.mActivity` gives access to some Android state that may be used by the Android API. For example:

```
from android import mActivity

    mActivity.getWindowManager()
    mActivity.getApplicationContext()
    mActivity.getSystemService( <service type> )
    mActivity.getContentResolver() 
```

It is also possible to write Java class implementations in Python using `PythonJavaClass`, [RTFM](https://github.com/kivy/pyjnius/blob/master/docs/source/api.rst#java-class-implementation-in-python) and [look at some examples](https://github.com/Android-for-Python/CameraXF-Example/blob/main/cameraxf/listeners.py). You will need to understand [Java signature format](https://github.com/kivy/pyjnius/blob/master/docs/source/api.rst#java-signature-format).

Note: some documentation examples are obsolete. If you see '.renpy.' as a sub field in an autoclass argument replace it with '.kivy.'.

### Pyjnius Performance

The `autoclass()` method add significant latency during the app start. If the app contains more than perhaps a dozen `autoclass()` calls you will probably notice the extra app startup time.

The way to address this is to create a Java file that references the Java classes that were referenced with autoclass(). Then reference your Java class with `autoclass()`. A Java file is included in the project using Buildozer's `android.add_src`. If your Java file is located at `<project>/src/org/me/myproject/my.java` then use `android.add_src = src` .

### Java Abstract Classes

It is not possible to import Java `abstract` classes or methods, as they have no `implementation` (abstract and implementation are Java keywords). And it it is not possible to provide the implementation in Python. You must write the implementation in Java and import that new class.

### Pyjnius Memory Management

You will be using two garbage collectors working on the same heap, but they don't know each other's boundaries. Python may free a local reference to a Java object because it cant see that the object is used. Obviously this will cause the app to crash in an ugly way. So use class variables, as shown below, to indicate persistence to the Python garbage collector.

```
    ###### DONT DO THIS ####
    def foobar(self):
        # instance a Java class
        request = DownloadManagerRequest(uri)
        # call a method in that class
        request.setNotificationVisibility(visibility)    
```
```
    ###### DO THIS ####
    def foobar(self):
        # instance a Java class
        self.request = DownloadManagerRequest(uri)
        # call a method in that class
        self.request.setNotificationVisibility(visibility)
        # Tell the Python garbage collector this will not be reused
        self.request = None
```

### Java Api Versions

Python for Android builds an apk with a minimum device api. Importing Java modules can invalidate this minimum. Check the [Added in API level field](https://developer.android.com/reference/android/provider/MediaStore.Downloads) in the class or method reference documentation.

### Calling Python from Java

The following illustrates calling a Python method ( here called `from_java()`) from Java. In the example there is one argument which is a string. Java sees the method as having a different name, in this case `callback_string()`. 

The technique relies on passing the method inside a wrapper class. The `interface` of the wrapper class is defined in Java. And the `implementation` of this wrapper class is defined in Python. The wrapper class is initialized with the Python method to be called. Java calls a method inside an the same instance of the same wrapper class. 

You are going to have to write some Java, get over it. To comprehend the following code fragments; read from top to bottom, then follow the path of the string from bottom to top.

In Python:

```
from jnius import autoclass, PythonJavaClass, java_method
SomeJavaClass = autoclass('org.wherever.whatever.SomeJavaClass')

class SomewhereInMyApp(somewidget):

         # instantiate the wrapper
         self.callback_instance = CallbackWrapper(self.from_java)

         # pass the class instance to Java
         SomeJavaClass(self.callback_instance)

     # the method to be called
     def from_java(self, filepath):
         print(filepath)   # prints "Greetings Earthlings"


# The wrapper implementation
class CallbackWrapper(PythonJavaClass):
    __javacontext__ = 'app'
    __javainterfaces__ = ['org/wherever/whatever/CallbackWrapper']

    def __init__(self, callback):
        super().__init__()
        self.callback = callback

    @java_method('(Ljava/lang/String;)V')        
    def callback_string(self, filepath):
        if self.callback:
            self.callback(filepath)
```

CallbackWrapper.java

```
package org.wherever.whatever;
// the wrapper interface
public interface CallbackWrapper {
    public void callback_string(String filepath);
}
```

The Java method generating the callback will be called from Java. The same Java need to reference the `CallbackWrapper` instance. Depending on the overall implementation you may need a Java wrapper to call the method and catch the result. Extra Java wrapper or not, call the Python like this:

SomeJavaClass.java

```
import org.wherever.whatever.CallbackWrapper;

class SomeJavaClass() {

   CallbackWrapper callbackClass;
   
   public SomeJavaClass(CallbackWrapper wrapper) {
       // self.callback_instance passed from Python
       CallbackWrapper callbackClass = wrapper;
   }

   // This would be the result of some Java callback, and not a static string.
   String javaCallBackResult = "Greetings Earthlings";

   // At last, the actual Java callback
   callbackClass.callback_string(javaCallBackResult);

}
```
Place the Java files in `<project>/src/org/wherever/whatever/` and in `buildozer.spec` set `android.add_src = src` .

## Android Notifications

[Android-Notification-in-Python](https://github.com/Guhan-SenSam/Android-Notification-in-Python)

# Kivy Related Topics

## Layout

A portable layout is not an Android specific issue. In general for Kivy apps use [density-independent pixels](https://kivy.org/doc/stable/api-kivy.metrics.html) 'dp' to specify an absolute size, and scale-independent pixels 'sp' to specify a font.

## KivyMD

The KivyMD widgets have the look and feel that Android users expect, but the Material Design rules mean you don't have the same flexibility as Kivy widgets.

KivyMD is in development, which means some functionality [is still changing](https://kivymd.readthedocs.io/en/latest/changelog/index.html). Next time KivyMD is downloaded the version number may be the same, but some widget may be different!

[How to use KivyMD with Buildozer](https://github.com/kivymd/KivyMD/blob/master/README.md#how-to-use-with-buildozer). There may be additional Buildozer settings required for KivyMD, see KivyMD's sample [buildozer.spec](https://github.com/kivymd/KivyMD/blob/master/demos/kitchen_sink/buildozer.spec).

## Kivy Lifecycle

Follow the [Kivy Lifecycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle), it abstracts the app behavior that Android expects.

Do not place code in the app that interacts with Android 'script style', to be executed before the Kivy build() call.

`request_permissions()` must only be called from the App's `build()` method, and only one once with an argument that is a list of all required permissions.

The App's `on_stop()` method is not always called, use `on_pause()` to save state.

## Kivy Garden

[Kivy Garden](https://github.com/kivy-garden/) is a library of components ('flowers'). It is mostly not maintained. Anybody who has had a garden knows a garden needs a gardener, Kivy Garden doesn't have one. Set your expectations accordingly.

For flowers that are maintained add them to your buildozer.spec like this:
`requirements = python3, kivy, kivy_garden.xcamera`. For flowers that are not maintained copy the code to your project and edit so that it builds.

There is a `#garden_requirements =` field in older buildozer.spec files. This is depreciated in the Buildozer 'master' and should not be used with any version of Buildozer.

# Cryptic Error Messages

## No module named 'msvcrt'

`No module named 'msvcrt'` and `No module named '_posixsubprocess'`

Newer versions of setuptools break the p4a build tools.

`pip3 list | grep setuptools` will show a version >= 60

The workaround requires that you are using a venv.

```
pip3 uninstall setuptools
pip3 install setuptools==58.0.0
```

And `buildozer appclean`.

## Aidl not found

`# Aidl not found, please install it.`

Aidl is part of the Google tools. To get the tools you have to accept the Google license agreements.

Delete `~/.buildozer` , then `buildozer android debug` and accept the license agreements.

## 64-bit instead of 32-bit

`ImportError: dlopen failed: "/data/data/org.test.myapp/files/app/_python_bundle/site-packages/cv2/cv2.so" is 64-bit instead of 32-bit`

The build process included the wrong binary file.

You can check this by changing arch from `armeabi-v7a` to `arm64-v8a`, `buildozer appclean`, and rebuild you will get a different error. This will say failed to link an x86_64 object, which is a more informative message.

The cause is an x86_64 binary from PyPl was used; because there is a requirements specification error in buildozer.spec

One of your requirements needs a recipe (or needs to be specified differently).

In this case the requirements for OpenCV is incorrectly specified, it should be `opencv` because that is the recipe name.

## No module named '_Socket'

`Module not found error: No module named '_Socket'`

Occurs with some VMs (just Colab?) in response to `buildozer android deploy run`

Because in most cases you can't `deploy run` from a VM because the VM can't see the physical USB port.

Copy the `.apk` to the desktop, and [use adb](#appendix-a--using-adb).

## weakly-referenced object

`ReferenceError: weakly-referenced object no longer exists`

This almost always due to a known issue in `kivy==1.11.1` with `Python >= 3.8`

The fix for this case is to [use the current Kivy](#requirements).

In any other case this issue can be hard to find. The programmer is assuming an object can be reused, and the garbage collector is assuming it will not be reused. One way to create this error is to assume some third party api is not stateful, when infact it does have state. 

## OpenCV requires Android SDK Tools

`Android SDK Tools: OpenCV requires Android SDK Tools revision 14 or newer.`

Yes, but OpenCV also requires Android SDK Tools revision **30 or older** (I assume it is 30). Buildozer currently uses 31 and this revision does not contain the tools that the OpenCV build expects. Hence the misleading error message, which should be `OpenCV requires Android SDK Tools revision 30 or older.`. 

It is possible to address this with a patch to `~/.buildozer` to include the older tools as described in [this thread](https://github.com/kivy/buildozer/issues/1144).

## No such file or directory: 'ffmpeg'

Occurs when the app is using subprocess() to run ffmpeg. 

There is no ffmpeg executable. You have to build it for ARM. The recipe builds a library, not an executable. After building the executable, copy it to the working directory. Android does not allow installing to the usual desktop directories.

# Resources

## Read the Fine Manual

Really.

[Buildozer](https://github.com/kivy/buildozer/tree/master/docs/source)

[Python for Android](https://github.com/kivy/python-for-android/tree/develop/doc/source)

[Pyjnius](https://github.com/kivy/pyjnius/tree/master/docs/source)

Don't rely on other sources, they can be obsolete.

## Android for Python

[Android for Python Examples](https://github.com/Android-for-Python/INDEX-of-Examples) contains examples of some Android features as used from Python. These examples only run on Android.

## KivAds
[KivAds](https://kivads.readthedocs.io/en/latest/index.html) is a Library that allows you to monetize your Kivy Apps using Google Admob. To use KivAds [minor modifications to p4a](https://github.com/Guhan-SenSam/KivAds/tree/v1.0.0#requirements) are currently required. 

## Other Resources

There are **a lot of useful features** to be found at these links:

[https://github.com/Sahil-pixel/Pykivdroid](https://github.com/Sahil-pixel/Pykivdroid)

[https://github.com/yunus-ceyhan](https://github.com/yunus-ceyhan)

[https://github.com/adywizard](https://github.com/adywizard)

[https://github.com/tshirtman](https://github.com/tshirtman)

[https://github.com/Kulothungan16/Example-Kivy-Apps](https://github.com/Kulothungan16/Example-Kivy-Apps)

# Release Builds

The following depends on using the master (as of 2022/01/28) version of Buildozer.

```
pip3 uninstall buildozer
pip3 install git+https://github.com/kivy/buildozer.git
```

In `buildozer.spec` set:

`p4a.branch = develop`

If you do not do this, you will get this error message: `This buildozer version requires a python-for-android version with AAB (Android App Bundle) support.`.

The Android Store requires that apps be built with a minimum API level of 30. Set
```
android.api = 30
```

After this install, or changing buildozer.spec you must:
```
buildozer appclean
```

To generate a multi-architecture apk or aab, `android.arch` becomes a list of architectures. 

```
android.arch = armeabi-v7a, arm64-v8a
```

A debug build always builds an `.apk`. A release build creates an `.aab` as a default. You can create a release `.apk` by setting:

```
android.release_artifact = apk
```
Note that `release_artifact` will not be in buildozer.spec unless this file was generated with the version of Buildozer specified at the start of this section.

Build a release apk or aab with:

```
buildozer android release
```

To install an .aab locally use [Bundletool](#appendix-f--install-bundletool), in place of adb. 

Sign the .aab or .apk. [The instructions are here](https://github.com/kivy/kivy/wiki/Creating-a-Release-APK) but don't just follow the instructions, read all the annotated comments by HeRo002. The instructions are flawed, but in combination with the comments they are good.

Here are some [very detailed signing instructions](https://gist.github.com/Guhan-SenSam/fa4ed215ef3419e7b3154de5cb71f641). 


# Appendix A : Using adb

The easiest way to get `adb` is to install Android Studio.

Add something like this to your PATH:
`C:\Users\UserName\AppData\Local\Android\Sdk\platform-tools`

Some `adb` commands:
```
adb devices
adb install -r  cameraxf-0.1-arm64-v8a-debug.apk
```
Android and Python messages to a file:
```
adb logcat -c
adb logcat > log.txt
```
Python messages only to the screen:
```
adb logcat *:S python:D
```

Now start your app.

Note that `adb` does not finish, it continues logging until it is terminated.


# Appendix B : Using an emulator

It is possible to debug using an emulator but this is not recomended initially, as it adds unknowns to the debug process. The emulator is useful for checking a debugged app on various devices and Android versions.

The apk **must** be built with buildozer.spec `android.arch` having the same as ABI the emulator, I find x86 works more reliably than x86_64.

Install Android Studio.

In Android Studio, go to Tools->AVD Manager

Use the 'Create Virtual Device..' button if the emulator you want is not listed.

Right click on an emulator (view details) to see it's name, for example: Nexus_4_API_21

Add something like this to your PATH:
     C:\Users\UserName\AppData\Local\Android\Sdk\emulator

Start an emulator using it's name
```
emulator @Nexus_4_API_21
```
Check the emulator is running using 'adb devices'. Then install an app in the emulator from adb. 

# Appendix C : Locally modifying a recipe

Modify an existing recipe by making a local copy.
Replace RECIPE_NAME with whatever recipe you are changing:

1) in buildozer.spec set `p4a.local_recipes =  ./p4a-recipes`

2) `mkdir ./p4a-recipes`

3) [Download python-for-android](https://github.com/kivy/python-for-android)

4) Copy the recipe you want from python-for-android/tree/develop/pythonforandroid/recipes/RECIPE_NAME to ./p4a-recipes

5) Change the files in a way that makes you happy

6) buildozer appclean

7) buildozer android debug


# Appendix D : Debugging on WSL

As an alternative to copying the apk from the WSL file system to the main Windows file system, and running adb in Windows, one can use two copies of adb as described here. 

The easiest way to get adb is to install Android Studio for both Linux Subsystem (WSL) and Windows of same *Android Debug Bridge version*, use `adb --version` command to find it out


Windows setup:

Add something like this to your PATH in Windows:
    ` C:\Users\UserName\AppData\Local\Android\Sdk\platform-tools`

In cmd / pwsh, run this command and this is your server and should not be closed until you finish debugging.
```
adb kill-server
adb -a -P 5037 nodaemon server
```
Add the below environmental variable to the ~/.bashrc or ~/.zshrc , depending on your shell

Linux Subsystem (WSL) setup:
```
export ADB_SERVER_SOCKET=tcp:192.168.1.250:5037
```
Note: `192.168.1.250` is local ip of your machine find it using `ipconfig` in cmd

Finally, `adb devices` in Linux Subsystem should show your device
```
List of devices attached
0A052FDE40019P  device
```

# Appendix E : Copying from private storage

To copy from app private storage on an Android device to the host computer:

Connect the device via USB, on device enable Developer Mode and USB debugging.

In Android Studio run `View->Tool Windows->Device File Explorer`
In Device File Explorer go to `/data/data/org.test.example/files` or lower
Right click the item and `Save As`

# Appendix F : Install Bundletool

Bundletool depends on JRE.

Download bundletool-all-1.8.0.jar (or newer) from https://github.com/google/bundletool/releases . Save in someplace that is not the project directory.

Simplify your life:

On Linux add to `.bashrc`:

```
alias bundletool='java -jar <pathToSomeplace>/bundletool-all-1.8.0.jar'
```

On Windows create bundltool.bat containing:

```
@echo off
java -jar <pathToSomeplace>\bundletool-all-1.8.0.jar %*
```

Then, for example:

`bundletool help`

`bundletool validate --bundle=Test-release-0.0.1-.aab`

To install a `.aab` to a locally connected device use bundletool:

```
bundletool build-apks --mode universal --bundle bin/myapp-0.0.1-armeabi-v7a_arm64-v8a-release.aab --output ./app.apk
bundletool install-multi-apks --apks app.apk
```

# Appendix G : Modifying p4a

Buildozer clones p4a and places a copy in `<project>/.buildozer`. Do not try modifying this, this is Buildozer's database - you don't control what happens there.

Make a local copy of [p4a](https://github.com/kivy/python-for-android), but **not** in the `<project>` directory. In buildozer.spec specify where the local copy is located:

```
p4a.source_dir = /someplace/python-for-android-develop
```

Modify the local copy in a way that makes you happy. This will be cloned into `<project>/.buildozer` so remember to `appclean` after changes.

As an alternative to making changes locally, you can reference changes saved on GitHub. The fork must be 'public' on Github. For example if you have a p4a fork on GitHub:

```
p4a.fork = YourGitHubName
```



