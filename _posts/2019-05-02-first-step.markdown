---
layout: post
title:  "First steps"
date:   2019-05-02 23:58:49 +0200
categories: camera2api
---

# Setup empty project

First of all create empty project in Android Studio, like on tutorial: [android create project][android-create-project]
in brief: 
1. Choose "Start a new Android Studio project".
2. Select "Empty Activity" and "Next" button.
3. Fill "Configuration" window - Name, Package name, Save location and Language. 
If you are not familiar with Kotlin choose Java. This tutorial will be written in Java.
4. Google for minimum api level for Camera2 API - 21. For now I will choose 27.
5. Button "Finish".

# We do not waste time

So let's create instrumented tests for running tests on android device, according to [documentation][android-instrumented-tests]
Why? Because good programmer write tests. For testing hid own code, for testing other's code.
Running tests on android device is wonderful solution. According to tutorial we have do:
1.a In some Android Studio versions instrumented tests are already ready to run.
You should check whether you have folder `app/src/androidTest/java` - if yes you have already configured instrumented tests.
1.b If you do not have these folders - create it. Inside it, create another folder similar to your package structure inside application code.
e.g. com.example.myapplication. Tip: in navigation panel change from `Android` to `Project` way. 


[android-create-project]: https://developer.android.com/studio/projects/create-project/
[android-instrumented-tests]: https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests
