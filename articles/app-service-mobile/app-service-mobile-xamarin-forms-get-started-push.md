---
title: Přidání nabízených oznámení do vaší aplikace na platformě Xamarin.Forms | Microsoft Docs
description: Naučte se používat k odesílání více platformami nabízená oznámení do aplikace Xamarin.Forms služby Azure.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 0bea00d411205541684e807182abd3236c09bd9d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Přidání nabízených oznámení do vaší aplikace na platformě Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled
V tomto kurzu přidání nabízených oznámení na všechny projekty, které je výsledkem [Xamarin.Forms úvodní](app-service-mobile-xamarin-forms-get-started.md). To znamená, že nabízených oznámení se neodesílají do všech klientů napříč platformami pokaždé, když vložení záznamu.

Pokud použijete serverový projekt stažené rychlý start, budete potřebovat balíček rozšíření nabízená oznámení. Další informace najdete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Požadavky
Pro iOS, budete potřebovat [programu pro vývojáře Apple členství](https://developer.apple.com/programs/ios/) a fyzickém zařízení iOS. [Simulátoru iOS nabízená oznámení nepodporuje](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurace centra oznámení
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizace serverový projekt k odesílání nabízených oznámení
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Konfiguraci a spuštění projektu pro Android (volitelné)
Dokončení této části ke zprovoznění nabízených oznámení pro Android Xamarin.Forms projekt pro Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Povolit Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurace mobilní aplikace back-endu odesílat žádosti o nabízenou pomocí FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Přidání nabízených oznámení do projektu pro Android
S back-end FCM nakonfigurované můžete přidat součásti a kódy pro klienta pro registraci se FCM. Můžete také zaregistrovat pro nabízená oznámení pomocí Azure Notification Hubs prostřednictvím back-end mobilní aplikace a přijímat oznámení.

1. V **Droid** projektu, klikněte pravým tlačítkem na **odkazy > spravovat balíčky NuGet...** .
1. V okně Správce balíčků NuGet Hledat **Xamarin.Firebase.Messaging** balíček a přidejte ji do projektu.
1. V projektu properies pro **Droid** projektu, nastavte aplikaci zkompilovat pomocí Android verze 7.0 nebo vyšší.
1. Přidat **google services.json** soubor, stažený z konzoly Firebase do kořenového adresáře **Droid** projektu a nastavit jeho procesu sestavení na **GoogleServicesJson**. Další informace najdete v tématu [přidejte soubor JSON služby Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrace Firebase cloudu zasílání zpráv

1. Otevřete **AndroidManifest.xml** soubor a vložte následující `<receiver>` elementy do `<application>` element:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementace služby Firebase instanci ID

1. Přidejte novou třídu do **Droid** projektu s názvem `FirebaseRegistrationService`a ujistěte se, že následující `using` příkazy nejsou v horní části souboru:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Nahraďte prázdné `FirebaseRegistrationService` třídy následujícím kódem:

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

    `FirebaseRegistrationService` Třída je zodpovědný za generování tokenů zabezpečení, které autorizovat aplikaci pro přístup k FCM. `OnTokenRefresh` Metoda je volána, když aplikace přijímá token registrace z FCM. Metoda načte tokenu z `FirebaseInstanceId.Instance.Token` vlastnost, která se asynchronně aktualizuje pomocí FCM. `OnTokenRefresh` Zřídka zavolání metody, protože token je aktualizovány pouze v případě, že aplikace je nainstalována nebo odinstalována, když uživatel odstraní data aplikací, když aplikace vymaže Instance ID, nebo když byl zabezpečení tokenu ohrožení zabezpečení. Kromě toho FCM Instance ID služby bude požadovat, aby aplikace aktualizuje jeho token pravidelně obvykle každých 6 měsíců.

    `OnTokenRefresh` Také vyvolá metoda `SendRegistrationTokenToAzureNotificationHub` metodu, která se používá k přidružení tokenu registrace uživatele do centra oznámení Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registrace do centra oznámení Azure

1. Přidejte novou třídu do **Droid** projektu s názvem `AzureNotificationHubService`a ujistěte se, že následující `using` příkazy nejsou v horní části souboru:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Nahraďte prázdné `AzureNotificationHubService` třídy následujícím kódem:

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

    `RegisterAsync` Metoda vytvoří šablonu oznámení jednoduché zprávy jako JSON a zaregistruje se pro příjem oznámení šablony z centra oznámení, pomocí tokenu registrace Firebase. Tím se zajistí, že zařízení reprezentována registrační token se zaměří na všechny oznámení odesílaná z centra oznámení Azure.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Zobrazení obsahu nabízených oznámení

1. Přidejte novou třídu do **Droid** projektu s názvem `FirebaseNotificationService`a ujistěte se, že následující `using` příkazy nejsou v horní části souboru:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. Nahraďte prázdné `FirebaseNotificationService` třídy následujícím kódem:

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

    `OnMessageReceived` Metoda, která se vyvolá, když aplikace obdrží oznámení od FCM, extrahuje obsahu zprávu a zavolá `SendNotification` metoda. Tato metoda převede obsah zprávy do místního oznámení, který se spustí, když aplikace běží, oznámení, které jsou uvedeny v oznamovací oblasti.

Teď můžete je připraven nabízená oznámení v aplikaci spuštěnou na zařízení se systémem Android nebo emulátor.

### <a name="test-push-notifications-in-your-android-app"></a>Nabízená oznámení v aplikacích pro Android
První dva kroky jsou povinné, jenom v případě, že testujete na emulátor.

1. Ujistěte se, že nasazení nebo ladění na zařízení nebo emulátoru, která je konfigurovaná pomocí služby Google Play. To můžete ověřit kontrolou, která **přehrání** aplikace jsou nainstalovány na emulátoru nebo zařízení.
2. Kliknutím na Přidat účet Google do zařízení s Androidem **aplikace** > **nastavení** > **přidejte účet**. Potom postupujte podle pokynů, které chcete přidat existující účet Google do zařízení, nebo vytvořte novou.
3. V sadě Visual Studio nebo Xamarin Studio, klikněte pravým tlačítkem **Droid** projektu a klikněte na tlačítko **nastavit jako spouštěný projekt**.
4. Klikněte na tlačítko **spustit** pro sestavení projektu a spusťte aplikaci v emulátoru nebo zařízení s Androidem.
5. V aplikaci zadejte úlohu a potom klikněte na tlačítko plus (**+**) ikona.
6. Ověřte, že se při přidání položky přijato oznámení.

## <a name="configure-and-run-the-ios-project-optional"></a>Konfiguraci a spuštění projektu pro iOS (volitelné)
Tato část se týká spuštění projektu Xamarin iOS pro zařízení s iOS. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurace centra oznámení pro služby APN
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Dále nakonfigurujete nastavení projektu iOS v nástroji Xamarin Studio nebo Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do vaší aplikace pro iOS
1. V **iOS** projektu, otevřete AppDelegate.cs a přidejte následující příkaz na začátek souboru kódu.

        using Newtonsoft.Json.Linq;
2. V **AppDelegate** třídy, přidejte přepsání pro **RegisteredForRemoteNotifications** události k registraci pro oznámení:

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
3. V **AppDelegate**, také přidat následující přepsání pro **DidReceiveRemoteNotification** obslužné rutiny události:

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

    Tato metoda zpracovává příchozí oznámení, když aplikace běží.
4. V **AppDelegate** třídy, přidejte následující kód, který **FinishedLaunching** metoda:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    To umožňuje podporu pro vzdálené oznámení a registrace nabízených požadavky.

Aplikace je nyní aktualizovat o podporu nabízených oznámení.

#### <a name="test-push-notifications-in-your-ios-app"></a>Nabízená oznámení v aplikaci s iOS
1. Klikněte pravým tlačítkem na projekt pro iOS a klikněte na tlačítko **nastavit jako spouštěný projekt**.
2. Stiskněte **spustit** tlačítko nebo **F5** v sadě Visual Studio pro sestavení projektu a spusťte aplikaci v zařízení se systémem iOS. Pak klikněte na tlačítko **OK** přijímat nabízená oznámení.

   > [!NOTE]
   > Je nutné explicitně přijmout nabízená oznámení z vaší aplikace. Tento požadavek dochází pouze při prvním spuštění aplikace.
   >
   >
3. V aplikaci zadejte úlohu a potom klikněte na tlačítko plus (**+**) ikona.
4. Ověřte, zda přijetí oznámení a pak klikněte na tlačítko **OK** k zavření oznámení.

## <a name="configure-and-run-windows-projects-optional"></a>Nakonfigurujte a spusťte projekty pro Windows (volitelné)
Tato část se týká spuštění Xamarin.Forms WinApp a WinPhone81 projekty pro zařízení se systémem Windows. Tyto kroky také podporují projekty univerzální platformu Windows (UWP). Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrace aplikace systému Windows pro nabízená oznámení pomocí služby oznámení Windows (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows
1. V sadě Visual Studio otevřete **App.xaml.cs** v systému Windows projekt a přidejte následující příkazy.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Nahraďte `<your_TodoItemManager_portable_class_namespace>` s oborem názvů vašeho přenosného projektu, který obsahuje `TodoItemManager` třídy.
2. V souboru App.xaml.cs přidejte následující **InitNotificationsAsync** metoda:

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

    Tato metoda získá kanál nabízená oznámení a zaregistruje šablonu do šablony oznámení dostávat, vaše Centrum oznámení. Šablony oznámení, která podporuje *messageParam* budou doručeny do tohoto klienta.
3. V souboru App.xaml.cs, aktualizovat **OnLaunched** definici metody obslužné rutiny událostí přidáním `async` modifikátor. Pak přidejte následující řádek kódu na konci metody:

        await InitNotificationsAsync();

    Tím se zajistí, že registrace nabízených oznámení je vytvořit nebo aktualizovat pokaždé, když je aplikace spuštěná. Je důležité k tomu zaručit, že kanál nabízené WNS je vždy aktivní.  
4. V Průzkumníku řešení pro sadu Visual Studio, otevřete **Package.appxmanifest** souboru a nastavit **informační podporující** k **Ano** pod **oznámení**.
5. Sestavte aplikaci a zkontrolujte, že jste žádné chyby. Klientská aplikace by teď zaregistrovat pro šablony oznámení z back-end mobilní aplikace. Tato část opakujte pro každý projekt Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Nabízená oznámení v aplikaci Windows
1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt Windows a klikněte na tlačítko **nastavit jako spouštěný projekt**.
2. Stiskněte tlačítko **Spustit** a sestavte projekt a spusťte aplikaci.
3. V aplikaci, zadejte název nové todoitem a pak klikněte na tlačítko plus (**+**) ikona Přidat.
4. Ověřte, že se při přidání položky přijato oznámení.

## <a name="next-steps"></a>Další postup
Další informace o nabízených oznámení:

* [Odesílání nabízených oznámení z Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase cloudu zasílání zpráv](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Vzdálená oznámení s Firebase cloudu zasílání zpráv](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostikovat problémy nabízená oznámení](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existují různé důvody, proč oznámení může získat vyřadit ani nekončí na zařízení. Toto téma ukazuje, jak analyzovat a zjistěte příčinu selhání nabízená oznámení.

Můžete také pokračovat na jednu z následujících kurzů:

* [Přidání ověřování do aplikace](app-service-mobile-xamarin-forms-get-started-users.md)  
  Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Zjistěte, jak pomocí back-endu Mobile Apps přidat do aplikace podporu offline režimu. S offline synchronizací, mohou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidávat a upravovat data&mdash;i v případě, že není žádné síťové připojení.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
