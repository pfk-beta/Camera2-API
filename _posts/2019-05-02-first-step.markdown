---
layout: post
title:  "First steps"
date:   2019-05-02 23:58:49 +0200
categories: camera2api
---

# Before coding
To understand Camera2 API take a fast look into [sample repo][android-camera2raw-repo] and [official documentation][android-camera2-doc]
 

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
Running tests on android device is wonderful solution. According to tutorial we have to do:

*
   * **a)** In some Android Studio versions instrumented tests are already ready to run.
            You should check whether you have folder `app/src/androidTest/java` - if yes you have already configured instrumented tests.
   * **b)** If you do not have these folders - create it. Inside it, create another folders similar to your application's package structure.
      e.g. `com.example.myapplication`.

* In you app's `build.gradle` you should have in dependencies `test:runner` and `test:rules` as follows
(versions may differs, if these packages are from `com.android.support.test` change it to `androidx.text`):
```
dependencies {
    androidTestImplementation 'androidx.test:runner:1.1.0'
    androidTestImplementation 'androidx.test:rules:1.1.0'
}
```
* Ensure that you have setup `testInstrumentationRunner` in `build.gradle` as follows:
```
android {
    ...
    defaultConfig {
        ...
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
}
```
* If you want to order execution of tests or group your tests, create and use test suite described in documentation.

# What Tiggers do best

* First of all we would like to just "open camera" using [method][android-camera2-manager-openCamera]
  1. so we need cameraId (obtained from `CameraManager`),
  2. `CameraDevice.StateCallback` object (which we will create),
  3.  and `Handler` object (we will create is as well).

1:
```java
// get context (remember to use getTargetContext())
Context context = InstrumentationRegistry.getInstrumentation().getTargetContext();
// get camera manager
CameraManager manager = (CameraManager) context.getSystemService(Context.CAMERA_SERVICE);
// iterate over all device's cameras
String cameraId = null;
for (String camId : manager.getCameraIdList()) {
    // and choose apropriete one, based on camera characteristics
    CameraCharacteristics characteristics = manager.getCameraCharacteristics(camId);
    
    Integer LENS_FACING = characteristics.get(CameraCharacteristics.LENS_FACING);
    if (LENS_FACING != null && LENS_FACING == CameraCharacteristics.LENS_FACING_BACK) {
        cameraId = camId;
        break;
    }
}
```

2: (to avoid problem define these elements inside test class, not inside of test method)
```java
// create variable for holding device
private CameraDevice cameraDevice;

// create state object, to pass to open method. Implements required methods
private CameraDevice.StateCallback mStateCallback = new CameraDevice.StateCallback() {
    @Override
    public void onOpened(@NonNull CameraDevice camera) {
        // this method is most important for us. Assign opened device to our variable defined above
        cameraDevice = camera;
    }

    @Override
    public void onDisconnected(@NonNull CameraDevice camera) {
        camera.close();
    }

    @Override
    public void onError(@NonNull CameraDevice camera, int error) {
        camera.close();
    }
};
```

3:
```Java
HandlerThread mBackgroundThread = new HandlerThread("CameraThread");
mBackgroundThread.start();
Handler backgroundHandler = new Handler(mBackgroundThread.getLooper());
```

And run:
```java
manager.openCamera(cameraId, mStateCallback, backgroundHandler);
```

At the end run test class


## Troubleshooting
* If you got error `Lacking privileges to access camera service`, google for it! and add:
```java
@Rule
public GrantPermissionRule permissionRule = GrantPermissionRule.grant(Manifest.permission.<PROPER_PREMISSION>);
```

* If you got error `No instrumentation registered! Must run under a registering instrumentation`, 
google for it! and change android.support to `androidx` packages. According to documentation, not to auto created file.

[android-create-project]: https://developer.android.com/studio/projects/create-project/
[android-instrumented-tests]: https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests
[android-camera2raw-repo]: https://github.com/googlesamples/android-Camera2Raw/
[android-camera2-doc]: https://developer.android.com/reference/android/hardware/camera2/package-summary
[android-camera2-manager-openCamera]: https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#openCamera(java.lang.String,%2520android.hardware.camera2.CameraDevice.StateCallback,%2520android.os.Handler)