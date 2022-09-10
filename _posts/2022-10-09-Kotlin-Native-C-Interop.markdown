---
layout: post
title:  "Use C Libraries in Kotlin Native Code (C Interop) for C Noobs"
date:   2022-09-10 10:00:00 +0100
categories: programming android kotlin native C Programming Interop Interoperability 
---

**TL;DR**: Calling C Code in Kotlin Native is very simple and much more intuitive then using the corresponding JVM-"Java Native Interface" (JNI). However, the sample code from TODORefJetbrains is too hard for C beginnings, hence I wrote this blog post. If you just want to see the tutorial, jump TODOhere, the code is also on TODORefGitHub
TODO In GitHub auch auf Blog ref

Hello everybody,

When I'm not developing some Android App, I enjoy learning more about the Kotlin Ecosystem as well. As some of you know, there is not only the normal "Kotlin" Programming Language, which compiles to Code which can be run on the Java Virtual Machine (JVM). There is also "Kotlin Native", a dialect which compiles to bear-metal binary code and thus does not need a JVM to run. This can lead to potentially faster programs or completely new areas of application for Kotlin Code, like embedded devices which can't handle a full-blown JVM.

Unfortunately, the one downside of Kotlin Native in comparison to "JVM-Kotlin" is the of loss of all Java/JVM-Libraries. The JVM is known for its rich ecosystem of libraries and frameworks. You lose all that when using Kotlin Native, as your program does not use the JVM at all. However, you still have another option left to add other people's code. And this is through C-Libraries. Like on the JVM, there is a rich ecosystem of C-libs which one can use. 

The big question is: **How do you call C libraries from your Kotlin Native code?**


There is already a [tutorial by Jetbrains](https://kotlinlang.org/docs/native-c-interop.html). However, I found it way too difficult  for a C beginner like me. For example, I don't know where header-files or shared objects are located on my device, and I don't know how linking of these into your code works. I will fill these gaps in this blog post.

TODORef In the first part of this blog post, we will link an already existing C-library (VLC) into our Kotlin Native, and play some music with it. We learn more about the C-infrastructure, which files one needs to use the interop, and how we can use the Gradle build system to call the C code in our Kotlin Code. TODORef In the second part of this blog post, we will create our own small C-lib and execute it in our Kotlin Native code. Here, we learn more about C and its build-system in general.

This tutorial should work on Linux-, OSX/MAC-, and Windows-Systems.

TODO Somewhere else?
For the sake of completeness, I want to mention the JNI. If you have a background in JVM-development, you probably heared of the Java Native Interface (JNI) TODO Cref. This is a protocol which allows you to call C code on the JVM. However, it is very cumbersome to use. For example, you have to write additional C-adapter to wrap the C-lib you want to use. With Kotlin Native, all of this is way easier, and you don't have to write any additional adapter-code, Gradle does all of this for you under the hood.


## Linking a C library into your Kotlin Native Code

Next, we will link the VLC-C-Library into our Kotlin Native code. It is a really easy and well documented library to play back music. Our goal is to play back some sound in our Kotlin Native Code through the C-library. 


We have four small steps to do

1. Create a new Kotlin Native Project
2. Download the VLC-Library
3. Link the VLC-Files to our Kotlin Native Project
4. Call the lib to play back sound.

### 1. - Create a new Kotlin Native Project

To create a new Kotlin Native Project, just install IntelliJ, create a new Project, and there click on "Kotlin Multiplatform -> Native Application". Let's call it the "CInteropProject". Now finish initalization and build the project.

### 2. - Download the VLC-Library

Next off, we will download the VLC-Files. I can't just give you the files, because they depend on the architecture of your machine, hence you have to download them through the package manager of your OS (Or build them yourself). Most of the time, the package is called something like `libvlc`, `vlc-devel`, or `vlc-dev`. Here are the command for the most common distributions:

Ubuntu/Linux Mint/Debian/...: `sudo apt install libvlc-dev`
Fedora/RHEL/CoreOS/...: `sudo dnf install libvlc-devel`
Arch Linux/Manjaro/...: `sudo pacman -S libvlc`
OSX/Mac: brew install --cask vlc

With this, you download two files. The first one is the `libvlc.so` file. This is the "shared object", which basically contains the  actual code of the vlc-library. Under most distributions, it is either located under `/lib`, `/lib64`. If you look inside the shared object, you will see that it is binary file, so you can't read it at all.  The second file that was installed is `vlc.h`. This is the header-file, and it should be located at `/usr/include` or `/usr/include/vlc`. This file you can actually read, and it tells you what the shared object actually contains. In Kotlin terminology, the header is like an "interface" of the shared object "class". 

### 3. - Link the VLC-Files to our Kotlin Native Project

In the third step, we connect these two vlc-files to our kotlin project. First of, in our Kotlin project, we create the folders `nativeInterop/cinterop`, where `nativeInterop` is on the same level as the already existing `nativeMain` folder. Note that the `I` in `nativeInterop` is capitalized, while the `i` in `cinterop` is not (this is important).

In the`cinterop`-folder, we add a `libvlc.def` file, which contains the locations of the vlc-files. This file contains the following configurations:


```
compilerOpts = -I /usr/include
headers = vlc/vlc.h

linkerOpts = -L /lib64 -L /lib64/vlc  -L /lib -L /opt/local/lib -L /opt/local/lib/vlc -L /usr/local/opt/ -L /usr/local/opt/vlc -l vlc
```

The `compilerOpts` options tells Gradle where to search for header files (in `/usr/include` folder). The `headers` option tells Gradle that we want to add the `/usr/include/vlc/vlc.h` header to our project. In the `linkerOpts` option, the `-L` parameters tell Gradle where to search for shared-object files. I added a lot of different folders so that this project runs on as many distributions as possible. The `-l` parameter in the end tells Gradle that we want to add the `libvlc.so` file to our project. As you can see, to add `libX.so` file to our project, the parameters has to be `-l X`

Afterwards, we have to tell Gradle that it should execute the def-file and create Kotlin-bindings for the C-Code in the vlc-header. We do this by adding the following code in our `build.gradle.kts` file, and there in the already existing ` nativeTarget.apply` block: 


```kotlin
nativeTarget.apply {
      ...
      compilations.getByName("main") {
          cinterops {
              val libvlc by creating
          }
      }
      ...
}
```

After we sync the project, Gradle created Kotlin functions for our code that match the functions in the `vlc.h` header file. This is an incredible improvement over the JNI, where we had to write these functions ourself.

### 4. Call the lib to play back sound.

Now, everything is combined and we can use the vlc-code in our project. As gradle created functions with the same function names as the ones in the vlc-header file, we can easily adapter the libvlc example code from TODORef [here](https://wiki.videolan.org/LibVLC_Tutorial/) to play back sound for our project. Add the following code to your `Main.kt` file:

```
import libvlc.*
import platform.posix.sleep

fun main(args: Array<String>) {
    val SOUND_FILE = "sound.mp3"

    val inst = libvlc_new(0, null)

    val m = libvlc_media_new_path(inst, SOUND_FILE)

    val mp = libvlc_media_player_new_from_media(m)

    libvlc_media_release(m)

    libvlc_media_player_play(mp)
    sleep(10)
}
```

You can put any `sound.mp3` file in the root-`CInteropProject` folder of your project, or adapt the `SOUND_FILE` variable as needed. This code will play back ten seconds of sounds.

Hooray, you used C code in your Kotlin project!  You can find the final project on GitHub TODORef

TODO Windows

If your IDE can't find the `libvlc_...`-functions, or can't import `libvlc.*`, then try to re-sync the Gradle file. For me, it worked by adding a useless character to the `build.gradle.kts` file, then syncing (thus creating an error)`, then removing the character again and re-syncing the project. 99% of the time, this solved the import-issue.

Next of, we want to take a short detour and create our own C-library and call it from the Kotlin project

## Create and Call your own C Library from Kotlin Native Code

Now, we want to create a small C library called `helloworld`. It contains a function `hello()` that prints `Hello World!` and returns `42`. Afterwards we want to call this function from our Kotlin code.

### Create a C library 

As you know from the first part of this blog, we need a shared object and a header file to call C code from our Kotlin project. The header is relatively easy, as it is human readable. 

Create the file `helloworld.h` and insert the following code:

```
#include <stdio.h>
int hello();
```

We need the `stdio.h` header file to call `printf` in the C code afterwards. Next, copy this file to the other header files in the `/usr/include/` folder.

Next, we need to create a shared object file which contains the actual code. First, we need to create a `helloworld.c` file with the following content:

```
#include <stdio.h>
int hello() {
  printf("Hello World!\n");
  return 42;
}
```

As you can see, the function `hello` prints "Hellow World!" to the console and returns 42.

TODO Windows
Next, we need to compile this c-file to a shared object. We do this with the following command:

```
gcc -shared -o libhelloworld.so helloworld.c
```
TODO Test without -fPIC works.

The command should be rather self-explanatory. We copy the `libhelloworld.so` file to `/lib`

Afterwards, we do the same things as for the vlc-library. First of, we in `cinterop`, we create a new `libhelloworld.def` file with the following content:


```
compilerOpts = -I /usr/include
headers = helloworld.h

linkerOpts = -L /lib64 -L /lib64/vlc  -L /lib -L /opt/local/lib -L /opt/local/lib/vlc -L /usr/local/opt/ -L /usr/local/opt/vlc -l helloworld
```

Next, we add the following line to the `build.gradle.kts`:

```kotlin
nativeTarget.apply {
      ...
      compilations.getByName("main") {
          cinterops {
              val libvlc by creating
              val libhelloworld by creating //Add this line
          }
      }
      ...
}
```

The sync the project and add the following lines to your `Main.kt`:

```kotlin
import libhelloworld.*

fun main(args: Array<String>) {
    val x = hello() //should be 42
    println("Return value was $x")
}
```

Now, your C should work, congratulations! Again, if it does not compile, try to resync the Gradle-file.

You can find the final project on GitHub TODORef

TODO Add Sound File + Quelle

## Conclusion

That's it for today!

You have seen the necessary steps to include C code in your Kotlin Native project. Additionally, you have seen how to create a C library yourself.

Please leave an email if this post helped you or if you have any questions regarding this blog post.

Thanks for reading and have a nice day,

Niklas
