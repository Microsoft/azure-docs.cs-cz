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
ms.date: 04/30/2019
ms.author: jowargo
ms.openlocfilehash: c21b1e38077575fc49221150a61693a23aa408a3
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509102"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>Kurz: Odesílání nabízených oznámení do zařízení s Androidem pomocí Azure Notification Hubs a služby Google Firebase Cloud Messaging

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs a služby Firebase Cloud Messaging (FCM) k odesílání nabízených oznámení do aplikace pro Android. V tomto kurzu vytvoříte prázdnou aplikaci pro Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM).

Dokončený kód v tomto kurzu si můžete stáhnout [z Githubu](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte projekt v Android Studiu.
> * Vytvoříte projekt Firebase, který podporuje Firebase Cloud Messaging.
> * Vytvoření centra.
> * Připojte svou aplikaci k centru.
> * Otestujete aplikaci.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/). 

Budete potřebovat následující položky: 

* Nejnovější verzi [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 nebo novější pro službu Firebase Cloud Messaging
* Úložiště Google verze 27 nebo novější pro službu Firebase Cloud Messaging
* Služby Google Play 9.0.2 nebo novější pro službu Firebase Cloud Messaging

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro aplikace pro Android způsobem.

## <a name="create-an-android-studio-project"></a>Vytvoření projektu v Android Studiu

1. Spusťte Android Studio.
2. Vyberte **souboru**, přejděte na **nový**a pak vyberte **nový projekt**. 
2. Na **zvolte projekt** stránce **prázdná aktivita**a pak vyberte **Další**. 
3. Na **nakonfigurujete svůj projekt** stránce, proveďte následující kroky: 
    1. Zadejte název aplikace.
    2. Zadejte umístění, do kterého chcete uložit soubory projektu. 
    3. Vyberte **Finish** (Dokončit). 

        ![Konfigurace projektu)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Vytvoření projektu Firebase, který podporuje FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurace rozbočovače

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurace nastavení služby Firebase Cloud Messaging pro centrum

1. V levém podokně v části **nastavení** vyberte **Google (GCM/FCM)** . 
2. Zadejte **klíč serveru** FCM projektu, který jste předtím uložili. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Centra oznámení Azure – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Na webu Azure portal a zobrazí upozornění, že centrum se úspěšně aktualizoval. **Uložit** je tlačítko neaktivní. 

Vaše centrum je nyní nakonfigurováno pro práci se službou Firebase Cloud Messaging. Máte také připojovací řetězce, které jsou potřebné pro odesílání oznámení do zařízení a zaregistrovat aplikaci pro příjem oznámení.

## <a id="connecting-app"></a>Připojte aplikaci k centru oznámení

### <a name="add-google-play-services-to-the-project"></a>Přidejte do projektu služby Google Play

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="add-azure-notification-hubs-libraries"></a>Přidání knihoven Azure Notification Hubs

1. V souboru Build.Gradle pro aplikaci přidejte následující řádky v části obsahující závislosti.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Přidejte následující úložiště za část obsahující závislosti.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Přidání podpory Google Firebase

1. V souboru Build.Gradle pro aplikaci, přidejte následující řádky v **závislosti** části, pokud ještě neexistují. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Pokud už tam není, přidejte následující modul plug-in na konci souboru. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Vyberte **synchronizovat nyní** na panelu nástrojů.

### <a name="update-the-androidmanifestxml-file"></a>Aktualizace souboru AndroidManifest.xml

1. Po obdržení registračního tokenu FCM, použijete ho k [zaregistrovat pomocí Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Podporu této registraci na pozadí s použitím `IntentService` s názvem `RegistrationIntentService`. Tato služba také aktualizaci vašeho registračního tokenu FCM.

    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    ```

2. Musíte také definovat příjemce pro příjem oznámení. Přidejte následující definice příjemce do souboru AndroidManifest.xml uvnitř značky `<application>`. 

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
    > Nahradit `<your package NAME>` zástupný symbol skutečným názvem vašeho balíčku, který se zobrazí v horní části souboru AndroidManifest.xml.
3. Přidejte následující nezbytná související s FCM oprávnění níže `</application>` značky.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Přidejte kód

1. V zobrazení projektu rozbalte **app** > **src** > **main** > **java**. Klikněte pravým tlačítkem na váš balíček ve složce **java**vyberte **nový**a pak vyberte **třídy Java**. Zadejte **NotificationSettings** název a pak vyberte **OK**.

    Nezapomeňte aktualizovat tyto tři zástupné symboly v následujícím kódu pro třídu `NotificationSettings`:

   * **HubListenConnectionString**: **DefaultListenAccessSignature** připojovací řetězec centra. Tento připojovací řetězec můžete zkopírovat kliknutím **zásady přístupu** ve vašem Centru v [Azure Portal].
   * **HubName**: Použijte název, který se zobrazí na stránce centra v centru [Azure Portal].

     Kód `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Zadejte **název** a **DefaultListenSharedAccessSignature** centra než budete pokračovat. 

3. Přidejte další novou třídu do projektu s názvem `RegistrationIntentService`. Tato třída implementuje `IntentService` rozhraní. Také obstará [obnovení tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a [registraci v centru oznámení](notification-hubs-push-notification-registration-management.md).

    Pro tuto třídu použijte následující kód.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

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
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
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

                // Check to see if the token has been compromised and needs refreshing.
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
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. V `MainActivity` třídy, přidejte následující `import` příkazy nad deklaraci třídy.

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

6. V `MainActivity` třídy, přidejte následující metodu ke kontrole dostupnosti služby Google Play.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
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

7. V `MainActivity` třídy, přidejte následující kód, který kontroluje služby Google Play před voláním `IntentService` k získání tokenu pro registraci a FCM a zaregistroval se v centru:

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

8. V `OnCreate` metodu `MainActivity` třídy, přidejte následující kód pro spuštění procesu registrace při vytvoření aktivity:

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

9. Pokud chcete ověřit stav a sestavy stav aplikace ve vaší aplikaci, přidejte tyto další metody pro `MainActivity`:

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

10. Metoda `ToastNotify` používá ovládání *„Hello World“* `TextView` k trvalému hlášení stavu a oznámení v aplikaci. Ve vaší **res** > **rozložení** > **activity_main.xml** rozložení, přidejte následující ID pro tento ovládací prvek.

    ```java
    android:id="@+id/text_hello"
    ```

11. Dále přidáte podtřídu pro příjemce, který jste definovali v souboru AndroidManifest.xml. Přidejte další novou třídu do projektu s názvem `MyHandler`.

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

13. Přidejte následující kód `MyHandler` třídy, a vytvořte tak podtřídu z `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Tento kód přepíše metodu `OnReceive`, takže obslužná rutina ohlašuje přijatá oznámení. Obslužná rutina také odesílá nabízená oznámení správci oznámení Android pomocí metody `sendNotification()`. Volání `sendNotification()` metodu, když není aplikace spuštěna a není přijato oznámení.

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

14. V nástroji Android Studio, na panelu nabídek vyberte **sestavení** > **znovu sestavit projekt** abyste měli jistotu, že nejsou nějaké chyby v kódu. Pokud se zobrazí chyba týkající `ic_launcher` ikonu, odeberte ze souboru AndroidManifest.xml následující příkaz: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
15. Spusťte aplikaci na vašem zařízení a ověřte, že se úspěšně zaregistrovala centru.

    > [!NOTE]
    > Registrace může selhat při počátečním spuštění neúspěšná, dokud `onTokenRefresh()` volání metody ze služby instance ID. Obnovení by mělo zahájit úspěšnou registraci v centru oznámení.

    ![Registrace zařízení byla úspěšná](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Odeslání zkušebního oznámení z centra oznámení

Můžete odesílat nabízená oznámení z [Azure Portal] podle následujících kroků:

1. Na webu Azure Portal, na stránce centra oznámení pro vaše centrum, vyberte **poslat na zkoušku** v **Poradce při potížích s** oddílu.
3. V položce **Platformy** vyberte **Android**.
4. Vyberte **Poslat**.  Oznámení na zařízení s Androidem zatím nezobrazí, že na něm ještě běží mobilní aplikace. Po spuštění v mobilní aplikaci, vyberte **odeslat** tlačítko znovu, abyste viděli zprávy oznámení.
5. Zobrazit výsledek operace v seznamu dole.

    ![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Zobrazí se zpráva upozornění na vašem zařízení. 

    ![Oznámení na zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Spustit mobilní aplikaci v emulátoru
Před testováním nabízená oznámení uvnitř emulátoru, ujistěte se, že bitová kopie emulátoru podporuje úroveň rozhraní Google API, kterou jste zvolili pro vaši aplikaci. Pokud vaše image nepodporuje nativní rozhraní Google API, může se zobrazit **služby\_není\_dostupné** výjimky.

Kromě toho se ujistěte, že jste přidali účet Google do svého spuštěného emulátoru pod **nastavení** > **účty**. V opačném případě může způsobit vaše pokusy o registraci s FCM **ověřování\_NEÚSPĚŠNÉ** výjimky.

## <a name="next-steps"></a>Další postup
V tomto kurzu použijete k vysílání oznámení pro všechna zařízení s Androidem, které jste zaregistrovali ve službě Firebase Cloud Messaging. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Kurz: Nabízená oznámení odesílaná konkrétním zařízením s Androidem](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
