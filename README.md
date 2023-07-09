
Android for Python Users
========================

*An unofficial Buildozer Users' Guide*

Revised 2023-07-03

# Table of Contents

- [Introduction](#introduction)
- [Confidence and Competence](#confidence-and-competence)
- [What is Different about Android?](#what-is-different-about-android)
  * [Posix](#posix)
  * [Wheels](#wheels)
  * [Meta-information](#meta-information)
  * [App Security](#app-security)
- [Android Storage](#android-storage)
  * [Private Storage](#private-storage)
    + [App Install Directory](#app-install-directory)
    + [App Storage Directory](#app-storage-directory)
    + [App Cache Directory](#app-cache-directory)
  * [Shared Storage](#shared-storage)
    + [Android Version Issues](#android-version-issues)
    + [MediaStore](#mediastore)
    + [androidstorage4kivy](#androidstorage4kivy)
    + [Storage Permissions](#storage-permissions)
  * [Sharing a file between apps](#sharing-a-file-between-apps)
- [Concurrency](#concurrency)
  * [Threads](#threads)
  * [Subprocess](#subprocess)
  * [Async](#async)
- [Android Service](#android-service)
  * [Specifying a Service](#specifying-a-service)
  * [Service Lifetime](#service-lifetime)
  * [Service Notifications](#service-notifications)
  * [p4a Service Implementation](#p4a-service-implementation)
- [App Permissions](#app-permissions)
  * [Manifest Permissions](#manifest-permissions)
  * [User Permissions](#user-permissions)
- [Buildozer and p4a](#buildozer-and-p4a)
  * [Install](#install)
    + [Most Common Issues](#most-common-issues)
    + [Non-deterministic Behavior](#non-deterministic-behavior)
  * [Changing buildozer.spec](#changing-buildozerspec)
  * [What should I change?](#what-should-i-change)
  * [If Buildozer fails to compile](#if-buildozer-fails-to-compile)
  * [Some buildozer.spec options](#some-buildozerspec-options)
    + [package.name](#packagename)
    + [package.domain](#packagedomain)
    + [version](#version)
    + [requirements](#requirements)
      - [requirements basics](#requirements-basics)
      - [Version pinning](#version-pinning)
      - [Find the Dependencies](#find-the-dependencies)
      - [Kivy Widget Dependencies](#kivy-widget-dependencies)
      - [Requirements Examples](#requirements-examples)
      - [Pure Python](#pure-python)
    + [orientation](#orientation)
    + [fullscreen](#fullscreen)
    + [source.include_exts](#sourceinclude_exts)
    + [android.permissions](#androidpermissions)
    + [android.api](#androidapi)
    + [android.minapi](#androidminapi)
    + [android.ndk](#androidndk)
    + [android.sdk](#androidsdk)
    + [android.archs](#androidarchs)
- [Debugging](#debugging)
  * [Get an Error Message](#get-an-error-message)
  * [Slow App Start](#slow-app-start)
- [Android Hardware](#android-hardware)
  * [Camera](#camera)
  * [Keyboard](#keyboard)
  * [Back Button and Gesture](#back-button-and-gesture)
- [Android Packages](#android-packages)
  * [The Android package](#the-android-package)
    + [BroadcastReceiver](#broadcastreceiver)
  * [Plyer](#plyer)
  * [Pyjnius](#pyjnius)
    + [Basic Pyjnius Usage](#basic-pyjnius-usage)
    + [Pyjnius Performance](#pyjnius-performance)
    + [Java Abstract Classes](#java-abstract-classes)
    + [Pyjnius Memory Management](#pyjnius-memory-management)
    + [Java Api Versions](#java-api-versions)
    + [Calling Python from Java](#calling-python-from-java)
    + [Java Listener Class](#java-listener-class)
- [Kivy Related Topics](#kivy-related-topics)
  * [disable_multitouch](#disable_multitouch)
  * [Layout](#layout)
    + [Screen Resolution](#screen-resolution)
    + [Screen Orientation](#screen-orientation)
    + [Screen Aspect Ratio](#screen-aspect-ratio)
    + [Testing Portability](#testing-portability)
  * [Kivy Filechooser](#kivy-filechooser)
  * [KivyMD](#kivymd)
  * [Kivy Lifecycle](#kivy-lifecycle)
  * [Kivy Garden](#kivy-garden)
- [Creating a Recipe](#creating-a-recipe)
  * [Why a Recipe](#why-a-recipe)
  * [Porting Overview](#porting-overview)
  * [P4A Recipe API](#p4a-recipe-api)
  * [Porting Build Instructions](#porting-build-instructions)
  * [Recipe Support](#recipe-support)
- [Resources](#resources)
  * [Read the Fine Manual](#read-the-fine-manual)
  * [Android for Python](#android-for-python)
  * [KivAds and KivMob](#kivads-and-kivmob)
  * [Android Notifications](#android-notifications)
  * [Android Billing](#android-billing)
  * [Other Resources](#other-resources)
- [Release Builds](#release-builds)
  * [Setup signing](#setup-signing)
  * [Build the release](#build-the-release)
- [Appendix A : Using adb](#appendix-a--using-adb)
- [Appendix B : Using an emulator](#appendix-b--using-an-emulator)
- [Appendix C : Locally modifying a recipe](#appendix-c--locally-modifying-a-recipe)
- [Appendix D : Debugging on WSL](#appendix-d--debugging-on-wsl)
- [Appendix E : Copying from private storage](#appendix-e--copying-from-private-storage)
- [Appendix F : Install Bundletool](#appendix-f--install-bundletool)
- [Appendix G : Modifying p4a](#appendix-g--modifying-p4a)
- [Appendix H : Cryptic Error Messages](#appendix-h--cryptic-error-messages)
  * [No module named 'msvcrt'](#no-module-named-msvcrt)
  * [Aidl not found](#aidl-not-found)
  * [Sdkmanager is not installed](#sdkmanager-is-not-installed)
  * [64-bit instead of 32-bit](#64-bit-instead-of-32-bit)
  * [EM_X86_64 instead of EM_AARCH64](#em_x86_64-instead-of-em_aarch64)
  * [No module named '_Socket'](#no-module-named-_socket)
  * [weakly-referenced object](#weakly-referenced-object)
  * [OpenCV requires Android SDK Tools](#opencv-requires-android-sdk-tools)
  * [No such file or directory: 'ffmpeg'](#no-such-file-or-directory-ffmpeg)
  * [Unsupported class file major version 62](#unsupported-class-file-major-version-62)
  * [Permission denied: '/storage/emulated/0/...'](#permission-denied-storageemulated0)
  * [ModuleNotFoundError: No module named 'PIL'](#modulenotfounderror-no-module-named-pil)
  * [Requested API target 27 is not available](#requested-api-target-27-is-not-available)
  * [BUILD FAILURE: No main.py(o)](#build-failure-no-mainpyo)
  * [/usr/bin/gzip: 1: ELF : not found](#usrbingzip-1-elf--not-found)
  * [SSL: CERTIFICATE_VERIFY_FAILED](#ssl-certificate_verify_failed)
  * [gradlew failed!](#gradlew-failed)
  * [android:exported](#androidexported)
  * [null pointer dereference](#null-pointer-dereference)
  * [No module named 'android'](#no-module-named-android)
  * [Hunk #1 FAILED](#hunk-1-failed)
  * [Kivy is too old](#kivy-is-too-old)
  * [C compiler cannot create executables](#c-compiler-cannot-create-executables)
  * [undefined macro LT_SYS_SYMBOL_USCORE](#undefined-macro-lt_sys_symbol_uscore)
  * [all is not a valid value for orientation](#all-is-not-a-valid-value-for-orientation)
  * [orientation have an invalid value](#orientation-have-an-invalid-value)
  * [build.gradle : 79: Unexpected input: '{'](#buildgradle--79-unexpected-input-)
  * [Could not resolve all files for configuration](#could-not-resolve-all-files-for-configuration)
  * [presplash-lottie: No such file or directory](#presplash-lottie-no-such-file-or-directory)
  * [ssl module in Python is not available](#ssl-module-in-python-is-not-available)
  * [AttributeError: 'str' object has no attribute 'stdout'](#AttributeError-str-object-has-no-attribute-stdout)
  * [error: expression is not assignable](#error-expression-is-not-assignable)
  * [error: possibly undefined macro: AM_ICONV](#error-possibly-undefined-macro-am_iconv)
  * [error: failed to read PNG](#error-failed-to-read-png)
  * [Build failed: Requested API target 31 is not available](#build-failed-requested-api-target-31-is-not-available)
  * [aaudio_DetectBrokenPlayState](#aaudio_DetectBrokenPlayState)
  * ['config.pxi' not found](#configpxi-not-found)
  * [extra tap when single tap expected](#extra-tap-when-single-tap-expected)
  * [No module named 'kivy._clock'](#no-module-named-kivy_clock)
  * [ValueError: 'border_radius' must have 4, got 0](#valueerror-border_radius-must-have-4-got-0)  

# Introduction

Python and Kivy are portable across operating systems because of POSIX, Python wheels, and pip. However Android is not POSIX compliant, wheels are not usually available for Android, and pip is not installed on Android. Clearly many apps won't 'just work' on Android. The document is about porting a Kivy app to Android.

For a well written app that only paints the screen, and does nothing else, building for Android will be 'push button'. One can for example build 'Hello World' with the default 'buildozer.spec' file. Of course this does not describe many apps.

This document is not a substitute for Reading The Fine Manual [(RTFM)](https://en.wikipedia.org/wiki/RTFM). Your basic resources are [Buildozer](https://github.com/kivy/buildozer/tree/master/docs/source), [Python for Android](https://github.com/kivy/python-for-android/tree/develop/doc/source), and [Android](https://developer.android.com/guide).

Python-for-Android is a truly amazing achievement, but there are some details to understand. The alternative is Java and the Android api, which has been shown to induce insanity in laboratory mice. 

Lastly this document is my understanding as of the date above. I am not a developer in any of the Kivy sub projects (except for a few PRs). I have created a suite of [Android orientated examples](https://github.com/Android-for-Python/INDEX-of-Examples).

The document is guaranteed to be incomplete is some way, and may possibly be wrong in another way. But reading it will hopefully provide you with come context with which to read the official documentation and to experiment. Those two steps are the key to insight and understanding, which in turn is key to making your idea real.

# Confidence and Competence

This document aims to provide the context to enable you to take yourself up the slope on the right side of the graph.

You can get stuck on the peak on the left by failing to read the documentation, watching old YouTube videos, or using ChatGPT to write your code. If you find yourself stuck in the valley this is your opportunity to recognize (and learn from) your choices that didn't work for you.

![Dunning-Kruger Effect](https://github.com/Android-for-Python/Android-for-Python-Users/blob/main/images/Dunning%E2%80%93Kruger_Effect_01.png)

Source: Wikimedia Commons

# What is Different about Android?

## Posix

Android is not POSIX compliant (POSIX is so deep in your assumptions about computers you probably don't know it exists).
   
The file system model is different, the app cannot use any directory in the file system. The app has specific private storage directories that support file operations. The app also has storage shared between apps, this is implemented as a database.

Threads are available like the desktop. Subprocess is available, but the excutable called by subprocess on the desktop almost certainly is not available.

Multi-tasking, on the desktop when an app loses focus or is minimized it continues to execute - it just doesnt see UI events. Android is not multi-tasking, when an app is removed from the UI it *pauses*, and then does not execute any app code. Execution without a UI requires an Android service.

Apps have a lifecycle, keep to the [Kivy Lifcycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle) so that the app keeps to the [Android Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle#alc). Android api calls made outside of the Kivy App class or its children may cause non-deterministic behavior or crashes.

## Wheels

Some Python packages are not written in Python (are not pure-Python), they contain code that must be compiled. Pip provides pre-compiled packages for desktop OSes, **but not for Android**. P4a addresses this with recipes, but not all impure packages are available. AVOID DISAPPOINTMENT, check availability first. 

A quick and easy way to determine if a package is pure Python is to go to the package on `pypi.org` (this is where pip gets wheels), then follow the "Download Files" link, and look under "Built Distribution".

If the "Built Distribution" *only* contains files that end with `py3-none-any.whl` ([for example](https://pypi.org/project/chardet/#files)) then the package is pure Python, and WILL NOT require a recipe. 

If the "Built Distribution" contains files that end with `win32.whl`, `and64.whl`, `arm64.whl` or similar ([for example](https://pypi.org/project/opencv-python/#files)) then the package is not pure Python, and WILL require a recipe.

If the package requires a recipe, check the list of available [recipes](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes). Note that the recipe name is not always identical to the Pypi package name, for example the "opencv-python" package has a recipe named "opencv".

If the package requires a recipe, and one does not exist - your app is not portable to Android without extra work. Read this summary of [your options](#pure-python).

*Note that it is possible but unlikley that a pure Python package may contain OS specific code, or depend on OS specific third party apps (for example ffmpeg).* 

## Meta-information

Unlike the desktop you must provide information *about* your Python code, this requirement causes everybody who doesn't understand it to crash and burn. This information must be supplied in the buildozer.spec file. It includes *all* the pip packages your app depends on, any file types your app depends on for data, and the Android permissions your app depends on. Meta-information may not be limited to that short list, but that list is critical.

## App Security

A mobile app is different from a desktop app in that it is less likely to be physically secure from bad actors. Anybody with Android Studio and a device on which your app is installed can view most of the contents of your app, though Python, Java, or C code must be decompiled to be human readable (this is not hard).

Nothing is perfectly secure (just ask any hacker, or anybody who prints there own copies of a government's paper money). You can make confidential information harder to reverse engineer by designing your app such that:

 - Any trade secrets, and also the app user's private data, are only saved on a secure server.

 - Password vaildation is with a secure server (no passwords are built into the app).

 - Any temporary refresh keys are saved in [app private storage](#app-storage-directory), and not in the app install directory. And for private storage location do not us p4a's `android.app_storage_path()` api call as this location is not secure 

# Android Storage

The view of the Android file system has changed a few times over the years. Modern Android devices physically use *external storage*, however the term is ambiguous as it applies to two incompatible uses cases.

Storage is either *Private Storage* or *Shared Storage*. **Private Storage content is only visible to the app that created it, Shared Storage is visible to all apps. Python file operations can only be performed in Private Storage, Shared Storage is a database not a file system.** Programmers used to only a desktop file system have a hard time accepting this storage architecture is different.

The storage architecture is summarized in the Android documentation https://developer.android.com/training/data-storage . The following subsections address storage from the point of view of a Buildozer user.

## Private Storage

An app can perform Python file operations (read, write, shutil) on its private storage. There are three usable storage location: the app install directory, the app storage directory, and the app cache directory.

No permissions are requires to read or write an app's private storage. [More on permissions below](#app-permissions).

Do not confuse private with secure, on older Android versions it is possible for other apps to read private storage. And more generally Android Studio allows inspection.

### App Install Directory

The install directory is `./`, files from the apk or aab can be accessed. An app update will overwrte this directory so this is not a good place to save files (though you can).

### App Storage Directory

Most of the time this is what you should use. 

The app storage directory is persistent over the installed lifetime of the app, and is removed when the app is uninstalled.

The app storage directory is accessed using:

```python
from android import mActivity

    context = mActivity.getApplicationContext()
    result =  context.getExternalFilesDir(None)   # don't forget the argument
    if result:
        storage_path =  str(result.toString())
```

The `result` in the code can be `None` on older Android devices that have removable external storage, and that storage is removed.  

In addition p4a provides a legacy storage directory, this works but does not return the same location as the Android documented `getExternalFilesDir(None)` above. The legacy storage directory may be used as a fallback in the case of removed external storage. But the location returned by `app_storage_path()` is **not secure**, and when removed the storage media is **not secure**.


```python
from android.storage import app_storage_path
from android import mActivity

    context = mActivity.getApplicationContext()
    result =  context.getExternalFilesDir(None)   # don't forget the argument
    if result:
        storage_path = str(result.toString())
    else:
        storage_path = app_storage_path()
```

### App Cache Directory

The app cache directory is temporary storage, the lifetime of files in this are is managed by Android.  The app storage directory is accessed using:
```python
from android import mActivity

    context = mActivity.getApplicationContext()
    result =  context.getExternalCacheDir()
    if result:
        app_cache_directory_path =  str(result.toString())
```

## Shared Storage

Shared storage is visible to all apps, and is persistent after an app is uninstalled. **A portable app can not perform Python file operations on Shared Storage. Files are copied to and from Shared Storage.**

### Android Version Issues

On devices running Android 10 and later or build android.api > 28 shared storage is implemented as a database, you cannot access shared storage with Python file operations because there is no file path. The [Kivy Filechooser](#kivy-filechooser) and KivyMD file chooser only work with private storage, you must use the Android Chooser for shared storage.

On devices with Android less than 10, shared storage is a file system and you can access file with `from android.storage import primary_external_storage_path`. But this **does not work** on devices running Android 10 or greater.

On devices running Android 10 or greater shared storage is accessed via the Java MediaStore api, or the Chooser api. 

### MediaStore

The MediaStore is a database, not a file system. The MediaStore is organized based on multiple root directories. For example 'Music', 'Movies', 'Pictures', 'Documents', and 'Downloads'.

The MediaStore files are accessed using a *content uri*, not a file path. Python requires a file path to access a file, not a content uri. Content uris are obtained from Android OS apis, for example in the MediaStore api.

"Are you telling me I can't ....?", I'm explaining that Android shared storage is different from our expectations, evolve or die out.

### AndroidStorage4Kivy

An Android version independent Python api for shared storage is implemented in the package [androidstorage4kivy](https://github.com/Android-for-Python/androidstorage4kivy).

This package is an abstraction of the MediaStore api. The abstraction is *copy or delete*, so *files can be copies to, copied from, and deleted from shared storage*. The MediaStore api, and content uris are thus hidden. For more details see https://github.com/Android-for-Python/androidstorage4kivy#programming-model and https://github.com/Android-for-Python/androidstorage4kivy#sharedstorage-class.

Example usage is in [shared_storage_example](https://github.com/Android-for-Python/shared_storage_example). The example also demonstrates using the Android file Chooser, using the SharedStorage4Kivy Chooser class.

### Storage Permissions

On devices running Android 10 and later no permissions are requires to read or write an app's own shared storage or private storage.

Reading another app's shared storage requires READ_EXTERNAL_STORAGE permission if android.api < 33 and READ_MEDIA_IMAGES, READ_MEDIA_VIDEO, READ_MEDIA_AUDIO [see](https://developer.android.com/reference/android/Manifest.permission#READ_EXTERNAL_STORAGE) if android.api >= 33.  An app cannot overwrite another app's shared file.

There is one special case, which is different from typical desktop usage. An app cannot read another app's file in the Downloads directory, regardless of permissions.

On devices running Android 9 and less, WRITE_EXTERNAL_STORAGE is required for any file writes. Or READ_EXTERNAL_STORAGE if the app only wants to do shared storage reads.

## Sharing a file between apps

Files in Shared Storage can be shared between apps. If you want to share a file first copy it to shared storage. Then send it to an Android ShareSheet. The complement operation, receiving a shared file requires copying the file to private storage so Python can access it.

The androidstorage4kivy package contains a ShareSheet class that invokes an Android ShareSheet, or sends a file directly to a specific app. The latter assumes that the specific app knows how to receive files of the type sent.

Examples of sending a file are in [share_send_example](https://github.com/Android-for-Python/share_send_example), and receiving a file in [share_receive_example](https://github.com/Android-for-Python/share_receive_example).

# Concurrency

## Threads

Kivy executes on the 'UI thread', Android requires that this thread is always responsive to UI events. As a consequence long latency operations (e.g. network access, sleep()) or computationally expensive operations must be performed in their own Python threads.

Threads must be truly asynchronous to the UI thread, so do not use `join()` in the UI thread. A non-UI thread may not write to a UI widget. [See this basics example](https://gist.github.com/el3/3c8d4e127d41e86ca3f2eae94c25c15f). A very thread safe way to return results to the UI thread is to use the `@mainthread` decorator, for example:

```python
from threading import Thread
from kivy.clock import mainthread

    def run_some_function_in_thread(self, arg0):
        # note the value of args is a tuple,
        # it always contains at least one comma
        Thread(target=self.some_function, args = (arg0_val,),
               daemon=True).start()

    def some_function(self, arg0):
        try:
            # the behavior goes here, creating some result
            result = 'greetings earthlings'
            # sync a *copy* of the result with the Kivy UI thread
            self.make_thread_safe(str(result))
        except:
            # handle if you want to
            pass

    @mainthread
    def make_thread_safe(self, text):
        # assign to some UI widget
        self.label.text = text
```

A daemon thread will exit when the app exits, providing all threads running at app exit are also daemon threads. Failure to always specify a daemon thread may cause an app to exit in a delayed way.


## Subprocess

The Python subprocess depends on having an ARM executable to run, this does not exist unless you build it and then `chmod 744`. The exception is system commands which of course are compiled for ARM; the executables for `ls`, `ps`, etc. are in Android's `/system/bin`. System commands may not have the same features as a Bash shell, and when run from `subprocess.Popen` have app only permission. 

There is no `python3` executable, Python's `sys.executable` is empty. To run a Python script in a new process, we use an Android Service.

## Async

### Kivy async usage

Python does not allow calling (async) coroutines from (traditional) routines. Kivy is constructed of routines, however Kivy has a method that allows it to be started as an async coroutine. Thus the Kivy and Async loops do not block one another, this does not mean any other Kivy routines become coroutines.

We normally start a Kivy app with:

```python
ExampleApp().run()
```

For basic Async usage we start Kivy with (we'll show trio later):

```python
asyncio.run(ExampleApp().async_run('asyncio'))
```

Alone this does not allow us to combine routines and coroutines. An app can't directly call a coroutine from say a Kivy UI event such as a Button event. But an app can do this indirectly, the details are different for the `trio` and `asyncio` packages.

The basic approaches are shown in the next two sections, with simple examples. Using `trio` a coroutine is *started* from a routine. Using `asyncio` a coroutine is *enabled* from a routine. 

### trio

Using `trio`, indirectly call a coroutine from a routine by initializing the app with the `trio` event loop, and using this to call `start_soon()`. The coroutine is *started* from a routine.

```python
from kivy.app import App
from kivy.uix.label import Label
import trio

class ExampleApp(App):
    def __init__(self, nursery):
        super().__init__()
        self.nursery = nursery

    def build(self):
        return Label(text = 'Greetings Earthlings')

    def on_start(self):
        print("Kivy app started")
        self.start_async_function()

    def start_async_function(self):
        self.nursery.start_soon(self.my_async_function) # starts coroutine

    async def my_async_function(self):
        print("woohooo running async function on kivy app")
        await self.another_async_function()

    async def another_async_function(self):
        print("another async function")

# Start kivy app as an asynchronous task
async def main():
    async with trio.open_nursery() as nursery:
        await ExampleApp(nursery).async_run("trio") # start Kivy
        nursery.cancel_scope.cancel()

trio.run(main)              # note this is a reference to main
```

Thanks to @Hamburguesa for this one.

### asyncio

Using `asyncio`, indirectly enable a coroutine from a routine by having the coroutine `await` on an `asyncio.Event` set by a routine. The coroutine is *enabled* from a routine.

```python
from kivy.app import App
from kivy.uix.label import Label
import asyncio

class ExampleApp(App):
    
    def __init__(self):
        super().__init__()
        self.started = asyncio.Event()

    def build(self):
        return Label(text = 'Greetings Earthlings')

    def on_start(self):
        print("Kivy app started")
        self.started.set()
    
    async def my_async_function(self):
        await self.started.wait()              # enables coroutine when set()
	self.started.clear()	     # not needed here but good to know about
        print("woohooo running async function on kivy app")
        await self.another_async_function()

    async def another_async_function(self):
        print("another async function")
        
# Start kivy app as an asynchronous task
async def main():
    app = ExampleApp()
    await asyncio.gather(app.async_run('asyncio'),  # starts Kivy
                         app.my_async_function(),   # starts coroutine
                         return_exceptions = True)  # for debugging

asyncio.run(main())             # note this is a call of main()
```

# Android Service

An [Android Service](https://developer.android.com/guide/components/services) performs operations that are not related to the UI. In the context of Kivy, an Android service is a Python script that executes in its own process.

**A service is a Python script, not a Kivy App**. You can use some functions from the Kivy package, but nothing that depends on an instantiated Kivy App class. There is no Kivy event loop (so no Clock), there is no SDL2 (so no codecs). Don't even think about instantiating a Kivy App class (there is no Window). **Most Kivy functions will not work in a stand alone Python script that does not contain an instantiated App.**     

There are two Kivy examples [Kivy Service Osc](https://github.com/tshirtman/kivy_service_osc), and [Mutli-Service Example](https://github.com/Android-for-Python/Multi-Service-Example). OSC is a good package for message passing between app and service. However it is not designed for passing large datas, consider using the file system in this case. OSC requires INTERNET permission.

## Specifying a Service 

We assign the service a name, and associate this with script file name in `buildozer.spec`. *The service name must be a valid Java class name. The first character and no other character must be upper case.* In the sample below `the_service.py` is the name of the script, and `Worker` is the name we give the service.

```
# (list) List of service to declare
services = Worker:the_service.py
```

We start the service from an app using the fully qualified service name, which is the app package name, with '.Service' and the service name appended:
```python
from android import mActivity

   def start_service(self, name):
       context =  mActivity.getApplicationContext()
       service_name = str(context.getPackageName()) + '.Service' + name
       service = autoclass(service_name)
       service.start(mActivity,'')   # starts or re-initializes a service
       return service

   self.start_service('Worker') # starts a service
```

`service_name` is the name of a Java class implementing the service, which in turn executes the file `the_service.py`.

Most documentation and examples show stopping a service like this (assuming the class variables have been previously set):

```python
   # This does not work with a sticky foreground service
   self.service.stop(self.mActivity)
```

As explained in the next section this does not work with a sticky foreground service. The general way to stop a service is to re-initialize the service then immediately stop it:

```python
   # a general way to stop a service
   # re-initializes then stops the service
   self.start_service('Worker').stop(mActivity)
```

When debugging, the default Python filter excludes any print statments in the service. To see output from the service use the `adb logcat -s` option, for example `adb logcat -s Worker`.

## Service Lifetime

The lifetime of the service is limited by the service type. Three types of service are avilabile, the difference between them is the service lifetime allowed by the OS. The service type is specified in `buildozer.spec`.

There is an api for restarting a service killed by the OS [`setAutoRestartService()`](https://github.com/kivy/python-for-android/blob/develop/doc/source/services.rst#service-auto-restart). This is only meaningful with a background service.

### Background Service

A background service is generally "short lived" as defined by the OS, its lifetime is never longer than the lifetime of the app that started it. A background service is specified in buildozer.spec with:

```
# (list) List of service to declare
services = Worker:the_service.py
```

### Foreground Service

A foreground service's lifetime is no longer than it's app lifetime, when the app stops (not pauses) the service stops. A foreground service is specified in buildozer.spec with:

```
# (list) List of service to declare
services = Worker:the_service.py:foreground

# (list) Permissions
android.permissions = FOREGROUND_SERVICE
```
A notification icon may be created in the task bar, see next section.

### Sticky Foreground Service

A sticky foreground service lifetime is nominally unconstrained. An app may stopand start (not just pause) and resume communication with a sticky foreground service. **An app is responsible for terminating a sticky foreground service, specially if the service is energy intensive.** A sticky foreground service is specified in buildozer.spec with:

```
# (list) List of service to declare
services = Worker:the_service.py:foreground:sticky

# (list) Permissions
android.permissions = FOREGROUND_SERVICE
```
A notification icon may be created in the task bar, see next section.

Because an app may stop and restart while a sticky foreground service is running, the app may not contain valid dynamic information about the service. Importantly a reference to the instance of the service saved in a class variable will be `None` when the app restarts. The restart-stop code above re-initializes the service to obtain a new reference to the service so that it can be stopped.

### Bound Service

There is no built-in support for a bound service.

## Service Notifications

For android.api < 33 a foreground service always places a notification icon in the task bar. Depending on Android device version there may be an up to 10 second delay in the appearance of the icon, this delay is an Android 'feature' and does not indicate that the foreground service has not started.

For android.api >= 33 a foreground service only places a notification icon in the task bar if POST_NOTIFICATIONS permission is specified in buildozer.spec and as a run time permission. If you do not do this, app behavior will vary with device version. See the [Android documentation](https://developer.android.com/develop/ui/views/notifications/notification-permission) and [App Permissions](#app-permissions). No notification does not mean the service is not started.

The default icon, and the notification title and text,can be changed by specifying three additional string arguments. 

```python
  service.start(mActivity, 'icon_resource_name', 'Title', 'Text', '')
```

'icon_resource_name' is the name of an Android resource. See `android.add_resources` in `buildozer.spec`. Remember to consider icon size.

For example adding icon resources from a local directory named 'res_icons':

```
android.add_resources =
    res_icons/all_inclusive.xml:mipmap,
    res_icons/align_vertical_top.png:drawable
```
And use one as:
```python
    service.start('all_inclusive', 'Important News', 'Suddenly, nothing happened.', '')
```

## p4a Service Implementation

If you want to understand the implementation of services in more detail, read the code: [PythonService.java](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/src/main/java/org/kivy/android/PythonService.java), [Service.tmpl.java](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/templates/Service.tmpl.java), and [build.py](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/build.py).

# App Permissions

Android restricts access to many features. An app must declare the permissions it requires. There are two different declarations, manifest and user. User permissions are a subset of manifest permissions.

The [full list of permissions](https://developer.android.com/reference/android/Manifest.permission) is documented by Google. In general you must research the permissions needed by your app, resist the temptation to blindly guess.

Note: starting with Android api 33 the names of the shared storage permissions changed. See [Storage Permissions](#storage-permissions).

## Manifest permissions

Manifest permissions are declared in the buildozer.spec file. Common examples are  CAMERA, INTERNET, BLUETOOTH_SCAN, RECORD_AUDIO. Apps that scan Bluetooth or scan Wifi may require multiple permissions.

For example if the app connects to a network add INTERNET permission.

WRITE_EXTERNAL_STORAGE is never required for api >= 30 https://developer.android.com/training/data-storage#permissions

READ_EXTERNAL_STORAGE is never required for api >= 33 https://developer.android.com/reference/android/Manifest.permission#READ_EXTERNAL_STORAGE 

## User permissions

Any app manifest permission documented as having "Protection level: dangerous" additionally requires a user permission. From Python this is initiated with a `request_permissions()` call. User permission requests are seen as the dialog that Android apps present to the user. Of the four manifest permissions listed above, three are "dangerous". 

Many old Kivy examples show `request_permissions()` at the top of main.py, on newer versions of Android this will lead to unexpected behavior. Because it violates the [Kivy Lifecycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle).

Currently `request_permissions()` can only be called *after (not from)* `on_start()`, or from `build()`, and can only be called once per time step. Generally calling after `on_start` simplifies the app control logic for handling both the 'request' case and the 'previously granted' case; of course the user may deny permission and the app must handle this case.

```python
from android.permissions import request_permissions, check_permission, Permission

     request_permissions([Permission.CAMERA, Permission.RECORD_AUDIO])

     ok = check_permission(Permission.RECORD_AUDIO)
```

An implementation example is the [`AndroidPermissions`](https://github.com/Android-for-Python/c4k_photo_example/blob/main/android_permissions.py) class which encapsulates permission behavior. You can copy this file and modify the actual permissions for your app. Then instantiate the class like this:

```python
    def on_start(self):
        self.dont_gc = AndroidPermissions(self.start_app)  

    def start_app(self):
        self.dont_gc = None
	# use whatever required permission
```

Note that the App class variable `self.dont_gc` delays garbage collection and it critically important.

Finally it is normal Android behavior that if a user denies permission, it may not be possible to grant that permission from the App. In this case the user must grant the permission from the Android Settings panel for the app.

# Buildozer and p4a

## Install

**The current release version of Buildozer is 1.5, by default always Buildozer uses the current release version of p4a.**

Buildozer runs on Linux, Windows users need a Linux virtual machine such as WSL, Colab, or VirtualBox to run Buildozer (most users use WSL or Colab). Buildozer also runs on a Mac, but the number of users is small and this is reflected in the availability of help.

Read the [Install documentation](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst), really. **There is an obsolete Docker image *- do not use it*,** the install documentation does not reference Docker, and the Docker image is not maintained.

**If building on WSL**, always build the project in a directory on the Linux partition (somewhere under WSL `cd ~`). You will have to copy your project from the Windows partition to the Linux partition. NEVER build the project in a directory on the Windows partition (usually `/mnt/c/` or `/mnt/d/`), as this may give unpredicatable results.

**Test your Buildozer install** by building (`buildozer android debug`) [Hello World](https://kivy.org/doc/stable/guide/basic.html#create-an-application) in `main.py`. Use the default `buildozer.spec` (create this with `buildozer init`, modify to set `android.api=32`). I know you just want to see your app run on Android, but this simple first step will provide you with familiarity with the overall process.

### Most Common Issues

Some things you can do now to prevent surprises later:

* [Read the install instructions](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst) to re-check that all the dependencies are installed.

* Re-check that the requirements in buildozer.spec are fully and correctly specified.

* Re-check that each of the requirements is either pure Python or has a recipe.

* The first time Buildozer is run type `y` each time you are asked to accept a Google License Agreement.

### Non-deterministic Behavior

Buildozer's behavior can be non-deterministic in any of these cases:

* It is run as root on an OS with user accounts (so Colab is OK).

* It is run on an NTFS partition mounted on a Linux system. WSL users, don't build your app on the Windows partition (`/mnt/c/Users....`). Because some Python packages implement OS specific behavior based on the disk root name.

* There are Python style trailing comments in the buildozer.spec file, or comment characters inside a list. Comment characters must be the first character on a line in the buildozer.spec file. 

* There is a white space or a tab before an uncommented option, an option must start on the first character of a line (this only applies to the first line of multi-line options).

* The buildozer.spec `source.exclude_dir` is used, as `source.include_ext` has priority, so some files in the excluded directory may not be excluded.

## Changing buildozer.spec

Note that Buildozer allows *specification* of build files, versions, and options; but unlike most other build tools it *does not do version management of you project options*. If does do version management of the Android NDK and SDK. If buildozer.spec is changed the change probably *won't* propagate into the apk on the next build.

After changing the buildozer.spec file (or any of the dependencies) users *must* do an appclean.
```
buildozer appclean
buildozer android debug
```
There may be some exceptions to this, the only one I know to be safe is one can add (but not change version of, or remove) a package in the [requirements](#requirements) list without the appclean.

You don't need to always do a `buildozer appclean` if you don't change buildozer.spec.

There is no magic universal buildozer.spec, its configuration depends on the functionality of your app.

Finally buildozer.spec's syntax is not Python's syntax:
 - comment characters must only occur on the first character of a line.
 - comment characters must not occur inside a list.
 - build options must start on the first character of a line.

## What should I change?

Generally change as few options as possible; resist the temptation to overspecify, you will over constrain the implementation. 

 - If the app is for you, change the `title` and `package.name`.

 - If the app is for the store, also change `package.domain`.

 - If the app includes data files, add any necessary file extensions in `source.include_exts`.

 - If the app uses Python packages that are normally installed with `pip3`, add these and their dependencies to `requirements`.

 - Optionally add `presplash.filename`, and `icon.filename` (icon must be a `.png`).

 - Add the required [Android Permissions](#app-permissions) to `android.permissions`. For example if the app connects to a network add INTERNET permission. 

 - If the app is for the store you will need to increase the default `android.api`.

 - If you want to add Java use `android.add_src`, `android.add_jars`, `android.add_aars`, or for Maven Java packages `android.gradle_dependencies`.

 - Want to speedup debug builds? Remove one of the elements in `android.archs`.

There are a lots of other options; most users can and should ignore these. 

## If Buildozer fails to compile

### Find the error message

- Look for an error message in the last part of the log after `STDOUT:` but before `STDERR:`. There is also a log output after `STDERR:`, this is supplemental information and generally does not contain the error message.

- If you don't can't find the error message in `STDOUT:`, try looking immediately above `STDOUT:` for lines starting with `[DEBUG]`. 

- If you can't find any message, get enough information so somebody can help you. Ask in one of the User Groups ([Discord Kivy](https://discord.com/invite/eT3cuQp), or [Google Groups Kivy](https://groups.google.com/g/kivy-users)). Always include the **FULL LOG** as a `.txt` file (*not screen shots*). 

### Understand the message

- If you are lucky the message is self explanatory, or a quick search will explain it. 

- If you don't understand the message, look in [Appendix H : Cryptic Error Messages](#appendix-h--cryptic-error-messages).

- If you still don't understand, get enough information so somebody can help you. Ask in one of the User Groups ([Discord Kivy](https://discord.com/invite/eT3cuQp), or [Google Groups Kivy](https://groups.google.com/g/kivy-users)). Always include **BOTH** the full contents of `STDOUT:` (or above) and full contents of `STDERR:` as a `.txt` file (*not screen shots*).

## Some buildozer.spec options

[RTFM](https://github.com/kivy/buildozer/blob/master/docs/source/specifications.rst), really. And see the [KivyMD section](#kivymd).

### package.name

This must contain only alpha numeric characters, do not use any other characters.

### package.domain

This must contain exactly one period `.` surrounded by alpha numeric characters, do not use any other characters. Anthing else will cause a Gradle fail.

### version

This must contain no more than two periods `.` surrounded by numeric characters. More than two periods will cause a Gradle fail.

### requirements

#### requirements basics

This is the list of pip packages (and possibly versions) that your app imports from.

**In addition** Buildozer needs to know the packages your packages depend on (because there is no pip3 on Android). Determining these is [shown in the next section](#find-the-dependencies).

Do not add Python system modules, only packages you might install with pip3 on the desktop. 

There are some pip3 packages that are added automatically, no need to put these in requirements: `libffi, openssl, sqlite3, setuptools, six, pyjnius, android`.

In an example of *poor tool design*, the names of some recipes do not match the Pypi package names. Use the 'Recipe Name', in place of the 'PyPi Name'.


| PyPi Name | Recipe Name |
------------|-------------|
| python-dateutil | dateutil |
| mysql-python | mysql |
| ndg-httpsclient | ndghttpsclient |
| opencv-python | opencv |
| opencv-contrib-python | opencv_extras |
| protobuf-python | protobuf_cpp |
| leveldb | pyleveldb |
| spine-cython | spine |
| pyav | av |


#### Version pinning

If a requirement is a package that requires a [recipe](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes), version pinning may result in a build error. Because a compile recipe is created for the version specified in the recipe, and may not apply to other versions if the compile model has changed.

Your options are to use the default version, or [locally modify the recipe](#appendix-c--locally-modifying-a-recipe).

#### Find the Dependencies

To recursively find an *installed* package dependencies, use pipdeptree.

```
pip3 install pipdeptree
```

Use `pipdeptree -p <packagename>`, for example:
```
pip3 install google-cloud-firestore
pipdeptree -p google-cloud-firestore
```

Returns a package dependency tree. It looks like this:
```
google-cloud-firestore==2.5.2
  - google-api-core [required: >=1.31.5,<3.0.0dev,!=2.3.0,!=2.2.*,!=2.1.*,!=2.0.*, installed: 2.8.1]
    - google-auth [required: >=1.25.0,<3.0dev, installed: 2.6.0]
      - cachetools [required: >=2.0.0,<6.0, installed: 5.0.0]
      - pyasn1-modules [required: >=0.2.1, installed: 0.2.8]
        - pyasn1 [required: >=0.4.6,<0.5.0, installed: 0.4.8]
      - rsa [required: >=3.1.4,<5, installed: 4.8]
        - pyasn1 [required: >=0.1.3, installed: 0.4.8]	
....and so on....
```

Each line is a dependency. Because it is a tree and we want a list many lines may be duplicates. Remove the version information (after and including the `[`), remove the duplicate lines, remove the newlines, and change the ` - ` to `,`. 

We get the list of requirements dependencies for this package.

```
google-cloud-firestore, googxle-api-core, google-auth, cachetools, pyasn1-modules, pyasn1, rsa, googleapis-common-protos, protobuf, requests, chardet, idna, urllib3, google-cloud-core, proto-plus
```

*However*, this technique is only as good as the package information. And exhibits platform variations. For example if this is run on Linux the `requests` dependencies `certifi, chardet, idna, urllib3` will not be listed. These missing dependencies will be found during debugging.  

#### Kivy Widget Dependencies

Some Kivy widgets have requirement dependencies:

`kivy.network.urlrequest` needs  `requests, urllib3, chardet, idna, certifi`

`kivy.uix.video` needs `ffpyplayer`

`kivy.core.audio.SoundLoader` needs `ffpyplayer, ffpyplayer_codecs` 

Not a Kivy Widget, but `requests` depends on `urllib3, chardet, idna, certifi` or `urllib3, charset-normalizer==2.1.1, idna, certifi`

#### Requirements Examples

For examples [see](https://github.com/Android-for-Python/INDEX-of-Examples#readme).

Using the technique described above, we get the following:

##### Pyrebase4
```
requirements = python3,kivy, pyrebase4, gcloud, googleapis-common-protos, protobuf, httplib2, pyparsing, oauth2client, pyasn1, pyasn1-modules, rsa, pycryptodome, python-jwt, jws, requests, certifi, chardet, idna, urllib3, requests-toolbelt , jwcrypto, cryptography, deprecated, wrapt
```
Run on Windows, the last four items were not determined automatically. Run on Linux the requests dependencies were missing. In both cases these were added during debugging.

##### firebase_admin
```
requirements = python3,kivy, firebase-admin, cachecontrol, msgpack, requests, certifi, chardet, idna, urllib3, google-api-core, google-auth, cachetools, pyasn1-modules, pyasn1, rsa, pyasn1, googleapis-common-protos, protobuf, google-api-python-client, google-auth-httplib2, httplib2, pyparsing, uritemplate, google-cloud-firestore, google-cloud-core, proto-plus, google-cloud-storage, google-resumable-media, google-crc32c
```

#### Pure Python

The packages you add here **must be pure Python, or have a recipe** [in this list](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes). If this is not the case, the options are to:

* Rewrite the app using a different package.

* Locally [modify an existing recipe](#appendix-c--locally-modifying-a-recipe).

* [Create a new recipe](#creating-a-recipe).

* Import the functionality from Java using [Pyjnius](#pyjnius).

None of these options are trivial. That is why it said AVOID DISAPPOINTMENT in [the Wheels section above](#wheels).

### orientation

Some combination of `portrait`, `landscape`, `portrait-reverse`, `landscape-reverse`. And `landscape` is `portrait` rotated 90 degrees counter clockwise.

The previous `all` option is no longer available, use:

```
orientation = portrait, landscape, portrait-reverse, landscape-reverse
```

**Note** As of 2023/01/28 a workaround for an api 31 issue, using KIVY_ORIENTATION, is no longer required.

### fullscreen

If the `fullscreen` value is `1` the Status Bar is not shown; defaults to `0`, Status Bar shown. When not shown, a swipe up/down will temporarily show the Status Bar. 

```
fullscreen = 0
```

### source.include_exts

If the app contains any data files, for example icons. Then add the file extension to this list:
```
source.include_exts = py,png,jpg,kv,atlas
```

### android.permissions

Research the Android permissions your app needs. List the permisions, for example:
```
android.permissions = INTERNET, CAMERA, BLUETOOTH_SCAN
```
Qualified permissions (only maxSdkVersion, or usesPermissionFlags) are supported by an extended syntax, for example:
```
android.permissions = INTERNET, CAMERA, (name=android.permission.BLUETOOTH_SCAN;usesPermissionFlags=neverForLocation)
```

If the app connects to a network you must include INTERNET permission.

### android.api

The current buildozer default is 31, but should be "as high as possible". 
```
android.api = 33
```

### android.minapi

The current default is 21.
For typical usage **don't change this**.

### android.ndk

The current default is 25b.
For typical usage **don't change this**.

### android.sdk

Is set by Buildozer to the newest supported.
For typical usage **don't change this**.

### android.archs

(previously android.arch)

Defaults to building both for ARM7 and ARM8. 
```
android.archs = armeabi-v7a, arm64-v8a
```
This is what you want when building for the Android Store. For debugging this is probably not what you want because it almost doubles the build time. Select one that matches your debug device.
```
android.archs = arm64-v8a
```
An install message INSTALL_FAILED_NO_MATCHING_ABIS means the apk was built for a different architecture than the phone or emulator.

# Debugging

## Get an Error Message

On the desktop your friends are the Python stack trace, and logging or print statements. It is no different on Android. To get these we [run the debugger](https://kivy.org/doc/stable/guide/android.html#debugging-your-application-on-the-android-platform).

First connect the Android device to a desktop via USB, on the Android device enable 'Developer Mode' and 'USB debugging'.

If Buildozer was run on a desktop you can use it's 'deploy run logcat' to install the app on the device.

If Buildozer was run on a virtual machine such as WSL or Colab then Buildozer may not be able to use the the physical USB port and the 'deploy run logcat' options will not work. In this case copy the `.apk` to a desktop OS, and use adb:

```
adb install -r myapp-0.1-arm64-v8a-debug.apk
```

For details on installing and using adb [see Appendix A](#appendix-a--using-adb).

Successful adb/Buildozer configuration is indicated by log output similar to:
```
List of devices attached
0A052FDE40019P  device
```
If 'List of devices attached' is followed by an empty line then the connection failed, regardless of what the Buildozer log says afterwards. Because either the device debug options are not set, or the debugger is run from a virtual machine that can't see a physical USB port.

In the logcat output search for 'Traceback', what follows is a Python stack trace, which usually indicates the cause of the issue. For example:
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

## Slow App Start

On the desktop if you start a Kivy app from a desktop icon, the app is slower to start than say from an IDE. This is because from the icon Python has to start first, on Android the same delay due to Python starting exists. On Android the splash screen is used to distract from this delay; the splash screen is a work around, not a cause of the delay.

If your app is *unusually slow* to start, it is because it is doing too much work in the `__init__()`, `build()` or `on_start()` methods. A common symptom of this is is a black screen *after* the splash screen has closed but before the app displays.

**This is your code, you can change this behavior.** Common causes are:
- monolithic `kv`
- compute intensive Python in the above methods
- blocking I/O operations in the above methods
- too many `autoclass()` statements

Monolithic `kv` is an example of doing too much work in the `build()` method. A solution for monolithic `kv` is to have a `kv` *file* for each screen and to instantiate the screen manager in Python. At `build()` only the first screen is added to the screen manager. Other screens are built and added after `on_start()`, either on demand or on some schedule. This is known as lazy loading.

Another common solution is to schedule any initial Python compute intensive, or I/O tasks to occur after `on_start()`, either on demand or on some schedule. And I/O operations must *always* be implemented in a non-blocking way, for example using threading.

Autoclass is expensive in startup time, if you have 10 or more autoclass statements this may be significant on older devices. The solution is to throw out most of that clever Pyjnius code you spent so long perfecting, and move the code to a Java class in a .java file. Then reference your Java with one or two autoclass in the usual way. Include your Java in the build with Buildozer's `android.add_src`.

There may be other causes, its your code.

# Android Hardware

## Camera

Use the 3rd party [Camera4Kivy Preview widget](https://github.com/Android-for-Python/Camera4Kivy#camera4kivy), and try its [examples](https://github.com/Android-for-Python/Camera4Kivy#examples).

Other cameras do not work on newer versions of Android; including OpenCV Camera, [Kivy Camera](https://kivy.org/doc/stable/api-kivy.uix.camera.html) and its derivatives such as [Xcamera](https://github.com/kivy-garden/xcamera).

## Keyboard

The relationship between the Android keyboard and the layout is somewhat configurable with [softinput_mode](https://kivy.org/doc/stable/api-kivy.core.window.html#kivy.core.window.WindowBase.softinput_mode), consider `Window.softinput_mode = 'below_target'`.

The type of keyboard can be set with `input_type` for example `TextInput(input_type = 'tel')`. Accepted values are 'text', 'number', 'url', 'mail', 'datetime', 'tel', or 'address'.

The `keyboard_suggestions` property does not work on all Android devices. For a workaround see https://github.com/kivy/kivy/blob/master/kivy/uix/behaviors/focus.py#L246-L250 . 

## Back Button and Gesture

The back button or gesture is used by Android to pause an app, It has historically been used to also navigate within a Kivy app as shown below.

Android 10 and up require that the back button/gesture can return the app to the Android home screen (pause an app).

Android 14 will show a preview of the 'Back target', this preview probably won't work inside Kivy apps. So using Back to navigate inside a Kivy app is proably going to be confusing to Android users.

To use for internal navigation, a back button/gesture can be detected with a test for key == 27, as shown in the [documentation](https://github.com/kivy/python-for-android/blob/develop/doc/source/apis.rst#handling-the-back-button). The key handler **must** return a boolean, which determines if the event is passed to the OS.

In order to use the button for internal navigation there must be a state in the app where back button/gesture is not consumed by the app. For example if the back button/gesture transitions between Kivy screens, from the "main" screen the app can on a back event go to the Android home screen:
```python
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

P4a provides Android specific utilities in the android package, this package is only available on Android. It is a Python interface to some Android OS apis, these apis only exist on Android devices and Android emulators. Which is why the package is only available on Android. The package is part of p4a and you will not find it standalone on PyPi.

The package is as they say 'self documenting', which really means there isn't any documentation. [Read the code](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes/android/src/android).

One (!) such utility is:

### BroadcastReceiver

A Python class to receive [Android Broadcasts](https://developer.android.com/guide/components/broadcasts). There are two examples [WiFi Scanner](https://github.com/Android-for-Python/BroadcastReceiver_examples/tree/main/WiFi_scanner_example) and [Bluetooth Scanner](https://github.com/Android-for-Python/BroadcastReceiver_examples/tree/main/Bluetooth_scanner_example).

To find a file containing a list of all the Android *system* Broadcast actions for a particular installed `android.api`: on a machine where Buildozer has been run, type `find ~/.buildozer -name broadcast_actions.txt`.

## Plyer

Plyer is an OS independent api for some non-POSIX OS features. See [Supported APIs](https://github.com/kivy/plyer#supported-apis). **Plyer is not well maintained**.

**Some Plyer modules and examples work on older Android versions, but not on newer Android versions. The Supported APIs table does not reflect this. For example Camera, Speech to text, Audio, and FileChooser do not work on newer Android versions.**

The [Plyer examples](https://github.com/kivy/plyer/tree/master/examples) are the documentation. 

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

[Pyjnus](https://github.com/kivy/pyjnius/tree/master/docs/source) allows import of Java code into Python code. It is an interface to the Java api and the Android api. The Android api is only available on Android devices and emulators, so Android api calls must be debugged on Android.

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

```python
from android import mActivity

    mActivity.getWindowManager()
    mActivity.getApplicationContext()
    mActivity.getSystemService( <service type> )
    mActivity.getContentResolver() 
```

Add your own Java to the package using the Buildozer options `android.add_src` and `android.add_jars`, see buildozer.spec. Java files should be organized in sub-directories reflecting the Java package hierarchy.

Add AndroidX Java packages (or other Maven packages) using the Buildozer option `android.gradle_dependencies`, see buildozer.spec and [Maven AndroidX Group](https://mvnrepository.com/artifact/androidx).

More challenging, it is also possible to write Java class implementations in Python using `PythonJavaClass`, [RTFM](https://github.com/kivy/pyjnius/blob/master/docs/source/api.rst#java-class-implementation-in-python) and [look at some examples](https://github.com/Android-for-Python/CameraXF-Example/blob/main/cameraxf/listeners.py). You will need to understand [Java signature format](https://github.com/kivy/pyjnius/blob/master/docs/source/api.rst#java-signature-format). This implementation is only visible in Python, Java classes cannot see it (though they can see the Java defined interface).

Note: some documentation examples are obsolete. If you see '.renpy.' as a sub field in an autoclass argument replace it with '.kivy.'.

### Pyjnius Performance

The `autoclass()` statement adds significant latency during the app start. If the app contains more than perhaps a dozen `autoclass()` calls you will probably notice the extra app startup time.

The way to address this is to create a Java file that references the Java classes that were referenced with autoclass(). Then reference your Java class with `autoclass()`. A Java file is included in the project using Buildozer's `android.add_src`. If your Java file is located at `<project>/src/org/me/myproject/my.java` then use `android.add_src = src` .

### Java Abstract Classes

It is not possible to import Java `abstract` classes or methods, as they have no `implementation` (abstract and implementation are Java keywords). And it it is not possible to provide the implementation in Python. You must write the implementation in Java and import that new class.

### Pyjnius Memory Management

You will be using two garbage collectors working on the same heap, but they don't know each other's boundaries. Python may free a local reference to a Java object because it cant see that the object is used. Obviously this will cause the app to crash in an ugly way. So use class variables, as shown below, to indicate persistence to the Python garbage collector.

```python
    ###### DONT DO THIS ####
    def foobar(self):
        # instance a Java class
        request = DownloadManagerRequest(uri)
        # call a method in that class
        request.setNotificationVisibility(visibility)    
```
```python
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

And if the app includes an `.aar` file, check that the version of Java used to build the aar is not newer than the version of OpenJDK you installed for Buildozer. If the aar is built with a newer Java you will get an error message similar to [this](#unsupported-class-file-major-version-62).

### Calling Python from Java

The following illustrates calling a Python method ( here called `from_java()`) from Java. In the example there is one argument which is a string. Java sees the method as having a different name, in this case `callback_string()`. 

The technique relies on passing the method inside a wrapper class. The `interface` of the wrapper class is defined in Java. And the `implementation` of this wrapper class is defined in Python. The wrapper class is initialized with the Python method to be called. Java calls a method inside an the same instance of the same wrapper class. 

You are going to have to write some Java, get over it. To comprehend the following code fragments; read from top to bottom, then follow the path of the string from bottom to top.

In Python:

```python
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

```java
package org.wherever.whatever;
// the wrapper interface
public interface CallbackWrapper {
    public void callback_string(String filepath);
}
```

The Java method generating the callback will be called from Java. The same Java need to reference the `CallbackWrapper` instance. Depending on the overall implementation you may need a Java wrapper to call the method and catch the result. Extra Java wrapper or not, call the Python like this:

SomeJavaClass.java

```java
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

### Java Listener Class

Sometimes the Java api has a listener class containing the Java callback method, and the approach in the previous section is not directly applicable.

In this case you will have to write a small amount of Java. Create a class that extends the required listener class, and initialize it with a CallbackWrapper class similar to the one in the previous section. The methods in your newly created subclass then call the callback wrapper methods.

The following code fragments are taken from the [speech recognizer example](https://github.com/Android-for-Python/speech_recognizer_example) where you can see the pieces assembled.

The custom Java listener, extends and overrides the default Java listener for the particular Java functionality:

```Java
public class KivyRecognitionListener implements RecognitionListener {

    private CallbackWrapper callback_wrapper;

    public KivyRecognitionListener(CallbackWrapper callback_wrapper) {	
	this.callback_wrapper = callback_wrapper;
    }       
    
    @Override
    public void onReadyForSpeech(Bundle bundle) {
	this.callback_wrapper.callback_data("onReadyForSpeech",""); 
    }

    @Override
    public void onRmsChanged(float v) {
	this.callback_wrapper.callback_data("onRmsChanged",String.valueOf(v)); 
    }
```

The Java part of the callback wrapper:

```Java
public interface CallbackWrapper {
    public void callback_data(String key, String value);
}
```

The Python part of the callback wrapper:

```Python
class CallbackWrapper(PythonJavaClass):
    __javacontext__ = 'app'
    __javainterfaces__ = ['org/kivy/speech/CallbackWrapper']

    def __init__(self, callback):
        super().__init__()
        self.callback = callback

    @java_method('(Ljava/lang/String;Ljava/lang/String;)V')        
    def callback_data(self, key, value):
        if self.callback:
            self.callback(key, value)

```

In Python, we then initialize the required Java functionality (in this case SpeechRecognizer) with our custom Java listener, and initialize our listener with an instance of the (PythonJavaClass) callback wrapper. In the case of SpeechRecognizer the listener is set with a method named `setRecognitionListener()`.

It is a characteristic of the SpeechRecognizer class that it must run on the Android UI thread, so we use the `@run_on_ui_thread` directive. 

```Python
    @run_on_ui_thread
    def create_recognizer(self, recognizer_event_handler):
        self.speechRecognizer = \
            SpeechRecognizer.createSpeechRecognizer(mActivity)
        self.callback_wrapper = CallbackWrapper(recognizer_event_handler)
        self.speechRecognizer.setRecognitionListener(
            KivyRecognitionListener(self.callback_wrapper))  # Java
```

Finally we handle the Java callbacks in Python.

Since in this case this method configures a Kivy UI Label widget, it must run on the Kivy main thread. We use the `@mainthread` directive, because the method was called from Java on the thread specified by `@run_on_ui_thread`. The "Android UI thread" is not the same thread as the thread on which Kivy UI widgets are updated.  

```Python
    @mainthread
    def recognizer_event_handler(self, key, value):
        if key == 'onReadyForSpeech':
            self.status.text = 'Status: Listening.' 
        elif key == 'onBeginningOfSpeech':
            self.status.text = 'Status: Speaker Detected.'
        elif key == 'onEndOfSpeech':
	    self.status.text = 'Status: Not Listening.'	    

```

For more details and context, see the full [speech recognizer example](https://github.com/Android-for-Python/speech_recognizer_example). In particular you will find the Python `autoclass` statements, and the Java `package` and `import` statements. Also the Java file hierarchy, and the `buildozer.spec` with the `android.add_src` directive.  

# Kivy Related Topics

## disable_multitouch

If you use `disable_multitouch` to disable that nasty red dot in a desktop Kivy app, do not use it on Android (or iOS) as it will result in duplicate touch events in some cases.

```python
from kivy.utils import platform
if platform not in ['android', 'ios']:
    from kivy.config import Config 
    Config.set('input', 'mouse', 'mouse, disable_multitouch')    
```

## Layout

A portable layout must be an *elastic* layout, because on a mobile device the Kivy window is defined by the screen and the screen changes between devices.

Setting widget properties such as size, size_hint, or orientation make the layout less elastic. But setting some of these layout constraints is required to achieve a particular layout, so portable layout is a balance between the two.

Mobile device screen resolution (dpi), screen orientation (landscape or portrait), and screen aspect ratio all impact the portability of a layout.

### Screen Resolution

Screen resolution can be addressed by specifying font size in units of sp, and widget size in units of dp. See [Kivy Metrics](https://kivy.org/doc/stable/api-kivy.metrics.html).

### Screen Orientation

Screen orientation on a desktop defaults to landscape, and on a mobile device usually defaults to portrait, but can be landscape or both. **Developing a layout in landscape will not provide a good layout in portrait.** Develop for your target orientation, or support both orientations as follows.  

For a mobile device the available orientation is set in buildozer.spec, `orientation` it can be some combination of `portrait`, `landscape`, `portrait-reverse`, `landscape-reverse`.

Supporting `orientation = <some list>` usually requires dynamically modifying layout parameters such as size, size_hint, or orientation. Do this using a Widget's `on_size()` method, and testing for orientation. [For example](https://github.com/Android-for-Python/c4k_photo_example/blob/main/applayout/photoscreen1.py#L34-L69). 

### Screen Aspect Ratio

Window aspect ratio on a desktop defaults to 4:3, but screen aspect ratio varies widely on mobile devices typically about 16:9 to 20:9. So a portable layout must be elastic. 

A truly elastic layout has only hints, and no sizes. In practice this may not look good in all cases. So we must specify the size of some widgets, but this requires the other widgets to be more elastic.

Geometry tells us there is no right answer. You can however localize issues by for example filling an AnchorLayout with a Label for background color, then centering a fixed size widget in this layout. There are probably many similar techniques, but as far as I know there is no documentation of these.

### Testing Portability

To test the portability of a layout using a desktop, set the required size and dpi on app start (change the numbers to whatever you want).
```
python3 main.py --size=420x720 --dpi=200
```
Manually making changes to this window size quickly explores many cases. This provides a sanity check that you really have a good layout design.

## Kivy Filechooser

Android storage is not organized in the same way as storage on a desktop, read about [Android Storage](#android-storage).

Kivy Filechooser may only be used with [Private Storage](#private-storage), and the `rootpath` property *must* be set to one of the three Private Storage locations. For example:

```
FileChooserListView(rootpath='.')   # install directory
```

```
from android import mActivity

    context = mActivity.getApplicationContext()
    result =  context.getExternalFilesDir(None)   
    if result:
        storage_path =  str(result.toString())
        FileChooserListView(rootpath=storage_path) # storage directory
```

For [Shared Storage](#shared-storage) use the Android Chooser. The [AndroidStorage4Kivy package](#androidstorage4kivy) provides a Python wrapper around the Android Chooser. Files in shared storage are referenced by a *content uri* not a *file path*, the package provides an api for copying between these. 


## KivyMD

The KivyMD widgets have the look and feel that Android users expect, but the Material Design rules mean you don't have the same flexibility as Kivy widgets.

**KivyMD has moved, it is now at [https://git.org.ru/KivyMD/kivymd](https://git.org.ru/KivyMD/kivymd). The previous location [https://github.com/kivymd/KivyMD](https://github.com/kivymd/KivyMD) is still there but will not be updated. The explaination is at [https://github.com/kivymd/KivyMD/blob/master/README.md#warning](https://github.com/kivymd/KivyMD/blob/master/README.md#warning).**

Be certain to use the same version of KivyMD on the desktop and with Buildozer, as the api may change with KivyMD versions.

The KivyMD [instructions for Buildozer](https://git.org.ru/KivyMD/kivymd#how-to-use-with-buildozer-https-github-com-kivy-buildozer) are updated for [KivyMD 2.1.1 with Buildozer](https://git.org.ru/KivyMD/kivymd#how-to-fix-a-shader-bug-on-an-android-device).

## Kivy Lifecycle

Follow the [Kivy Lifecycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle), it abstracts the app behavior that Android expects.

Do not place code in the app that interacts with Android 'script style', to be executed before the Kivy build() call.

`request_permissions()` must only be called from the App's `build()` method, and only one once with an argument that is a list of all required permissions.

The App's `on_stop()` method is not always called, use `on_pause()` to save state.

## Kivy Garden

[Kivy Garden](https://github.com/kivy-garden/) is a library of components ('flowers'). It consists of user contributions, and is mostly not maintained. Anybody who has had a garden knows a garden needs a gardener, Kivy Garden doesn't have one. Set your expectations accordingly.

For flowers that are maintained add them to your buildozer.spec requirements as  `kivy_garden.flower`, and in the app `from kivy_garden.flower import FlowerClass` replacing `flower` with the actual name, for example `xcamera`.

For flowers that are not maintained copy the flower code to your project and edit so that it builds. Note: `garden.matplotlib` is an example of a flower that is not maintained (also garden.matplotlib depends on matplotlib, this should be added to requirements).

There is a `#garden_requirements =` field in older buildozer.spec files. This is depreciated and should not be used.

# Creating a Recipe

## Why a Recipe

Some Python packages are not pure Python, they contain compiled code. This must be compiled for the specific platform.

Python packages are generally distributed in a wheel, this will contain pre-compiled binaries for supported platforms. Python wheels do not support Android, so to port a package to Android is must be explicitly compiled for ARM/Android.

P4a provides an api for creating package build scripts, such a script is called a "recipe". The task in creating a recipe is to port existing build instructions. To create a recipe you must understand both of these:

- The p4a recipe API.

- The existing build instruction for the package.

## Porting Overview

A recipe is simply a format required by p4a, it doesn't do much except set the environment variables for Clang, define some steps (environment, setup, build, install), and automate simple cases. 

The potentially hard and unique part is understanding what you want the package's build scripts to do, once you understand that then to automate within p4a's format is fairly simple.

Its easy to incorrectly focus one's attention on the p4a api when the real issue is understanding what you want the build scripts to do. As humans we hope the recipe will magically make our dreams come true, because the work of understanding a build in order to port it can be hard.

By analogy think of your self as an author of a recipe book, the easy part is formatting for the publisher (p4a recipe) . The potentially hard part is interpreting the chef's instructions (make, cmake, setup, configure.....), so you can modify the instructions for making the dish in a household kitchen. 

## P4A Recipe API

A recipe generally consists of two parts; a package build, and a package install. 

The recipe api attempts to automate common cases such as Cython or `setup.py`, but likely this will not be sufficient - you will have to dig deeper.

The [documentation](https://github.com/kivy/python-for-android/blob/develop/doc/source/recipes.rst) provides an overview. Your best resource is the [examples](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes).

## Porting Build Instructions

Build instructions could be a `setup.py`, `a shell script`, `Make`, `CMake`, `Bazel`, or something else. These instructions may be processor or OS specific, and usually depend on the GCC tools.

A port involves specifying the Android NDK supplied compiler tools (Clang and LLVM) in place of GCC tools; and patching any of the existing build scripts as necessary.

For background reading the [Build System Maintainers Guide](https://android.googlesource.com/platform/ndk/+/master/docs/BuildSystemMaintainers.md) provides some perspective of the Android build tools. 

## Recipe Support

This is full custom work. You can't schedule this, you are finished when the surprises stop. This task is for those who consider themselves self-supporting. Requests for help usually go unanswered because nobody will have experience of that specific issue in that context.

# Resources

## Read the Fine Manual

Really.

[Buildozer](https://github.com/kivy/buildozer/tree/master/docs/source)

[Python for Android](https://github.com/kivy/python-for-android/tree/develop/doc/source)

[Pyjnius](https://github.com/kivy/pyjnius/tree/master/docs/source)

Don't rely on other sources, they can be obsolete.

## Android for Python

[Android for Python Examples](https://github.com/Android-for-Python/INDEX-of-Examples) contains examples of some Android features as used from Python. These examples only run on Android.

## KivAds and KivMob
[KivAds](https://kivads.readthedocs.io/en/latest/index.html) is a Library that allows you to monetize your Kivy Apps using Google Admob. To use KivAds [minor modifications to p4a](https://github.com/Guhan-SenSam/KivAds/tree/v1.0.0#requirements) are currently required.

[KivMob](https://github.com/MichaelStott/KivMob) is a Library that allows you to monetize your Kivy Apps using Google Admob. The documentation for `android.gradle_dependencies` needs to be [updated](https://github.com/MichaelStott/KivMob/issues/115#issuecomment-1132929035).

## Android Notifications

[Android-Notification-in-Python](https://github.com/Guhan-SenSam/Android-Notification-in-Python)

## Android Billing

[In-app billing](https://github.com/shashi278/IABKivy)

## Other Resources

There are **a lot of useful features** to be found at these links:

[https://github.com/Sahil-pixel/Pykivdroid](https://github.com/Sahil-pixel/Pykivdroid)

[https://github.com/yunus-ceyhan](https://github.com/yunus-ceyhan)

[https://github.com/adywizard](https://github.com/adywizard)

[https://github.com/tshirtman](https://github.com/tshirtman)

[https://github.com/Kulothungan16/Example-Kivy-Apps](https://github.com/Kulothungan16/Example-Kivy-Apps)

[https://github.com/AM-ash-OR-AM-I/Passlock](https://github.com/AM-ash-OR-AM-I/Passlock)

[https://github.com/T-Dynamos/pyjnius-scripts](https://github.com/T-Dynamos/pyjnius-scripts)

[https://github.com/mp-007/kivy_matplotlib_widget](https://github.com/mp-007/kivy_matplotlib_widget)

# Release Builds

Setup signing *before* your release build.

## Setup signing

The official [Android signing overview is here](https://developer.android.com/studio/publish/app-signing.html).

For a Buildozer build, setting up signing consists of two steps:

 - Create a key, do this once for an app. You will use `keytool` for this. Save the keystore, you will need it in the future to update the app in the store.

 - Tell Bulldozer to sign the app using the key. You will set environment variables to pass the information about the key to Buildozer.   

[Follow the Kivy signing instructions](https://github.com/kivy/kivy/wiki/Creating-a-Release-APK) but don't just follow the instructions, read all the annotated comments by HeRo002. The instructions are flawed, but in combination with the comments they are good.

Here are some [very detailed signing instructions](https://gist.github.com/Guhan-SenSam/fa4ed215ef3419e7b3154de5cb71f641).

If you are updating an app in the store, you **must have** the same keystore that was previously used to submit the app to the store. Check that the key values are exported (see links above) as this is how the keys are passed to Buildozer.   

## Build the release

The Android Store requires that apps be built with a minimum API level of 31. Set
```
android.api = 33
```

To generate a multi-architecture apk or aab, `android.archs` specifies a list of architectures. (The legacy `android.arch` still works, but will be removed.)

```
android.archs = armeabi-v7a, arm64-v8a
```

A debug build always builds an `.apk`. A release build creates an `.aab` as a default. You can create a release `.apk` by setting:

```
android.release_artifact = apk
```

After changing buildozer.spec you must:

```
buildozer appclean
```

Build a release apk or aab with:

```
buildozer android release
```

To install an .aab locally use [Bundletool](#appendix-f--install-bundletool), in place of adb. 

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

You can also connect the device wirelessly using adb, see https://www.makeuseof.com/use-adb-over-wifi-android/  but this does not appear to work from WSL.


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

## Local Copy

Make a local copy of [p4a](https://github.com/kivy/python-for-android), but **not** in the `<project>` directory. In buildozer.spec specify where the local copy is located:

```
p4a.source_dir = /someplace/python-for-android-develop
```

Modify the local copy in a way that makes you happy. This will be cloned into `<project>/.buildozer` so remember to `appclean` after changes.

## From GitHub

As an alternative to making changes locally, you can reference changes saved on GitHub. The fork must be 'public' on Github. For example if you have a p4a fork on GitHub under `SomeGitHubName`, and optionally some branch `some_branch`:

```
p4a.fork = SomeGitHubName

p4a.branch = some_branch
```

# Appendix H : Cryptic Error Messages

  * [No module named 'msvcrt'](#no-module-named-msvcrt)
  * [Aidl not found](#aidl-not-found)
  * [Sdkmanager is not installed](#sdkmanager-is-not-installed)
  * [64-bit instead of 32-bit](#64-bit-instead-of-32-bit)
  * [EM_X86_64 instead of EM_AARCH64](#em_x86_64-instead-of-em_aarch64)
  * [No module named '_Socket'](#no-module-named-_socket)
  * [weakly-referenced object](#weakly-referenced-object)
  * [OpenCV requires Android SDK Tools](#opencv-requires-android-sdk-tools)
  * [No such file or directory: 'ffmpeg'](#no-such-file-or-directory-ffmpeg)
  * [Unsupported class file major version 62](#unsupported-class-file-major-version-62)
  * [Permission denied: '/storage/emulated/0/...'](#permission-denied-storageemulated0)
  * [ModuleNotFoundError: No module named 'PIL'](#modulenotfounderror-no-module-named-pil)
  * [Requested API target 27 is not available](#requested-api-target-27-is-not-available)
  * [BUILD FAILURE: No main.py(o)](#build-failure-no-mainpyo)
  * [/usr/bin/gzip: 1: ELF : not found](#usrbingzip-1-elf--not-found)
  * [SSL: CERTIFICATE_VERIFY_FAILED](#ssl-certificate_verify_failed)
  * [gradlew failed!](#gradlew-failed)
  * [android:exported](#androidexported)
  * [null pointer dereference](#null-pointer-dereference)
  * [No module named 'android'](#no-module-named-android)
  * [Hunk #1 FAILED](#hunk-1-failed)
  * [Kivy is too old](#kivy-is-too-old)
  * [C compiler cannot create executables](#c-compiler-cannot-create-executables)
  * [undefined macro LT_SYS_SYMBOL_USCORE](#undefined-macro-lt_sys_symbol_uscore)
  * [all is not a valid value for orientation](#all-is-not-a-valid-value-for-orientation)
  * [orientation have an invalid value](#orientation-have-an-invalid-value)
  * [build.gradle : 79: Unexpected input: '{'](#buildgradle--79-unexpected-input-)
  * [Could not resolve all files for configuration](#could-not-resolve-all-files-for-configuration)
  * [presplash-lottie: No such file or directory](#presplash-lottie-no-such-file-or-directory
)
  * [ssl module in Python is not available](#ssl-module-in-python-is-not-available)
  * [AttributeError: 'str' object has no attribute 'stdout'](#AttributeError-str-object-has-no-attribute-stdout)
  * [error: expression is not assignable](#error-expression-is-not-assignable)
  * [error: possibly undefined macro: AM_ICONV](#error-possibly-undefined-macro-am_iconv)
  * [error: failed to read PNG](#error-failed-to-read-png)
  * [Build failed: Requested API target 31 is not available](#build-failed-requested-api-target-31-is-not-available)
  * [aaudio_DetectBrokenPlayState](#aaudio_DetectBrokenPlayState)
  * ['config.pxi' not found](#configpxi-not-found)
  * [extra tap when single tap expected](#extra-tap-when-single-tap-expected)
  * [No module named 'kivy._clock'](#no-module-named-kivy_clock)
  * [ValueError: 'border_radius' must have 4, got 0](#valueerror-border_radius-must-have-4-got-0)

## No module named 'msvcrt'

`No module named 'msvcrt'` and `No module named '_posixsubprocess'`

Newer versions of setuptools break the p4a build tools.

`pip3 list | grep setuptools` will show a version >= 60

The workaround requires that you are using a venv.

```
pip3 uninstall setuptools
pip3 install setuptools==58.0.0
```

And [buildozer appclean](#changing-buildozerspec).

## Aidl not found

`# Aidl not found, please install it.`

Aidl is part of the Google tools. To get the tools you have to accept the Google license agreements.

Delete `~/.buildozer` and `<project>/.buildozer`, then `buildozer android debug` and **accept the Google license agreements**.

## Sdkmanager is not installed

`# sdkmanager path "/home/????/.buildozer/android/platform/android-sdk/tools/bin/sdkmanager" does not exist, sdkmanager is notinstalled`

Sdkmanager is part of the Google tools. To get the tools you have to accept the Google license agreements.

Delete `~/.buildozer` , then `buildozer android debug` and accept the Google license agreements.

## 64-bit instead of 32-bit

`ImportError: dlopen failed: "/data/data/org.test.myapp/files/app/_python_bundle/site-packages/cv2/cv2.so" is 64-bit instead of 32-bit`

The build process included the wrong binary file.

You can check this by changing arch from `armeabi-v7a` to `arm64-v8a`, `buildozer appclean`, and rebuild you will get a different error. This will say failed to link an x86_64 object, which is a more informative message.

The cause is an x86_64 binary from PyPl was used; because of a requirements specification error in buildozer.spec

One of the app requirements needs a recipe (or needs to be specified differently). See the [Wheels](#wheels) section.

In this case the requirements for OpenCV is incorrectly specified, it should be `opencv` because that is the recipe name.

If this message is `charset_normalizer` related, in Buildozer requirements set `charset-normalizer==2.1.1`.

And [buildozer appclean](#changing-buildozerspec).

## EM_X86_64 instead of EM_AARCH64

`ImportError: dlopen failed: "<something>/_python_bundle/site-packages/<somepackage>/_binding.so" is for EM_X86_64 (62) instead of EM_AARCH64 (183)`

The build process included the wrong binary file.

The cause is an x86_64 binary from PyPl was used; because of a requirements specification error in buildozer.spec 

One of the app requirements needs a recipe (or needs to be specified differently). See the [Wheels](#wheels) section.

If this message is `charset-normalizer` related, in Buildozer requirements set `charset-normalizer==2.1.1`.

And [buildozer appclean](#changing-buildozerspec).

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

## Unsupported class file major version 62

`[DEBUG]:          General error during semantic analysis: Unsupported class file major version 62 `

Version 62 Is Java 18. Error is probably due to an added jar built with Java 18. This is not going to work.

The Java version used by p4a is the version required for the gradle version used. This can be any version between openJDK-11 and 17, the install instructions suggest 17.

## Permission denied: '/storage/emulated/0/...'

`PermissionError: [Errno 13] Permission denied: '/storage/emulated/0/org.test.x' - Kivy on android write and save file`

This occurs on devices running Android 10 or higher. What was once known as "external strorage" is now "shared storage", and is a database not a file system. The app's "local storage" is now "private storage", private storage is still a file system. See the [Android Storage](#android-storage) section.

App file operations should occur in private storage. If you want to share the file then it must be 'copied' from private to shared storage, this requires a database 'create' operation.

The package [androidstorage4kivy](https://github.com/Android-for-Python/androidstorage4kivy) contains a `copy_to_shared()` method that implements the database create. Sample usage is [here](https://github.com/Android-for-Python/shared_storage_example/blob/main/main.py).

## ModuleNotFoundError: No module named 'PIL'

`ModuleNotFoundError: No module named 'PIL'`

The most common cases are:

Either 'pillow' was not specified in buildozer.spec [requirements](#requirements), in which case it must be added.

Or buildozer has been run with root permissions. Running Buildozer as root is known to cause [non deterministic behavior](#non-deterministic-behavior), this is one example. 

Simply running buildozer as user is not sufficent to fix the issue. Before running buildozer with user permissions you must first cleanup the corrupted state.

```
cd <project directory>
sudo rm -rf .buildozer 
sudo rm -rf ~/.buildozer
# and if either of these exist:
sudo rm -rf /.buildozer 
sudo rm -rf ~/.gradle   
```

Check also that your project files are owned by a user, and not by root.

## Requested API target 27 is not available

`[ERROR]:   Build failed: Requested API target 27 is not available, install it with the SDK android tool.`

In buildozer.spec set [android.api](#androidapi) to 33.

And [buildozer appclean](#changing-buildozerspec).

## BUILD FAILURE: No main.py(o)

`BUILD FAILURE: No main.py(o) found in your app directory`

Things to check:

The file containing your App class is named main.py.

The project directory is the current working directory.

There is no hidden directory (the name starts with a period) in the project path will cause this failure. This is a p4a error.

An app build error, try `python3 -m compileall main.py` to check for errors.

## /usr/bin/gzip: 1: ELF : not found

`/usr/bin/gzip: 1: ELF : not found`

This is due to regressive behavior in WSL 1, [ref](https://askubuntu.com/questions/1417255/trying-to-unzip-a-tgz-in-wsl-but-get-elf-not-found-error).

Check the current WSL version at the Windows Command prompt with `wsl -l -v`.

The fix is to upgrade to WSL 2.

## SSL: CERTIFICATE_VERIFY_FAILED

`[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate`

Add `certifi` to [requirements](#requirements), and possibly explicitly specify the location of the certificate.

```
import certifi

resp = request.urlopen(req, cafile=certifi.where())
```

And [buildozer appclean](#changing-buildozerspec).

## gradlew failed!

`[WARNING]: ERROR: whatever/.buildozer/android/platform/build-arm64-v8a_armeabi-v7a/dists/appname/gradlew failed!`

The Gradle error message is further up the log file. Search upwards for 'BUILD FAILED' or 'what went wrong' (without the ''), the error messages will be in this general area.

Generally Gradle errors are due to an incorrect Java version, Java usage errors, missing Java files, a Java jar [built with a newer](#unsupported-class-file-major-version-62) version of Java, missing Android Java packages, or misconfigured Android resource or configuration files. Gradle does not analyze Python errors.

And [buildozer appclean](#changing-buildozerspec).

## android:exported

`[DEBUG]: android:exported needs to be explicitly specified for element <whatever>`

This is a Gradle error message, it is about using an obsolete version of the Android package named in the message.

You can research Android package versions at [Maven](https://mvnrepository.com), and you will need a version that is compatible which whatever Python code you are using.

## null pointer dereference

`DEBUG : Cause: null pointer dereference`

This message is not from Python, it is from the Android run time system. Some Android api call has been corrupted. This is a memory corruption issue it may exhibit differently (or not at all) on different devices. Probably due to a misuse of Pyjnius, Plyer, or android_permissions.

**ALERT** There is an [issue on some devices with KivyMD 1.1.1](https://github.com/kivymd/KivyMD/issues/1393) that can cause a null pointer deference, this issue does not exist in KivyMD 1.0.2 . You will see an (unfiltered) backtrace like this:
```
NOTE:   /data/data/org.mac.tec2023/files/app/_python_bundle/site-packages/kivy/_event.so
DEBUG   :   NOTE:   /data/data/org.mac.tec2023/files/app/_python_bundle/site-packages/kivy/graphics/compiler.so
DEBUG   :   NOTE:   /data/data/org.mac.tec2023/files/app/_python_bundle/site-packages/kivy/graphics/instructions.so
NOTE:   /data/data/org.mac.tec2023/files/app/_python_bundle/site-packages/kivy/graphics/vbo.so
```
[To workaround, see the instructions for KivyMD 2.1.1](https://github.com/Android-for-Python/Android-for-Python-Users#kivymd).

Memory issues are incredibly hard to debug, the error may or may not be local to the symptom. So removing code from the app may just move the issue, not remove the issue. That doesn't mean don't cut your app down, it means it is not sufficent to see your app work - you must also understand which code of yours broke the app.

Some possibilities:

 - A corrupted build [try an appclean](#changing-buildozerspec). This probably it not the issue but if it is you'll save a lot of work.

 - Pyjnius code that does not take into account the use of two garbage collectors, [modify your code](#pyjnius-memory-management).

 - Plyer or android_permissions calls that do not occur in the 'App functions' block of the [Kivy Lifecycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle). Modify your code.

 - It is also possible that an [impure Python package that has no recipe](#wheels) could generate this error; though [other error messages](#em_x86_64-instead-of-em_aarch64) [or](#64-bit-instead-of-32-bit) are more likely.

And [buildozer appclean](#changing-buildozerspec).

## No module named 'android'

`ModuleNotFoundError: No module named 'android'`

The android package is only available on Android. This error occurs on a desktop, because there is no Android api implementation. The android package is only available on Android, and is installed by p4a.

Perhaps you want platform specific code:

```python
from kivy.utils import platform
if platform == 'android':
    import android
```

## Hunk #1 FAILED

```
STDOUT:
patching file somefile
Hunk #1 FAILED
```

Usually this is due to [version pinning](#version-pinning) a compile recipe, the error is due to pinning a version that is incompatible with the compile recipe.

Remove the version pin, or locally modify the recipe.

And [buildozer appclean](#changing-buildozerspec).

## Kivy is too old

`Exception: The version of Kivy installed on this system is too old. (You have 2.1.0, but the application requires 2.2.0)`

This message comes from KivyMD, and is seen on all platforms. The message may not be seen and you may see an Android crash (hidden by the Python log filter), related to graphics.

Use Kivy 2.2.0, for the version of KivyMD to use, contact KivyMD support.

See also https://github.com/kivymd/KivyMD/blob/master/README.md#how-to-fix-a-shader-bug-on-an-android-device 

And [buildozer appclean](#changing-buildozerspec).

## C compiler cannot create executables

`[DEBUG]:   configure: error: C compiler cannot create executables`

May occur when building `libffi`.

This can be due to an interaction between WSL 1 and Clang in NDK 25b.

Options: 

 - Upgrade to WSL 2

 - Patch Clang as described here https://github.com/kivy/buildozer/issues/1543#issuecomment-1382574110 (EXPERTS ONLY)

Ref : https://github.com/microsoft/WSL/issues/8681  https://github.com/kivy/buildozer/issues/1543

And [buildozer appclean](#changing-buildozerspec).

## undefined macro LT_SYS_SYMBOL_USCORE

`[DEBUG]:        configure.ac:215: error: possibly undefined macro: LT_SYS_SYMBOL_USCORE`

Re-check that the Buildozer dependencies are installed https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst#android-on-ubuntu-2004-and-2204-64bit

And [buildozer appclean](#changing-buildozerspec).

## all is not a valid value for orientation

`[app] "all" is not a valid value for "orientation"`

As of Buildozer 1.5, in buildozer.spec
`orientation = all`
must be replaced with:
`orientation = portrait, landscape, portrait-reverse, landscape-reverse`

## orientation have an invalid value

`[app] "orientation" have an invalid value`

Upgrade to Buildozer 1.5

## build.gradle : 79: Unexpected input: '{' 

`build.gradle': 79: Unexpected input: '{' @ line 79, column 14.`

As of Buildozer 1.5 the values of the gradle_dependencies option must not be in quotes. The previous approach was a security risk.

For example change:

`android.gradle_dependencies = "org.tensorflow:tensorflow-lite:+",'org.tensorflow:tensorflow-lite-support:0.0.0-nightly'`

to

`android.gradle_dependencies = org.tensorflow:tensorflow-lite:+,org.tensorflow:tensorflow-lite-support:0.0.0-nightly`

And [buildozer appclean](#changing-buildozerspec).

## Could not resolve all files for configuration

```
Could not resolve all files for configuration ':debugRuntimeClasspath'.
[DEBUG]:           > Could not find "com.google.mlkit:face-detection:16.0.6".

```

As of Buildozer 1.5 the values of the gradle_dependencies option must not be in quotes. The previous approach was a security risk.

In the above example change:

`
android.gradle_dependencies = "com.google.mlkit:face-detection:16.0.6"
`
to
`
android.gradle_dependencies = com.google.mlkit:face-detection:16.0.6
`

And [buildozer appclean](#changing-buildozerspec).

## presplash-lottie: No such file or directory

```
'--presplash-lottie', "/Home/user/dir/'.pics/4.json'",
......
FileNotFoundError: [Errno 2] No such file or directory: "/Home/user/project/'.pics/4.json'"
```

As of Buildozer 1.5 the values of the Lottie file path option must not be in quotes. The previous approach was a security risk.

Change:
`
android.presplash_lottie = "./pics/4.json"
`
to
`
android.presplash_lottie = ./pics/4.json
`

And [buildozer appclean](#changing-buildozerspec).

## ssl module in Python is not available

`WARNING: pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.`

Re-check that the Buildozer install instructions were followed https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst#android-on-ubuntu-2004-and-2204-64bit

And [buildozer appclean](#changing-buildozerspec).

## AttributeError: 'str' object has no attribute 'stdout'

```
File ".buildozer/android/platform/python-for-android/pythonforandroid/build.py", line 35, in get_targets
    targets = avdmanager('list', 'target').stdout.split('\n')
AttributeError: 'str' object has no attribute 'stdout'
```

Occurs because the default behavior of the Python `sh` package changed.

Workaround: in buildozer.spec set `p4a.branch = develop`.

And [buildozer appclean](#changing-buildozerspec).

## error: expression is not assignable

There are two different causes, pick the one that applies:

```
jnius/jnius.c:55290:5: error: expression is not assignable
    ++Py_REFCNT(o);
```

Upgrade Cython `pip3 uninstall cython`, `pip3 install -U cython`.

And [buildozer appclean](#changing-buildozerspec).

```
c/_cffi_backend.c:407:23: error: expression is not assignable
        Py_REFCNT(ct) = 43;
```

Set `p4a.branch = develop`

And [buildozer appclean](#changing-buildozerspec).

## error: possibly undefined macro: AM_ICONV

```
configure.as:109: error: possibly undefined macro: AM_ICONV
```

Install gettext or gettext-dev
```
sudo apt-get install gettext
```

And [buildozer appclean](#changing-buildozerspec).

## error: failed to read PNG

```
 ERROR:/content/.buildozer/android/......../src/main/res/mipmap/icon.png: AAPT: error: failed to read PNG signature: file does not start with PNG signature.
```

Set `icon.filename` to a PNG file.

And [buildozer appclean](#changing-buildozerspec).

## Build failed: Requested API target 31 is not available

```
[ERROR]:   Build failed: Requested API target 31 is not available, install it with the SDK android tool.
```

Buildozer defaults to a version of the Android tools that is no longer available, increase [android.api](https://github.com/Android-for-Python/Android-for-Python-Users#androidapi) to 33

And [buildozer appclean](https://github.com/Android-for-Python/Android-for-Python-Users#changing-buildozerspec).

## aaudio_DetectBrokenPlayState

```
/data/app/org.test.myapp-gVqZaGUKP2suWwYkosnKNQ==/lib/arm/libSDL2.so (aaudio_DetectBrokenPlayState+72) 
```

Add this code at the very top of your main.py

```python
import os
from kivy.utils import platform
if platform == "android":
     os.environ["SDL_AUDIODRIVER"] = "android"
```

## 'config.pxi' not found

```
jnius/jnius.pyx:100:0: 'config.pxi' not found
```

First check for a corrupted Buildozer database: [buildozer appclean](#changing-buildozerspec) and build again.

If that does not fix it, **the Java install is probably mis-configured**. If you are using a VM and you installed Java inside, don't do this.

If you are using Gentoo, check that the Java VM is configured for [the correct JDK](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst#android-on-ubuntu-2004-and-2204-64bit) using [eselect](https://wiki.gentoo.org/wiki/Java#Configuring_the_Java_Virtual_Machine).

To reset the Java install: Uninstall all Java packages (this is important, don't skip it), and install the JDK as shown in the [Buildozer install instructions](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst#android-on-ubuntu-2004-and-2204-64bit).

And [buildozer appclean](#changing-buildozerspec).

## extra tap when single tap expected

This is unexpected behavior, not an error message. It is due to using the Kivy `disable_multitouch` feature on a mobile device. Kivy multitouch creates a nasty red dot on the screen of a desktop, it is commonly disabled with `disable_multitouch`. The red dot does not occur on a mobile device, and should not be disabled on a mobile device.

[Do this](#disable_multitouch).

## No module named 'kivy._clock'

```
05-22 01:24:23.273 10977 12969 I python : ModuleNotFoundError: No module named 'kivy._clock'
```

Follow the [Buildozer install instructions](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst#android-on-ubuntu-2004-and-2204-64bit).

And [buildozer appclean](#changing-buildozerspec).

## ValueError: 'border_radius' must have 4, got 0

```
File "kivy/graphics/boxshadow.pyx", line 316, in kivy.graphics.boxshadow.BoxShadow._check_iter
I   ValueError: 'border_radius' must have 4, got 0
```

or

```
File "kivy\graphics\boxshadow.pyx", line 311, in kivy.graphics.boxshadow.BoxShadow._check_iter
TypeError: 'spread_radius' accepts only list/tuple, got <class 'float'>

```

This is a KivyMD issue, please ask that project.

Background:

In Kivy border_radius (introduced in 2.2.0) is a 4-tuple. https://github.com/kivy/kivy/blob/2.2.0/kivy/graphics/boxshadow.pyx#L53

The version of KivyMD you are using appears to be using it as a scalar. Ask the KivyMD folks what version of their code to use. 

A quick look at the versions shows KivyMD 1.1.1 https://github.com/kivymd/KivyMD/blob/1.1.1/kivymd/uix/behaviors/elevation.py does not use border_radius. For any side effects of using this version contact KivyMD.
