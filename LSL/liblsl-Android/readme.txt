This is a minimal Android setup with LSL, using a text based android build chain. There is also a Visual Studio based build chain available at liblsl-Android/android-vs2013. The text based build chain demonstrates how to generate and use java swig wrappers. The Visual Studio option assumes that you will be writing an app that has as little Java as possible. There is nothing preventing these two options from being combined except time and desire.

These instructions assume that you have installed Android CodeWorks 1R3, by NVidia, but only so that the Android ndk has the expected contents and location. It is possible to get the Android ndk from elsewhere, but the paths below may change somewhat.

1) To regenerate the wrapper files for Python and Java after an interface change, you need to run the following two lines. Note that if all you want to do is recompile the wrapper for your particular Android version, you can skip these two lines.

>cd LSL\liblsl-Android\jni
>swig -c++ -java -package edu.ucsd.sccn.lsl -outdir ../src/edu/ucsd/sccn/lsl -o liblsl_wrap_java.cxx liblsl.i
>swig -python -c++ -o liblsl_wrap_python.cxx liblsl.i

2) After you are done with this, run ndk_build.cmd in this folder to rebuild liblslAndroid.so.

>cd LSL\liblsl-Android
>c:\NVPACK\android-ndk-r10e\ndk-build

3) You can now deploy an app to an attached Android device. Replace ${Project_Name} with the app you wish to install.

>cd LSL\liblsl-Android\${Project_Name}
>android update project -p . -t android-15
>c:\nvpack\apache-ant-1.8.2\bin\ant debug install

The ${Project_Name} app should appear on your device. When selected, it opens a stream and sends or receives simulated data. Note that apps will continue running until force quit: Settings -> Apps -> ${Proect_Name} -> Force Stop. This allows you to open both a receiver and a sender if desired, so you that you can verify that LSL is working on the network local to the device.

4) liblsl-Android/src/ also includes the file lslAndroid.py. Note that the Python interface expects the .so file to be called _lslAndroid.so (and likes to reside in the same directory as the .py file).

Note that you need to have Python set up for use on Android to build the Python interface; by default the file liblsl_wrap_python.cxx is not included in the build sources (in Android.mk).

I (Matthew Grivich) have not tested the Python interface for Android, but I have left the comments and files from Christian Kothe.

// Android Studio Build 12/09/2016
Added an android stuido project called native. This project uses the Android Studio 2.22 IDE to build liblsl.so for Android.
There are 2 example. 
   liblslcmake
       Uses android studio + gradle + cmake to build the project.
   liblslnative
       Uses ndkbuild and the original Android.mk file with the SWIG generated file removed.

To compile for different architectures other than armeabi-v7a you can remove
android {
    externalNativeBuild {
        abiFilters "armeabi-v7a" // Add other filters or removed to build all
    }
}
running the gradle task <project_name>->Tasks->other->externalNativeBuild<build_flavor_name> should build the liblsl.so file for Andorid. You can include this as a module in your project to build it in or grab the so and move it to the correct <project>src/main/jniLib/<architecture>/liblsl.so dir

jniLibs.srcDirs = ['jniLibs']
jni.srcDirs = []

// *.so location
<module_dir>/build/outputs/aar/liblslcmake-<flavor_type>.aar is the android lib that it outputs
<module_dir>\build\intermediates\cmake\debug\obj\armeabi-v7a\

<module_dir>\liblslnative\build\intermediates\bundles\debug\jni\armeabi-v7a\liblslAndroid.so
