---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112089"
---
### <a name="validate-package-name-and-permissions"></a>Ověřit název a oprávnění balíčku

1. V **PushDemo. Androidu**otevřete **Možnosti projektu** a pak **aplikace pro Android** z části **Build (sestavení** ).

1. Ověřte, že **název balíčku** se shoduje s hodnotou, kterou jste použili v projektu **PushDemo** [konzoly Firebase](https://console.firebase.google.com) . **Název balíčku** byl ve formátu ``com.<organization>.pushdemo`` .

1. Nastavte **minimální verzi Androidu** na **Android 8,0 (rozhraní API úrovně 26)** a **cílovou verzi Androidu** na nejnovější **úroveň rozhraní API**.

    > [!NOTE]
    > Pro účely tohoto kurzu se podporují jenom ta zařízení s **rozhraním API, která používají verzi 26 a vyšší** . můžete je ale zvětšit tak, aby podporovala zařízení, na kterých běží starší verze.

1. Zajistěte, aby byla povolená oprávnění **Internet** a **READ_PHONE_STATE** v části **požadovaná oprávnění**.

1. Klikněte na tlačítko **OK**.

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Přidejte balíčky Xamarin Služby Google Play Base a Xamarin. Firebase. Messaging.

1. V **PushDemo. Androidu** **Control**  +  **klikněte na** složku **balíčky** a pak zvolte **Spravovat balíčky NuGet...**.

1. Vyhledejte **Xamarin. GooglePlayServices. Base** (ne **Basement**) a ujistěte se, že je zaškrtnuté.

1. Vyhledejte **Xamarin. Firebase. Messaging** a ujistěte se, že je zaškrtnuté.

1. Klikněte na **Přidat balíčky**a po zobrazení výzvy k přijetí **licenčních podmínek**klikněte na **přijmout** .

### <a name="add-the-google-services-json-file"></a>Přidat soubor JSON služeb Google

1. **Ovládací prvek**  +  **Klikněte** na `PushDemo.Android` projekt a pak zvolte **existující soubor...** v nabídce **Přidat** .

1. Zvolte *google-services.js* souboru, který jste dříve stáhli při nastavování projektu **PushDemo** v [konzole Firebase](https://console.firebase.google.com) , a pak klikněte na **otevřít**.

1. Po zobrazení výzvy vyberte **kopírování souboru do adresáře**.

1. **Ovládací prvek**  +  **Klikněte** na *google-services.jsv* souboru v rámci `PushDemo.Android` projektu a pak ověřte, že je **GoogleServicesJson** nastavené jako **Akce sestavení**.

### <a name="handle-push-notifications-for-android"></a>Zpracování nabízených oznámení pro Android

1. **Ovládací prvek**  +  **Klikněte** na `PushDemo.Android` projekt, v nabídce **Přidat** zvolte **Nová složka** a pak klikněte na **Přidat** pomocí *služby* jako **název složky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **služby** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Vyberte **Obecné**  >  **prázdné třídy**, jako **název**zadejte *DeviceInstallationService.cs* a pak klikněte na **Nový** přidání následující implementace.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Tato třída poskytuje jedinečné ID (pomocí [Secure. AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) jako součást datové části registrace centra oznámení.

1. Do složky **Services** s názvem *PushNotificationFirebaseMessagingService.cs*přidejte další **prázdnou třídu** a pak přidejte následující implementaci.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. V **MainActivity.cs**zajistěte, aby byly do horní části souboru přidány následující obory názvů.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. V **MainActivity.cs**nastavte **LaunchMode** na **jedna nahoře** , aby **MainActivity** se znovu nevytvořil po otevření.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Přidáním privátních vlastností a odpovídajících zálohovaných polí uložte odkaz na implementace **IPushNotificationActionService** a **IDeviceInstallationService** .

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implementujte rozhraní **IOnSuccessListener** pro načtení a uložení tokenu **Firebase** .

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Přidejte novou metodu s názvem **ProcessNotificationActions** , která ověří, zda má daný **záměr** hodnotu navíc s názvem *Action*. Podmíněně aktivuje tuto akci pomocí implementace **IPushDemoNotificationActionService** .

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Přepište metodu **OnNewIntent** pro volání metody **CheckIntentForNotificationActions** .

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Vzhledem k tomu, že **LaunchMode** pro **aktivitu** je nastavena na **jedna nahoře**, **záměr** bude odeslán do existující instance **aktivity** prostřednictvím metody **OnNewIntent** spíše než pomocí metody **Create** , takže je nutné zpracovat příchozí záměr v metodách **Create** a **OnNewIntent** .

1. Aktualizujte metodu **Create** tak, aby volala `Bootstrap.Begin` hned po volání k `base.OnCreate` předání v implementaci **IDeviceInstallationService**specifické pro platformu.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Ve stejné metodě podmíněně volejte **getinstanceid** na instanci **FirebaseApp** , hned po volání metody `Bootstrap.Begin` a přidejte **MainActivity** jako **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Stále v rámci metody **Create**volejte **ProcessNotificationActions** hned po volání metody `LoadApplication` předání v aktuálním **záměru**.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Aplikaci musíte znovu zaregistrovat pokaždé, když ji spustíte, a pokud ji zastavíte z relace ladění, budete moct dál přijímat nabízená oznámení.
