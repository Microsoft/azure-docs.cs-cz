---
title: Zasílání nabízených oznámení aplikacím pro Android službami Azure Notification Hubs a Firebase Cloud Messaging | Microsoft Docs
description: V tomto kurzu se naučíte používat služby Azure Notification Hubs a Google Firebase Cloud Messaging k zasílání nabízených oznámení zařízením s Androidem.
services: notification-hubs
documentationcenter: android
keywords: nabízená oznámení, nabízené oznámení, nabízené oznámení android, fcm, firebase cloud messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/05/2019
ms.author: jowargo
ms.openlocfilehash: d2de4a4be8838cf696d2d3ed6589e8f154a6ca05
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55959823"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Kurz: Odesílání nabízených oznámení do zařízení s Androidem pomocí Azure Notification Hubs a služby Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs a služby Firebase Cloud Messaging (FCM) k odesílání nabízených oznámení do aplikace pro Android. V tomto kurzu vytvoříte prázdnou aplikaci pro Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM).

Dokončený kód v tomto kurzu lze stáhnout z portálu Github [zde](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte projekt v Android Studiu.
> * Vytvoříte projekt Firebase, který podporuje Firebase Cloud Messaging.
> * Vytvoříte centrum oznámení.
> * Připojíte aplikaci k centru oznámení.
> * Otestujete aplikaci.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/).

* Kromě aktivního účtu Azure uvedeného výše budete v tomto kurzu potřebovat nejnovější verzi [Android Studia](https://go.microsoft.com/fwlink/?LinkId=389797).
* Android 2.3 nebo novější pro službu Firebase Cloud Messaging.
* Služba Firebase Cloud Messaging vyžaduje úložiště Google verze 27 nebo novější.
* Služby Google Play 9.0.2 nebo novější pro službu Firebase Cloud Messaging.
* Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro Android Apps.

## <a name="create-an-android-studio-project"></a>Vytvoření projektu v Android Studiu

1. Spusťte Android Studio.
2. Vyberte **souboru** přejděte v nabídce **nový**a vyberte **nový projekt**. 
2. Na **zvolte projekt** stránce **prázdná aktivita**a vyberte **Další**. 
3. Na **nakonfigurujete svůj projekt** stránce, proveďte následující kroky: 
    1. Zadejte **název** pro aplikaci.
    2. Zadejte umístění, kam chcete uložit soubory projektu. 
    3. Vyberte **Finish** (Dokončit). 

        ![Konfigurace projektu)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Vytvoření projektu Firebase, který podporuje FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Konfigurace centra oznámení

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurace nastavení služby Firebase Cloud Messaging pro centrum

1. Vyberte **Google (GCM/FCM)** pod **nastavení** v nabídce vlevo. 
2. Vložit **klíč serveru** FCM projektu, který jste předtím uložili. 
3. Daných **Uložit** na panelu nástrojů. 

    ![Azure Notification Hubs – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Zobrazí se zpráva ve výstrahách, notification hubs se úspěšně aktualizoval. **Uložit** je tlačítko neaktivní. 

Vaše centrum oznámení je teď nakonfigurováno pro práci se službou Firebase Cloud Messaging. Zároveň máte připojovací řetězce, pomocí kterých můžete svojí aplikaci zaregistrovat pro příjem a odesílání nabízených oznámení.

## <a id="connecting-app"></a>Připojte aplikaci k centru oznámení

### <a name="add-google-play-services-to-the-project"></a>Přidejte do projektu služby Google Play

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Přidání knihoven Azure Notification Hubs

1. Do souboru `Build.Gradle` pro **aplikaci** přidejte následující řádky v části **závislosti**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Přidejte následující úložiště za část **závislosti**.

    ```gradle
    repositories {
        maven {
            url "http://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Přidání podpory Google Firebase

1. V `Build.Gradle` souboru **aplikace**, přidejte následující řádky v **závislosti** části, pokud ještě neexistuje. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.0'
    ```

2. Pokud ještě neexistuje, přidejte následující modul plug-in na konci souboru. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```

### <a name="updating-the-androidmanifestxml"></a>Aktualizace souboru AndroidManifest.xml

1. Abyste zajistili podporu FCM, musíte do vašeho kódu implementovat službu naslouchání ID instance, která se používá k [získání registračních tokenů](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) pomocí [rozhraní FirebaseInstanceId API společnosti Google](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). V tomto kurzu je název třídy `MyInstanceIDService`.

    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`.

    ```xml
    <service android:name=".MyInstanceIDService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Jakmile získáte z rozhraní API instance Firebase registrační token FCM, použijte ho k [registraci do služby Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). K podpoře registrace na pozadí slouží `IntentService` s názvem `RegistrationIntentService`. Tato služba také zodpovídá za aktualizaci registračního tokenu FCM.

    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Musíte také definovat příjemce pro příjem oznámení. Přidejte následující definice příjemce do souboru AndroidManifest.xml uvnitř značky `<application>`. 

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

    > [!IMPORTANT]
    > Nahraďte zástupný symbol `<your package NAME>` skutečným názvem vašeho balíčku zobrazeného v horní části souboru `AndroidManifest.xml`.
4. Vyberte **synchronizovat nyní** na panelu nástrojů.
5. Přidejte následující nezbytná oprávnění související s FCM **níže** `</application>` značky.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="adding-code"></a>Přidání kódu

1. V zobrazení projektu rozbalte **app** > **src** > **main** > **java**. Klikněte pravým tlačítkem na váš balíček ve složce **java**, klikněte na tlačítko **Nový** a pak klikněte na tlačítko **třída jazyka Java**. Zadejte `NotificationSettings` pro název a vyberte **OK**.

    Nezapomeňte aktualizovat tyto tři zástupné symboly v následujícím kódu pro třídu `NotificationSettings`:

   * **HubListenConnectionString**: **DefaultListenAccessSignature** připojovací řetězec centra. Tento připojovací řetězec můžete zkopírovat kliknutím na **Zásady přístupu** ve vašem centru na webu [Azure Portal].
   * **HubName**: Použijte název, který se zobrazí na stránce centra v centru oznámení [Azure Portal].

     Kód `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

    > [!IMPORTANT]
    > Zadejte **název** a **DefaultListenSharedAccessSignature** vašeho centra oznámení před emitentových Další. 
2. Přidejte další třídu s názvem `MyInstanceIDService`. Tato třída představuje vaši implementaci naslouchací služby ID instance.

    Kód pro tuto třídu volá vaši službu `IntentService`, aby na pozadí [obnovila token FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class MyInstanceIDService extends FirebaseInstanceIdService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.d(TAG, "Refreshing FCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Přidejte další novou třídu do projektu s názvem, `RegistrationIntentService`. Tato třída implementuje rozhraní `IntentService` a provádí [obnovení tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a [registraci v centru oznámení](notification-hubs-push-notification-registration-management.md).

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
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
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
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
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

4. Do vaší třídy `MainActivity` přidejte následující prohlášení `import` nad deklaraci třídy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

5. Přidejte následující členy v horní části třídy. Tato pole slouží ke [kontrole dostupnosti Služeb Google Play, kterou doporučuje Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

6. Ve vaší `MainActivity` třídy, přidejte následující metodu ke kontrole dostupnosti služby Google Play.

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

7. Ve třídě `MainActivity` přidejte následující kód, který kontroluje Služby Google Play před voláním služby `IntentService`, aby získal registrační token FCM a zaregistroval se v centru oznámení.

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

8. Do metody `OnCreate` třídy `MainActivity` přidejte následující kód pro spuštění procesu registrace při vytvoření aktivity.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        MyHandler.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

9. Pro účely kontroly stavu aplikace a hlášení stavu v rámci aplikace přidejte do souboru `MainActivity` tyto další metody.

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

10. Metoda `ToastNotify` používá ovládání *„Hello World“* `TextView` k trvalému hlášení stavu a oznámení v aplikaci. Ve vaší **res** -> **rozložení** -> **activity_main.xml** rozložení, přidejte následující ID pro tento ovládací prvek.

    ```java
    android:id="@+id/text_hello"
    ```

11. Dále přidejte podtřídu příjemce definovanou v souboru AndroidManifest.xml. Přidejte další novou třídu do projektu s názvem `MyHandler`.

12. Nad `MyHandler.java` přidejte následující příkazy pro import:

    ```java
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;    
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    ```

13. Přidejte následující kód pro třídu `MyHandler` a vytvořte tak podtřídu `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Tento kód přepíše metodu `OnReceive`, takže obslužná rutina ohlašuje přijatá oznámení. Obslužná rutina také odesílá nabízená oznámení správci oznámení Android pomocí metody `sendNotification()`. Metoda `sendNotification()` musí být spouštěna, když není aplikace spuštěna a není přijato oznámení.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
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
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
                NotificationsManager.handleNotifications(context, "", MyHandler.class);
            }
        }
    }
    ```

14. V Android Studio na řádku nabídek klikněte na tlačítko **Sestavit** > **Znovu sestavit projekt** a ujistěte se, zda se ve vašem kódu nenachází žádné chyby. Pokud se zobrazí chyba týkající `ic_launcher` ikonu, odeberte ze souboru AndroidManifest.xml následující příkaz. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Spusťte aplikaci na svém zařízení a ověřte, jestli se úspěšně zaregistrovala v centru oznámení.

    > [!NOTE]
    > Registrace může být při počátečním spuštění neúspěšná, dokud se nezavolá metoda `onTokenRefresh()` ze služby instance ID. Obnovení by mělo zahájit úspěšnou registraci v centru oznámení.

    ![Registrace zařízení byla úspěšná](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Odeslání zkušebního oznámení z centra oznámení

Když chcete z webu [Azure Portal] odeslat nabízená oznámení, proveďte následující akce:

1. Na webu Azure Portal na **centra oznámení** stránky pro vaše Centrum oznámení, vyberte **poslat na zkoušku** v **Poradce při potížích s** oddílu.
3. V položce **Platformy** vyberte **Android**.
4. Vyberte **Poslat**.  Oznámení se na zařízení s Androidem nezobrazí, protože jste na něm nespustili mobilní aplikaci. Po spuštění mobilní aplikace znovu vyberte tlačítko **Poslat**, aby se zobrazilo oznámení.
5. **Výsledek** operace si můžete prohlédnout v seznamu dole v okně.

    ![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Zobrazí se zpráva oznámení na vašem zařízení. 

    ![Oznámení na zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Spustit mobilní aplikaci v emulátoru
Pokud chcete testovat nabízená oznámení uvnitř emulátoru, ověřte, zda bitová kopie emulátoru podporuje úroveň rozhraní Google API, kterou jste zvolili pro vaši aplikaci. Pokud vaše image nepodporuje nativní rozhraní Google API, se může zobrazit **služby\_není\_dostupné** výjimky.

Také se ujistěte, že jste svůj účet Google přidali do spuštěného emulátoru v části **Nastavení** > **Účty**. V opačném případě mohou vaše pokusy o registraci s GCM mít za následek výjimku **OVĚŘOVÁNÍ\_SE NEZDAŘILO**.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste použili službu Firebase Cloud Messaging k odeslání nabízených oznámení zařízením s Androidem. Pokud se chcete naučit posílat nabízená oznámení službou Google Cloud Messaging, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení zařízením s Androidem službou Google Cloud Messaging](notification-hubs-android-push-notification-google-gcm-get-started.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
