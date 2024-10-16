   # Deep Link

   # React Native Deep Linking Guide (For React Native CLI)

   This document explains how to set up and manage deep linking in a React Native CLI project. Deep linking allows your app to respond to URLs like `tazakka://product?id=123` and navigate to specific screens.

   ## Table of Contents

   1. [What is Deep Linking?](#what-is-deep-linking)
   2. [Why Use Deep Linking?](#why-use-deep-linking)
   3. [Setup for Deep Linking](#setup-for-deep-linking)
   - [Installing Dependencies](#installing-dependencies)
   - [Navigation Configuration](#navigation-configuration)
   - [Platform-Specific Configuration](#platform-specific-configuration)
   4. [Handling Query Parameters](#handling-query-parameters)
   5. [Testing Deep Linking](#testing-deep-linking)
   6. [Conclusion](#conclusion)

   ---

   ## What is Deep Linking?

   Deep linking refers to the process of launching a mobile app from an external link and routing the user to a specific location within the app. For example, a deep link like `tazakka://product?id=123` will open the app and display the product with ID 123.

   ## Why Use Deep Linking?

   Deep linking is useful when you need:
   - To open specific screens in the app from an external source.
   - To pass parameters (such as IDs or names) through the URL for navigation.
   - To enhance user experience in marketing campaigns, push notifications, or cross-app navigation.

   ---

   ## Setup for Deep Linking

   ### Installing Dependencies

   First, if you're using **React Navigation** for managing routes, install the required packages:

   ```bash
   npm install @react-navigation/native @react-navigation/stack react-native-screens react-native-safe-area-context
   ```
   For React Native CLI (without Expo), the Linking API will be used for deep linking.

   Navigation Configuration
   To enable deep linking, configure your NavigationContainer to handle incoming deep links. Here's an example configuration:

   ### Sample Code:

   ```javascript
   import * as React from 'react';
   import { NavigationContainer } from '@react-navigation/native';
   import { createStackNavigator } from '@react-navigation/stack';
   import { Linking } from 'react-native';

   const Stack = createStackNavigator();

   const config = {
   screens: {
      Home: 'home',
      Profile: 'profile/:id',
      Product: 'product',
   },
   };

   const linking = {
   prefixes: ['tazakka://'],
   config,
   getInitialURL: async () => {
      const url = await Linking.getInitialURL();
      return url;
   },
   subscribe(listener) {
      const onReceiveURL = ({ url }) => listener(url);
      Linking.addEventListener('url', onReceiveURL);
      return () => Linking.removeEventListener('url', onReceiveURL);
   },
   };

   function HomeScreen() {
   return (
      // Home Screen Component
   );
   }

   function ProfileScreen({ route }) {
   const { id } = route.params;
   return (
      // Profile Screen with ID from route.params
   );
   }

   function ProductScreen({ route }) {
   const { id, name } = route.params || {};
   return (
      // Product Screen with query params
   );
   }

   export default function App() {
   return (
      <NavigationContainer linking={linking}>
      <Stack.Navigator>
         <Stack.Screen name="Home" component={HomeScreen} />
         <Stack.Screen name="Profile" component={ProfileScreen} />
         <Stack.Screen name="Product" component={ProductScreen} />
      </Stack.Navigator>
      </NavigationContainer>
   );
   }
   ```
   Platform-Specific Configuration
   # Android
   In Android, you need to add an intent filter in the AndroidManifest.xml file.

   1- Open android/app/src/main/AndroidManifest.xml.

   2- Add the following <intent-filter> inside the <activity> tag to handle the tazakka:// URL scheme:

```java script
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application>
        <activity
            android:name=".MainActivity"
            android:launchMode="singleTask">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <!-- Custom URL scheme -->
                <data android:scheme="tazakka" android:host="*" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```
# iOS
For iOS, update the Info.plist file to handle the custom deep link scheme.

1- Open ios/[projectName]/Info.plist.

2- Add the following block to handle tazakka:// links:

```
<dict>
  <key>CFBundleURLTypes</key>
  <array>
    <dict>
      <key>CFBundleURLSchemes</key>
      <array>
        <string>tazakka</string>
      </array>
    </dict>
  </array>
</dict>
```

# Handling Query Parameters
If your deep link contains query parameters (e.g., tazakka://product?id=123&name=phone), you can access these parameters using route.params in the component.

For example, in the ProductScreen:
```
function ProductScreen({ route }) {
  const { id, name } = route.params || {};

  useEffect(() => {
    if (id && name) {
      console.log(`Product ID: ${id}, Name: ${name}`);
      // Handle product details logic
    }
  }, [id, name]);

  return (
    // Your Product UI
  );
}
```

# Testing Deep Linking
To test deep linking, you can run the app and open the defined URLs. For example:

tazakka://home
tazakka://profile/42
tazakka://product?id=123&name=phone
You can simulate these links using command line tools:

# Android:

```bash
adb shell am start -W -a android.intent.action.VIEW -d "tazakka://product?id=123&name=phone" com.yourapp.package
```
# iOS:

```bash
xcrun simctl openurl booted "tazakka://product?id=123&name=phone"
```




**With appreciation,  
Tazkka Team**
