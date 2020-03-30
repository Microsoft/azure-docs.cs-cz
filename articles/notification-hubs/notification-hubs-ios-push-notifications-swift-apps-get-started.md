---
title: Odesílání nabízených oznámení do aplikací Swift pro iOS, které používají Azure Notification Hubs | Dokumenty společnosti Microsoft
description: Přečtěte si, jak odesílat oznámení do aplikací Swift pro iOS, které používají Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336636"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Kurz: Odesílání nabízených oznámení do aplikací Swift pro iOS pomocí rozhraní REST API centra oznámení

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

V tomto kurzu pomocí Centra oznámení Azure nabízená oznámení na aplikaci iOS založené swift pomocí [rozhraní REST API](/rest/api/notificationhubs/). Můžete také vytvořit prázdnou aplikaci pro iOS, která přijímá nabízená oznámení pomocí [služby Nabízených oznámení Apple (APNs).](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)

Tento kurz vás provede následujícími kroky:

> [!div class="checklist"]
> * Vygenerujte soubor žádosti o podpis certifikátu.
> * Požádejte svou aplikaci o nabízená oznámení.
> * Vytvořte profil zřizování pro aplikaci.
> * Vytvoříte centrum oznámení.
> * Nakonfigurujte centrum oznámení s informacemi apns.
> * Připojte aplikaci pro iOS k centru oznámení.
> * Otestujte roztok.

Kompletní kód pro tento kurz lze nalézt [na GitHubu](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Požadavky

Chcete-li sledovat, musíte:

- Chcete-li projít [přehled Centra oznámení Azure,](notification-hubs-push-notification-overview.md) pokud nejste obeznámeni se službou.
- Chcete-li se dozvědět o [registracích a instalaci](notification-hubs-push-notification-registration-management.md).
- Aktivní [vývojářský účet Apple](https://developer.apple.com).
- Mac se systémem Xcode spolu s platným vývojářským certifikátem nainstalovaným do řetězce klíčů.
- Fyzické zařízení iPhone, se kterým můžete spouštět a ladit, protože nemůžete testovat nabízená oznámení se simulátorem.
- Vaše fyzické zařízení iPhone registrované na [portálu Apple](https://developer.apple.com) a přidružené k vašemu certifikátu.
- [Předplatné Azure,](https://portal.azure.com) kde můžete vytvářet a spravovat prostředky.

I v případě, že nemáte žádné předchozí zkušenosti s vývojem iOS, měli byste být schopni postupovat podle kroků pro vytvoření tohoto příkladu první principy. Budete však mít prospěch ze znalosti následujících konceptů:

- Vytváření aplikací pro iOS s Xcode a Swiftem.
- Konfigurace [centra oznámení Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro iOS.
- [Portál pro vývojáře Apple](https://developer.apple.com) a portál [Azure](https://portal.azure.com).

> [!NOTE]
> Centrum oznámení bude nakonfigurováno tak, aby používalo pouze režim ověřování **izolovaného prostoru.** Tento režim ověřování byste neměli používat pro produkční úlohy.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Připojení aplikace pro iOS k centru oznámení

V této části vytvoříte aplikaci pro iOS, která se připojí k centru oznámení.  

### <a name="create-an-ios-project"></a>Vytvoření projektu iOS

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu **Jediné zobrazení aplikace**.

1. Při nastavování možností pro nový projekt:

   1. Zadejte **název produktu** (PushDemo) a identifikátor **organizace** (`com.<organization>`), který jste použili při nastavení **identifikátoru sady na** portálu pro vývojáře Apple.

   1. Vyberte **tým,** pro který bylo **ID aplikace** nastaveno.

   1. Nastavte **jazyk** na **Swift**.

   1. Vyberte **další**.

1. Vytvořte novou složku s názvem **SupportingFiles**.

1. Vytvořte nový soubor p-list s názvem **devsettings.plist** ve složce **SupportingFiles.** Nezapomeňte přidat tuto složku do souboru **gitignore,** aby nebyla potvrzena při práci s úložištěm git. V produkční aplikaci by pravděpodobně podmíněně nastavení těchto tajných kódů jako součást automatizovaného procesu sestavení. Tato nastavení nejsou zahrnuty v tomto návodu.

1. Aktualizujte **soubor devsettings.plist** tak, aby zahrnoval následující položky konfigurace pomocí vlastních hodnot z centra oznámení, které jste zříditi:

   | Klíč                            | Typ                     | Hodnota                     |
   |--------------------------------| -------------------------| --------------------------|
   | oznámeníHubKey             | Řetězec                   | \<> hubkey                  |
   | oznámeníHubKeyName         | Řetězec                   | \<> název_hubKeyName              |
   | oznámeníHubName            | Řetězec                   | \<hubName>                 |
   | oznámeníHubNamespace       | Řetězec                   | \<> hubNamespace            |

   Požadované hodnoty najdete tak, že přejdete k prostředku centra oznámení na webu Azure Portal. Zejména hodnoty **notificationHubName** a **notificationHubNamespace** jsou v pravém horním rohu souhrnu **Essentials** na stránce **Přehled.**

   ![Shrnutí Centra pro oznámení Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Můžete také najít **hodnoty notificationHubKeyName** a **notificationHubKey** tak, že přejdete na **zásady přístupu** a vyberete příslušné **zásady přístupu**, například `DefaultFullSharedAccessSignature`. Poté zkopírujte z **primárního připojovacího řetězce** hodnotu předponou `SharedAccessKeyName=` pro `notificationHubKeyName` a hodnotu předponou `SharedAccessKey=` pro . `notificationHubKey`

   Připojovací řetězec by měl být v následujícím formátu:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Chcete-li, aby `DefaultFullSharedAccessSignature` to bylo jednoduché, zadejte, abyste mohli použít token k odesílání oznámení. V praxi `DefaultListenSharedAccessSignature` by bylo lepší volbou pro situace, kdy chcete dostávat pouze oznámení.

1. V **části Project Navigator**vyberte **název projektu** a pak vyberte kartu **Obecné.**

1. Najděte **identitu** a nastavte hodnotu **identifikátoru svazku** tak, aby odpovídala `com.<organization>.PushDemo`, což je hodnota použitá pro **ID aplikace** z předchozího kroku.

1. Najděte **možnosti & podepisování**a vyberte příslušný **tým** pro svůj **vývojářský účet Apple**. Hodnota **Team** by se měla shodovat s hodnotou, pod kterou jste vytvořili certifikáty a profily.

1. Xcode by měl automaticky stáhnout příslušnou hodnotu **profilu zřizování** na základě **identifikátoru sady**. Pokud nevidíte novou hodnotu **profilu zřizování,** zkuste aktualizovat profily **pro podpisovou identitu** tak, že vyberete**účet** **předvoleb** >  **Xcode** > a pak vyberte tlačítko **Stáhnout ruční profily** a stáhněte si profily.

1. Stále na kartě **Možnosti podepisování &** klikněte na tlačítko **+ Schopnosti** a dvakrát klepněte na **nabízená oznámení** ze seznamu, abyste zajistili, že jsou **povolena nabízená oznámení.**

1. Otevřete soubor **AppDelegate.swift** k implementaci protokolu **UNUserNotificationCenterDelegate** a přidejte následující kód na začátek třídy:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    Tyto členy použijete později. Konkrétně budete používat **tagy** člen jako součást registrace pomocí **vlastní šablony**. Další informace o značkách naleznete v [tématu Značky pro registrace](notification-hubs-tags-segment-push-message.md) a [registrace šablon](notification-hubs-templates-cross-platform-push-messages.md).

1. Ve stejném souboru přidejte do funkce **didFinishLaunchingWithOptions** následující kód:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Tento kód načte hodnoty nastavení z **souboru devsettings.plist**, nastaví třídu **AppDelegate** jako **delegáta UNUserNotificationCenter,** požádá o autorizaci nabízených oznámení a pak zavolá **registerForRemoteNotifications**.

    Aby to bylo jednoduché, kód podporuje *pouze iOS 10 a novější*. Můžete přidat podporu pro předchozí verze operačního systému podmíněně pomocí příslušných api a přístupy jako obvykle.

1. Do stejného souboru přidejte následující funkce:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Kód používá **installationId** a **pushChannel** hodnoty zaregistrovat s centrem oznámení. V tomto případě používáte **UIDevice.current.identifierForVendor** poskytnout jedinečnou hodnotu k identifikaci zařízení a potom formátování **deviceToken** poskytnout požadovanou hodnotu **pushChannel.** Funkce **showAlert** existuje jednoduše pro zobrazení textu zprávy pro demonstrační účely.

1. Stále v souboru **AppDelegate.swift,** přidejte **funkce willPresent** a **didReceive** do **unUserNotificationCenterDelegate**. Tyto funkce zobrazí výstrahu, když jsou upozorněni, že aplikace běží v popředí nebo na pozadí.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Přidejte příkazy tisku na konec funkce **didRegisterForRemoteNotificationsWithDeviceToken,** abyste **ověřili,** že jsou přiřazeny hodnoty id a **pushChannel.**

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Vytvořte složky **Modely**, **Služby**a **Nástroje** pro základní součásti, které budete přidávat do projektu později.

1. Zkontrolujte, zda projekt sestaví a běží na fyzickézařízení. Nabízená oznámení nelze testovat pomocí simulátoru.

### <a name="create-models"></a>Vytváření modelů

V tomto kroku vytvoříte sadu modelů, které představují datové části [rozhraní REST API pro oznámení](/rest/api/notificationhubs/) a ukládají data tokenu požadovaného sdíleného přístupového podpisu (SAS).

1. Přidejte do složky **Modely** nový soubor Swift s názvem **PushTemplate.swift.** Tento model poskytuje strukturu představující **tělo** jednotlivé šablony jako součást datové části **DeviceInstallation.**

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Přidejte do složky **Modely** nový soubor Swift s názvem **DeviceInstallation.swift.** Tento soubor definuje strukturu představující datovou část pro vytvoření nebo **aktualizaci instalace zařízení**. Do souboru přidejte následující kód:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Přidejte do složky **Models** nový soubor Swift s názvem **TokenData.swift.** Tento model se použije k uložení tokenu SAS spolu s jeho vypršením platnosti.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Generovat token SAS

Centra oznámení používají stejnou infrastrukturu zabezpečení jako Azure Service Bus. Chcete-li volat rozhraní REST API, budete muset [programově generovat token SAS,](/rest/api/eventhub/generate-sas-token) který lze použít v záhlaví **autorizace** požadavku.  

Výsledný token bude v následujícím formátu:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Samotný proces zahrnuje stejných šest klíčových kroků:  

1. Výpočet vypršení platnosti ve formátu [času UNIX Epoch,](https://en.wikipedia.org/wiki/Unix_time) což znamená počet sekund, které uplynuly od půlnoci Koordinovaný světový čas, leden 1, 1970.
1. Formátování **ResourceUrl,** který představuje prostředek, ke kterému se pokoušíte získat přístup, takže je kódovaný procentuální a malá písmena. **Adresa ResourceUrl** má `'https://<namespace>.servicebus.windows.net/<hubName>'`formulář .
1. Příprava **StringToSign**, který je `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`formátován jako .
1. Výpočetní a Base64 kódování **podpis** pomocí HMAC-SHA256 hash **stringToSign** hodnoty. Hodnota hash se používá s **klíčovou** částí **připojovacího řetězce** pro příslušné **autorizační pravidlo**.
1. Formátování kódu Base64 **Signature** tak, aby byl zakódován procentuální.
1. Vytvoření tokenu v očekávaném formátu pomocí hodnot **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**a **UrlEncodedResourceUrl.**

Podrobnější přehled sdíleného přístupového podpisu a způsobu, jakým ho používají Azure Service Bus a Notification Hubs, najdete v dokumentaci k [Azure Service Bus.](../service-bus-messaging/service-bus-sas.md)

Pro účely tohoto příkladu Swift, budete používat Apple open-source **CommonCrypto** knihovny pomoci s hash podpisu. Vzhledem k tomu, že se jedná o knihovnu C, není přístupná v Swiftu po vybalení z krabice. Knihovnu můžete zpřístupnit pomocí přemosťující hlavičky.

Přidání a konfigurace přemosťovacího záhlaví:

1. V xcode vyberte **Soubor** > **Složky** > s**hlavičkou souboru** > **Header File**. Pojmenujte soubor záhlaví **BridgingHeader.h**.

1. Upravit soubor pro import **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aktualizujte nastavení **sestavení** cíle tak, aby odkazovalo na přemosťující záhlaví:

   1. Klepněte na projekt **PushDemo** a přejděte dolů do sekce **Swift Compiler.**

   1. Ujistěte se, že je možnost **Instalovat hlavičku kompatibility cíle C** nastavena na **ano**.

   1. Zadejte cestu `'<ProjectName>/BridgingHeader.h'` k souboru do možnosti **Přemostění záhlaví Objective-C.** Toto je cesta k našemu přemostění záhlaví.

   Pokud tyto možnosti nemůžete najít, ujistěte se, že máte vybrané zobrazení **Vše,** nikoli **Základní** nebo **Přizpůsobené**.

   Existuje mnoho knihoven obálky s otevřeným zdrojovým kódem třetích stran, které mohou usnadnit použití **CommonCrypto.** Diskuse o těchto knihoven je však nad rámec tohoto článku.

1. Přidejte nový soubor Swift s názvem **TokenUtility.swift** do složky **Nástroje** a přidejte následující kód:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Tento nástroj zapouzdřuje logiku zodpovědnou za generování tokenu SAS.

   Jak je uvedeno dříve, funkce **getSasToken** orchestruje kroky vysoké úrovně potřebné k přípravě tokenu. Funkce bude volána instalační službou dále v tomto kurzu.

   Další dvě funkce jsou volány funkcí **getSasToken:** **sha256HMac** pro výpočet podpisu a **urlEncodedString** pro kódování přidruženého řetězce URL. Funkce **urlEncodedString** je vyžadována, protože není možné dosáhnout požadovaného výstupu pomocí vestavěné funkce **addingPercentEncoding.**

   [Sada Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) je vynikajícím příkladem toho, jak přistupovat k těmto operacím v objective-C. Další informace o tokenech Azure Service Bus SAS najdete v [dokumentaci k Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

1. V **souboru AppDelegate.swift**přidejte do funkce *didRegisterForRemoteNotificationsWithDeviceToken* následující kód, který ověří, že **tokenToken.getSasToken** generuje platný token.
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Nezapomeňte nahradit zástupné hodnoty v **řetězci baseAddress** vlastním řetězcem

### <a name="verify-the-sas-token"></a>Ověření tokenu SAS

Před implementací instalační služby v klientovi zkontrolujte, zda naše aplikace správně generuje token SAS pomocí vašeho http nástroje podle výběru. Pro účely tohoto výukového programu bude naším nástrojem výběru **Pošťák**.

Poznamenejte si **hodnoty id instalace** a **tokenu** generované aplikací.

Chcete-li volat rozhraní API **pro instalace,** postupujte takto:

1. V **pošťáku**otevřete novou kartu.

1. Nastavte požadavek na **GET** a zadejte následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Nakonfigurujte záhlaví požadavků následujícím způsobem:

   | Klíč           | Hodnota            |
   | ------------- | ---------------- |
   | Typ obsahu  | application/json |
   | Autorizace | \<sasToken>       |
   | x-ms-verze  | 2015-01          |

1. Vyberte tlačítko **Kód,** které se zobrazí v pravém horním pod tlačítkem **Uložit.** Požadavek by měl vypadat podobně jako v následujícím příkladu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Vyberte tlačítko **Odeslat.**

Pro zadané **id instalace** v tomto okamžiku neexistuje žádná registrace. Výsledkem ověření by měla být odpověď "404 nebyla nalezena" spíše než odpověď "401 Neautorizovaná". Tento výsledek by měl potvrdit, že token SAS byl přijat.

### <a name="implement-the-installation-service-class"></a>Implementace třídy instalačního servisu

Dále budete implementovat naše základní obálku kolem [instalace REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Přidejte nový soubor Swift s názvem **NotificationRegistrationService.swift** do složky **Služby** a přidejte do tohoto souboru následující kód:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Požadované podrobnosti jsou uvedeny jako součást inicializace. Značky a šablony jsou volitelně předány do funkce **registru,** aby byly součástí datové části **JSON pro instalaci zařízení.**  

Funkce **register** volá další soukromé funkce pro přípravu požadavku. Po přijetí odpovědi je voláno dokončení a označuje, zda byla registrace úspěšná.  

Koncový bod požadavku je vytvořen funkcí **getBaseAddress.** Konstrukce používá parametry centra oznámení *obor názvů* a *název,* které byly poskytnuty během inicializace.  

Funkce **getSasToken** zkontroluje, zda je aktuálně uložený token platný. Pokud token není platný, funkce volá **TokenUtility** generovat nový token a pak ukládá před vrácením hodnoty.

Nakonec **encodeToJson** převede příslušné objekty modelu do JSON pro použití jako součást těla požadavku.

### <a name="invoke-the-notification-hubs-rest-api"></a>Vyvolání rozhraní REST ROZHRANÍ REST CENTER o znacích

Posledním krokem je aktualizace **AppDelegate** použít **NotificationRegistrationService** zaregistrovat s naší **NotificationHub**.

1. Otevřete **soubor AppDelegate.swift** a přidejte proměnné na úrovni třídy pro uložení odkazu na **noficiationRegistrationService** a obecnou **pushtemplate**:

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. Ve stejném souboru aktualizujte funkci **didRegisterForRemoteNotificationsWithDeviceToken,** abyste inicializovali **službu NotificationRegistrationService** s parametry požadované a potom volejte funkci **registru.**

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Chcete-li zachovat jednoduché, budete používat několik tiskových příkazů k aktualizaci výstupního okna s výsledkem operace **registru.**

1. Teď sestavte a spusťte aplikaci na fyzickém zařízení. Ve výstupním okně byste měli vidět "Registrováno".

## <a name="test-the-solution"></a>Otestujte roztok

Naše aplikace v této fázi je registrována u **NotificationHub** a může přijímat nabízená oznámení. V Xcode zastavte ladicí program a zavřete aplikaci, pokud je aktuálně spuštěná. Dále zkontrolujte, zda jsou podrobnosti **o instalaci zařízení** podle očekávání a že naše aplikace teď může přijímat nabízená oznámení.  

### <a name="verify-the-device-installation"></a>Ověření instalace zařízení

Nyní můžete provést stejný požadavek jako dříve pomocí **Postman** pro [ověření tokenu SAS](#verify-the-sas-token). Za předpokladu, že platnost tokenu SAS nevypršela, odpověď by nyní měla obsahovat podrobnosti o instalaci, které jste zadali, jako jsou šablony a značky.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

Pokud platnost předchozího **tokenu SAS** vypršela, můžete přidat **zarážku** na **řádek 24** třídy **TokenUtility,** abyste získali nový **token SAS** a **aktualizovali** hlavičku Autorizace s tou novou hodnotou.

### <a name="send-a-test-notification-azure-portal"></a>Odeslání testovacího oznámení (portál Azure)

Nejrychlejší způsob, jak otestovat, že teď můžete dostávat oznámení je přejít do centra oznámení na webu Azure Portal:

1. Na webu Azure Portal přejděte na kartu **Přehled** v centru oznámení.

1. Vyberte **možnost Testovat odeslání**, která je nad souhrnem **Essentials** v levém horním rohu okna portálu:

    ![Tlačítko odeslat souhrnný test Centra oznámení Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Ze seznamu Platformy zvolte **Vlastní** **šablona.**

1. Zadejte **hodnotu 12345** pro **výraz Odeslat značku**. Tuto značku jste již dříve zadali v naší instalaci.

1. Volitelně upravit **zprávu** v datové části JSON:

    ![Odeslání testu center oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Vyberte **Poslat**. Portál by měl uvádět, zda bylo oznámení úspěšně odesláno do zařízení:

    ![Výsledky odesílání testů center oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Za předpokladu, že aplikace není spuštěná v popředí, měli byste se na vašem zařízení zobrazit také oznámení v **Oznamovacím centru.** Klepnutím na oznámení otevřete aplikaci a zobrazíte upozornění.

    ![Příklad přijatého oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Odeslat oznámení o testu (Mail Carrier)

Můžete posílat oznámení prostřednictvím [rozhraní REST API](/rest/api/notificationhubs/) pomocí **Postman**, což může být pohodlnější způsob testování.

1. Otevřete novou kartu v **Pošťákovi**.

1. Nastavte požadavek na **POST**a zadejte následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Nakonfigurujte záhlaví požadavků následujícím způsobem:

   | Klíč                            | Hodnota                          |
   | ------------------------------ | ------------------------------ |
   | Typ obsahu                   | aplikace/json;charset=utf-8 |
   | Autorizace                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | šablona                       |
   | Značky                           | "12345"                        |

1. Nakonfigurujte **tělo** požadavku tak, aby **používalo RAW - JSON (application.json)** s následující datovou částí JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Vyberte tlačítko **Kód,** které je pod tlačítkem **Uložit** v pravém horním okně. Požadavek by měl vypadat podobně jako v následujícím příkladu:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Vyberte tlačítko **Odeslat.**

Měli byste získat **201 Vytvořený** kód stavu úspěchu a přijímat oznámení na klientském zařízení..

## <a name="next-steps"></a>Další kroky
Teď máte základní aplikaci pro iOS Swift připojenou k centru oznámení prostřednictvím [rozhraní REST API](/rest/api/notificationhubs/) a můžete odesílat a přijímat oznámení. Další informace najdete v těchto článcích:

- [Přehled center Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Rest API centra oznámení](/rest/api/notificationhubs/)
- [Sada SDK centra oznámení pro operace back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Centra oznámení SDK na GitHubu](https://github.com/Azure/azure-notificationhubs)
- [Registrace s back-endem aplikace](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Správa registrace](notification-hubs-push-notification-registration-management.md)
- [Práce se značkami](notification-hubs-tags-segment-push-message.md) 
- [Práce s vlastními šablonami](notification-hubs-templates-cross-platform-push-messages.md)
- [Řízení přístupu služby Service Bus se sdílenými přístupovými podpisy](../service-bus-messaging/service-bus-sas.md)
- [Programově generovat tokeny SAS](/rest/api/eventhub/generate-sas-token)
- [Zabezpečení Apple: běžné kryptografické](https://developer.apple.com/security/)
- [Čas epochy unixového systému](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
