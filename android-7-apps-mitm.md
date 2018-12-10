### MITM'ing Android apps on non-rooted devices running Android 7 or later
#### Solving "The client failed to negotiate an SSL connection to [...]" Burp Suite alerts

Starting on Android 7, user CA certificates--e.g. imported Burp Suite's self-signed CA cert--are ignored by Android apps that target API Level 24 and above. The following solution to intercept apps' SSL traffic does _not_ require a rooted handset.

1. [Import](https://support.portswigger.net/customer/portal/articles/1841102-installing-burp-s-ca-certificate-in-an-android-device) Burp CA cert if not yet done

2. Disassemble APK using [apktool](https://ibotpeaches.github.io/Apktool/)

   `$ apktool d hackme.apk`

3. Add a new network security config

   ```$ cat>./hackme/res/xml/network_security_config.xml```
   ```xml
   <network-security-config>
       <base-config>
           <trust-anchors>
               <certificates src="system" />
               <!-- The following line is the key to solving this issue! -->
               <certificates src="user" />
           </trust-anchors>
       </base-config>
   </network-security-config>
    ```

4. Add `android:networkSecurityConfig="@xml/network_security_config"` attribute to `<application>` tag in `./hackme/AndroidManifest.xml`

5. Re-assemble

   `$ apktool b ./hackme/`

6. Create dummy key store 

   `$ keytool -genkey -v -keystore test.keystore -storepass password -alias android -keypass password -keyalg RSA -keysize 2048 -validity 10000`

   _Leave default fields (just press Enter) and type `yes` when asked if certificate fields are correct_

7. Sign

   `$ jarsigner -verbose -keystore test.keystore -storepass password -keypass password ./hackme/dist/hackme.apk android`
      
8. Install APK while handset is connected via USB

   `$ adb install ./hackme/dist/hackme.apk`

If you have Android Studio installed and have at least created 1 project with it, the following step can be used as a subtitute for steps 6 and 7:

   `$ jarsigner -keystore ~/.android/debug.keystore -storepass android -keypass android hackme/hackme.apk androiddebugkey`
