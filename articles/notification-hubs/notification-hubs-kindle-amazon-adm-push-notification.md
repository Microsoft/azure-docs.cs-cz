---
title: Zasílání nabízených oznámení aplikacím pro Kindle službou Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Kindle.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/29/2019
ms.author: jowargo
ms.openlocfilehash: abc77ad4d06dc719ee1a89cd8fcf29d42d96b483
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147658"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Začínáme s použitím Notification Hubs pro aplikace Kindle

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Kindle. Vytvoříte prázdnou aplikaci pro Kindle, která přijímá nabízená oznámení službou ADM (Amazon Device Messaging).

V tomto kurzu vytvoříte nebo aktualizujete kód tak, aby prováděl následující úlohy:

> [!div class="checklist"]
> * Přidejte novou aplikaci do portálu pro vývojáře
> * Vytvořte klíč rozhraní API
> * Vytvoření a konfigurace centra oznámení
> * Nastavte aplikaci
> * Vytvořte obslužnou rutinu zpráv ADM
> * Přidejte do aplikace svůj klíč rozhraní API
> * Spuštění aplikace
> * Odešlete zkušební oznámení

## <a name="prerequisites"></a>Požadavky

- [Android Studio](https://developer.android.com/studio/?gclid=CjwKCAjwwZrmBRA7EiwA4iMzBPZ9YYmR0pbb5LtjnWhWCxe8PWrmjmeaR6ad5ksCr_j2mmkVj_-o6hoCAqwQAvD_BwE).
- Postupujte podle kroků v [Nastavení vašeho vývojového prostředí](https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html) pro nastavení vývojového prostředí pro Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Přidejte novou aplikaci do portálu pro vývojáře

1. Přihlaste se [portálu pro vývojáře Amazon](https://developer.amazon.com/apps-and-games/console/apps/list.html).
2. Vyberte **přidat novou aplikaci**a pak vyberte **Android**.  

    ![Tlačítko Přidat novou aplikaci](./media/notification-hubs-kindle-get-started/add-new-app-button.png)
1. Na **nové odeslání aplikace** stránce, postupujte podle těchto kroků **klíč aplikace**:
    1. Zadejte název **název aplikace**.
    2. Vyberte některou **kategorie** (například: vzdělávání)
    4. Zadejte e-mailovou adresu pro **Zákaznická podpora e-mailovou adresu** pole. 
    5. Vyberte **Uložit**.

        ![Nová stránka odesílání aplikace](./media/notification-hubs-kindle-get-started/new-app-submission-page.png) 
2.  V horní části stránky, přejděte **App Services** kartu.

    ![Karta aplikace služby](./media/notification-hubs-kindle-get-started/app-services-tab.png)
1. Na **App Services** kartu, posuňte se dolů a vyberte **zobrazení Mobile Ads** v **Mobile Ads** oddílu. Zobrazí **Mobile Ads** stránku na nové kartě ve webovém prohlížeči. 

    ![Části Mobile Ads - propojení zobrazení Mobile Ads](./media/notification-hubs-kindle-get-started/view-mobile-ads-link.png)
1. Na **Mobile Ads** stránce, proveďte následující kroky: 
    1. Určete, jestli je vaše aplikace směrovat primárně na děti v části 13. Pro účely tohoto kurzu vyberte **ne**.
    1. Vyberte **odeslat**. 

        ![Stránka Mobile Ads](./media/notification-hubs-kindle-get-started/mobile-ads-page.png)
    3. Kopírovat **klíč aplikace** z **Mobile Ads** stránky. 

        ![Klíč aplikace](./media/notification-hubs-kindle-get-started/application-key.png)
3.  Teď přepněte na kartu webový prohlížeč, který má **App Services** kartě otevřít, a proveďte následující kroky:
    1. Přejděte **zasílání zpráv zařízení** oddílu.     
    1. Rozbalte **vyberte existující profil zabezpečení nebo vytvořit nový**a pak vyberte **vytvořit profil zabezpečení**. 

        ![Vytvoření tlačítka profil zabezpečení](./media/notification-hubs-kindle-get-started/create-security-profile-button.png)
    1. Zadejte **název** pro svůj profil zabezpečení. 
    2. Zadejte **popis** pro svůj profil zabezpečení. 
    3. Vyberte **Uložit**. 

        ![Uložit profil zabezpečení](./media/notification-hubs-kindle-get-started/save-security-profile.png)
    1. Vyberte **povolit zasílání zpráv zařízení** povolit zasílání zpráv na tento profil zabezpečení zařízení. 

        ![Povolením zasílání zpráv zařízení](./media/notification-hubs-kindle-get-started/enable-device-messaging.png)
    1. Vyberte **zobrazit profil zabezpečení** na stránce výsledků. 
1. Nyní na **profil zabezpečení** stránce, proveďte následující kroky: 
    1. Přepněte **nastavení webového** kartu a zkopírujte **ID klienta** a **tajný kód klienta** hodnoty pro pozdější použití. 

        ![Získání ID klienta a tajný klíč](./media/notification-hubs-kindle-get-started/client-id-secret.png) 
    2. Přepněte **nastavení Android/Kindle** stránce a stránce nechte otevřené. Zadáte tyto hodnoty v další části. 

## <a name="create-an-api-key"></a>Vytvořte klíč rozhraní API
1. Otevřete příkazový řádek s oprávněními správce.
2. Přejděte do složky sady SDK pro Android.
3. Zadejte následující příkaz:

    ```shell
    keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
    ```
4. Pro heslo **keystore** zadejte **android**.
5. Kopírovat **MD5** a **SHA256** otisky prstů. 
6. Zpět na portálu pro vývojáře **nastavení Android/Kindle** kartu, proveďte následující kroky: 
    1. Zadejte **název pro klíč rozhraní API**. 
    2. Zadejte **název balíčku** pro vaši aplikaci (například **com.fabrikam.mykindleapp**) a **MD5** hodnotu.
        
        >[!IMPORTANT]
        > Při vytváření aplikace v nástroji Android Studio, použijte zde stejný název balíčku, který jste zadali. 
    1. Vložit **digitální podpis MD5** jste si zkopírovali dříve. 
    2. Vložit **SHA256 podpis** jste si zkopírovali dříve.  
    3. Vyberte **generovat nový klíč**.

        ![Nastavení Android/Kindle – generování klíče](./media/notification-hubs-kindle-get-started/android-kindle-settings.png)
    4. Teď vyberte **zobrazit** v seznamu zobrazíte klíč rozhraní API. 

        ![Nastavení Android/Kindle – zobrazit klíč rozhraní API](./media/notification-hubs-kindle-get-started/show-api-key-button.png) 
    5. V **podrobnosti klíč rozhraní API** okna, zkopírujte klíč rozhraní API a uložte ho někam. Vyberte **X** v pravém horním rohu zavřete okno. 


## <a name="create-and-configure-a-notification-hub"></a>Vytvoření a konfigurace centra oznámení

1. Postupujte podle kroků v [vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md) článek k vytvoření centra oznámení. 
2. Vyberte **Amazon (ADM)** pod **nastavení** nabídky.
3. Vložit **ID klienta** a **tajný kód klienta** jste předtím uložili. 
4. Na panelu nástrojů vyberte **Uložit**. 

    ![Konfigurace nastavení ADM pro Centrum oznámení](./media/notification-hubs-kindle-get-started/configure-notification-hub.png)
5. Vyberte **zásady přístupu** v levé nabídce a vyberte **kopírování** u připojovacího řetězce pro tlačítko **DefaultListenSharedAccessSignature** zásad. Někam si ho uložte. Použijete ho později ve zdrojovém kódu. 

    ![Centra oznámení – připojovací řetězec naslouchání](./media/notification-hubs-kindle-get-started/event-hub-listen-connection-string.png)    

## <a name="set-up-your-application"></a>Nastavte aplikaci

1. Spusťte Android Studio. 
2. Vyberte **souboru**, přejděte na **nový**a pak vyberte **nový projekt**. 
3. V **zvolte projekt** okno na **telefony a tablety** kartu, vyberte možnost **prázdná aktivita**a vyberte **Další**. 
4. V **nakonfigurujete svůj projekt** okno, proveďte následující kroky:
    1. Zadejte **název pro vaši aplikaci**. Můžete chtít shodovat s názvem aplikace, kterou jste vytvořili na portálu pro vývojáře Amazon. 
    2. Zadejte **název balíčku**. 
        
        >[!IMPORTANT]
        >Název balíčku musí odpovídat názvu balíčku, který jste zadali v portálu pro vývojáře Amazon.
    3. Zkontrolovat a aktualizovat zbývající hodnoty podle potřeby. 
    4. Vyberte **Finish** (Dokončit). 

        ![Konfigurace projektu pro Android](./media/notification-hubs-kindle-get-started/new-android-studio-project.png)
5. Stáhněte si [Amazon SDK pro vývojáře pro Android](https://developer.amazon.com/sdk-download) knihovny na pevný disk. Extrahujte soubor zip sady SDK.
6. V nástroji Android Studio, změnit strukturu složek ze **Android** k **projektu** Pokud již není nastavená na **projektu**. 

    ![Android Studio – přepínač tak, aby struktura projektu](./media/notification-hubs-kindle-get-started/android-studio-project-view.png)
7. Rozbalte **aplikace** zobrazíte **libs** složky ve stromovém zobrazení.     
8. V okně Průzkumníka souborů přejděte do složky, kam jste stáhli sady Android SDK Amazon.
9. Stisknutím klávesy **CTRL** a přetáhněte myší **amazon – zařízení – zasílání zpráv – 1.0.1.jar** do souboru **lib** uzlu ve stromovém zobrazení. 

    ![Android Studio – přidat soubor JAR pro zasílání zpráv zařízení Amazon](./media/notification-hubs-kindle-get-started/drag-drop-amazon-device-messaging-jar.png)
9. V **kopírování** okně **OK**. Pokud se zobrazí **přesunout** okno místo **kopírování** okně, zavřete ho a zkuste operaci přetažení s **CTRL** stisknutí tlačítka. 

    ![Android Studio – Zkopírujte soubor JAR](./media/notification-hubs-kindle-get-started/copy-jar-window.png)
10. Přidejte následující příkaz, kterým **build.gradle vaší aplikace** ve **závislosti** části: `implementation files('libs/amazon-device-messaging-1.0.1.jar')`. 

    ![Android Studio – přidání ADM do souboru build.gradle](./media/notification-hubs-kindle-get-started/adm-build-gradle.png)
11. V `Build.Gradle` souboru **aplikace**, přidejte následující řádky v **závislosti** části: 

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
12. Přidejte následující úložiště **po** **závislosti** části:

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```
13. V editoru pro **build.gradle** souboru **aplikace**vyberte **synchronizovat nyní** na panelu nástrojů. 

    ![Android Studio – synchronizace build.gradle aplikace](./media/notification-hubs-kindle-get-started/gradle-sync-now.png)
14. Přepněte zpátky na struktuře Android ve stromovém zobrazení.  Přidejte obor názvů Amazon do kořenového prvku manifestu:

    ```xml
    xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```
   
    ![Obor názvů Amazon do manifestu](./media/notification-hubs-kindle-get-started/amazon-namespace-manifest.png)
2. Přidejte oprávnění jako první prvek v rámci prvku manifestu. Nahraďte **[název vašeho balíčku]** balíčkem, který jste použili k vytvoření vaší aplikace.

    ```xml
    <permission
        android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
        android:protectionLevel="signature" />

    <uses-permission android:name="android.permission.INTERNET"/>

    <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

    <!-- This permission allows your app access to receive push notifications
    from ADM. -->
    <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

    <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
3. Vložte následující prvek jako první podřízený prvek aplikace. Nahraďte **[název vašeho balíčku]** názvem balíčku, se kterým jste aplikaci vytvořili. V dalším kroku vytvoříte MyADMMessageHandler třídy. 

    ```xml
        <amazon:enable-feature
            android:name="com.amazon.device.messaging"
            android:required="true"/>
        <service
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler"
            android:exported="false" />
        <receiver
            android:name="[YOUR PACKAGE NAME].MyADMMessageHandler$Receiver"
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
                <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
                <!-- Replace the name in the category tag with your app's package name. -->
                <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Vytvořte obslužnou rutinu zpráv ADM

1. Přidejte novou třídu do `com.fabrikam.mykindleapp` balíčku v projektu, který dědí z `com.amazon.device.messaging.ADMMessageHandlerBase` a pojmenujte ho `MyADMMessageHandler`, jak je znázorněno na následujícím obrázku:

    ![Vytvořte třídu MyADMMessageHandler](./media/notification-hubs-kindle-get-started/create-adm-message-handler.png)

2. Přidejte následující `import` příkazy `MyADMMessageHandler` třídy:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.support.v4.app.NotificationCompat;
    import android.util.Log;
    import com.amazon.device.messaging.ADMMessageReceiver;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    ```

3. Přidejte následující kód ve třídě, kterou jste vytvořili. Nezapomeňte, `[HUB NAME]` a `[LISTEN CONNECTION STRING]` s názvem notification hub a vlastností listenurimode nastavenou na připojovací řetězec: 

    ```java
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    private static NotificationHub hub;

    public static NotificationHub getNotificationHub(Context context) {
        Log.v("com.wa.hellokindlefire", "getNotificationHub");
        if (hub == null) {
            hub = new NotificationHub("[HUB NAME]", "[HUB NAMESPACE CONNECTION STRING]", context);
        }
        return hub;
    }

    public MyADMMessageHandler() {
        super("MyADMMessageHandler");
    }

    @Override
    protected void onMessage(Intent intent) {
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    }

    @Override
    protected void onRegistrationError(String s) {

    }

    @Override
    protected void onRegistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).register(s);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    }

    @Override
    protected void onUnregistered(String s) {
        try {
            getNotificationHub(getApplicationContext()).unregister();
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
        }
    }

    public static class Receiver extends ADMMessageReceiver
    {
        public Receiver()
        {
            super(MyADMMessageHandler.class);
        }
    }

    private void sendNotification(String msg) {
        Context ctx = getApplicationContext();

        mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);


        NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                        .setSmallIcon(R.mipmap.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle()
                                .bigText(msg))
                        .setContentText(msg);

        mBuilder.setContentIntent(contentIntent);
        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
    }
    ```
## <a name="create-an-adm-object"></a>Vytvoření objektu ADM
1 v `MainActivity.java` přidejte následující příkazy pro import:

    ```java
    import android.os.AsyncTask;
    import android.util.Log;
    import com.amazon.device.messaging.ADM;
    ```
2. Na konec metody `OnCreate` přidejte následující kód:

    ```java
    final ADM adm = new ADM(this);
    if (adm.getRegistrationId() == null)
    {
        adm.startRegister();
    } else {
        new AsyncTask() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                    } catch (Exception e) {
                        Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
    }
    ```


## <a name="add-your-api-key-to-your-app"></a>Přidejte do aplikace svůj klíč rozhraní API
1. Postupujte podle těchto kroků přidejte složku prostředků do projektu. 
    1. Přepněte **projektu** zobrazení. 
    2. Klikněte pravým tlačítkem na **aplikace**.
    3. Vyberte **Nový**.
    4. Vyberte **složky**. 
    5. Vyberte **prostředky složky**. 

        ![Přidat nabídku složky prostředky](./media/notification-hubs-kindle-get-started/add-assets-folder-menu.png)    
    6. Na **konfigurace komponenty** stránce, proveďte následující kroky:
        1. Vyberte **změnit umístění složky**
        2. Potvrďte, že je nastavena na složku: `src/main/assets`.
        3. Vyberte **Finish** (Dokončit). 
        
            ![Nakonfigurujte složku pro prostředky](./media/notification-hubs-kindle-get-started/configure-asset-folder.png)
2. Přidejte do ní soubor **api_key.txt** k **prostředky** složky. Ve stromovém zobrazení rozbalte **aplikace**, rozbalte **src**, rozbalte **hlavní**a klikněte pravým tlačítkem na **prostředky**, přejděte na **nový**a pak vyberte **souboru**. Zadejte **api_key.txt** pro název souboru. 3. 
5. Zkopírujte klíč rozhraní API, kterou jste vygenerovali v portálu pro vývojáře Amazon api_key.txt souboru. 
6. Sestavte projekt. 

## <a name="run-the-app"></a>Spuštění aplikace
1. Na zařízení Kindle prstem z horní části a klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **Můj účet** a zaregistrovat pomocí platného účtu Amazon.
2. Spuštění aplikace na zařízení Kindle z Android Studio. 

> [!NOTE]
> Pokud dojde k potížím, zkontrolujte čas emulátoru (nebo zařízení). Časová hodnota musí být přesná. Chcete-li změnit čas emulátoru Kindle, můžete spustit následující příkaz z adresáře nástrojů platformy Android SDK:

```shell
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Odeslání oznámení

Odeslání zprávy pomocí .NET:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

Ukázkový kód, naleznete v tématu [v tomto příkladu na Githubu](https://github.com/Azure/azure-notificationhubs-dotnet/blob/master/Samples/SendPushSample/SendPushSample/Program.cs).

![][7]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste poslali vysílané oznámení všem zařízením Kindle registrovaných back-endem. Informace o nabízená oznámení odesílaná konkrétním zařízením Kindle, přejděte k následujícímu kurzu:  V následujícím kurzu ukazuje, jak nabízená oznámení odesílaná konkrétním zařízením s Androidem, ale můžete použít stejnou logiku pro odesílání nabízených oznámení na konkrétní zařízení Kindle.

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[Amazon developer portal]: https://developer.amazon.com/home.html
[download the SDK]: https://developer.amazon.com/public/resources/development-tools/sdk
[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
