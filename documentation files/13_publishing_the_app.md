# Publishing the App

## Module Introduction 
* In this module we're going to learn how to move our application from **development** to **deployment**. We will also learn other things before moving to deployment.

* To be precise, we're going to learn how to:
    * Add App Launch Icon.
    * Add Splash Screen.
    * Publish the application to the store both for **Android** and **IOS**.


## Adding Launcher Icon
* Both platforms proide a default **launcher icon** to appear on your device on the app launcher. We wish to put a nice one.

* In **Android** you need to:
    1. Open **Android Studio**.
    2. Navigate to ` android -> app -> src -> main -> res ` directory.
    3. Hit right click on ` res ` directory and hit ` new -> Image Asset `.
    4. For ` Icon Type ` Choose either ` Launcher Icons(Adaptive and Legacy) ` If your target Api is higher or ` Launcher Icons(Legacy only) `.
    5. You can leave the same name to override the default launcher.
    6. Choose for ` Asset Type ` -> ` Image `.
    7. Pick your image. This will generate multiple images for different devices.
    8. Hit ` next ` then ` finish `.
    9. You need to uninstall and reinstall the app and You're done.


## Adding a Splash Screen
* One last thing we need to do before releasing is adding a **splash screen**. This provides something to look at while your application is opening. This becomes crucial in applications that take some time in the initial startup.

* There are a bunch of solutions for that. It really depends on the packages you use in your application. For example since we use ` react-native-navigation ` package it provides us with a native ` SplashActivity ` which our ` MainActivity ` ` extens `. ` SplashActivity ` provides a method named ` createSplashLayout ` which we can ` override `. The bad thing is that we have to do that programmatically and return a ` LinearLayout ` object. For a complex **splash screen** it would be tough to do that programmatically. However, for a simple one, it is easy. We will create a simple splash screen that has a background color and a text in the center with a certain color.In ` MainActivity.java `.
```java
@Override
public LinearLayout createSplashLayout() {
    LinearLayout view = new LinearLayout( this );
    TextView text = new TextView( this );

    view.setBackgroundColor( Color.parseColor( "#521751" ) );
    view.setGravity( Gravity.CENTER );

    text.setTextColor( Color.parseColor( "#fa923f" ) );
    text.setText( "RN Course Project" );
    text.setGravity( Gravity.CENTER );
    text.setTextSize( TypedValue.COMPLEX_UNIT_DIP, 40 );

    view.addView( text );

    return view;
}
```
Since I have a background in **Android**, this code does not look much strange for me. Do not forget to ` import ` these things.
```java
import android.widget.LinearLayout;
import android.widget.TextView;
import android.graphics.Color;
import android.view.Gravity;
import android.util.TypedValue;
```
Ofcourse **Android Studio** can ` import ` these for you.


## Configuring & Building the App
* A few steps have to be followed in order to build your app for production. We can simply follow the instrauctions provided by ` React Native ` in ` Running on Device ` [article](https://facebook.github.io/react-native/docs/running-on-device) under ` Guides ` section. There for **Android**, you find a link to ` Generating Signed APK ` [article](https://facebook.github.io/react-native/docs/signed-apk-android) which walks you through what have to be done to build for deployment.  