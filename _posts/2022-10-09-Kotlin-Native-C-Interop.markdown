---
layout: post
title:  "Use C Libraries in Kotlin Native Code (C Interop) for C Noobs"
date:   2022-09-10 10:00:00 +0100
categories: programming android kotlin native C Programming Interop Interoperability 
---

**TL;DR**: Calling C Code in Kotlin Native is very simple and much more intuitive then using the corresponding JVM-"Java Native Interface" (JNI). However, the sample code from [JetBrains](https://kotlinlang.org/docs/native-c-interop.html) is IMHO too hard for C beginners, hence I wrote this blog post. If you just want to see the code, look at [GitHub](https://github.com/NWuensche/KotlinNativeCInteropProject).


Hello everybody,

When I'm not developing some Android app, I enjoy learning more about the Kotlin Ecosystem as well. As some of you might know, there is not only the "normal" JVM Kotlin programming language, which compiles to bytecode which can be run on the Java Virtual Machine (JVM). There is also "Kotlin Native", a dialect which compiles to bear-metal binary code and thus does not need a JVM to run. This can lead to potentially faster programs or completely new areas of application for Kotlin Code, like embedded devices which can't handle a full-blown JVM.

Unfortunately, the one downside of Kotlin Native in comparison to "JVM-Kotlin" is the of loss of all Java/JVM-Libraries. The JVM is known for its rich ecosystem of libraries and frameworks. You lose all of that when using Kotlin Native as your program does not use the JVM at all. However, you still have another option left to add other people's code:  through C Libraries. Like on the JVM, there is a rich ecosystem of C-libs which one can use. 

The big question is: **How do you call C libraries from your Kotlin Native code?**


There is already a [tutorial by JetBrains](https://kotlinlang.org/docs/native-c-interop.html) which answers this question. However, I found it way too difficult  for a C beginner like me. For example, I don't know where header-files or shared objects are located on my computer, and I don't know how linking C-libraries in general works. For you, my dear readers, I will fill these gaps in this blog post after researching this topic in more detail.

In the first part of this blog post, we will link an already existing C-library (VLC) into our Kotlin Native code and play some music with it. We learn more about the C-infrastructure, which files one needs to bind everything together, and how we can use the Gradle build system to call the C code in our Kotlin Code. 

In the second part of this blog post, we will create our own small C-lib and execute it in our Kotlin Native code. Here, we learn more about C and its build-system in general.

This tutorial should work on Linux-, OSX/Mac-, and Windows-Systems.



## Linking a C library into your Kotlin Native Code

Next, we will link the VLC-C-Library into our Kotlin Native code. It is a really easy and well documented library to play back music. Our goal is to play back some sound in our Kotlin Native Code through the C-library. 

For the sake of completeness, I want to mention the JNI. If you have a background in JVM-development, you probably heard of the [Java Native Interface (JNI)](https://docs.oracle.com/en/java/javase/18/docs/specs/jni/intro.html). This is a protocol which allows you to call C code on the JVM. However, it is very cumbersome to use. For example, you have to write additional C-adapter to wrap the C-lib you want to use. With Kotlin Native, all of this is way easier and you don't have to write any additional adapter-code. Gradle does all of this hard work for you under the hood.

We have four small steps to do

1. Create a new Kotlin Native Project
2. Download the VLC-Library
3. Link the VLC-Files to our Kotlin Native Project
4. Call the library to play back sound.

### 1. - Create a new Kotlin Native Project

To create a new Kotlin Native Project, just install IntelliJ, create a new Project, and there click on "Kotlin Multiplatform -> Native Application". Let's call it the "KotlinNativeCInteropProject". Now finish initialization and build the project.

### 2. - Download the VLC-Library

Next, we will download the VLC files. I can't just give you the files, because they depend on the architecture of your machine, hence you have to download them through the package manager of your OS (or build them yourself). Most of the time, the package is called something like `libvlc`, `vlc-devel`, or `vlc-dev`. Here are the command for the most common distributions:

Ubuntu/Linux Mint/Debian/...: `sudo apt install libvlc-dev`

Fedora/RHEL/CoreOS/...: `sudo dnf install libvlc-devel`

Arch Linux/Manjaro/...: `sudo pacman -S libvlc`

OSX/Mac: brew install --cask vlc

Windows: Unfortunately, you have to compile the code yourself from [source](https://wiki.videolan.org/VLC_Source_code/).

With this, you download two files. The first one is the `libvlc.so` file. This is the "shared object", which basically contains the  actual code of the vlc-library. Under most distributions, it is either located under `/lib` or `/lib64`. If you look inside the shared object, you will see that it is binary file, so you can't read it at all.  The second file that was installed is `vlc.h`. This is the header-file, and it should be located at `/usr/include` or `/usr/include/vlc`. One can actually read this file, and it tells you which functions the shared object actually contains. In Kotlin terminology, the header is like an "interface" of the shared object "class". 

### 3. - Link the VLC-Files to our Kotlin Native Project

In the third step, we connect these two vlc-files to our Kotlin project. First of, in our Kotlin project, we create the folders `nativeInterop/cinterop`, where `nativeInterop` is on the same level as the already existing `nativeMain` folder. Note that the "I" in `nativeInterop` is uppercase, while the "i" in `cinterop` is lowercase (this is important).

In the`cinterop`-folder, we add a `libvlc.def` file, which contains the locations of the vlc-files. This file contains the following configurations:


```
compilerOpts = -I /usr/include
headers = vlc/vlc.h

linkerOpts = -L /lib64 -L /lib64/vlc  -L /lib -L /opt/local/lib -L /opt/local/lib/vlc -L /usr/local/opt/ -L /usr/local/opt/vlc -l vlc
```

The `compilerOpts` options tells Gradle where to search for header files (i.e. in the `/usr/include` folder). The `headers` option tells Gradle that we want to add the `/usr/include/vlc/vlc.h` header to our project. In the `linkerOpts` option, the `-L` parameters tell Gradle where to search for shared-object files. I added a lot of different folders so that this project runs on as many distributions as possible, but you can remove the unnecessary ones if you want to. The `-l` parameter in the end tells Gradle that we want to add the `libvlc.so` file to our project. As you can see, to add `libX.so` file to our project, the parameters has to be `-l X`.

If you work on Windows, you have to adapter the `compilerOpts` and `linkerOpts` to point to the folder of your `vlc.h` and `libvlc.so` file.

Afterwards, we have to tell Gradle that it should execute the `libvlc.def`-file and create Kotlin bindings for the C function which are part of the vlc-header. We do this by adding the following code in our `build.gradle.kts` file, and there in the already existing `nativeTarget.apply` block: 


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

After we sync the project, Gradle created Kotlin functions that match the functions in the `vlc.h` header file. This is an incredible improvement over the JNI, where we had to write these functions ourself.

### 4. Call the library to play back sound.

Now, everything is combined and we can use the vlc-library in our project. As Gradle created functions with the same function names as the ones in the vlc-header file, we can easily adapt the [libvlc example code](https://wiki.videolan.org/LibVLC_Tutorial/#Sample_libVLC_code) to play back sound for our project. Add the following code to your `Main.kt` file:

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

You can put any `sound.mp3` file in the root-"KotlinNativeCInteropProject" folder of your project, or adapt the `SOUND_FILE` variable as needed. For example, you could use [this file](https://pixabay.com/sound-effects/dr-tribal-percussion-triplet-loop-high-passed-106bpm-25935/). Our code will play back ten seconds of sounds due to the `sleep(10)` command.

Hooray, you used C code in your Kotlin project!  You can find the final project on [GitHub](https://github.com/NWuensche/KotlinNativeCInteropProject).


If your IDE can't find the `libvlc_...`-functions, or can't import `libvlc.*`, then nevertheless try to execute the code by clicking the run arrow next to the `main`-function. If this does not work, try to re-sync the Gradle file. For me, it worked by adding a useless character to the `build.gradle.kts` file, then syncing (thus creating an error), then removing the character again and re-syncing the project. 99% of the time, this solved the import-issue.

Next, we want to take a short detour and create our own C-library and call it from the Kotlin project.

## Create and Call your own C Library from Kotlin Native Code

Now, we want to create a small C library called `helloworld`. It contains a function `hello()` that prints `Hello World!` and returns `42`. Afterwards we want to call this function from our Kotlin code.

### Create a C library 

As you know from the first part of this blog post, we need a shared object and a header file to call C code from our Kotlin project. The header is relatively easy, as it is human readable. 

Create the file `helloworld.h` and insert the following code:

```
#include <stdio.h>
int hello();
```

We need to import the `stdio.h` header to call `printf()` in the C code afterwards. Next, copy this file to the other header files in the `/usr/include/` folder.

Next, we need to create a shared object file which contains the actual code. First, we need to create a `helloworld.c` file with the following content:

```
#include <stdio.h>
int hello() {
  printf("hello world!\n");
  return 42;
}
```

As you can see, the function `hello` prints "Hello World!" to the console and returns 42.

Next, we need to compile this C file to a shared object. We do this with the following command:

```
gcc -shared -o libhelloworld.so helloworld.c
```

The command should be rather self-explanatory. We copy the `libhelloworld.so` file to `/lib`.

Afterwards, we do the same things as for the vlc-library. First of, in the `cinterop` folder of our Kotlin project, we create a new `libhelloworld.def` file with the following content:


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

Then sync the project and add the following lines to your `Main.kt`:

```kotlin
import libhelloworld.*

fun main(args: Array<String>) {
    val x = hello() //should be 42
    println("Return value was $x")
}
```


Now, your C should work, congratulations! Again, if it does not compile, try to re-sync the Gradle-file.  You can find the final project on [GitHub](https://github.com/NWuensche/KotlinNativeCInteropProject).


## Conclusion

That's it for today!

You have seen the necessary steps to include C code in your Kotlin Native project. Additionally, you have seen how to create a C library yourself.

Please leave an email if this post helped you or if you have any questions regarding this blog post.

Thanks for reading and have a nice day,

Niklas
