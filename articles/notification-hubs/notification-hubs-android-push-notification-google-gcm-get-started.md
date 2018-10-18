---
title: Zasílání nabízených oznámení aplikacím pro Android službami Azure Notification Hubs a Google Cloud Messaging | Microsoft Docs
description: V tomto kurzu se naučíte používat služby Azure Notification Hubs a Google Firebase Cloud Messaging k zasílání nabízených oznámení zařízením s Androidem.
services: notification-hubs
documentationcenter: android
keywords: nabízená oznámení;nabízené oznámení;nabízené oznámení Android
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 8268c6ef-af63-433c-b14e-a20b04a0342a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/05/2018
ms.author: dimazaid
ms.openlocfilehash: 1e9ed9d3500b112f98492e6a85d02a1f1843c7c3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365573"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging"></a>Kurz: Zasílání nabízených oznámení zařízením s Androidem službami Azure Notification Hubs a Google Cloud Messaging
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace systému Android.
Vytvoříte prázdnou aplikaci pro Android, která přijímá nabízená oznámení službou Google Cloud Messaging (GCM).

> [!IMPORTANT]
> Toto téma popisuje nabízená oznámení ve službě Google Cloud Messaging (GCM). Pokud používáte Firebase Cloud Messaging (FCM) od Googlu, přečtěte si článek [Odesílání nabízených oznámení do systému Android pomocí služeb Azure Notification Hubs a FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

Dokončený kód v tomto kurzu lze stáhnout z portálu Github [zde](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).

V tomto kurzu provedete následující akce: 

> [!div class="checklist"]
> * Vytvoříte projekt, který podporuje službu Google Cloud Messaging.
> * Vytvoříte centrum oznámení.
> * Připojte aplikaci k centru oznámení
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 
- [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Vytvoření projektu, který podporuje službu GCM (Google Cloud Messaging)
[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Vytvoříte centrum oznámení.
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>Konfigurace nastavení GCM pro centrum oznámení

1. V **NASTAVENÍ OZNÁMENÍ** vyberte **Google (GCM)**. 
2. Zadejte **klíč API**, který jste získali z Google Cloud Console. 
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Vaše centrum oznámení je nyní nakonfigurováno pro práci se službou GCM. Zároveň máte připojovací řetězce, pomocí kterých můžete svou aplikaci zaregistrovat pro příjem a odesílání nabízených oznámení.

## <a id="connecting-app"></a>Připojte aplikaci k centru oznámení
### <a name="create-a-new-android-project"></a>Vytvořte nový projekt Android
1. V nástroji Android Studio spusťte nový projekt Android Studio.
   
   ![Android Studio – nový projekt][13]
2. Zvolte faktor formuláře **Telefon i tablet** a hodnotu **Minimální SDK**, které chcete podporovat. Pak klikněte na tlačítko **Další**.
   
   ![Android Studio – pracovní postup vytvoření projektu][14]
3. Zvolte možnost **Prázdná aktivita** pro hlavní aktivitu, klikněte na tlačítko **Další** a pak klikněte na tlačítko **Dokončit**.

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
1. Aby bylo možné podporovat GCM, implementujte do kódu, který se používá k [získání registračních tokenů](https://developers.google.com/cloud-messaging/android/client#sample-register) službu naslouchání ID instance. Použijte k tomu [rozhraní API ID instance od Googlu](https://developers.google.com/instance-id/). V tomto kurzu je název třídy `MyInstanceIDService`. 
   
    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`. Nahraďte zástupný symbol `<your package>` skutečným názvem vašeho balíčku zobrazeného v horní části souboru `AndroidManifest.xml`.
  
    ```xml 
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```
2. Jakmile aplikace dostane z rozhraní API ID instance registrační token GCM, použije ho k [registraci do služby Azure Notification Hubs](notification-hubs-push-notification-registration-management.md). K registraci na pozadí slouží `IntentService` s názvem `RegistrationIntentService`. Tato služba také zodpovídá za [aktualizaci registračního tokenu GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
   
    Přidejte následující definice služby do souboru AndroidManifest.xml uvnitř značky `<application>`. Nahraďte zástupný symbol `<your package>` skutečným názvem vašeho balíčku zobrazeného v horní části souboru `AndroidManifest.xml`. 
   
    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```
3. Definujte příjemce oznámení. Přidejte následující definice příjemce do souboru AndroidManifest.xml uvnitř značky `<application>`. Nahraďte zástupný symbol `<your package>` skutečným názvem vašeho balíčku zobrazeného v horní části souboru `AndroidManifest.xml`.
   
    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```
4. Pod značku `</application>` přidejte následující oprávnění potřebné pro GCM. Nahraďte `<your package>` názvem balíčku, který je zobrazený nahoře v souboru `AndroidManifest.xml`.
   
    Další informace o těchto oprávnění naleznete v tématu [Nastavení klientské aplikace GCM pro Android](https://developers.google.com/cloud-messaging/android/client#manifest).
   
    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```
### <a name="adding-code"></a>Přidání kódu
1. V zobrazení projektu rozbalte **app** > **src** > **main** > **java**. Klikněte pravým tlačítkem na váš balíček ve složce **java**, klikněte na tlačítko **Nový** a pak klikněte na tlačítko **třída jazyka Java**. Přidejte novou třídu s názvem `NotificationSettings`. 
   
    ![Android Studio – nová třída Java][6]
   
    V následujícím kódu třídy `NotificationSettings` aktualizujte tyto tři zástupné symboly:
   
   * **ID odesílatele**: číslo projektu, které jste získali výše u [konzoly Google Cloud](http://cloud.google.com/console).
   * **HubListenConnectionString**: připojovací řetězec **DefaultListenAccessSignature** pro rozbočovač. Připojovací řetězec můžete zkopírovat tak, že na webu [Azure Portal] ve svém centru kliknete na stránce **Nastavení** na **Zásady přístupu**.
   * **HubName:** Použijte název centra oznámení, který se zobrazí na stránce centra na webu [Azure Portal].
     
     Kód `NotificationSettings`:
     
    ```java
    public class NotificationSettings {
    
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
    }
    ```
2. Přidejte další třídu pojmenovanou `MyInstanceIDService`. Tato třída představuje vaši implementaci naslouchací služby ID instance.
   
    Kód této třídy volá `IntentService`, aby na pozadí [aktualizoval token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
   
    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```
1. Přidejte další novou třídu do projektu s názvem, `RegistrationIntentService`. Tato třída implementuje rozhraní `IntentService`, které zajistí [aktualizaci tokenu GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) a [registraci v centru oznámení](notification-hubs-push-notification-registration-management.md).
   
    Pro tuto třídu použijte následující kód.
   
    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
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

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);        
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {        
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);        
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```
2. Na začátek třídy `MainActivity` přidejte následující příkazy `import`.

    ```java   
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```
3. Přidejte následující soukromé členy v horní části třídy. Tento kód [zkontroluje dostupnost Služeb Google Play podle doporučení společnosti Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).
   
    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;    
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
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
5. Do třídy `MainActivity` přidejte následující kód, který před voláním služby `IntentService` zkontroluje Služby Google Play, získá registrační token GCM a provede registraci v centru oznámení.
   
    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
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
7. Přidejte  tyto další metody do `MainActivity` pro ověření stavu aplikace a stavu sestavy ve vaší aplikaci.
   
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
   
    ```
    android:id="@+id/text_hello"
    ```
9. Přidejte podtřídu příjemce definovanou v souboru AndroidManifest.xml. Přidejte další novou třídu do projektu s názvem `MyHandler`.
10. Nad `MyHandler.java` přidejte následující příkazy pro import:
    
    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
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

## <a name="testing-your-app"></a>Testování vaší aplikace
### <a name="run-the-mobile-application"></a>Spuštění mobilní aplikace
1. Spusťte aplikaci a všimněte si, že je ID registrace hlášené pro úspěšnou registraci.
   
      ![Testování v systému Android – registrace kanálu][18]
2. Zadejte zprávu oznámení k odeslání do všech zařízení Android, která byla zaregistrovaná v centru.
   
      ![Testování v systému Android – odesílání zprávy][19]

3. Stiskněte tlačítko **Odeslat oznámení**. Všechna zařízení, která mají spuštěné aplikace, zobrazí instance `AlertDialog` se zprávou nabízených oznámení. Zařízení, která nemají spuštěnou aplikaci, ale byla dříve registrována pro nabízená oznámení, obdrží oznámení ve správci oznámení Android. Oznámení si můžete prohlédnout, když z levého horního rohu potáhnete prstem dolů.
   
      ![Testování v systému Android – oznámení][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Odeslání testovacích nabízených oznámení z webu Azure Portal
Příjem nabízených oznámení aplikací můžete otestovat, když je odešlete z webu [Azure Portal]. 

1. V části **Poradce při potížích** vyberte **Poslat na zkoušku**. 
2. V položce **Platformy** vyberte **Android**.
3. Testovací oznámení odešlete volbou **Poslat**. 
4. Potvrďte, že se oznámení zobrazí na zařízení s Androidem. 

    ![Azure Notification Hubs – testovací odeslání](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Nabízená oznámení v emulátoru
Pokud chcete testovat nabízená oznámení uvnitř emulátoru, ověřte, zda bitová kopie emulátoru podporuje úroveň rozhraní Google API, kterou jste zvolili pro vaši aplikaci. Pokud vaše image nepodporuje nativní rozhraní Google API, zobrazí se výjimka **SLUŽBA\_NENÍ\_K DISPOZICI**.

Také se ujistěte, že jste svůj účet Google přidali do spuštěného emulátoru v části **Nastavení** > **Účty**. V opačném případě mohou vaše pokusy o registraci s GCM mít za následek výjimku **OVĚŘOVÁNÍ\_SE NEZDAŘILO**.


## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Volitelné) Zasílání nabízených oznámení přímo z aplikace
Za normálních okolností byste odesílali oznámení pomocí serveru backend. V některých případech můžete chtít možnost zasílání nabízených oznámení přímo z klientské aplikace. Tato část vysvětluje postup odesílání oznámení z klienta pomocí [API služby REST centra oznámení Azure](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. V zobrazení projektu Android Studio rozbalte možnost **App** > **src** > **main** > **res** > **layout**. Otevřete soubor rozložení `activity_main.xml` a klikněte na kartu **Text** pro aktualizaci textového obsahu souboru. Aktualizujte ho pomocí kódu níže, který přidává nové `Button` a `EditText` ovládací prvky pro zasílání zpráv s nabízeným oznámením centra oznámení. Přidejte tento kód v dolní části, těsně před `</RelativeLayout>`.
   
    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```
2. V zobrazení projektu Android Studio rozbalte **App** > **src** > **main** > **res** > **values**. Otevřete soubor `strings.xml` a přidejte řetězcové hodnoty, které jsou odkazovány pomocí nového `Button` a `EditText` ovládacími prvky. Na konec souboru (těsně před `</resources>`) přidejte následující řádky.

    ```xml   
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```
3. Do souboru `NotificationSetting.java` přidejte následující nastavení na třídu `NotificationSettings`.
   
    Aktualizujte `HubFullAccess` pomocí připojovacího řetězce **DefaultFullSharedAccessSignature** pro centrum. Připojovací řetězec můžete zkopírovat z webu [Azure Portal] tak, že na stránce **Nastavení** kliknete u svého centra oznámení na **Zásady přístupu**.
   
    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```
4. Na začátek souboru `MainActivity.java` přidejte následující příkazy `import`.
   
    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```
5. Do souboru `MainActivity.java` přidejte následující členy v horní části třídy `MainActivity`.    
   
    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```
6. Vytvořte token SaS (Software Access Signature) pro ověření požadavku POST kvůli odeslání zpráv do centra oznámení. Parsujte data klíče připojovacího řetězce a vytvořte token SaS podle pokynů v referenční dokumentaci k rozhraní REST API v části [Základní pojmy](http://msdn.microsoft.com/library/azure/dn495627.aspx). Následující kód představuje příklad implementace.
   
    Do `MainActivity.java` přidejte následující metodu do třídy `MainActivity` k analýze připojovacího řetězce.
   
    ```java
    /**
        * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```
7. Do `MainActivity.java` přidejte následující metodu do třídy `MainActivity` k vytvoření ověřovacího tokenu SaS.
   
    ```java
    /**
        * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```
8. Do `MainActivity.java` přidejte následující metodu do třídy `MainActivity` pro zajištění kliknutí na tlačítko **Odeslat oznámení** a odešlete zprávu nabízeného oznámení do centra pomocí předdefinovaného REST API.
   
    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization", 
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get reponse
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste poslali oznámení všem zařízením s Androidem registrovaným back-endem. Pokud se chcete naučit zasílat nabízená oznámení určitým zařízením s Androidem, pokračujte následujícím kurzem:  

 > [!div class="nextstepaction"] 
 > [Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md) 


<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
