
Android for Python Users
========================

*An unofficial Users' Guide*

Revised 2022-10-19

# Table of Contents

- [Introduction](#introduction)
- [What is Different about Android?](#what-is-different-about-android)
  * [Posix](#posix)
  * [Wheels](#wheels)
  * [Meta-information](#meta-information)
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
  * [asyncio](#asyncio)
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
  * [Some buildozer.spec options](#some-buildozerspec-options)
    + [package.name](#packagename)
    + [package.domain](#packagedomain)
    + [requirements](#requirements)
      - [requirements basics](#requirements-basics)
      - [Find the Dependencies](#find-the-dependencies)
      - [Kivy Widget Dependencies](#kivy-widget-dependencies)
      - [Requirements Examples](#requirements-examples)
      - [Pure Python](#pure-python)
    + [source.include_exts](#sourceinclude_exts)
    + [android.permissions](#androidpermissions)
    + [android.api](#androidapi)
    + [android.minapi](#androidminapi)
    + [android.ndk](#androidndk)
    + [android.archs](#androidarchs)
- [Debugging](#debugging)
    + [Get an Error Message](#get-an-error-messge)
    + [Slow App Start](#slow-app-start)
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
    + [Screen Resolution](#screen-resolution)
    + [Screen Orientation](#screen-orientation)
    + [Screen Aspect Ratio](#screen-aspect-ratio)
    + [Testing Portability](#testing-portability)
  * [KivyMD](#kivymd)
  * [Kivy Lifecycle](#kivy-lifecycle)
  * [Kivy Garden](#kivy-garden)
- [Creating a Recipe](#creating-a-recipe)
  * [Why a Recipe](#why-a-recipe)
  * [P4A Recipe API](#p4a-recipe-api)
  * [Porting Build Instructions](#porting-build-instructions)
  * [Recipe Support](#recipe-support)
- [Resources](#resources)
  * [Read the Fine Manual](#read-the-fine-manual)
  * [Android for Python](#android-for-python)
  * [KivAds and KivMob](#kivads-and-kivmob)
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

# Introduction

Python and Kivy are portable across operating systems because of POSIX, Python wheels, and pip. However Android is not POSIX compliant, wheels are not usually available for Android, and pip is not installed on Android. Clearly many apps won't 'just work' on Android. The document is about porting a Kivy app to Android.

For a well written app that only paints the screen, and does nothing else, building for Android will be 'push button'. One can for example build 'Hello World' with the default 'buildozer.spec' file. Of course this does not describe many apps.

This document is not a substitute for Reading The Fine Manual [(RTFM)](https://en.wikipedia.org/wiki/RTFM). Your basic resources are [Buildozer](https://github.com/kivy/buildozer/tree/master/docs/source), [Python for Android](https://github.com/kivy/python-for-android/tree/develop/doc/source), and [Android](https://developer.android.com/guide).

Python-for-Android is a truly amazing achievement, but there are some details to understand. The alternative is Java and the Android api, which has been shown to induce insanity in laboratory mice. 

Lastly this document is my understanding as of the date above. I am not a developer in any of the Kivy sub projects (except for a few PRs). I have created a suite of [Android orientated examples](https://github.com/Android-for-Python/INDEX-of-Examples).

The document is guaranteed to be incomplete is some way, and may possibly be wrong in another way. But reading it will hopefully provide you with come context with which to read the official documentation and to experiment. Those two steps are the key to insight and understanding, which in turn is key to making your idea real.

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

However in the storage model described here storage is either *Private Storage* or *Shared Storage*. **Private storage content is only visible to the app that created it, shared storage is visible to all apps. Python file operations can only be performed in Private Storage**.

## Private Storage

An app can perform Python file operations (read, write, shutil) on its private storage. There are three usable storage location: the app install directory, the app storage directory, and the app cache directory.

No permissions are requires to read or write an app's private storage. [More on permissions below](#app-permissions).

Do not confuse private with secure, on older Android versions it is possible for other apps to read private storage. And more generally Android Studio allows inspection.

### App Install Directory

The install directory is `./`, files from the apk or aab can be accessed. An app update will overwrte this directory so this is not a good place to save files (though you can).

### App Storage Directory

Most of the time this is what you should use. 

The app storage directory is persistent over the installed lifetime of the app, and is removed when the app is uninstalled. The app storage directory is accessed using:
```python
from android.storage import app_storage_path

    app_storage_directory_path = app_storage_path()
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

On devices running Android 10 and later shared storage is implemented as a database, you cannot access shared storage with Python file operations because there is no file path. The Kivy and KivyMD file choosers do not work, you must use the Android Chooser.

On devices with Android less than 10, shared storage is a file system and you can access file with `from android.storage import primary_external_storage_path`. But this **does not work** on devices running Android 10 or greater.

On devices running Android 10 or greater shared storage is accessed via the Java MediaStore api, or the Chooser api. 

### MediaStore

The MediaStore is a database, not a file system. The MediaStore is organized based on multiple root directories. For example 'Music', 'Movies', 'Pictures', 'Documents', and 'Downloads'.

The MediaStore is accessed by copying a file to or from private storage. Because MediaStore files have no file path that Python requires to access a file.

"Are you telling me I can't ....?", I'm explaining that Android shared storage is different from our expectations, evolve or die out.

### AndroidStorage4Kivy

An Android version independent Python api for shared storage is implemented in the package [androidstorage4kivy](https://github.com/Android-for-Python/androidstorage4kivy).

The usage model is, files can be copies to, copied from, and deleted from shared storage. For more details see the SharedStorage class documentation.

Example usage is in [shared_storage_example](https://github.com/Android-for-Python/shared_storage_example). The example also demonstrates using the Android file Chooser, using the SharedStorage4Kivy Chooser class.

### Storage Permissions

On devices running Android 10 and later no permissions are requires to read or write an app's own shared storage. Reading another app's shared storage requires READ_EXTERNAL_STORAGE permission. An app cannot overwrite another app's file.

There is one special case, an app cannot read another app's file in the Downloads directory, regardless of permissions.

On devices running Android 9 and less, WRITE_EXTERNAL_STORAGE is required for any file writes. Or READ_EXTERNAL_STORAGE if the app only want to do shared storage reads.

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

## asyncio

### basic asyncio usage

Without special handling Kivy and asyncio would block one another on a single task OS like Android or iOS, and have OS dependent behavior on a multitasking OS. So we always start a Kivy app that interacts with asyncio as an asyncio coroutine. 

We normally start a Kivy app with:

```python
ExampleApp().run()
```

For basic asyncio usage we start Kivy with:

```python
asyncio.run(ExampleApp().async_run('asyncio'))
```

The Kivy App class defines a *coroutine* `async_run()` which starts Kivy. This starts two interleaved event loops, a Kivy routine loop and an asyncio co-routine loop.

Kivy is constructed of routines not of co-routines, the rules for calling co-routines from routines apply. This means Kivy abstractions such as bind or properties use routines not co-routines.

For portability the asyncio usage must mimic the Kivy lifecycle. Generally async IO calls must only occur while the Kivy clock is running.

An app will terminate when **both** loops terminate. One loop may terminate before the other, the app code must handle any cases that arise due to the app design. Generally try/except is the way to do this, alternatively use Kivy lifecycle state.

### lifecycle asyncio usage

The following describes controling asyncio state based on Kivy lifecycle state. 

A Kivy app that runs concurrently with an asyncio loop can be started with:

```python
async def main(app):
    await asyncio.gather(app.async_run('asyncio'),  # starts Kivy
                         app.async_lifecycle(),     # starts other coroutine
                         return_exceptions = True)  # for debugging
asyncio.run(main(ExampleApp()))
```

On Android an async loop that uses IO can only be active when the Kivy clock is ticking, and after any required user permissions have been granted. On the desktop an async loop must be explicitly terminated `on_stop()`.

For Android a simple implementation of `async_lifecycle()` might be:

```python
    def __init__(self):
        super().__init__()
        self.kivy_clock_running = False

    # Kivy lifecycle

    def on_start(self):
        # see AndroidPermissions example referenced in "User permissions"
        self.dont_gc = AndroidPermissions(self.start_app)  

    def start_app(self):
        self.dont_gc = None
        self.kivy_clock_running = True

    def on_pause(self):
        self.kivy_clock_running = False

    def on_resume(self):
        self.kivy_clock_running = True

    # Coroutines

    async def async_lifecycle(self):
        while True:  # assume recurring async behavior
            await self.kivy_clocking()
            await self.some_async_behavior()

    async def kivy_clocking(self):
        while not self.kivy_clock_running:
            await asyncio.sleep(1/30)

    async def some_async_behavior(self):
        # Catch any I/O exceptions
        try:
            # Replace with your async behavior
            # proxy for latency of your async behavior
            await asyncio.sleep(5) 
            # proxy for your async behavior result
            self.label.text = 'greetings earthlings'  
        except:
            # Handle if you want to.
            pass	    

```

Note that on a desktop this code will not exit, because `async_lifecycle()` never completes (this doesn't matter on Android because we handled the `on_pause` case). To additionally support exit on the desktop:

```python
    # in __init__()
        self.kivy_running = True	

    def on_stop(self):
        self.kivy_running = False

    async def async_lifecycle(self):
        while self.kivy_running:   # assuming a recurring async behavior
            await self.kivy_clocking()
            tasks = [asyncio.create_task(self.some_async_behavior()),
                     asyncio.create_task(self.kivy_stopped())]
            await next(asyncio.as_completed(tasks))
            for task in tasks:
                if not task.done():
                    task.cancel()

    async def kivy_stopped(self):
        while self.kivy_running:
            await asyncio.sleep(1/30)
```

# Android Service

An [Android Service](https://developer.android.com/guide/components/services) performs operations that are not related to the UI. In the context of Kivy, an Android service is a Python script.

There are two Kivy examples [Kivy Service Osc](https://github.com/tshirtman/kivy_service_osc), and [Mutli-Service Example](https://github.com/Android-for-Python/Multi-Service-Example). OSC is a good package for message passing between app and service. However it is not designed for passing large datas, consider using the file system in this case. OSC requires INTERNET permission.

## Specifying a Service 

We assign the service a name, and associate this with script file name in `buildozer.spec`. *The service name must be a valid Java class name.* In the sample below `the_service.py` is the name of the script, and `Worker` is the name we give the service.

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

There is an api for restarting a service killed by the OS [`setAutoRestartService()`](https://github.com/kivy/python-for-android/blob/develop/doc/source/services.rst#service-auto-restart). This is only meaningful with a background service, in this case it is probably better to use a foreground service.

### Background Service

A background service is generally short lived as defined by the OS, it is specified in buildozer.spec with:

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

### Sticky Foreground Service.

A sticky foreground service lifetime is nominally unconstrained. An app may stopand start (not just pause) and resume communication with a sticky foreground service. **An app is responsible for terminating a sticky foreground service, specially if the service is energy intensive.** A sticky foreground service is specified in buildozer.spec with:

```
# (list) List of service to declare
services = Worker:the_service.py:foreground:sticky

# (list) Permissions
android.permissions = FOREGROUND_SERVICE
```
A notification icon may be created in the task bar, see next section.

Because an app may stop and restart while a sticky foreground service is running, the app may not contain valid dynamic information about the service. Importantly a reference to the instance of the service saved in a class variable will be `None` when the app restarts. The restart-stop code above re-initializes the service to obtain a new reference to the service so that it can be stopped.   

## Service Notifications

For build api < 33 a foreground service always places a notification icon in the task bar. Depending on version there may be a few second delay in the appearance of the icon, this delay is an Android 'feature' and does not indicate that the foreground service has not started.

For build api >= 33 a foreground service only places a notification icon in the task bar if POST_NOTIFICATIONS permission is requested and granted.

The default icon displayed in the task bar is derived from the app icon, and for the Kivy icon it is a white circle. There is no api that will change this, or example of how to change this.  

## p4a Service Implementation

If you want to understand the implementation of services in more detail, read the code: [PythonService.java](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/src/main/java/org/kivy/android/PythonService.java), [Service.tmpl.java](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/templates/Service.tmpl.java), and [build.py](https://github.com/kivy/python-for-android/blob/develop/pythonforandroid/bootstraps/common/build/build.py).

# App Permissions

Android restricts access to many features. An app must declare the permissions it requires. There are two different declarations, manifest and user. User permissions are a subset of manifest permissions. The [full list of permissions](https://developer.android.com/reference/android/Manifest.permission) is documented by Google. In general you must research the permissions needed by your app, resist the temptation to blindly guess.

## Manifest permissions

Manifest permissions are declared in the buildozer.spec file. Common examples are  CAMERA, INTERNET, READ_EXTERNAL_STORAGE, RECORD_AUDIO. Apps that scan Bluetooth or scan Wifi may require multiple permissions. 

Python for Android always enables manifest WRITE_EXTERNAL_STORAGE permission. WRITE_EXTERNAL_STORAGE implies READ_EXTERNAL_STORAGE. [WRITE_EXTERNAL_STORAGE is never required](https://developer.android.com/training/data-storage#permissions) for devices running api >= 30.

## User permissions

Any app manifest permission documented as having "Protection level: dangerous" additionally requires a user permission. The four listed above are all "dangerous". User permission requests are seen as the dialog that Android apps present to the user. From Python this is initiated with a `request_permissions()` call.

Many old examples show request_permissions() at the top of main.py, on newer versions of Android this will lead to unexpected behavior. Because it violates the [Kivy Lifecycle](https://kivy.org/doc/stable/guide/basic.html#kivy-app-life-cycle).

One easy approach is to copy [the `AndroidPermissions` class](https://github.com/Android-for-Python/c4k_photo_example/blob/main/android_permissions.py) which encapsulates permission behavior, and modify the actual permissions for your app. Then instantiate the class like this:
```python
    def on_start(self):
        self.dont_gc = AndroidPermissions(self.start_app)  

    def start_app(self):
        self.dont_gc = None
```

Note that the App class variable `dont_gc` delays garbage collection and it critically important.

That example shows all permissions requested at the start of app execution. Permissions can also be requsted individually when needed by the app. However a permission request must not be initiated while another permission request is active.

More generally in a Kivy App, the constraints in using `request_permissions()` are that it may **only** be called from the `build()` method, or from one or more timestep after `on_start()`. There can **only be one** such call in the `build()` method, or only one call in any given timestep. Calling after on_start() simplifies the logic for handling both the 'request' case and the 'previously granted' case.

Finally it is normal Android behavior that if a user denies permission, it may not be possible to grant that permission from the App. Grant the permission from the Android Settings panel for the app.  

# Buildozer and p4a

## Install

Buildozer runs on Linux, Windows users need a Linux virtual machine such as WSL, Colab, or VirtualBox to run Buildozer. Buildozer also runs on a Mac, but the number of users is small and this is reflected in the availability of help and in software maturity.

[Install documentation](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst), RTFM, really.

**Test your Buildozer install by building (`buildozer android debug`) and running [Hello World](https://kivy.org/doc/stable/guide/basic.html#create-an-application) with the default `buildozer.spec` (create this with `buildozer init`). I know you just want to see your app run on Android, but this simple first step will provide you will a framework to address any future issues.**

### Most Common Issues

Errors during a Buildozer build are usually because the user:

* Failed to [read the install instructions](https://github.com/kivy/buildozer/blob/master/docs/source/installation.rst), and so failed to install a dependency.

* Failed to accept the Google License Agreements.

* Failed to correctly specify requirements.

* Failed to check if there is a recipe for an impure Python package.

### Non-deterministic Behavior

Buildozer's behavior can be non-deterministic in any of these cases:

* It is run as root.

* It is run on an NTFS partition mounted on a Linux system. WSL users, don't build your app on the Windows partition (`/mnt/c/Users....`). Because some Python packages implement OS specific behavior based on the disk root name.

* There are Python style trailing comments in the buildozer.spec file. Comment characters must be the first character on a line in the buildozer.spec file.

## Changing buildozer.spec

Note that Buildozer allows *specification* of build files, versions, and options; but unlike most other build tools it *does not do version management*. If buildozer.spec is changed the change probably *won't* propagate into the apk on the next build. After changing the buildozer.spec file users *must* do an appclean.
```
buildozer appclean
buildozer android debug
```
There may be some exceptions to this, the only one I know to be safe is one can add (but not change version of, or remove) a package in the [requirements](#requirements) list without the appclean.

There is no magic universal buildozer.spec, its configuration depends on the functionality of your app. 

## What should I change?

Generally change as few options as possible; resist the temptation to overspecify, you will over constrain the implementation. 

 - If the app is for you, change the `title` and `package.name`.

 - If the app is for the store, also change `package.domain`.

 - If the app includes data files, add any necessary file extensions in `source.include_exts`.

 - If the app uses Python packages that are normally installed with `pip3`, add these and their dependencies to `requirements`.

 - You want pretty? add `presplash.filename` and `icon.filename`.

 - Add the required [Android Permissions](#app-permissions) to `android.permissions`.

 - If the app is for the store you will need to increase the default `android.api`.

 - Want to speedup debug builds? Remove one of the elements in `android.archs`.

There are a lots of other options about tool versions, Java, and stuff; most users can and should ignore these. 

## Some buildozer.spec options

[RTFM](https://github.com/kivy/buildozer/blob/master/docs/source/specifications.rst), really. And see the [KivyMD section](#kivymd).

### package.name

This must contain only alpha numeric characters, do not use any other characters.

### package.domain

This must contain exctly one period (.) surrounded by alpha numeric characters, do not use any other characters. Anthing else will cause a Gradle fail.

### requirements

#### requirements basics

This is the list of pip packages (and possibly versions) that your app imports from.

**In addition** Buildozer needs to know the packages your packages depend on (because there is no pip3 on Android). Determining these is [shown in the next section](#find-the-dependencies).

Do not add Python system modules, only packages you might install with pip3 on the desktop. 

There are some pip3 packages that are added automatically, no need to put these in requirements: `libffi, openssl, sqlite3, setuptools, six, pyjnius, android, certifi`.

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
google-cloud-firestore, googxle-api-core, google-auth, cachetools, pyasn1-modules, pyasn1, rsa, googleapis-common-protos, protobuf, requests, charset-normalizer, idna, urllib3, google-cloud-core, proto-plus
```

*However*, this technique is only as good as the package information. And exhibits platform variations. For example if this is run on Linux the `requests` dependencies `certifi, charset-normalizer, idna, urllib3` will not be listed. These missing dependencies will be found during debugging.  

#### Kivy Widget Dependencies

Some Kivy widgets have requirement dependencies:

`kivy.network.urlrequest` needs  `requests, urllib3, charset-normalizer, idna, certifi`

`kivy.uix.video` needs `ffpyplayer`

`kivy.core.audio.SoundLoader` needs `ffpyplayer, ffpyplayer_codecs` 


#### Requirements Examples

For examples [see](https://github.com/Android-for-Python/INDEX-of-Examples#readme).

Using the technique described above, we get the following:

##### Pyrebase4
```
requirements = python3,kivy, pyrebase4, gcloud, googleapis-common-protos, protobuf, httplib2, pyparsing, oauth2client, pyasn1, pyasn1-modules, rsa, pycryptodome, python-jwt, jws, requests, certifi, charset-normalizer, idna, urllib3, requests-toolbelt , jwcrypto, cryptography, deprecated, wrapt
```
Run on Windows, the last four items were not determined automatically. Run on Linux the requests dependencies were missing. In both cases these were added during debugging.

##### firebase_admin
```
requirements = python3,kivy, firebase-admin, cachecontrol, msgpack, requests, certifi, charset-normalizer, idna, urllib3, google-api-core, google-auth, cachetools, pyasn1-modules, pyasn1, rsa, pyasn1, googleapis-common-protos, protobuf, google-api-python-client, google-auth-httplib2, httplib2, pyparsing, uritemplate, google-cloud-firestore, google-cloud-core, proto-plus, google-cloud-storage, google-resumable-media, google-crc32c
```

#### Pure Python

The packages you add here **must be pure Python, or have a recipe** [in this list](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes). If this is not the case, the options are to:

* Rewrite the app

* Locally modify an existing recipe [see Appendix C](#appendix-c--locally-modifying-a-recipe).

* [Create a new recipe](#creating-a-recipe).

* Import the functionality from Java.

None of these options are trivial. That is why it said AVOID DISAPPOINTMENT in [the Wheels section above](#wheels).

If you have a problem run the [debugger](#debugging).

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
android.api = 33
```

### android.minapi

Python for Android enables `android.minapi = 21`. Don't decrease this.

### android.ndk

The current default is 23b. You can increase this, but not decrease it. Increasing this may create issues.

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

## Slow App Start

On the desktop if you start a Kivy app from a desktop icon, the app is slower to start that say from an IDE. This is because Python has to start, on Android the same delay due to Python starting exists. On Android the splash screen is used to distract from this delay; the splash screen is a work around, not a cause of the delay.

If your app is *unusually slow* to start it is because it is doing too much work in the `build()` and `on_start()` methods. This is your code, you can change this behavior. Common causes are monolithic `kv`, compute intensive Python in the above methods, or I/O intensive Python in the above methods. 

A solution for monolithic `kv` is to have a `kv` file for each screen and to instantiate the screen manager in Python. At `build()` only the first screen is added to the screen manager. Other screens are built and added after `on_start()`, either on demand or on some schedule. This is known as lazy loading.

Another common solution is to schedule any initial compute or I/O intensive tasks to occur after `on_start()`, either on demand or on some schedule.

There may be other causes, its your code.

# Android Hardware

## Camera

It is hard to get the [Kivy Camera widget](https://kivy.org/doc/stable/api-kivy.uix.camera.html) to work on Android, [for example](https://github.com/kivy/kivy/issues?q=is%3Aissue+is%3Aopen+camera). There are several 3rd party alternatives. Look at the [Preview widget in Camera4Kivy](https://github.com/Android-for-Python/Camera4Kivy#camera4kivy), and try its [examples](https://github.com/Android-for-Python/Camera4Kivy#tested-examples-and-platforms).

Also try the [Xcamera widget](https://github.com/kivy-garden/xcamera) from the Kivy Garden, or [kivy-anderoid-camera](https://github.com/alecvn/kivy-android-camera). For OpenCV try [Kivy-Android-Camera](https://github.com/tibssy/Kivy-Android-Camera). Also there is [Color blind camera](https://github.com/inclement/colour-blind-camera) and [zbarcam](https://github.com/kivy-garden/zbarcam). 

## Keyboard

The relationship between the Android keyboard and the layout is somewhat configurable with [softinput_mode](https://kivy.org/doc/stable/api-kivy.core.window.html#kivy.core.window.WindowBase.softinput_mode), consider `Window.softinput_mode = 'below_target'`.

The type of keyboard can be set with `input_type` for example `TextInput(input_type = 'tel')`. Accepted values are 'text', 'number', 'url', 'mail', 'datetime', 'tel', or 'address'. 

## Back Button and Gesture

A back button/gesture can be detected with a test for key == 27, as shown in the [documentation](https://github.com/kivy/python-for-android/blob/develop/doc/source/apis.rst#handling-the-back-button). The key handler **must** return a boolean.

Android 10 and up require that the back button/gesture can return the app to the Android home screen, therefore there must be a state in the app where back button/gesture is not consumed by the app. For example if the back button/gesture transitions between Kivy screens, from the "main" screen the app can on a back event go to the Android home screen:
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
	                  # (but you probably want the code below here)
```

On an Android back gesture/button, by default Kivy **incorrectly** stops the app and leaves it in the app list. The correct behavior would be to pause the app. The workaround is:

```python
from kivy.utils import platform
from android import mActivity

    def keyboard(self,window,key,*args):
        if key == 27 and platform == 'android':	
    	    mActivity.moveTaskToBack(True)
            return True 
```

If you wish to stop the app and remove it from the app list, use:

```python
            mActivity.finishAndRemoveTask()
```

# Android Packages

## The Android package

P4a provides Android specific utilities in the android package, this is only available on Android. It is as they say 'self documenting', which really means there isn't any documentation. [Read the code](https://github.com/kivy/python-for-android/tree/develop/pythonforandroid/recipes/android/src/android).

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

```python
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

## Android Notifications

[Android-Notification-in-Python](https://github.com/Guhan-SenSam/Android-Notification-in-Python)

# Kivy Related Topics

## Layout

A portable layout must be an *elastic* layout, because on a mobile device the Kivy window is defined by the screen and the screen changes between devices.

Setting widget properties such as size, size_hint, or orientation make the layout less elastic. But setting some of these layout constraints is required to achieve a particular layout, so portable layout is a balance between the two.

Mobile device screen resolution (dpi), screen orientation (landscape or portrait), and screen aspect ratio all impact the portability of a layout.

### Screen Resolution

Screen resolution can be addressed by specifying font size in units of sp, and widget size in units of dp. See [Kivy Metrics](https://kivy.org/doc/stable/api-kivy.metrics.html).

### Screen Orientation

Screen orientation on a desktop defaults to landscape, and on a mobile device usually defaults to portrait, but can be landscape or both. **Developing a layout in landscape will not provide a good layout in portrait.** Develop for your target orientation, or support both orientations as follows.  

For a mobile device the available orientation is set in buildozer.spec, `orientation` it can be one of `all`, `portrait`, or `landscape`.

Supporting `orientation = all` usually requires dynamically modifying layout parameters such as size, size_hint, or orientation. Do this using a Widget's `on_size()` method, and testing for orientation. [For example](https://github.com/Android-for-Python/c4k_photo_example/blob/main/applayout/photoscreen1.py#L34-L69). 

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

There is a `#garden_requirements =` field in older buildozer.spec files. This is depreciated and should not be used.

# Creating a Recipe

## Why a Recipe

Some Python packages are not pure Python, they contain compiled code. This must be compiled for the specific platform.

Python packages are generally distributed in a wheel, this will contain pre-compiled binaries for supported platforms. Python wheels do not support Android, so to port a package to Android is must be explicitly compiled for ARM/Android.

P4a provides an api for creating package build scripts, such a script is called a "recipe". The task in creating a recipe is to port existing build instructions. To create a recipe you must understand both of these:

- The p4a recipe API.

- The existing build instruction for the package.

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

## Other Resources

There are **a lot of useful features** to be found at these links:

[https://github.com/Sahil-pixel/Pykivdroid](https://github.com/Sahil-pixel/Pykivdroid)

[https://github.com/yunus-ceyhan](https://github.com/yunus-ceyhan)

[https://github.com/adywizard](https://github.com/adywizard)

[https://github.com/tshirtman](https://github.com/tshirtman)

[https://github.com/Kulothungan16/Example-Kivy-Apps](https://github.com/Kulothungan16/Example-Kivy-Apps)

[https://github.com/AM-ash-OR-AM-I/Passlock](https://github.com/AM-ash-OR-AM-I/Passlock)

# Release Builds

The following depends on using Buildozer 1.4.0 or later.

Setup signing *before* your release build.

## Setup signing

The official [Android signing overview is here](https://developer.android.com/studio/publish/app-signing.html).

For a Buildozer build, setting up signing consists of two steps:

 - Create a key, do this once for an app. You will use `keytool` for this.

 - Tell Bulldozer to sign the app using the key. You will set environment variables to pass the information about the key to Buildozer.   

[Follow the Kivy signing instructions](https://github.com/kivy/kivy/wiki/Creating-a-Release-APK) but don't just follow the instructions, read all the annotated comments by HeRo002. The instructions are flawed, but in combination with the comments they are good.

Here are some [very detailed signing instructions](https://gist.github.com/Guhan-SenSam/fa4ed215ef3419e7b3154de5cb71f641). 

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

One of the app requirements needs a recipe (or needs to be specified differently).

In this case the requirements for OpenCV is incorrectly specified, it should be `opencv` because that is the recipe name.

## EM_X86_64 instead of EM_AARCH64

`ImportError: dlopen failed: "<something>/_python_bundle/site-packages/<somepackage>/_binding.so" is for EM_X86_64 (62) instead of EM_AARCH64 (183)`

The build process included the wrong binary file.

The cause is an x86_64 binary from PyPl was used; because of a requirements specification error in buildozer.spec

One of the app requirements needs a recipe (or needs to be specified differently).

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

Version 62 Is Java 18. Error is probably due to an added aar built with Java 18. This is not going to work.

The Java version used by p4a is the version required for the gradle version used. For p4a master, this can be any version between openJDK-11 and 17, the install instructions suggest 17.

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

## BUILD FAILURE: No main.py(o)

`BUILD FAILURE: No main.py(o) found in your app directory`

Things to check:

A hidden directory (the name starts with a period) in the project path will cause this failure. This is a tool error.

An app build error, try `python3 -m compileall main.py` to check for errors.

## /usr/bin/gzip: 1: ELF : not found

`/usr/bin/gzip: 1: ELF : not found`

This is due to regressive behavior in WSL 1, [ref](https://askubuntu.com/questions/1417255/trying-to-unzip-a-tgz-in-wsl-but-get-elf-not-found-error).

Check the current WSL version at the Windows Command prompt with `wsl -l -v`.

The fix is to upgrade to WSL 2.



