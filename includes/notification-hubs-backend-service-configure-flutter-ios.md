---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147788"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Konfigurace cíle spouštěče a souboru info. plist

1. V **Visual Studio Code** **ovládací prvek**  +  **klikněte** na složku **iOS** a pak zvolte **otevřít v Xcode**.

1. V **Xcode**klikněte na **Runner** ( **XCODEPROJ** v horní části, ne na složku) a pak vyberte cíl **spouštěče** a pak **podepisování možností &**. Když je vybraná možnost **všechny** konfigurace sestavení, vyberte pro **tým**vývojářský účet. Zajistěte, aby se zkontrolovala možnost automaticky spravovat podpis, a váš podpisový certifikát a zřizovací profil se automaticky vyberou.

    > [!NOTE]
    > Pokud nevidíte novou hodnotu zřizovacího profilu, zkuste aktualizovat profily pro podpisovou identitu tak, že vyberete **Xcode**  >  **Preferences**  >  **účet** předvoleb Xcode a pak kliknutím na tlačítko **Stáhnout ruční profily** Stáhněte profily.

1. Klikněte na **+ funkce**a pak vyhledejte **nabízená oznámení**. Pro přidání této funkce **poklikejte** na **nabízená oznámení** .

1. Otevřete soubor **info. plist** a nastavte **minimální verzi systému** na **13,0**.

    > [!NOTE]
    > Pro účely tohoto kurzu se podporují jenom zařízení se systémem **iOS 13,0 a novějším** , ale můžete je zvětšit tak, aby podporovala zařízení, na kterých běží starší verze.

1. Otevřete **Runner. oprávnění** a ujistěte se, že nastavení **prostředí APS** je nastavené na možnost **vývoj**.

### <a name="handle-push-notifications-for-ios"></a>Zpracování nabízených oznámení pro iOS

1. **Ovládací prvek**  +  **Klikněte** na složku **spouštěče** (v rámci projektu spouštěče) a pak jako název zvolte **Nová skupina** pomocí **služby** .

1. **Ovládací prvek**  +  **Klikněte** na složku **služby** a pak zvolte **nový soubor...**. Pak zvolte **soubor SWIFT** a klikněte na **Další**. Pro název zadejte **DeviceInstallationService** a pak klikněte na **vytvořit**.

1. Implementujte **DeviceInstallationService. SWIFT** pomocí následujícího kódu.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Tato třída implementuje pro kanál protějšek specifickou pro konkrétní platformu `com.<your_organization>.pushdemo/deviceinstallation` . To bylo definováno v části flutter aplikace v rámci **DeviceInstallationService. DART**. V tomto případě jsou volání provedena ze společného kódu do nativního hostitele. Nezapomeňte nahradit **<your_organization>** s vlastní organizací bez ohledu na to, kde se používá.
    >
    > Tato třída poskytuje jedinečné ID (pomocí hodnoty [UIDevice. identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) ) jako součást datové části registrace centra oznámení.

1. Do složky **Services** s názvem *NotificationRegistrationService*přidejte další **soubor SWIFT** a přidejte následující kód.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Tato třída implementuje pro kanál protějšek specifickou pro konkrétní platformu `com.<your_organization>.pushdemo/notificationregistration` . To bylo definováno v části flutter aplikace v rámci **NotificationRegistrationService. DART**. V tomto případě jsou volání uskutečněna z nativního hostitele do společného kódu. Bez ohledu na to, co se používá, je potřeba, abyste nahradili **<your_organization>** s vlastní organizací.

1. Do složky **Services** s názvem *NotificationActionService*přidejte další **soubor SWIFT** a přidejte následující kód.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Tato třída implementuje pro kanál protějšek specifickou pro konkrétní platformu `com.<your_organization>.pushdemo/notificationaction` . To bylo definováno v části flutter aplikace v rámci **NotificationActionService. DART**. V tomto případě lze v obou směrech provést volání. Nezapomeňte nahradit **<your_organization>** s vlastní organizací bez ohledu na to, kde se používá.

1. V **AppDelegate. SWIFT**přidejte proměnné, do kterých se uloží odkaz na služby, které jste vytvořili dříve.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Přidejte funkci nazvanou **processNotificationActions** pro zpracování dat oznámení. Podmíněně aktivuje tuto akci nebo ji uloží pro pozdější použití, pokud se akce zpracovává během spuštění aplikace.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Přepište funkci **didRegisterForRemoteNotificationsWithDeviceToken** , která je nastavena jako hodnota **tokenu** pro **DeviceInstallationService**. Pak zavolejte **refreshRegistration** na **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Přepište funkci **didReceiveRemoteNotification** předáním argumentu **UserInfo** funkci **processNotificationActions** .

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Potlačením funkce **didFailToRegisterForRemoteNotificationsWithError** Zaprotokolujte chybu.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > To je velmi velký zástupný symbol. Pro produkční scénáře budete chtít implementovat správné protokolování a zpracování chyb.

1. V **didFinishLaunchingWithOptions**vytvořte instanci proměnných **deviceInstallationService**, **notificationRegistrationService**a **notificationActionService** .

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. Ve stejné funkci bezpodmínečně vyžádáte autorizaci a zaregistrujte se na vzdálená oznámení.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Pokud **launchOptions** obsahuje klíč **RemoteNotification** , zavolejte **processNotificationActions** na konci funkce **didFinishLaunchingWithOptions** . Předejte výsledný objekt **UserInfo** a použijte *hodnotu true* pro argument *launchAction* . Hodnota *true* označuje, že akce je zpracovávána během spuštění aplikace.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
