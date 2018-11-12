---
title: Zasílání nabízených oznámení aplikacím pro Kindle službou Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Kindle.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: bf5cb2851acdcf1f9353e88fc2f2caa3c356804e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230358"
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Začínáme s použitím Notification Hubs pro aplikace Kindle
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu zjistíte, jak používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace Kindle. Vytvoříte prázdnou aplikaci pro Kindle, která přijímá nabízená oznámení službou ADM (Amazon Device Messaging).

V tomto kurzu vytvoříte nebo aktualizujete kód tak, aby prováděl následující úlohy: 

> [!div class="checklist"]
> * Přidejte novou aplikaci do portálu pro vývojáře
> * Vytvořte klíč rozhraní API
> * Přidejte pověření do centra
> * Nastavte aplikaci
> * Vytvořte obslužnou rutinu zpráv ADM
> * Přidejte do aplikace svůj klíč rozhraní API
> * Spuštění aplikace
> * Odešlete zkušební oznámení 

## <a name="prerequisites"></a>Požadavky

* Potřebujete získat sadu Android SDK (za předpokladu, že používáte Eclipse) z <a href="https://go.microsoft.com/fwlink/?LinkId=389797">webu Androidu</a>.
* Postupujte podle kroků v <a href="https://developer.amazon.com/docs/fire-tablets/ft-set-up-your-development-environment.html">Nastavení vašeho vývojového prostředí</a> pro nastavení vývojového prostředí pro Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Přidejte novou aplikaci do portálu pro vývojáře
1. Nejprve vytvořte aplikaci na [portálu pro vývojáře Amazon].
   
    ![][0]
2. Zkopírujte **klíč aplikace**.
   
    ![][1]
3. Na portálu, klikněte na název vaší aplikace a pak klikněte na kartu **Zasílání zpráv zařízení**.
   
    ![][2]
4. Klikněte na tlačítko **Vytvoření nového profilu zabezpečení** a pak vytvořte nový profil zabezpečení (například **profil zabezpečení TestAdm**). Potom klikněte na **Uložit**.
   
    ![][3]
5. Kliknutím na **bezpečnostní profily** zobrazíte vytvořený profil zabezpečení. Zkopírujte hodnoty **ID klienta** a **Tajný klíč klienta** pro pozdější použití.
   
    ![][4]

## <a name="create-an-api-key"></a>Vytvořte klíč rozhraní API
1. Otevřete příkazový řádek s oprávněními správce.
2. Přejděte do složky sady SDK pro Android.
3. Zadejte následující příkaz:
   
        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
   
    ![][5]
4. Pro heslo **keystore** zadejte **android**.
5. Zkopírujte otisk prstu **MD5**.
6. Zpět na portálu pro vývojáře na kartě **Zasílání zpráv** klikněte na tlačítko **Android/Kindle** a zadejte název balíčku pro vaši aplikaci (například **com.sample.notificationhubtest**) a hodnotu **MD5** a pak klikněte na tlačítko **Vygenerovat klíč rozhraní API**.

## <a name="add-credentials-to-the-hub"></a>Přidejte pověření do centra
Na portálu přidejte sdílený tajný klíč klienta a ID klienta na kartě **Konfigurovat** centra oznámení.

## <a name="set-up-your-application"></a>Nastavte aplikaci
> [!NOTE]
> Při vytváření aplikace použijte alespoň 17 úrovní rozhraní API.

Přidejte knihovny ADM do projektu Eclipse:

1. Pro získání knihovny ADM [stáhněte sadu SDK]. Extrahujte soubor zip sady SDK.
2. V Eclipse klikněte pravým tlačítkem na projekt a pak klikněte na tlačítko **Vlastnosti**. Vyberte možnost **Cesta sestavení Java** na levé straně a pak vyberte kartu Knihovny v horní části. Klikněte na tlačítko **Přidat externí Jar** a vyberte soubor `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` z adresáře, do kterého jste extrahovali Amazon SDK.
3. Stáhněte si sadu SDK NotificationHubs pro Android (odkaz).
4. Rozbalte balíček a pak přetáhněte soubor `notification-hubs-sdk.jar` do složky `libs` v Eclipse.

Upravte manifest aplikace pro podporu ADM:

1. Přidejte obor názvů Amazon do kořenového prvku manifestu:

    ```xml
        xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```

1. Přidejte oprávnění jako první prvek v rámci prvku manifestu. Nahraďte **[VÁŠ NÁZEV BALÍČKU]** balíčkem, který jste použili k vytvoření aplikace.
   
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
2. Vložte následující prvek jako první podřízený prvek aplikace. Nezapomeňte nahradit **[SVŮJ NÁZEV SLUŽBY]** názvem vaší obslužné rutiny zpráv ADM, které vytvoříte v další části (včetně balíčku) a nahraďte **[NÁZEV VAŠEHO BALÍČKU]** názvem balíčku, pomocí kterého jste vytvořili vaší aplikaci.
   
    ```xml
        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />
   
        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />
   
            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
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
1. Vytvořte novou třídu, která zdědí z `com.amazon.device.messaging.ADMMessageHandlerBase` a pojmenujte ji `MyADMMessageHandler`, jak je znázorněno na následujícím obrázku:
   
    ![][6]
2. Přidejte následující příkazy `import`:
   
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub
    ```
3. Přidejte následující kód ve třídě, kterou jste vytvořili. Nezapomeňte nahradit název centra a připojovací řetězec (naslouchání):
   
    ```java
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
          private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }
   
        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
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
4. Do metody `OnMessage()` přidejte následující kód:
   
    ```java
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    ```
5. Do metody `OnRegistered` přidejte následující kód:
   
    ```java
        try {
            getNotificationHub(getApplicationContext()).register(registrationId);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    ```
6. Do metody `OnUnregistered` přidejte následující kód:
   
    ```java
         try {
             getNotificationHub(getApplicationContext()).unregister();
         } catch (Exception e) {
             Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
         }
    ```
7. Do metody `MainActivity` přidejte následující příkaz importu:
   
    ```java
        import com.amazon.device.messaging.ADM;
    ```
8. Na konec metody `OnCreate` přidejte následující kód:
   
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
1. V nástroji Eclipse vytvořte nový soubor s názvem **api_key.txt** v majetku adresáře projektu.
2. Otevřete soubor a zkopírujte klíč rozhraní API generovaný na portálu pro vývojáře Amazon.

## <a name="run-the-app"></a>Spuštění aplikace
1. Spusťte emulátor.
2. V emulátoru potáhněte prstem z horní části a klikněte na tlačítko **Nastavení** a pak klikněte na tlačítko **Můj účet** a zaregistrujte se pomocí platného účtu Amazon.
3. V nástroji Eclipse spusťte aplikaci.

> [!NOTE]
> Pokud dojde k potížím, zkontrolujte čas emulátoru (nebo zařízení). Časová hodnota musí být přesná. Chcete-li změnit čas emulátoru Kindle, můžete spustit následující příkaz z adresáře nástrojů platformy Android SDK:
> 

```
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

![][7]

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste poslali vysílané oznámení všem zařízením Kindle registrovaných back-endem. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením Kindle, pokračujte následujícím kurzem. V něm se dozvíte, jak posílat nabízená oznámení určitým zařízením s Androidem, ale stejnou logiku můžete použít i k zasílání nabízených oznámení určitým zařízením Kindle. 

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení do konkrétních zařízení](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[portálu pro vývojáře Amazon]: https://developer.amazon.com/home.html
[stáhněte sadu SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
