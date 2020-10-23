---
title: Posílání nabízených oznámení do systému Android pomocí Azure Notification Hubs a sady Firebase SDK verze 0,6 | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí služby Azure Notification Hubs a služby Google Firebase Cloud Messaging odesílat nabízená oznámení na zařízení s Androidem (verze 0,6).
services: notification-hubs
documentationcenter: android
keywords: nabízená oznámení, nabízené oznámení, nabízené oznámení android, fcm, firebase cloud messaging
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: c5485dacc4d9e3210ad69819caf4e36f96c626da
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428388"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-06"></a>Kurz: odeslání nabízených oznámení na zařízení s Androidem pomocí sady Firebase SDK verze 0,6

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs a sadu SDK FCM (Firebase Cloud Messaging) verze 0,6 k odesílání nabízených oznámení do aplikace pro Android. V tomto kurzu vytvoříte prázdnou aplikaci pro Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM).

Dokončený kód tohoto kurzu si můžete stáhnout [z GitHubu](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte projekt v Android Studiu.
> * Vytvoříte projekt Firebase, který podporuje Firebase Cloud Messaging.
> * Vytvořte centrum.
> * Připojte aplikaci k centru.
> * Otestujete aplikaci.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/). 

Potřebujete také následující položky: 

* Nejnovější verze [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2,3 nebo vyšší pro Firebase Cloud Messaging
* Revize úložiště Google 27 nebo vyšší pro zasílání zpráv Firebase Cloud
* Služby Google Play 9.0.2 nebo vyšší pro Firebase cloudové zasílání zpráv

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy Notification Hubs pro aplikace pro Android.

## <a name="create-an-android-studio-project"></a>Vytvoření projektu v Android Studiu

1. Spusťte Android Studio.
2. Vyberte **soubor**, přejděte na **Nový**a potom vyberte **Nový projekt**. 
2. Na stránce **Zvolte projekt** vyberte možnost **prázdná aktivita**a potom vyberte možnost **Další**. 
3. Na stránce **Konfigurovat projekt** proveďte následující kroky: 
    1. Zadejte název aplikace.
    2. Zadejte umístění, do kterého chcete uložit soubory projektu. 
    3. Vyberte **Dokončit**. 

        ![Konfigurace projektu)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Vytvoření projektu Firebase, který podporuje FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurace centra

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurace nastavení služby Firebase Cloud Messaging pro centrum

1. V levém podokně v části **Nastavení** vyberte **Google (GCM/FCM)**. 
2. Zadejte **klíč serveru** pro projekt FCM, který jste předtím uložili. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Centrum oznámení Azure – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure Portal zobrazí zprávu v upozorněních, že se centrum úspěšně aktualizovalo. Tlačítko **Uložit** je zakázané. 

Vaše centrum je teď nakonfigurované tak, aby fungovalo s Firebase cloudovým zasíláním zpráv. Máte také připojovací řetězce, které jsou nezbytné k odesílání oznámení do zařízení a k registraci aplikace pro příjem oznámení.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Připojte aplikaci k centru oznámení

### <a name="add-google-play-services-to-the-project"></a>Přidejte do projektu služby Google Play

1. V Android Studio v nabídce vyberte **nástroje** a pak vyberte **správce SDK**. 
2. Vyberte cílovou verzi Android SDK, která se používá ve vašem projektu. Pak vyberte **Zobrazit podrobnosti balíčku**. 

    ![Android SDK Manager – výběr cílové verze](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Vyberte **Google API**, pokud ještě není nainstalovaná.

    ![Správce Android SDK – vybraná rozhraní API pro Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Přepněte na kartu **SDK Tools** . Pokud jste ještě nenainstalovali Služby Google Play, vyberte **služby Google Play** , jak je znázorněno na následujícím obrázku. Pak vyberte **použít** k instalaci. Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

    ![Android SDK Manager – Služby Google Play vybráno](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Pokud se zobrazí dialogové okno **Potvrdit změnu** , vyberte **OK**. Instalační program součásti nainstaluje požadované součásti. Po instalaci součástí vyberte **Dokončit** .
4. Kliknutím na **tlačítko OK** zavřete dialogové okno **nastavení pro nové projekty** .  
1. Otevřete soubor AndroidManifest.xml a přidejte následující značku do značky *aplikace* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Přidání knihoven Azure Notification Hubs

1. V souboru Build. Gradle pro aplikaci přidejte následující řádky do oddílu závislosti.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
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

1. V souboru Build. Gradle pro aplikaci přidejte následující řádky v části **závislosti** , pokud ještě neexistují. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Na konec souboru přidejte následující modul plug-in, pokud tam ještě není. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Vyberte **synchronizovat hned** na panelu nástrojů.

### <a name="update-the-androidmanifestxml-file"></a>Aktualizace souboru AndroidManifest.xml

1. Po přijetí registračního tokenu FCM ho použijete k [registraci v Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Tuto registraci podporujete na pozadí pomocí `IntentService` pojmenovaného `RegistrationIntentService` . Tato služba také aktualizuje registrační token FCM. Vytvoříte také třídu s názvem `FirebaseService` jako podtřídu `FirebaseMessagingService` a přepíšete `onMessageReceived` metodu pro příjem a zpracování oznámení. 

    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. Přidejte následující nezbytná oprávnění související s FCM pod `</application>` značkou.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Přidat kód

1. V zobrazení projektu rozbalte položku **App**  >  **Src**  >  **Main**  >  **Java**. Klikněte pravým tlačítkem myši na složku balíčku v kategorii **Java**, vyberte možnost **Nový**a potom vyberte možnost **Třída Java**. Jako název zadejte **NotificationSettings** a pak vyberte **OK**.

    Nezapomeňte aktualizovat tyto tři zástupné symboly v následujícím kódu pro třídu `NotificationSettings`:

   * **HubListenConnectionString**: připojovací řetězec **DefaultListenAccessSignature** pro rozbočovač. Tento připojovací řetězec můžete zkopírovat kliknutím na **zásady přístupu** v centru v [Azure Portal].
   * **HubName**: použijte název vašeho rozbočovače, který se zobrazí na stránce centra v [Azure Portal].

     Kód `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Než budete pokračovat, zadejte **název** a **DefaultListenSharedAccessSignature** svého rozbočovače. 

2. Přidejte další novou třídu do projektu s názvem `RegistrationIntentService`. Tato třída implementuje `IntentService` rozhraní. Také zpracovává [aktualizaci tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a [registraci v centru oznámení](notification-hubs-push-notification-registration-management.md).

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
               else if (!(storedToken = sharedPreferences.getString("FCMtoken", "")).equals(FCM_token)) {

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

3. Ve `MainActivity` třídě přidejte následující `import` příkazy nad deklaraci třídy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Přidejte následující členy v horní části třídy. Tato pole slouží ke [kontrole dostupnosti Služeb Google Play, kterou doporučuje Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Ve `MainActivity` třídě přidejte následující metodu pro kontrolu dostupnosti služby Google Play.

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

6. Ve `MainActivity` třídě přidejte následující kód, který kontroluje služby Google Play před voláním metody `IntentService` pro získání registračního tokenu FCM a registraci v rámci vašeho centra:

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

7. Do `OnCreate` metody `MainActivity` třídy přidejte následující kód, který spustí proces registrace při vytvoření aktivity:

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. K ověření stavu aplikace a stavu sestavy ve vaší aplikaci přidejte tyto další metody `MainActivity` :

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

9. Metoda `ToastNotify` používá ovládání *„Hello World“* `TextView` k trvalému hlášení stavu a oznámení v aplikaci. V rozložení **res**  >  **rozložení** zdrojů  >  **activity_main.xml** rozložení přidejte následující ID pro tento ovládací prvek.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Snímek obrazovky zobrazující ID Androidu: ID = "@ + ID/text_hello" použité pro ovládací prvek TextView](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Dále přidáte podtřídu pro přijímače, který jste definovali v AndroidManifest.xml. Přidejte další novou třídu do projektu s názvem `FirebaseService`.

11. Nad `FirebaseService.java` přidejte následující příkazy pro import:

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. Přidejte následující kód pro `FirebaseService` třídu a vytvořte její podtřídu `FirebaseMessagingService` .

    Tento kód přepíše `onMessageReceived` metodu a oznámení, která jsou přijata. odesílá také nabízená oznámení do správce oznámení Androidu pomocí `sendNotification()` metody. Volání `sendNotification()` metody, když aplikace neběží a oznámení se přijme.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
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
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. V Android Studio na řádku nabídek vyberte **sestavit znovu**  >  **sestavit projekt** , abyste se ujistili, že ve vašem kódu nejsou žádné chyby. Pokud se zobrazí chyba týkající se této `ic_launcher` ikony, odeberte z AndroidManifest.xml souboru následující příkaz: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Ujistěte se, že máte virtuální zařízení pro spuštění aplikace. Pokud žádný nemáte, přidejte ho následujícím způsobem:
    1. ![Otevřít Správce zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Vytvořit virtuální zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Spusťte aplikaci na vybraném zařízení a ověřte, že se do centra úspěšně zaregistruje.

    > [!NOTE]
    > Při počátečním spuštění může být registrace neúspěšná, dokud nebude `onTokenRefresh()` volána metoda služby ID instance. Obnovení by mělo zahájit úspěšnou registraci v centru oznámení.

    ![Registrace zařízení byla úspěšná.](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Odeslání zkušebního oznámení z centra oznámení

Nabízená oznámení můžete odesílat z [Azure Portal] provedením následujících kroků:

1. V Azure Portal na stránce Centra oznámení pro vaše centrum vyberte v části **Poradce při potížích** možnost **test Send** .
3. V položce **Platformy** vyberte **Android**.
4. Vyberte **Poslat**.  Na zařízení s Androidem se ještě nezobrazuje oznámení, protože jste na něm nespouštěli mobilní aplikaci. Po spuštění mobilní aplikace znovu vyberte tlačítko **Odeslat** a zobrazí se zpráva s oznámením.
5. Výsledek operace si můžete prohlédnout v seznamu dole v okně.

    ![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Na zařízení se zobrazí zpráva s oznámením. 

    ![Zpráva oznámení na zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Spustit mobilní aplikaci na emulátoru

Než otestujete nabízená oznámení v emulátoru, ujistěte se, že vaše image emulátoru podporuje úroveň rozhraní Google API, kterou jste zvolili pro vaši aplikaci. Pokud vaše image nepodporuje nativní rozhraní Google API, může být **Služba \_ \_ nedostupná** výjimka.

Také se ujistěte, že jste do svého spuštěného emulátoru v části **Nastavení**  >  **účty**přidali účet Google. V opačném případě se můžou vaše pokusy o registraci v **FCM \_ způsobit výjimku ověřování** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili Firebase Cloud Messaging pro vysílání oznámení na všechna zařízení s Androidem, která byla zaregistrovaná ve službě. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Kurz: nabízená oznámení na konkrétní zařízení s Androidem](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
