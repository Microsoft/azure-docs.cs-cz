---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9330488debb0b7611fdd63656e4c555566c749dd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060483"
---
### <a name="configure-required-android-packages"></a>Konfigurovat požadované balíčky Android

Balíček je [automaticky propojený](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) při sestavování aplikace. Pro dokončení procesu konfigurace máte k dispozici několik dalších kroků.

### <a name="configure-android-manifest"></a>Konfigurace manifestu Android

V části Android/App/src/Main/AndroidManifest.xml ověřte název balíčku, oprávnění a požadované služby. Ujistěte se, že jste registrovaní a příjemci zaregistrovali a `RNPushNotificationPublisher` `RNPushNotificationBootEventReceiver` zaregistrovali `RNPushNotificationListenerService` službu. Metadata oznámení lze použít k přizpůsobení vzhledu nabízených oznámení.

  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      package="YOUR_PACKAGE_NAME">

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

        <application
          android:name=".MainApplication"
          android:label="@string/app_name"
          android:usesCleartextTraffic="true"
          android:icon="@mipmap/ic_launcher"
          android:roundIcon="@mipmap/ic_launcher_round"
          android:allowBackup="false"
          android:theme="@style/AppTheme">

          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_name"
                      android:value="PushDemo Channel"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_channel_description"
                      android:value="PushDemo Channel Description"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_foreground"
                      android:value="true"/>
          <meta-data  android:name="com.dieam.reactnativepushnotification.notification_color"
                      android:resource="@android:color/white"/>

          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationPublisher" />
          <receiver android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationBootEventReceiver">
              <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
              </intent-filter>
          </receiver>

          <service
              android:name="com.dieam.reactnativepushnotification.modules.RNPushNotificationListenerService"
              android:exported="false" >
              <intent-filter>
                  <action android:name="com.google.firebase.MESSAGING_EVENT" />
              </intent-filter>
          </service>

          <activity
            android:name=".MainActivity"
            android:label="@string/app_name"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|uiMode"
            android:launchMode="singleTask"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
          </activity>
          <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        </application>

  </manifest>
  ```

### <a name="configure-google-services"></a>Konfigurace služeb Google

V "Android/App/Build. Gradle" zaregistrujte služby Google:

```gradle
dependencies {
  ...
  implementation 'com.google.firebase:firebase-analytics:17.3.0'
  ...
}

apply plugin: 'com.google.gms.google-services'
```

Zkopírujte soubor google-services.json, který jste stáhli během instalace FCM do složky projektu Android/App/.

### <a name="handle-push-notifications-for-android"></a>Zpracování nabízených oznámení pro Android

Nakonfigurovali jste existující `RNPushNotificationListenerService` službu pro zpracování příchozích nabízených oznámení Androidu. Tato služba byla zaregistrována dříve v manifestu aplikace. Zpracovává příchozí oznámení a proxy servery, aby je mohla nakládat do nativní části reakce na různé platformy. Nejsou nutné žádné další kroky.
