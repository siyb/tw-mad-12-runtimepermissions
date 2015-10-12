% Runtime Permissions
% Patrick Sturm
% 12.10.2015

# Resources

## Resources

* Runtime Permissions: [http://android-developers.blogspot.co.at/2015/08/building-better-apps-with-runtime.html](http://android-developers.blogspot.co.at/2015/08/building-better-apps-with-runtime.html)
* Asking For Permissions: [https://www.youtube.com/watch?v=iZqDdvhTZj0](https://www.youtube.com/watch?v=iZqDdvhTZj0)
* Patterns: [https://www.google.com/design/spec/patterns/permissions.html?utm_campaign=runtime-permissions-827&utm_source=dac&utm_medium=blog](https://www.google.com/design/spec/patterns/permissions.html?utm_campaign=runtime-permissions-827&utm_source=dac&utm_medium=blog)
* ActivityCompat: [http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html)
* ContextCompat: [http://developer.android.com/reference/android/support/v4/content/ContextCompat.html](http://developer.android.com/reference/android/support/v4/content/ContextCompat.html)

# Permissions / Runtime Permissions

## Permissions - Introduction 1

* We have used permissions before
```xml
<uses-permission 
	android:name="android.permission.INTERNET"/>
```
* On older Android versions (before Marshmallow - 6.0, API Level 23), users were prompted with required permissions on install
* This behaviour can now be adjusted if you are targeting API Level 23 and your App is installed on a device running Android 6.0
* Please note that you can already minimize permissions for your App by using Intents to start Apps that do the job for you (e.g. Camera App)

## Permissions - Introduction 2

* Enter Runtime Permissions!
* User will not be asked to grant permissions during the installation or update process
	* Permissions are requested on a "need to be granted"-basis
	* The user will be promted to grant permissions, the "do not show again" checkbox will only be shown _after_ the user declined to grant the permission that was asked for ...

## Permissions - Howto 1

* Runtime Permissions can be queried or requested using the support library
* Two relevant methods
	* ```checkSelfPermission(Context c, String permission)```
		* Is part of the ContextCompat API provided by the support library
		* Static method!
		* Checks if the user has granted a certain permission
		* Returns ```PackageManager.PERMISSION_GRANTED``` or ```PackageManager.PERMISSION_DENIED```
	* ```requestPermissions(Activity a, String[] permissions, int requestCode)```
		* Is part of the ActivityCompat API provided by the support library
		* Static method
		* Asks a user to grant permissions
		* Calls a callback: ActivityCompat.OnRequestPermissionsResultCallback

## Permissions - Howto 2

* Please note that the user can revoke permissions at any time
	* You need to check if your App still holds the appropriate permission every time before you are using it!
	* If the permission has not been granted by the user, you will need to ask her / him again
* Even though your permissions are not requested at install time any more, you will still need to provide all permissions that your App requires in your AndroidManifest.xml

## Permissions - Howto 3

* Permissions are categorized using:
	* PROTECTION_NORMAL
		* will be granted automatically at install time
		* e.g. INTERNET permission
	* PROTECTION_SIGNATURE
		* will be granted automatically at install time provided your App has the same signature as the App declaring the permission!
	* PROTECTION_DANGEROUS
		* must be prompted during runtime (on 23+)
		* e.g. ACCESS_FINE_LOCATION
	* PROTECTION_SIGNATURE_OR_SYSTEM
		* Deprecated now (API 23) - use PROTECTION_SIGNATURE | PROTECTION_FLAG_PRIVILEGED instead

## Permissions - Howto 4

* For our purposes, two permission levels are relevant
	* normal:
		* does not risk the user's privacy directly
		* automatically granted
	* dangerous:
		* does risk the user's privacy by providing access to confidential data
		* needs to be granted

## Permissions - Howto 5

* In order to request a permission at runtime, it should have the "PROTECTION_DANGEROUS" protection level, since signature and normal permissions are granted at install time anyway
* ```requestPermissions(...)``` may start an Activity in order to ask the user for certain permissions, meaning that your Activity or Fragment may be paused! Please refer to the Activity / Fragment lifecycle!

## Permissions - Howto 6

* In addition, ```requestPermissions(...)``` might cause your App to restart, be prepared (Activity Stack will be reconstructed before the callback is received)
* The dialog that is shown by ```requestPermissions(...)``` will describe the permission group, _not_ individual permissions
	* e.g. requesting COARSE and FINE location at once will promt the user for location access
	* you still need to ask for all permissions of a group individually though! (no user interaction required!)

## Permissions - Howto 7

* Use ```shouldShowRequestPermissionRationale(Activity a, String permission)``` to check if you need to provide a rationale i.e. an explanation on why your App needs a certain permissions
* This method will return ```true``` when a user has turned down a previous permission request or if the device policy does not allow granting the permission

## Permissions - Howto 8

* "For example, if you write a camera app, requesting the camera permission would be expected by the user and no rationale for why it is requested is needed. If however, the app needs location for tagging photos then a non-tech savvy user may wonder how location is related to taking photos. In this case you may choose to show UI with rationale of requesting this permission." - [Documentation](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html#shouldShowRequestPermissionRationale(android.app.Activity, java.lang.String))

# Example Code

# Asking For Permissions

## Permissions - How To Ask For Permissions 1
* Differentiation (permissions):
	* Critical - This permission is required to provide the primary functionality of your App
	* Non-Critical - This permission is required by a secondary functionality of your App
	* Obvious - Most users would understand why granting the permission is required
	* Non-Obvious - Users are not likely to understand why granting the permission is required

## Permissions - How To Ask For Permissions 2

![Source: https://www.google.com/design/spec/patterns/permissions.html](../patterns_permissions_patterns0.png)

## Permissions - How To Ask For Permissions 3

* Critical and obvious permissions should be ***asked up-front***
	* e.g. your photo App requires the camera permission
	* ask when your App is started
* The user may be ***educated up-front*** when you are dealing with a Critical and non-obvious permission
	* e.g. your game requires the contacts permission
	* educate the user when the App is started, and tell her / him why you need the the permission

## Permissions - How To Ask For Permissions 4

* Non-Critical and obvious permissions should ***ask in context***
	* e.g. your photo App requires access to the media gallery to load existing pictures
	* ask when your App actually needs access to the feature
* The user should be ***educated in context*** when non-Critical and non-obvious permissions have to be granted
	* e.g. your photo App requires access to the user's location (EXIF)
	* educate the user when the feature is accessed