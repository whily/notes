Notes of profiling Scala programs.

As Scala is based on JVM, profiling tools for Java typically work for
Scala as well. This is also the case for the tools discussed in this
note.

### Profiling Android app

Add following lines in appropriate places, e.g. method `onCreate`.

    android.os.Debug.startMethodTracing()
    ...
    android.os.Debug.stopMethodTracing()

Add the SD card access permission in AndroidManifest.xml, as below:

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

Run the Android app, and then copy the trace file out of phone, and
then view the trace file, e.g.

     adb pull /sdcard/dmtrace.trace .
     traceview dmtrace.trace

### Profiling Java library with VisualVM

VisualVM is bundled with JDK and can be launched with command `jvisualvm`.
For profiling, simply select the JVM program (e.g. sbt), in the Profiler page,
check "Settings" and select the class to be profiled (e.g. ignore the java classes).
