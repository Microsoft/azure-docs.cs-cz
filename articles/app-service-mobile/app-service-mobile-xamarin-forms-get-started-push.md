---
title: Přidání nabízených oznámení do aplikace Xamarin. Forms | Microsoft Docs
description: Naučte se používat služby Azure k posílání nabízených oznámení více platforem do aplikací Xamarin. Forms.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: ce5750a5071ee0dfc257498f83f41b6d59c99a8b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388520"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Přidání nabízených oznámení do aplikace Xamarin. Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete do vaší mobilní aplikace integrovat cloudové služby, ještě dnes se zaregistrujte do služeb [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do všech projektů, které vznikly v [rychlém startu Xamarin. Forms](app-service-mobile-xamarin-forms-get-started.md). To znamená, že nabízené oznámení se pošle všem klientům pro více platforem při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru pro rychlé zahájení, budete potřebovat balíček rozšíření nabízených oznámení. Další informace najdete v tématu [práce s back-end serverem .NET SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Požadavky

Pro iOS budete potřebovat [členství v programu Apple Developer](https://developer.apple.com/programs/ios/) a fyzické zařízení s iOS. [Simulátor iOS nepodporuje nabízená oznámení](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizace projektového serveru pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurace a spuštění projektu pro Android (volitelné)

Dokončete tuto část, pokud chcete povolit nabízená oznámení pro projekt Xamarin. Forms Droid pro Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Povolit Firebase Cloud Messaging (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurace back-endu Mobile Apps k odesílání nabízených žádostí pomocí FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Přidání nabízených oznámení do projektu pro Android

S back-end nakonfigurovaným pomocí FCM můžete do klienta přidat komponenty a kódy pro registraci v FCM. Můžete se také zaregistrovat k nabízeným oznámením pomocí Azure Notification Hubs prostřednictvím back-endu Mobile Apps a dostávat oznámení.

1. V projektu **Droid** klikněte pravým tlačítkem na **odkazy > spravovat balíčky NuGet...** .
1. V okně Správce balíčků NuGet vyhledejte balíček **Xamarin. Firebase. Messaging** a přidejte ho do projektu.
1. Ve vlastnostech projektu projektu **Droid** nastavte aplikaci na kompilovat pomocí Androidu verze 7,0 nebo vyšší.
1. Přidejte soubor **Google-Services. JSON** stažený z konzoly Firebase do kořenového adresáře projektu **Droid** a nastavte jeho akci sestavení na **GoogleServicesJson**. Další informace najdete v tématu [přidat soubor JSON služby Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrace ve službě Firebase Cloud Messaging

1. Otevřete soubor **AndroidManifest.xml** a vložte následující elementy `<receiver>` do elementu `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementace služby Firebase Instance ID

1. Přidejte novou třídu do projektu **Droid** s názvem `FirebaseRegistrationService`a ujistěte se, že jsou v horní části souboru k dispozici následující příkazy `using`:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Nahraďte prázdnou `FirebaseRegistrationService` třídu následujícím kódem:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    Třída `FirebaseRegistrationService` zodpovídá za generování tokenů zabezpečení, které aplikaci opravňují k přístupu k FCM. `OnTokenRefresh` Metoda je voláno, když aplikace obdrží registračního tokenu z FCM. Metoda načte token z `FirebaseInstanceId.Instance.Token` vlastnost, která se aktualizuje asynchronně pomocí FCM. `OnTokenRefresh` Zřídka vyvoláním metody, protože token se aktualizuje jenom při instalaci nebo odinstalaci, když uživatel odstraní data aplikací, pokud aplikace odstraní Identifikátor Instance aplikace nebo tokenu zabezpečení byl dojde k ohrožení bezpečnosti. Kromě toho FCM Instance ID služby bude požadovat, že aplikace aktualizuje svůj token pravidelně, obvykle každých 6 měsíců.

    `OnTokenRefresh` Také vyvolá metodu `SendRegistrationTokenToAzureNotificationHub` metodu, která slouží k přidružení tokenu registrace uživatele do centra oznámení Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registraci do centra oznámení Azure

1. Přidejte novou třídu do projektu **Droid** s názvem `AzureNotificationHubService`a ujistěte se, že jsou v horní části souboru k dispozici následující příkazy `using`:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Nahraďte prázdnou `AzureNotificationHubService` třídu následujícím kódem:

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    Metoda `RegisterAsync` vytvoří jednoduchou šablonu zprávy oznámení jako JSON a registruje pro příjem oznámení šablon z centra oznámení pomocí registračního tokenu Firebase. Tím se zajistí, že všechna naše oznámení odeslaná do centra oznámení Azure se zaměří na zařízení reprezentována registračního tokenu.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Zobrazení obsahu nabízené oznámení

1. Přidejte novou třídu do projektu **Droid** s názvem `FirebaseNotificationService`a ujistěte se, že jsou v horní části souboru k dispozici následující příkazy `using`:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Nahraďte prázdnou `FirebaseNotificationService` třídu následujícím kódem:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    Metoda `OnMessageReceived`, která je vyvolána, když aplikace obdrží oznámení od FCM, extrahuje obsah zprávy a zavolá metodu `SendNotification`. Tato metoda převede obsah zprávy do místního oznámení, který se spustí, když je spuštěná aplikace s oznámením v oznamovací oblasti.

Nyní jste připraveni nabízená oznámení testů v aplikaci spuštěné na zařízení s Androidem nebo v emulátoru.

### <a name="test-push-notifications-in-your-android-app"></a>Testování nabízených oznámení v aplikaci pro Android

První dva kroky jsou požadovány pouze při testování emulátoru.

1. Ujistěte se, že nasazujete nebo ladíte na zařízení nebo emulátoru nakonfigurovaném pomocí Služby Google Play. Můžete to ověřit tak, že zkontrolujete, jestli jsou aplikace pro **přehrávání** nainstalované v zařízení nebo emulátoru.
2. Kliknutím na **aplikace** > **Nastavení** > **Přidat účet**přidejte do zařízení se systémem Android účet Google. Pak podle pokynů přidejte existující účet Google do zařízení nebo vytvořte nový.
3. V sadě Visual Studio nebo Xamarin Studio klikněte pravým tlačítkem na projekt **Droid** a klikněte na **nastavit jako spouštěný projekt**.
4. Kliknutím na **Spustit** sestavíte projekt a spustíte aplikaci na svém zařízení nebo emulátoru Android.
5. V aplikaci zadejte úlohu a potom klikněte na ikonu Plus ( **+** ).
6. Ověřte, že je při přidání položky přijata oznámení.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurace a spuštění projektu iOS (volitelné)

Tato část se týká spuštění projektu Xamarin iOS pro zařízení s iOS. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurace centra oznámení pro APNS

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Dále nakonfigurujete nastavení projektu iOS v Xamarin Studio nebo Visual Studiu.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do aplikace pro iOS

1. V projektu **iOS** otevřete AppDelegate.cs a přidejte následující příkaz do horní části souboru kódu.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Do třídy **AppDelegate** přidejte přepsání pro událost **RegisteredForRemoteNotifications** pro registraci oznámení:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. V **AppDelegate**přidejte také následující přepsání pro obslužnou rutinu události **DidReceiveRemoteNotification** :

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    Tato metoda zpracovává příchozí oznámení v době, kdy aplikace běží.

4. Do třídy **AppDelegate** přidejte následující kód do metody **FinishedLaunching** :

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    To umožňuje podporu pro vzdálená oznámení a žádosti o nabízenou registraci.

Vaše aplikace se teď aktualizovala tak, aby podporovala nabízená oznámení.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testování nabízených oznámení v aplikaci pro iOS

1. Klikněte pravým tlačítkem na projekt pro iOS a pak klikněte na **nastavit jako spouštěný projekt**.
2. Stisknutím tlačítka **Spustit** nebo **F5** v aplikaci Visual Studio Sestavte projekt a spusťte aplikaci na zařízení se systémem iOS. Pak kliknutím na **OK** potvrďte nabízená oznámení.

   > [!NOTE]
   > Nabízená oznámení musíte explicitně přijmout z vaší aplikace. Tato žádost nastane jenom při prvním spuštění aplikace.

3. V aplikaci zadejte úlohu a potom klikněte na ikonu Plus ( **+** ).
4. Ověřte, zda bylo přijato oznámení, a poté kliknutím na tlačítko **OK** zavřete oznámení.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurace a spuštění projektů Windows (volitelné)

Tato část je určena pro spouštění projektů Xamarin. Forms WinApp a WinPhone81 pro zařízení s Windows. Tyto kroky také podporují projekty Univerzální platforma Windows (UWP). Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrace aplikace pro Windows pro nabízená oznámení pomocí služby oznamování systému Windows (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows

1. V aplikaci Visual Studio otevřete **App.XAML.cs** v projektu Windows a přidejte následující příkazy.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Nahraďte `<your_TodoItemManager_portable_class_namespace>` oborem názvů vašeho přenosného projektu, který obsahuje třídu `TodoItemManager`.

2. Do App.xaml.cs přidejte následující metodu **InitNotificationsAsync** :

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    Tato metoda načte kanál nabízených oznámení a zaregistruje šablonu pro příjem oznámení šablon z vašeho centra oznámení. Do tohoto klienta bude doručeno oznámení šablony, které podporuje *messageParam* .

3. V App.xaml.cs aktualizujte definici metody obslužné rutiny události po **spuštění** přidáním modifikátoru `async`. Pak na konec metody přidejte následující řádek kódu:

    ```csharp
    await InitNotificationsAsync();
    ```

    Tím zajistíte, že se registrace nabízených oznámení vytvoří nebo aktualizuje při každém spuštění aplikace. Je důležité to udělat, aby se zaručilo, že kanál nabízených oznámení WNS je vždycky aktivní.  

4. V Průzkumník řešení pro Visual Studio otevřete soubor **Package. appxmanifest** a v části **oznámení**nastavte **informační** zprávy s možností **Ano** .
5. Sestavte aplikaci a ověřte, že nedošlo k chybám. Vaše klientská aplikace by se teď měla zaregistrovat pro oznámení šablon z Mobile Apps back-endu. Tento oddíl opakujte pro každý projekt Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testování nabízených oznámení v aplikaci pro Windows

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt Windows a klikněte na **nastavit jako spouštěný projekt**.
2. Stiskněte tlačítko **Spustit** a sestavte projekt a spusťte aplikaci.
3. V aplikaci zadejte název nového TodoItem a potom klikněte na ikonu Plus ( **+** ), kterou chcete přidat.
4. Ověřte, že je při přidání položky přijata oznámení.

## <a name="next-steps"></a>Další kroky

Můžete získat další informace o nabízených oznámeních:

* [Posílání nabízených oznámení z Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Vzdálená oznámení pomocí zasílání zpráv Firebase do cloudu](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostikovat problémy s nabízenými oznámeními](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existují různé důvody, proč mohou být oznámení vyřazena nebo nekončí na zařízeních. V tomto tématu se dozvíte, jak analyzovat a zjistit hlavní příčinu selhání nabízených oznámení.

Můžete také pokračovat k jednomu z následujících kurzů:

* [Přidání ověřování do aplikace](app-service-mobile-xamarin-forms-get-started-users.md)  
  Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Zjistěte, jak pomocí back-endu Mobile Apps přidat do aplikace podporu offline režimu. Při offline synchronizaci můžou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není k dispozici žádné síťové připojení.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
