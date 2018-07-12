---
title: Přidání nabízených oznámení do aplikace Xamarin.Forms | Dokumentace Microsoftu
description: Zjistěte, jak odesílat multiplatformní nabízená oznámení do aplikací Xamarin.Forms pomocí služby Azure.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595702"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Přidání nabízených oznámení do aplikace Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Přehled
V tomto kurzu přidáte nabízená oznámení na všechny projekty, které je výsledkem [Xamarin.Forms úvodní](app-service-mobile-xamarin-forms-get-started.md). To znamená, že nabízené oznámení je odesláno všem klientům multiplatformní pokaždé, když se vložení záznamu.

Pokud použijete serverový projekt stažené rychlý start, budete potřebovat balíček rozšíření nabízené oznámení. Další informace najdete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Požadavky
Pro iOS, budete potřebovat [členství v programu Apple Developer](https://developer.apple.com/programs/ios/) a fyzickém zařízení iOS. [Simulátoru iOS nepodporuje nabízená oznámení](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>Konfigurace centra oznámení
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualizovat projekt serveru k odesílání nabízených oznámení
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Nakonfigurujte a spusťte projekt pro Android (nepovinné)
Dokončení této části ke zprovoznění nabízených oznámení pro Xamarin.Forms Droid projekt pro Android.

### <a name="enable-firebase-cloud-messaging-fcm"></a>Povolení služby Firebase Cloud Messaging (FCM)
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>Konfigurace back end Mobile Apps k odesílání nabízených oznámení požadavků pomocí FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Přidání nabízených oznámení do projektu pro Android
Back-endu nakonfigurovanou FCM můžete přidat komponenty a kódy pro klienta k registraci ve službě FCM. Můžete také zaregistrovat pro nabízená oznámení pomocí Azure Notification Hubs pomocí back-endu mobilní aplikace a přijímat oznámení.

1. V **Droid** projektu, klikněte pravým tlačítkem na **odkazy > spravovat balíčky NuGet...** .
1. V okně Správce balíčků NuGet, vyhledejte **Xamarin.Firebase.Messaging** balíček a přidejte do projektu.
1. V projektu properies pro **Droid** projektu, nastavte aplikaci zkompilovat pomocí verze Androidu 7.0 nebo vyšší.
1. Přidat **souboru google-services.json** soubor, stáhnout z konzole Firebase do kořenového adresáře **Droid** projektu a nastavte jeho akci sestavení na **GoogleServicesJson**. Další informace najdete v tématu [přidat soubor JSON služby Google](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File).

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrace ve službě Firebase Cloud Messaging

1. Otevřete soubor **AndroidManifest.xml** a vložte následující elementy `<receiver>` do elementu `<application>`:

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

#### <a name="implementing-the-firebase-instance-id-service"></a>Implementace služby Firebase Instance ID

1. Přidejte novou třídu do **Droid** projekt s názvem `FirebaseRegistrationService`a ujistěte se, že následující `using` příkazy jsou k dispozici v horní části souboru:

        using System.Threading.Tasks;
        using Android.App;
        using Android.Util;
        using Firebase.Iid;
        using Microsoft.WindowsAzure.MobileServices;

1. Nahraďte prázdnou `FirebaseRegistrationService` třídy následujícím kódem:

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

    `FirebaseRegistrationService` Třída je zodpovědný za generování tokenů zabezpečení, které aplikaci autorizujte pro přístup k FCM. `OnTokenRefresh` Metoda je voláno, když aplikace obdrží registračního tokenu z FCM. Metoda načte token z `FirebaseInstanceId.Instance.Token` vlastnost, která se aktualizuje asynchronně pomocí FCM. `OnTokenRefresh` Zřídka vyvoláním metody, protože token se aktualizuje jenom při instalaci nebo odinstalaci, když uživatel odstraní data aplikací, pokud aplikace odstraní Identifikátor Instance aplikace nebo tokenu zabezpečení byl dojde k ohrožení bezpečnosti. Kromě toho FCM Instance ID služby bude požadovat, že aplikace aktualizuje svůj token pravidelně, obvykle každých 6 měsíců.

    `OnTokenRefresh` Také vyvolá metodu `SendRegistrationTokenToAzureNotificationHub` metodu, která slouží k přidružení tokenu registrace uživatele do centra oznámení Azure.

#### <a name="registering-with-the-azure-notification-hub"></a>Registraci do centra oznámení Azure

1. Přidejte novou třídu do **Droid** projekt s názvem `AzureNotificationHubService`a ujistěte se, že následující `using` příkazy jsou k dispozici v horní části souboru:

        using System;
        using System.Threading.Tasks;
        using Android.Util;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

1. Nahraďte prázdnou `AzureNotificationHubService` třídy následujícím kódem:

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

    `RegisterAsync` Metoda vytvoří šablonu zpráv s oznámením jednoduchá jako JSON a registry získat šablonu oznámení v centru oznámení pomocí služby Firebase registračního tokenu. Tím se zajistí, že všechna naše oznámení odeslaná do centra oznámení Azure se zaměří na zařízení reprezentována registračního tokenu.

#### <a name="displaying-the-contents-of-a-push-notification"></a>Zobrazení obsahu nabízené oznámení

1. Přidejte novou třídu do **Droid** projekt s názvem `FirebaseNotificationService`a ujistěte se, že následující `using` příkazy jsou k dispozici v horní části souboru:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V7.App;
        using Android.Util;
        using Firebase.Messaging;

1. Nahraďte prázdnou `FirebaseNotificationService` třídy následujícím kódem:

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

    `OnMessageReceived` Metodu, která je vyvolána, když aplikace obdrží oznámení z FCM, extrahuje obsah zprávy a volá `SendNotification` metody. Tato metoda převede obsah zprávy do místního oznámení, který se spustí, když je spuštěná aplikace s oznámením v oznamovací oblasti.

Nyní jsou připravené testovací nabízená oznámení v aplikaci spuštěnou na zařízení s Androidem nebo emulátoru.

### <a name="test-push-notifications-in-your-android-app"></a>Nabízená oznámení v aplikaci pro Android
První dva kroky jsou povinné, pouze při testování na emulátoru.

1. Ujistěte se, že jsou nasazení nebo ladění na zařízení nebo emulátor, který je nakonfigurovaný pomocí služby Google Play. Můžete to ověřit tak, že zkontrolujete, který **Přehrát** aplikace jsou nainstalované na zařízení nebo emulátoru.
2. Kliknutím přidejte účet Google na zařízení s Androidem **aplikace** > **nastavení** > **přidat účet**. Postupujte podle pokynů, které chcete přidat existující účet Google do zařízení, nebo vytvořte novou.
3. V sadě Visual Studio nebo Xamarin Studio, klikněte pravým tlačítkem **Droid** projektu a klikněte na tlačítko **nastavit jako spouštěný projekt**.
4. Klikněte na tlačítko **spustit** se projekt sestavil a aplikace se spustila v emulátoru nebo zařízení s Androidem.
5. V aplikaci, zadejte úlohu a potom klikněte na znaménko plus (**+**) ikonu.
6. Ověřte, že obdržení oznámení při přidání položky.

## <a name="configure-and-run-the-ios-project-optional"></a>Nakonfigurujte a spusťte projekt pro iOS (volitelné)
Tato část se týká spuštění projektu Xamarin iOS pro zařízení s iOS. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>Konfigurace centra oznámení pro služby APN
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Dále nakonfigurujete nastavení projektu iOS v nástroji Xamarin Studio nebo Visual Studio.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>Přidání nabízených oznámení do aplikace pro iOS
1. V **iOS** projektu, otevřete AppDelegate.cs a přidejte následující příkaz do horní části souboru kódu.

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
3. V **AppDelegate**, přidejte také následující přepsání **DidReceiveRemoteNotification** obslužné rutiny události:

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
4. V **AppDelegate** třídy, přidejte následující kód, který **FinishedLaunching** metody:

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    To umožňuje podporu pro vzdálené oznámení a požadavky nabízené registrace.

Vaše aplikace je nyní aktualizována o podporu nabízených oznámení.

#### <a name="test-push-notifications-in-your-ios-app"></a>Nabízená oznámení v aplikaci pro iOS
1. Klikněte pravým tlačítkem na projekt pro iOS a klikněte na tlačítko **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy **spustit** tlačítko nebo **F5** v sadě Visual Studio se projekt sestavil a spustil aplikaci v zařízení s Iosem. Pak klikněte na tlačítko **OK** přijímat nabízená oznámení.

   > [!NOTE]
   > Je nutné explicitně přijmout nabízená oznámení z vaší aplikace. Tento požadavek dochází pouze při prvním spuštění aplikace.
   >
   >
3. V aplikaci, zadejte úlohu a potom klikněte na znaménko plus (**+**) ikonu.
4. Ověřte, že přijetí oznámení a potom klikněte na tlačítko **OK** chcete oznámení zavřít.

## <a name="configure-and-run-windows-projects-optional"></a>Konfigurace a spuštění projektů Windows (volitelné)
Tato část se týká s Xamarin.Forms WinApp a WinPhone81 projekty pro zařízení s Windows. Tyto kroky také podporují projekty univerzální platformu Windows (UPW). Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>Registrace aplikace Windows pro nabízená oznámení pomocí služby oznámení Windows (WNS)
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>Konfigurace centra oznámení pro služby nabízených oznámení Windows
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Přidání nabízených oznámení do aplikace pro Windows
1. V sadě Visual Studio, otevřete **App.xaml.cs** v Windows projekt a přidejte následující příkazy.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Nahraďte `<your_TodoItemManager_portable_class_namespace>` s oborem názvů přenosného projektu, který obsahuje `TodoItemManager` třídy.
2. V souboru App.xaml.cs přidejte následující **InitNotificationsAsync** metody:

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

    Tato metoda získá kanál nabízená oznámení a registruje šablonu pro příjem oznámení šablony z vašeho centra oznámení. Šablony oznámení, která podporuje *messageParam* bude doručen do tohoto klienta.
3. V souboru App.xaml.cs, aktualizujte **OnLaunched** definici metody obslužné rutiny události přidáním `async` modifikátor. Poté přidejte následující řádek kódu na konec metody:

        await InitNotificationsAsync();

    Tím se zajistí, že registrace nabízených oznámení vytvoří nebo aktualizuje pokaždé, když je aplikace spuštěná. Je potřeba provést k zajištění, že kanál nabízených oznámení WNS je vždy aktivní.  
4. V Průzkumníku řešení sady Visual Studio, otevřete **Package.appxmanifest** souboru a nastavit **podporuje informační zprávy** k **Ano** pod **oznámení**.
5. Sestavení aplikace a zkontrolujte, že jste žádné chyby. Klientské aplikace by měla nyní zaregistrovat pro šablony oznámení z back-endu Mobile Apps. Tato část opakujte pro každý projekt Windows ve vašem řešení.

#### <a name="test-push-notifications-in-your-windows-app"></a>Nabízená oznámení v aplikaci pro Windows
1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt Windows a klikněte na tlačítko **nastavit jako spouštěný projekt**.
2. Stiskněte tlačítko **Spustit** a sestavte projekt a spusťte aplikaci.
3. V aplikaci, zadejte název nového úkolu a potom klikněte na znaménko plus (**+**) ikonu a přidejte ji.
4. Ověřte, že je přijato oznámení, když je položka přidána.

## <a name="next-steps"></a>Další postup
Další informace o nabízených oznámení:

* [Odesílání nabízených oznámení z Azure Mobile Apps](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Služba firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Vzdálená oznámení pomocí služby Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [Diagnostikujte problémy nabízených oznámení](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  Existují různé důvody, proč oznámení může získáte nebo nekončí na zařízeních. V tomto tématu se dozvíte, jak analyzovat a zjistěte příčinu selhání nabízené oznámení.

Také budete moct pokračovat do jednoho z následujících kurzů:

* [Přidání ověřování do aplikace](app-service-mobile-xamarin-forms-get-started-users.md)  
  Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Povolení offline synchronizace u aplikace](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Zjistěte, jak pomocí back-endu Mobile Apps přidat do aplikace podporu offline režimu. Offline synchronizace, mohou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není žádné síťové připojení.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
