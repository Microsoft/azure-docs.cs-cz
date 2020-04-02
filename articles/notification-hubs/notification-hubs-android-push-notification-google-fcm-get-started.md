---
title: Odesílání nabízených oznámení androidu pomocí Center oznámení Azure a Firebase | Dokumenty společnosti Microsoft
description: V tomto kurzu se naučíte používat služby Azure Notification Hubs a Google Firebase Cloud Messaging k zasílání nabízených oznámení zařízením s Androidem.
services: notification-hubs
documentationcenter: android
keywords: nabízená oznámení, nabízené oznámení, nabízené oznámení android, fcm, firebase cloud messaging
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 09/11/2019
ms.openlocfilehash: e6bc4ed94c8b8e62740f81497231a163283ebcb7
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521557"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase"></a>Kurz: Odesílání nabízených oznámení do zařízení Android pomocí Firebase

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Tento kurz ukazuje, jak používat Azure Notification Hubs a Firebase Cloud Messaging (FCM) k nabízení oznámení do aplikace pro Android. V tomto kurzu vytvoříte prázdnou aplikaci pro Android, která bude přijímat nabízená oznámení pomocí služby Firebase Cloud Messaging (FCM).

Vyplněný kód pro tento kurz lze stáhnout [z GitHubu](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte projekt v Android Studiu.
> * Vytvoříte projekt Firebase, který podporuje Firebase Cloud Messaging.
> * Vytvořte rozbočovač.
> * Připojte aplikaci k rozbočovači.
> * Otestujete aplikaci.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/free/). 

Potřebujete také následující položky: 

* Nejnovější verze [Android Studia](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 nebo vyšší pro cloudové zprávy Firebase
* Revize úložiště Google 27 nebo vyšší pro zasílání zpráv Cloud Messaging Firebase
* Služby Google Play 9.0.2 nebo vyšší pro zasílání zpráv firebase cloud

Dokončení tohoto kurzu je předpokladem pro provádění všech ostatních kurzů centra oznámení pro aplikace pro Android.

## <a name="create-an-android-studio-project"></a>Vytvoření projektu v Android Studiu

1. Spusťte Android Studio.
2. Vyberte **Soubor**, přejděte na **Nový**a pak vyberte **Nový projekt**. 
2. Na stránce **Vybrat projekt** vyberte **Vyprázdnit aktivitu**a pak vyberte **Další**. 
3. Na stránce **Konfigurace projektu postupujte** takto: 
    1. Zadejte název aplikace.
    2. Určete umístění, do kterého chcete soubory projektu uložit. 
    3. Vyberte **Finish** (Dokončit). 

        ![Konfigurace projektu)](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>Vytvoření projektu Firebase, který podporuje FCM

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>Konfigurace rozbočovače

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurace nastavení služby Firebase Cloud Messaging pro centrum

1. V levém podokně vyberte v části **Nastavení** **Google (GCM/FCM).** 
2. Zadejte **klíč serveru** pro projekt FCM, který jste uložili dříve. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Azure Notification Hub – Google (FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Portál Azure zobrazí zprávu ve výstrahách, že centrum bylo úspěšně aktualizováno. Tlačítko **Uložit** je zakázáno. 

Vaše centrum je teď nakonfigurované pro práci s Firebase Cloud Messaging. Máte také připojovací řetězce, které jsou nezbytné k odesílání oznámení do zařízení a zaregistrovat aplikaci pro příjem oznámení.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Připojte aplikaci k centru oznámení

### <a name="add-google-play-services-to-the-project"></a>Přidejte do projektu služby Google Play

1. V Android Studiu vyberte v nabídce **Nástroje** a pak vyberte **Správce sady SDK**. 
2. Vyberte cílovou verzi sady Android SDK, která se používá v projektu. Pak vyberte **Zobrazit podrobnosti balíčku**. 

    ![Android SDK Manager - vyberte cílovou verzi](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Pokud ještě není nainstalovaná , vyberte **vyberte google api**.

    ![Android SDK Manager - Google API vybrána](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Přepněte na kartu **Nástroje sady SDK.** Pokud jste služby Google Play ještě nenainstalovali, vyberte **služby Google Play,** jak je znázorněno na následujícím obrázku. Pak vyberte **Použít** k instalaci. Poznamenejte si cestu k sadě SDK, abyste ji mohli použít později.

    ![Android SDK Manager - Google Play služby vybrané](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Pokud se zobrazí dialogové okno **Potvrdit změnu,** vyberte **OK**. Instalační služba součásti nainstaluje požadované součásti. Po instalaci součástí vyberte **Dokončit.**
4. Výběrem **možnosti OK** zavřete dialogové okno **Nastavení pro nové projekty.**  
1. Otevřete soubor AndroidManifest.xml a přidejte do značky *aplikace* následující značku.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Přidání knihoven Center oznámení Azure

1. Do souboru Build.Gradle pro aplikaci přidejte následující řádky v části závislosti.

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

1. Do souboru Build.Gradle pro aplikaci přidejte následující řádky v části **závislosti,** pokud ještě neexistují. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. Pokud soubor ještě neexistuje, přidejte na konec souboru následující modul plug-in. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. Na panelu nástrojů vyberte **Synchronizovat.**

### <a name="update-the-androidmanifestxml-file"></a>Aktualizace souboru AndroidManifest.xml

1. Po obdržení registračního tokenu FCM ho použijete k [registraci v Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). Podporujete tuto registraci na `IntentService` pozadí `RegistrationIntentService`pomocí pojmenované . Tato služba také aktualizuje registrační token FCM. Můžete také vytvořit `FirebaseService` třídu pojmenovanou jako podtřída `FirebaseMessagingService` a přepsat metodu `onMessageReceived` pro příjem a zpracování oznámení. 

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
3. Pod `</application>` značku přidejte následující potřebná oprávnění související s FCM.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>Přidat kód

1. V zobrazení projektu rozbalte hlavní**main** > **java** **aplikace** > **src** > . Klikněte pravým tlačítkem myši na složku balíčku v **javě**, vyberte **Nový**a pak vyberte **Java Class**. Zadejte **NotificationSettings** pro název a pak vyberte **OK**.

    Nezapomeňte aktualizovat tyto tři zástupné symboly v následujícím kódu pro třídu `NotificationSettings`:

   * **HubListenConnectionString**: připojovací řetězec **DefaultListenAccessSignature** pro rozbočovač. Tento připojovací řetězec můžete zkopírovat kliknutím na **přístupové zásady** ve vašem centru na [webu Azure Portal].
   * **HubName**: Použijte název svého centra, který se zobrazí na stránce centra na [webu Azure Portal].

     Kód `NotificationSettings`:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > Zadejte **název** a **DefaultListenSharedAccessSignature** vašeho centra před pokračováním dále. 

2. Přidejte další novou třídu do projektu s názvem `RegistrationIntentService`. Tato třída implementuje `IntentService` rozhraní. Také zpracovává [aktualizace tokenu FCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a [registraci v centru oznámení](notification-hubs-push-notification-registration-management.md).

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

3. Ve `MainActivity` třídě přidejte `import` následující příkazy nad deklaraci třídy.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. Do horní části třídy přidejte následující členy. Tato pole slouží ke [kontrole dostupnosti Služeb Google Play, kterou doporučuje Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. Do `MainActivity` třídy přidejte následující metodu, abyste zkontrolovali dostupnost služeb Google Play.

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

6. Do `MainActivity` třídy přidejte následující kód, který zkontroluje `IntentService` služby Google Play, než zavoláte na registrační token FCM a zaregistrujte se u svého centra:

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

7. V `OnCreate` metodě `MainActivity` třídy přidejte následující kód pro spuštění procesu registrace při vytvoření aktivity:

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

8. Pokud chcete ověřit stav aplikace a stav sestavy `MainActivity`v aplikaci, přidejte do aplikace tyto další metody:

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

9. Metoda `ToastNotify` používá ovládání *„Hello World“* `TextView` k trvalému hlášení stavu a oznámení v aplikaci. Do**rozložení** >  **res** > **activity_main.xml** přidejte pro tento ovládací prvek následující ID.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. Dále přidáte podtřídu pro příjemce, který jste definovali v Souboru AndroidManifest.xml. Přidejte další novou třídu do projektu s názvem `FirebaseService`.

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

12. Přidejte následující kód `FirebaseService` pro třídu, takže `FirebaseMessagingService`je podtřídou .

    Tento kód přepíše metodu `onMessageReceived` a hlásí oznámení, která jsou přijata. také odešle nabízené oznámení správce oznámení Android `sendNotification()` pomocí metody. Volání `sendNotification()` metody, když aplikace není spuštěna a je přijato oznámení.

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

13. V Android Studio, na řádku nabídek, vyberte **sestavení** > **projektu znovu sestavit** a ujistěte se, že nejsou žádné chyby ve vašem kódu. Pokud se zobrazí chyba `ic_launcher` o ikonu, odeberte následující příkaz ze souboru AndroidManifest.xml: 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. Ujistěte se, že máte virtuální zařízení pro spuštění aplikace. Pokud ho nemáte, přidejte jej takto:
    1. ![Otevřít Správce zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![Vytvoření virtuálního zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. Spusťte aplikaci na vybraném zařízení a ověřte, zda se úspěšně zaregistruje v centru.

    > [!NOTE]
    > Registrace může selhat během `onTokenRefresh()` počátečního spuštění, dokud není volána metoda služby ID instance. Obnovení by mělo zahájit úspěšnou registraci v centru oznámení.

    ![Registrace zařízení byla úspěšná.](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>Odeslání zkušebního oznámení z centra oznámení

Nabízená oznámení z [webu Azure Portal] můžete odesílat následujícím postupem:

1. Na webu Azure Portal na stránce Centrum oznámení pro vaše centrum vyberte **testovat odeslání** v části Poradce **při potížích.**
3. V položce **Platformy** vyberte **Android**.
4. Vyberte **Poslat**.  Na zařízení Android se zatím nezobrazí oznámení, protože jste na něm mobilní aplikaci nespouštěli. Po spuštění mobilní aplikace znovu zosakejte klepnutím na tlačítko **Odeslat,** aby se zoatožně zobrazovala zpráva s oznámením.
5. Výsledek operace si můžete prohlédnout v seznamu dole v okně.

    ![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. Na zařízení se zobrazí oznámení. 

    ![Oznamovací zpráva na zařízení](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>Spuštění mobilní aplikace v emulátoru
Před testováním nabízených oznámení uvnitř emulátoru se ujistěte, že obrázek emulátoru podporuje úroveň rozhraní Google API, kterou jste pro aplikaci zvolili. Pokud obrázek nepodporuje nativní rozhraní API Google, může se stát, že se vám výjimka **služba\_není\_k dispozici.**

Také se ujistěte, že jste přidali svůj účet Google do spuštěnée emulátoru v části**Účty** **Nastavení** > . V opačném případě může mít pokusy o registraci u fcm způsobit výjimku **OVĚŘENÍ\_SE NEZDAŘILO.**

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste použili Firebase Cloud Messaging k vysílání oznámení do všech zařízení Android, které byly registrovány ve službě. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Kurz: Nabízená oznámení konkrétním zařízením Android](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[portál Azure]: https://portal.azure.com
