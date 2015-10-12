% Runtime Permissions
% Patrick Sturm
% 12.10.2015

# Runtime Permissions

## Resources

* Runtime Permissions: [http://android-developers.blogspot.co.at/2015/08/building-better-apps-with-runtime.html](http://android-developers.blogspot.co.at/2015/08/building-better-apps-with-runtime.html)
* Asking For Permissions: [https://www.youtube.com/watch?v=iZqDdvhTZj0](https://www.youtube.com/watch?v=iZqDdvhTZj0)
* Patterns: [https://www.google.com/design/spec/patterns/permissions.html?utm_campaign=runtime-permissions-827&utm_source=dac&utm_medium=blog](https://www.google.com/design/spec/patterns/permissions.html?utm_campaign=runtime-permissions-827&utm_source=dac&utm_medium=blog)

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

* In order to request a permission at runtime, it should have the "PROTECTION_DANGEROUS" protection level, since signature and normal permissions are granted at install time anyway
* ```requestPermissions(...)``` may start an Activity in order to ask the user for certain permissions, meaning that your Activity or Fragment may be paused! Please refer to the Activity / Fragment lifecycle!
* In addition, ```requestPermissions(...)``` might cause your App to restart, be prepared (Activity Stack will be reconstructed before the callback is received)

## Permissions - Howto 5

* Use ```shouldShowRequestPermissionRationale(Activity a, String permission)``` to check if you need to provide a rationale i.e. an explanation on why your App needs a certain permissions
* "For example, if you write a camera app, requesting the camera permission would be expected by the user and no rationale for why it is requested is needed. If however, the app needs location for tagging photos then a non-tech savvy user may wonder how location is related to taking photos. In this case you may choose to show UI with rationale of requesting this permission." - [Documentation](http://developer.android.com/reference/android/support/v4/app/ActivityCompat.html#shouldShowRequestPermissionRationale(android.app.Activity, java.lang.String))

# Example Code

