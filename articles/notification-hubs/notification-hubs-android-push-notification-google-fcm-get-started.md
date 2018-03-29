---
title: Začínáme pracovat s Azure Notification Hubs pro aplikace pro Android a se službou Firebase Cloud Messaging | Dokumentace Microsoftu
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs a službu služby Firebase Cloud Messaging k odesílání nabízených oznámení do zařízení Android.
services: notification-hubs
documentationcenter: android
keywords: nabízená oznámení, nabízené oznámení, nabízené oznámení android, fcm, firebase cloud messaging
author: jwhitedev
manager: kpiteira
editor: ''
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 0f4c766bd68227a85e2438bc68b2d61c69ce706c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-notification-hubs-for-android-apps-and-firebase-cloud-messaging"></a>Začínáme pracovat s Azure Notification Hubs pro aplikace pro Android a se službou Firebase Cloud Messaging
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled
> [!IMPORTANT]
> Tento článek popisuje nabízená oznámení ve službě Google Firebase Cloud Messaging (FCM). Pokud stále používáte službu Google Cloud Messaging (GCM), přečtěte si článek [Odesílání nabízených oznámení do systému Android a pomocí Azure Notification Hubs a služby GCM](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

V tomto kurzu zjistíte, jak používat Azure Notification Hubs a službu Firebase Cloud Messaging k odesílání nabízených oznámení do aplikace systému Android. V tomto kurzu vytvoříte prázdnou aplikaci pro Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM).

[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Dokončený kód v tomto kurzu lze stáhnout z portálu Github [zde](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

## <a name="prerequisites"></a>Požadavky
> [!IMPORTANT]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).
> 
> 

* Kromě aktivního účtu Azure uvedeného výše budete v tomto kurzu potřebovat nejnovější verzi [Android Studia](http://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 nebo novější pro službu Firebase Cloud Messaging.
* Služba Firebase Cloud Messaging vyžaduje úložiště Google verze 27 nebo novější.
* Služby Google Play 9.0.2 nebo novější pro službu Firebase Cloud Messaging.
* Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro Android Apps.

## <a name="create-a-new-android-studio-project"></a>Vytvoření nového projektu v Android Studiu
1. V nástroji Android Studio spusťte nový projekt Android Studio.
   
    ![Android Studio – nový projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)
2. Zvolte faktor formuláře **Telefon i tablet** a hodnotu **Minimální SDK**, které chcete podporovat. Pak klikněte na tlačítko **Další**.
   
    ![Android Studio – pracovní postup vytvoření projektu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)
3. Zvolte možnost **Prázdná aktivita** pro hlavní aktivitu, klikněte na tlačítko **Další** a pak klikněte na tlačítko **Dokončit**.

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Vytvoření projektu, který podporuje službu Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-new-notification-hub"></a>Konfigurace nového centra oznámení
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. V části **Notification Services** vyberte **Google (GCM)**. Zadejte klíč serveru FCM, který jste si zkopírovali z [konzoly Firebase](https://firebase.google.com/console/) a klikněte na **Uložit**.

&emsp;&emsp;![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Vaše centrum oznámení je teď nakonfigurováno pro práci se službou Firebase Cloud Messaging. Zároveň máte připojovací řetězce, pomocí kterých můžete svojí aplikaci zaregistrovat pro příjem a odesílání nabízených oznámení.

## <a id="connecting-app"></a>Připojte aplikaci k centru oznámení
### <a name="add-google-play-services-to-the-project"></a>Přidejte do projektu služby Google Play
[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Přidání knihoven Azure Notification Hubs
1. Do souboru `Build.Gradle` pro **aplikaci** přidejte následující řádky v části **závislosti**.
   
    ```java
        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Přidejte následující úložiště za část **závislosti**.
   
    ```java
        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }
    ```

### <a name="updating-the-androidmanifestxml"></a>Probíhá aktualizace souboru AndroidManifest.xml.
1. Abyste zajistili podporu FCM, musíte do vašeho kódu implementovat službu naslouchání ID instance, která se používá k [získání registračních tokenů](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) pomocí [rozhraní FirebaseInstanceId API společnosti Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). V tomto kurzu je název třídy `MyInstanceIDService`. 
   
    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`. 
   
    ```xml
        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>
    ```

2. Po obdržení registračního tokenu FCM z rozhraní FirebaseInstanceId API ho použijete k [registraci do centra oznámení Azure](notification-hubs-push-notification-registration-management.md). Tuto registrace podpoříte na pozadí pomocí `IntentService` s názvem `RegistrationIntentService`. Tato služba bude také odpovědná za aktualizaci vašeho registračního tokenu FCM.
   
    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`. 
   
    ```xml
        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>
    ```

3. Musíte také definovat příjemce pro příjem oznámení. Přidejte následující definice příjemce do souboru AndroidManifest.xml uvnitř značky `<application>`. Nahraďte zástupný symbol `<your package>` skutečným názvem vašeho balíčku zobrazeného v horní části souboru `AndroidManifest.xml`.

    ```xml
        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>
    ```

4. Přidejte následující nezbytná oprávnění související s FCM pod značkou `</application>`. Nezapomeňte nahradit `<your package>` názvem balíčku, který je zobrazen v horní části souboru `AndroidManifest.xml`.
   
    Další informace o těchto oprávnění najdete v článku [Nastavení klientské aplikace GCM pro Android](https://developers.google.com/cloud-messaging/android/client#manifest) a [Migrace klientské aplikace GCM pro Android do služby Firebase Cloud Messaging](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm).
   
    ```xml
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Přidání kódu
1. V zobrazení projektu rozbalte **app** > **src** > **main** > **java**. Klikněte pravým tlačítkem na váš balíček ve složce **java**, klikněte na tlačítko **Nový** a pak klikněte na tlačítko **třída jazyka Java**. Přidejte novou třídu s názvem `NotificationSettings`. 
   
    ![Android Studio – nová třída Java](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)
   
    Nezapomeňte aktualizovat tyto tři zástupné symboly v následujícím kódu pro třídu `NotificationSettings`:
   
   * **SenderId:** ID odesílatele, které jste získali v [konzole Firebase](https://firebase.google.com/console/)v nastavení projektu na kartě **Zprávy v cloudu**.
   * **HubListenConnectionString**: připojovací řetězec **DefaultListenAccessSignature** pro rozbočovač. Tento připojovací řetězec můžete zkopírovat kliknutím na **Zásady přístupu** ve vašem centru na webu [Azure Portal].
   * **HubName:** Použijte název centra oznámení, který se zobrazí v okně centra na webu [Azure Portal].
     
     Kód `NotificationSettings`:
     
    ```java
       public class NotificationSettings {
     
           public static String SenderId = "<Your project number>";
           public static String HubName = "<Your HubName>";
           public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
       }
    ```

2. Pomocí předchozího postupu přidejte další novou třídu s názvem `MyInstanceIDService`. Toto je vaše implementace služby naslouchání ID instance.
   
    Kód pro tuto třídu volá vaši službu `IntentService`, aby na pozadí [obnovila token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
   
    ```java
        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;

        public class MyInstanceIDService extends FirebaseInstanceIdService {

            private static final String TAG = "MyInstanceIDService";

            @Override
            public void onTokenRefresh() {

                Log.d(TAG, "Refreshing GCM Registration Token");

                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };
    ```

1. Přidejte další novou třídu do projektu s názvem, `RegistrationIntentService`. Toto je implementace pro vaši službu `IntentService`, která zpracovává [obnovení tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a [registraci v centru oznámení](notification-hubs-push-notification-registration-management.md).
   
    Pro tuto třídu použijte následující kód.
   
    ```java
        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class RegistrationIntentService extends IntentService {
   
            private static final String TAG = "RegIntentService";
   
            private NotificationHub hub;
   
            public RegistrationIntentService() {
                super(TAG);
            }
   
            @Override
            protected void onHandleIntent(Intent intent) {
   
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
   
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
   
                    // Storing the registration ID that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
   
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
   
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
   
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
   
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
   
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
   
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }
    ```

2. Do vaší třídy `MainActivity` přidejte následující prohlášení `import` nad deklaraci třídy.
   
    ```java
        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

3. Add the following private members at the top of the class. You use these to [check the availability of Google Play Services as recommended by Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
    ```java
        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

4. Ve své třídě `MainActivity` přidejte následující metodu pro dostupnost služeb Google Play. 
   
    ```java
        /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }
    ```

5. Ve třídě `MainActivity` přidejte následující kód, který kontroluje Služby Google Play před voláním služby `IntentService`, aby získal registrační token FCM a zaregistroval se v centru oznámení.
   
    ```java
        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }
    ```

6. Do metody `OnCreate` třídy `MainActivity` přidejte následující kód pro spuštění procesu registrace při vytvoření aktivity.
   
    ```java
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }
    ```

7. Pro účely kontroly stavu aplikace a hlášení stavu v rámci aplikace přidejte do souboru `MainActivity` tyto další metody.
   
    ```java
        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
   
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
   
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
   
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
   
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }
    ```

8. Metoda `ToastNotify` používá ovládání *„Hello World“* `TextView` k trvalému hlášení stavu a oznámení v aplikaci. Do rozložení souboru activity_main.xml přidejte následující ID pro tento ovládací prvek.
   
    ```java
       android:id="@+id/text_hello"
    ```

9. Dále přidáte podtřídu pro vašeho příjemce, kterého jste definovali v souboru AndroidManifest.xml. Přidejte další novou třídu do projektu s názvem `MyHandler`.
10. Nad `MyHandler.java` přidejte následující příkazy pro import:
    
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;
    ```

11. Přidejte následující kód pro třídu `MyHandler` a vytvořte tak podtřídu `com.microsoft.windowsazure.notifications.NotificationsHandler`.
    
    Tento kód přepíše metodu `OnReceive`, takže obslužná rutina ohlašuje přijatá oznámení. Obslužná rutina také odesílá nabízená oznámení správci oznámení Android pomocí metody `sendNotification()`. Metoda `sendNotification()` musí být spouštěna, když není aplikace spuštěna a není přijato oznámení.
    
    ```java
        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
    
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
    
            private void sendNotification(String msg) {
    
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
    
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
    
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }
    ```

12. V Android Studio na řádku nabídek klikněte na tlačítko **Sestavit** > **Znovu sestavit projekt** a ujistěte se, zda se ve vašem kódu nenachází žádné chyby.
13. Spusťte aplikaci na svém zařízení a ověřte, jestli se úspěšně zaregistrovala v centru oznámení. 
    
    > [!NOTE]
    > Registrace může být při počátečním spuštění neúspěšná, dokud se nezavolá metoda `onTokenRefresh()` ze služby instance ID. Obnovení by mělo zahájit úspěšnou registraci v centru oznámení.
    > 
    > 

## <a name="sending-push-notifications"></a>Odeslání nabízených oznámení
Příjem nabízených oznámení ve vaší aplikaci můžete otestovat jejich odesláním prostřednictvím webu [Azure Portal] – hledejte v centru část **Řešení potíží**, jak je znázorněno níže.

![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="testing-your-app"></a>Testování vaší aplikace
#### <a name="push-notifications-in-the-emulator"></a>Nabízená oznámení v emulátoru
Pokud chcete testovat nabízená oznámení uvnitř emulátoru, ověřte, zda bitová kopie emulátoru podporuje úroveň rozhraní Google API, kterou jste zvolili pro vaši aplikaci. Pokud vaše image nepodporuje nativní rozhraní Google API, zobrazí se výjimka **SLUŽBA\_NENÍ\_K DISPOZICI**.

Kromě výše uvedeného zajistěte, že jste přidali účet Google do svého spuštěného emulátoru pod položkou **Nastavení** > **účtů**. V opačném případě mohou vaše pokusy o registraci s GCM mít za následek výjimku **OVĚŘOVÁNÍ\_SE NEZDAŘILO**.

#### <a name="running-the-application"></a>Spouštění aplikace.
1. Spusťte aplikaci a všimněte si, že je ID registrace hlášené pro úspěšnou registraci.
   
    ![Testování v systému Android – registrace kanálu](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)
2. Zadejte zprávu oznámení k odeslání do všech zařízení Android, která byla zaregistrovaná v centru.
   
    ![Testování v systému Android – odesílání zprávy](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)
3. Stiskněte tlačítko **Odeslat oznámení**. Všechna zařízení, která mají spuštěné aplikace, zobrazí instance `AlertDialog` se zprávou nabízených oznámení. Zařízení, která nemají spuštěnou aplikaci, ale byla dříve registrována pro nabízená oznámení, obdrží oznámení ve správci oznámení Android. Ta lze zobrazit potažením dolů z levého horního rohu.
   
    ![Testování v systému Android – oznámení](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

## <a name="next-steps"></a>Další kroky
Jako další krok doporučujeme tutoriál [Použití Notification Hubs k odeslání nabízených oznámení uživatelům]. Předvádí postup odesílání oznámení z back-endu ASP.NET s využitím značek k cílení na konkrétní uživatele.


Pokud chcete segmentovat uživatele podle zájmových skupin, podívejte se na tutoriál [Používání centra oznámení k odesílání novinek].

Další obecné informace o centrech oznámení naleznete v tématu naše [Pokyny centra oznámení].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Pokyny centra oznámení]: notification-hubs-push-notification-overview.md
[Použití Notification Hubs k odeslání nabízených oznámení uživatelům]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Používání centra oznámení k odesílání novinek]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
