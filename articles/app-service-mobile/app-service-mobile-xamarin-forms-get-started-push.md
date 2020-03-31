---
title: Přidání nabízených oznámení do aplikace Xamarin.Forms
description: Přečtěte si, jak pomocí služeb Azure odesílat nabízená oznámení pro více platforem do aplikací Xamarin.Forms.
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f23ac2d693492695c398893c103d5a77a0e93129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461466"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Přidání nabízených oznámení do aplikace Xamarin.Forms

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled

V tomto kurzu přidáte nabízená oznámení do všech projektů, které vznikly z [Xamarin.Forms rychlý start](app-service-mobile-xamarin-forms-get-started.md). To znamená, že nabízené oznámení je odesláno všem klientům napříč platformami při každém vložení záznamu.

Pokud nepoužíváte stažený projekt serveru rychlého startu, budete potřebovat balíček rozšíření nabízených oznámení. Další informace naleznete [v tématu Práce s back-endovým serverem .NET SDK pro mobilní aplikace Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Požadavky

Pro iOS budete potřebovat členství v [Programu pro vývojáře Apple](https://developer.apple.com/programs/ios/) a fyzické iOS zařízení. [Simulátor iOS nepodporuje nabízená oznámení](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-a-notification-hub"></a><a name="configure-hub"></a>Konfigurace centra oznámení

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizace projektu serveru pro odesílání nabízených oznámení

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfigurace a spuštění projektu Android (volitelné)

Vyplňte tuto část a povolte nabízená oznámení pro projekt Xamarin.Forms Droid pro Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Povolit zasílání zpráv cloudu Firebase (FCM)

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurace back-endu mobilních aplikací pro odesílání nabízených požadavků pomocí FCM

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Přidání nabízených oznámení do projektu Android

S back-end nakonfigurován s FCM, můžete přidat komponenty a kódy do klienta se zaregistrovat s FCM. Můžete se také zaregistrovat pro nabízená oznámení pomocí centra Azure Notification Hubs prostřednictvím back-endu mobilních aplikací a přijímat oznámení.

1. V projektu **Droid** klepněte pravým tlačítkem myši na **odkazy > Spravovat balíčky NuGet ...**.
1. V okně Správce balíčků NuGet vyhledejte balíček **Xamarin.Firebase.Messaging** a přidejte ho do projektu.
1. Ve vlastnostech projektu **projektu Droid** nastavte aplikaci tak, aby se zkompilovala pomocí verze 7.0 systému Android nebo vyšší.
1. Přidejte soubor **google-services.json,** stažený z konzole Firebase, do kořenového adresáře projektu **Droid** a nastavte jeho akci sestavení na **GoogleServicesJson**. Další informace naleznete [v tématu Add the Google Services JSON File](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

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

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementace služby ID instance Firebase

1. Přidejte novou třídu do `FirebaseRegistrationService`projektu **Droid** s `using` názvem a ujistěte se, že v horní části souboru jsou následující příkazy:

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. Nahraďte `FirebaseRegistrationService` prázdnou třídu následujícím kódem:

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

    Třída `FirebaseRegistrationService` je zodpovědná za generování tokenů zabezpečení, které autorizují aplikaci pro přístup k FCM. Metoda `OnTokenRefresh` je vyvolána, když aplikace obdrží registrační token z FCM. Metoda načte token z `FirebaseInstanceId.Instance.Token` vlastnosti, která je asynchronně aktualizována FCM. Metoda `OnTokenRefresh` je zřídka vyvolána, protože token je aktualizován pouze při instalaci nebo odinstalaci aplikace, když uživatel odstraní data aplikace, když aplikace vymaže ID instance nebo když došlo k ohrožení zabezpečení tokenu. Kromě toho služba ID instance FCM bude požadovat, aby aplikace pravidelně aktualizuje svůj token, obvykle každých 6 měsíců.

    Metoda `OnTokenRefresh` také vyvolá `SendRegistrationTokenToAzureNotificationHub` metodu, která se používá k přidružení registračního tokenu uživatele k centru oznámení Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrace v Centru oznámení Azure

1. Přidejte novou třídu do `AzureNotificationHubService`projektu **Droid** s `using` názvem a ujistěte se, že v horní části souboru jsou následující příkazy:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. Nahraďte `AzureNotificationHubService` prázdnou třídu následujícím kódem:

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

    Metoda `RegisterAsync` vytvoří jednoduchou šablonu oznámení jako JSON a zaregistruje přijímat oznámení šablony z centra oznámení pomocí tokenu registrace Firebase. Tím zajistíte, že všechna oznámení odeslaná z Centra oznámení Azure se zaměří na zařízení reprezentované registračním tokenem.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Zobrazení obsahu nabízeného oznámení

1. Přidejte novou třídu do `FirebaseNotificationService`projektu **Droid** s `using` názvem a ujistěte se, že v horní části souboru jsou následující příkazy:

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. Nahraďte `FirebaseNotificationService` prázdnou třídu následujícím kódem:

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
            //Unique request code to avoid PendingIntent collision.
            var requestCode = new Random().Next();
            var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);
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

    Metoda, `OnMessageReceived` která je vyvolána, když aplikace obdrží oznámení z FCM, extrahuje obsah zprávy a volá metodu. `SendNotification` Tato metoda převede obsah zprávy do místní oznámení, které je spuštěno, když je aplikace spuštěna, s oznámením v oznamovací oblasti.

Nyní jste připraveni otestovat nabízená oznámení v aplikaci spuštěné na zařízení Android nebo emulátoru.

### <a name="test-push-notifications-in-your-android-app"></a>Testování nabízených oznámení v aplikaci pro Android

První dva kroky jsou vyžadovány pouze při testování na emulátoru.

1. Ujistěte se, že nasazujete nebo ladíte zařízení nebo emulátor, který je nakonfigurován ve službách Google Play. To lze ověřit kontrolou, zda jsou aplikace **Play** nainstalovány v zařízení nebo emulátoru.
2. Přidejte účet Google do zařízení Android kliknutím na**Nastavení** >  **aplikací** > **Přidat účet**. Potom podle pokynů přidejte do zařízení existující účet Google nebo vytvořte nový.
3. V sadě Visual Studio nebo Xamarin Studio klepněte pravým tlačítkem myši na projekt **Droid** a klepněte na příkaz **Nastavit jako spouštěcí projekt**.
4. Kliknutím na **Spustit** sestavíte projekt a spustíte aplikaci na zařízení Android nebo emulátoru.
5. V aplikaci zadejte úkol a klikněte**+** na ikonu plus ( ).
6. Ověřte, zda je přijato oznámení při přidání položky.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfigurace a spuštění projektu iOS (volitelné)

Tato část se týká spuštění projektu Xamarin iOS pro zařízení s iOS. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurace centra oznámení pro apns

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Dále nakonfigurujete nastavení projektu iOS v Xamarin Studio nebo Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do aplikace pro iOS

1. V projektu **iOS** otevřete AppDelegate.cs a přidejte následující příkaz do horní části souboru kódu.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. Ve třídě **AppDelegate** přidejte přepsání události **RegisteredForRemoteNotifications,** chcete-li zaregistrovat oznámení:

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

3. V **aplikaci AppDelegate**přidejte také následující přepsání obslužné rutiny události **DidReceiveRemoteNotification:**

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

    Tato metoda zpracovává příchozí oznámení, když je aplikace spuštěná.

4. Ve třídě **AppDelegate** přidejte do metody **FinishedLaunch** následující kód:

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

    To umožňuje podporu pro vzdálená oznámení a požadavky nabízené registrace.

Vaše aplikace je teď aktualizována, aby podporovala nabízená oznámení.

#### <a name="test-push-notifications-in-your-ios-app"></a>Testování nabízených oznámení v aplikaci pro iOS

1. Klikněte pravým tlačítkem myši na projekt iOS a klikněte na **Nastavit jako počáteční projekt**.
2. Stisknutím tlačítka **Spustit** nebo **F5** v Sadě Visual Studio vytvořte projekt a spusťte aplikaci v zařízení se systémem iOS. Potom klepnutím na tlačítko **OK** přijměte nabízená oznámení.

   > [!NOTE]
   > Z aplikace musíte explicitně přijímat nabízená oznámení. K tomuto požadavku dochází pouze při prvním spuštění aplikace.

3. V aplikaci zadejte úkol a klikněte**+** na ikonu plus ( ).
4. Ověřte, zda je přijato oznámení, a klepnutím na tlačítko **OK** oznámení zavřete.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurace a spuštění projektů systému Windows (volitelné)

Tato část je určen pro spuštění Xamarin.Forms WinApp a WinPhone81 projekty pro zařízení se systémem Windows. Tyto kroky také podporují projekty univerzální platformy Windows (UPW). Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrace aplikace pro Windows pro nabízená oznámení pomocí služby Windows Notification Service (WNS)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro službu WNS

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows

1. V sadě Visual Studio otevřete **App.xaml.cs** v projektu systému Windows a přidejte následující příkazy.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    Nahraďte `<your_TodoItemManager_portable_class_namespace>` obor názvů přenosného projektu, který obsahuje třídu. `TodoItemManager`

2. V App.xaml.cs přidejte následující metodu **InitNotificationsAsync:**

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

    Tato metoda získá kanál nabízených oznámení a zaregistruje šablonu pro příjem oznámení šablony z centra oznámení. Oznámení šablony, které podporuje *messageParam* budou doručeny tomuto klientovi.

3. V App.xaml.cs aktualizujte definici metody obslužné rutiny události **OnLaunched** přidáním modifikátoru. `async` Potom přidejte následující řádek kódu na konci metody:

    ```csharp
    await InitNotificationsAsync();
    ```

    Tím zajistíte, že registrace nabízených oznámení se vytvoří nebo aktualizuje při každém spuštění aplikace. Je důležité to udělat, aby bylo zaručeno, že wns push kanál je vždy aktivní.  

4. V Průzkumníku řešení pro Visual Studio otevřete soubor **Package.appxmanifest** a nastavte **toast schopný** **ano** v části **Oznámení**.
5. Vytvořte aplikaci a ověřte, že nemáte žádné chyby. Vaše klientská aplikace by se teď měla zaregistrovat pro oznámení šablony z back-endu Mobilních aplikací. Opakujte tuto část pro každý projekt systému Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Testování nabízených oznámení v aplikaci pro Windows

1. V sadě Visual Studio klepněte pravým tlačítkem myši na projekt systému Windows a klepněte na příkaz **Nastavit jako spouštěcí projekt**.
2. Stiskněte tlačítko **Spustit** a sestavte projekt a spusťte aplikaci.
3. V aplikaci zadejte název nové todopoložky a kliknutím**+** na ikonu plus ( ) ji přidejte.
4. Ověřte, zda je přijato oznámení při přidání položky.

## <a name="next-steps"></a>Další kroky

Další informace o nabízených oznámeních:

* [Odesílání nabízených oznámení z mobilních aplikací Azure](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Zasílání zpráv v cloudu Firebase](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Vzdálená oznámení pomocí cloudových zpráv Firebase](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostika problémů s nabízená oznámení](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existují různé důvody, proč oznámení mohou být zrušena nebo nekončí na zařízeních. Toto téma ukazuje, jak analyzovat a zjistit hlavní příčinu selhání nabízených oznámení.

Můžete také pokračovat na jeden z následujících výukových programů:

* [Přidání ověřování do aplikace](app-service-mobile-xamarin-forms-get-started-users.md)  
  Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Zjistěte, jak pomocí back-endu Mobile Apps přidat do aplikace podporu offline režimu. Při offline synchronizaci mohou uživatelé&mdash;pracovat se zobrazením,&mdash;přidáváním nebo úpravou dat v mobilní aplikaci, i když neexistuje žádné síťové připojení.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
