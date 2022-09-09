Notes for GearVR development with Unity3D.

### Setup steps

1. Configure Android SDK location in Unity: in the editor, select
   Unity > Preferences from the menu and then click on External
   Tools in the preferences window.

Switch your build platform to Android. Open menu Edit -> Project Settings -> Player. Select Other Settings, check the Virtual Reality Supported checkbox.
You must include an Oculus signature file in your project.
Build and run. Insert the device into your headset and see the skybox with head tracking.

https://developer.oculus.com/documentation/game-engines/latest/concepts/unity-build-android/

Build Settings

From the File menu, select Build Settings…. From the Build Settings… menu, select Android as the platform. Set Texture Compression to ASTC.

Player Settings

Click the Player Settings… button and select the Android tab. In the Other Settings frame, select Virtual Reality Supported. All required settings are enforced automatically, but you may wish to make additional settings as appropriate, such as enabling Multithreaded Rendering and setting Graphics APIs to OpenGLES3.


2. In Unity project directory, put Oculus signature file in diretory
   Assets/Plugins/Android/assets.
