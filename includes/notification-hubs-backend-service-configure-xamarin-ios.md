---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 1dc491084f65bc90397b0897de6b6cfe4f2fd410
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448773"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Konfigurace info. plist a Nároks. plist

1. Ujistěte se, že jste přihlášeni ke svému **účtu Apple Developer** v předvolbách sady **Visual Studio**  >  **...**  >  **Publikování**  >  Stáhli jsme **účty Apple Developer** a příslušný *certifikát* a *profil pro zřizování* . Tyto assety byste měli vytvořit jako součást předchozích kroků.

1. V **PushDemo. iOS**otevřete **info. plist** a ujistěte se, že **BundleIdentifier** odpovídá hodnotě, která se použila pro příslušný zřizovací profil na portálu pro [vývojáře Apple](https://developer.apple.com). **BundleIdentifier** byl ve formátu ``com.<organization>.PushDemo`` .

1. Ve stejném souboru nastavte **minimální verzi systému** na **13,0**.

    > [!NOTE]
    > Pro účely tohoto kurzu se podporují jenom zařízení se systémem **iOS 13,0 a novějším** , ale můžete je zvětšit tak, aby podporovala zařízení, na kterých běží starší verze.

1. Otevřete **Možnosti projektu** pro **PushDemo. iOS** (dvakrát klikněte na projekt).

1. V **možnostech projektu**v části **sestavení > podepisování sady prostředků iOS**se ujistěte, že je v části **tým**vybraný vývojářský účet. Pak se ujistěte, že je vybraná možnost automaticky spravovat podpis, a automaticky se vyberou váš podpisový certifikát a zřizovací profil.

    > [!NOTE]
    > Pokud není váš *podpisový certifikát* a *zřizovací profil* automaticky vybraný, zvolte **Ruční zřizování**a pak klikněte na **možnosti podepisování sady prostředků**. Ujistěte se, že je váš *tým* vybraný pro **podpisovou identitu** *a že je* pro **zřizovací** profil vybraný profil zřizování pro konfiguraci **ladění** i vydaných **verzí** , aby bylo zajištěno, že pro **platformu** v obou případech je pro iPhone vybraná možnost **iPhone** .

1. V **PushDemo. iOS**otevřete **oprávnění. plist** a zajistěte, aby se při zobrazení na kartě **nároky** zkontrolovala **možnost Povolit nabízená oznámení** . Pak zajistěte, aby nastavení **prostředí APS** bylo při zobrazení na kartě **zdroj** nastavené na možnost **vývoj** .

### <a name="handle-push-notifications-for-ios"></a>Zpracování nabízených oznámení pro iOS

1. **Ovládací prvek**  +  **Klikněte** na projekt **PushDemo. iOS** , v nabídce **Přidat** zvolte **Nová složka** a pak klikněte na **Přidat** pomocí *služby* jako **název složky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **služby** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Vyberte **Obecné**  >  **prázdné třídy**, jako **název**zadejte *DeviceInstallationService.cs* a pak klikněte na **Nový** přidání následující implementace.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Tato třída poskytuje jedinečné ID (pomocí hodnoty [UIDevice. IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) ) a datové části registrace centra oznámení.

1. Přidejte novou složku do projektu **PushDemo. iOS** s názvem *Extensions* a pak přidejte **prázdnou třídu** do této složky s názvem *NSDataExtensions.cs* s následující implementací.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. V **AppDelegate.cs**zajistěte, aby byly do horní části souboru přidány následující obory názvů.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Přidejte soukromé vlastnosti a jejich příslušná zálohovaná pole pro uložení odkazu na implementace **IPushDemoNotificationActionService**, **INotificationRegistrationService**a **IDeviceInstallationService** .

    ```csharp
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
    ```

1. Přidejte metodu **RegisterForRemoteNotifications** pro registraci nastavení oznámení uživateli a pak pro vzdálená oznámení pomocí služby **APN**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Přidejte metodu **CompleteRegistrationAsync** , která nastaví `IDeviceInstallationService.Token` hodnotu vlastnosti. Aktualizuje registraci a uloží token zařízení do mezipaměti, pokud byl od posledního uložení aktualizovaný.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Přidejte metodu **ProcessNotificationActions** pro zpracování dat oznámení **NSDictionary** a podmíněně zavolejte **NotificationActionService. TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Přepište metodu **RegisteredForRemoteNotifications** předáním argumentu **deviceToken** metodě **CompleteRegistrationAsync** .

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Přepište metodu **ReceivedRemoteNotification** předáním argumentu **UserInfo** metodě **ProcessNotificationActions** .

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Přepsáním metody **FailedToRegisterForRemoteNotifications** Zaprotokolujte chybu.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > To je velmi velký zástupný symbol. Pro produkční scénáře budete chtít implementovat správné protokolování a zpracování chyb.

1. Aktualizujte metodu **FinishedLaunching** tak, aby volala `Bootstrap.Begin` hned po volání metody, která `Forms.Init` je specifická pro platformu **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. Ve stejné metodě bezpodmínečně vyžádáte autorizaci a zaregistrujte se na vzdálená oznámení hned po `Bootstrap.Begin` .

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Stále v **FinishedLaunching**volejte **ProcessNotificationActions** hned po volání, `LoadApplication` Pokud argument **Options** obsahuje **UIApplication. LaunchOptionsRemoteNotificationKey** , který se předává do výsledného objektu **UserInfo** .

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
