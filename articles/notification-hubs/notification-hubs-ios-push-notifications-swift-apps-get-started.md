---
title: Nabízená oznámení v aplikacích SWIFT pro iOS, které používají Azure Notification Hubs | Microsoft Docs
description: Přečtěte si, jak nabízet oznámení aplikacím SWIFT iOS, které používají Azure Notification Hubs.
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
ms.openlocfilehash: b830538f81d1696c34db3e4f66a07346c17bcdcc
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211953"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Kurz: Nabízená oznámení v aplikacích SWIFT pro iOS, které používají Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

V tomto kurzu použijete Azure Notification Hubs k odesílání oznámení do aplikace iOS založené na SWIFT pomocí [REST API](/rest/api/notificationhubs/). Také vytvoříte prázdnou aplikaci pro iOS, která přijímá nabízená oznámení pomocí [služby APNs (Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Tento kurz vás provede následujícími kroky:

> [!div class="checklist"]
> * Vygenerujte soubor žádosti o podepsání certifikátu.
> * Vyžádejte si aplikaci pro nabízená oznámení.
> * Vytvořte zřizovací profil pro aplikaci.
> * Vytvoříte centrum oznámení.
> * Nakonfigurujte centrum oznámení s informacemi o službě APN.
> * Připojte svoji aplikaci pro iOS k centru oznámení.
> * Otestujte řešení.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postup sledovat, potřebujete:

- Pokud nejste obeznámeni se službou, Projděte si [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md) .
- Další informace o [registracích a instalaci](notification-hubs-push-notification-registration-management.md).
- Aktivní [vývojářský účet Apple](https://developer.apple.com).
- Počítač Mac se systémem Xcode spolu s platným certifikátem pro vývojáře nainstalovaným v řetězci klíčů.
- Fyzické zařízení iPhone, se kterým můžete spustit a ladit, protože nemůžete testovat nabízená oznámení s simulátorem.
- Vaše fyzické zařízení pro iPhone registrované na [portálu Apple](https://developer.apple.com) a přidružené k vašemu certifikátu.
- [Předplatné Azure](https://portal.azure.com) , kde můžete vytvářet a spravovat prostředky.

I v případě, že nemáte žádné předchozí zkušenosti s vývojem pro iOS, měli byste být schopni postupovat podle kroků pro vytvoření tohoto příkladu první zásady. Je ale výhodné se seznámit s následujícími koncepty:

- Sestavování aplikací pro iOS pomocí Xcode a SWIFT
- Konfiguruje se [Centrum oznámení Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro iOS.
- [Portál pro vývojáře Apple](https://developer.apple.com) a [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Centrum oznámení se nakonfiguruje jenom na použití režimu ověřování **izolovaného prostoru (sandbox)** . Tento režim ověřování byste neměli používat pro produkční úlohy.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Připojení aplikace pro iOS k centru oznámení

V této části vytvoříte aplikaci pro iOS, která se připojí k centru oznámení.  

### <a name="create-an-ios-project"></a>Vytvoření projektu pro iOS

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu **Jediné zobrazení aplikace**.

1. Při nastavování možností pro nový projekt:

   1. Zadejte **název produktu** (PushDemo) a **identifikátor organizace** (`com.<organization>`), který jste použili při nastavování **identifikátoru sady prostředků** na portálu pro vývojáře Apple.

   1. Vyberte **tým** , pro který bylo nastaveno **ID aplikace** .

   1. Nastavte **jazyk** na **SWIFT**.

   1. Vyberte **Další**.

1. Vytvořte novou složku s názvem **SupportingFiles**.

1. Vytvořte nový soubor v seznamu s názvem **devsettings. plist** ve složce **SupportingFiles** . Nezapomeňte tuto složku přidat do souboru **gitignore** , aby se při práci s úložištěm Git netvrdila. V produkční aplikaci pravděpodobně budete tyto tajné klíče podmíněně nastavovat jako součást procesu automatizovaného sestavení. Tato nastavení se nevztahují v tomto návodu.

1. Aktualizujte **devsettings. plist** tak, aby zahrnovaly následující položky konfigurace pomocí vlastních hodnot z centra oznámení, které jste zřídili:

   | Klíč                            | type                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Řetězec                   | \<hubKey>                  |
   | notificationHubKeyName         | Řetězec                   | \<hubKeyName>              |
   | notificationHubName            | Řetězec                   | \<hubName>                 |
   | notificationHubNamespace       | Řetězec                   | \<hubNamespace>            |

   Požadované hodnoty můžete najít tak, že přejdete na prostředek centra oznámení v Azure Portal. Zejména hodnoty **notificationHubName** a **notificationHubNamespace** jsou v pravém horním rohu souhrnu **Essentials** na stránce **Přehled** .

   ![Shrnutí Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Hodnoty **notificationHubKeyName** a **notificationHubKey** můžete najít také tak, že přejdete na **zásady přístupu** a vyberete příslušné **zásady přístupu**, například `DefaultFullSharedAccessSignature`. Potom zkopírujte z **primárního připojovacího řetězce** hodnotu s `SharedAccessKeyName=` předponou pro `notificationHubKeyName` a hodnotu s `SharedAccessKey=` předponou pro `notificationHubKey`.

   Připojovací řetězec by měl být v následujícím formátu:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Chcete-li tuto možnost zjednodušit, zadejte `DefaultFullSharedAccessSignature` , abyste mohli použít token k odesílání oznámení. V praxi `DefaultListenSharedAccessSignature` by byla lepší volbou pro situace, kdy chcete dostávat jenom oznámení.

1. V části **Navigátor projektu**vyberte **název projektu** a pak vyberte kartu **Obecné** .

1. Najděte **identitu** a pak nastavte hodnotu **identifikátoru sady prostředků** tak, aby `com.<organization>.PushDemo`odpovídala hodnotě, která je použitá pro **ID aplikace** z předchozího kroku.

1. Vyhledejte **podepisování**a potom vyberte příslušný **tým** pro svůj **účet Apple Developer**. Hodnota **týmu** by měla odpovídat tomu, ve kterém jste vytvořili certifikáty a profily.

1. Xcode by měl automaticky stáhnout příslušnou hodnotu **zřizovacího profilu** na základě **identifikátoru sady prostředků**. Pokud nevidíte novou hodnotu **zřizovacího profilu** , zkuste obnovit profily pro **podpisovou identitu** tak, že vyberete zobrazení**účtu** >  **Xcode** > **Předvolby** >  **. Podrobnosti**. Vyberte **podpisovou identitu**a pak kliknutím na tlačítko **aktualizovat** v pravém dolním rohu Stáhněte profily.

1. Vyberte kartu **Možnosti** a zajistěte, aby byla povolená **nabízená oznámení** .

1. Otevřete soubor **AppDelegate. SWIFT** pro implementaci protokolu **UNUserNotificationCenterDelegate** a přidejte následující kód do horní části třídy:

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
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Tyto členy budete používat později. Konkrétně použijete **značky** a členy **genericTemplate** jako součást registrace. Další informace o značkách naleznete v tématu [značky pro registrace](notification-hubs-tags-segment-push-message.md) a [Registrace šablon](notification-hubs-templates-cross-platform-push-messages.md).

1. Do stejného souboru přidejte následující kód do funkce **didFinishLaunchingWithOptions** :

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

    Tento kód načte hodnoty nastavení z **devsettings. plist**, nastaví třídu **AppDelegate** jako delegáta **UNUserNotificationCenter** , žádá o autorizaci nabízených oznámení a potom volá **. registerForRemoteNotifications**.

    Pro zachování jednoduchého kódu podporuje *pouze iOS 10 a novější*. Podporu pro předchozí verze operačních systémů můžete přidat podmíněně pomocí příslušných rozhraní API a přístupů, jako byste to normálně provedete.

1. Do stejného souboru přidejte následující funkce:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Kód používá hodnoty **installationId** a **pushChannel** k registraci v centru oznámení. V tomto případě používáte **UIDevice. Current. identifierForVendor** k poskytnutí jedinečné hodnoty k identifikaci zařízení a pak formátování **deviceToken** pro poskytnutí požadované hodnoty **pushChannel** . Funkce **showAlert** existuje jednoduše k zobrazení textu zprávy pro demonstrační účely.

1. Stále v souboru **AppDelegate. SWIFT** přidejte funkce **willPresent** a **didReceive** do **UNUserNotificationCenterDelegate**. Tyto funkce zobrazí výstrahu, když jsou informováni o tom, že aplikace běží buď v popředí, nebo na pozadí.

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

1. Přidáním příkazů Print do dolní části funkce **didRegisterForRemoteNotificationsWithDeviceToken** ověřte, zda jsou přiřazovány hodnoty **installationId** a **pushChannel** .

1. Vytvořte si **modely**, **služby**a složky **nástrojů** pro základní komponenty, které do projektu přidáte později.

1. Ověřte, že se projekt sestaví a spouští na fyzickém zařízení. Nabízená oznámení nelze testovat pomocí simulátoru.

### <a name="create-models"></a>Vytváření modelů

V tomto kroku vytvoříte sadu modelů reprezentujících datové části [Notification Hubs REST API](/rest/api/notificationhubs/) a uložíte požadovaná data tokenu sdíleného přístupového podpisu (SAS).

1. Do složky **modely** přidejte nový soubor SWIFT s názvem **PushTemplate. SWIFT** . Tento model poskytuje strukturu reprezentující **tělo** jednotlivé šablony jako součást datové části **DeviceInstallation** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Do složky **modely** přidejte nový soubor SWIFT s názvem **DeviceInstallation. SWIFT** . Tento soubor definuje strukturu reprezentující datovou část pro vytvoření nebo aktualizaci **instalace zařízení**. Do souboru přidejte následující kód:

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

1. Do složky **modely** přidejte nový soubor SWIFT s názvem **TokenData. SWIFT** . Tento model se použije k uložení tokenu SAS spolu s jeho vypršením platnosti.

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

### <a name="generate-a-sas-token"></a>Vygenerovat token SAS

Notification Hubs používat stejnou infrastrukturu zabezpečení jako Azure Service Bus. Chcete-li volat REST API, budete muset [programově vygenerovat token SAS](/rest/api/eventhub/generate-sas-token) , který lze použít v **autorizační** hlavičce žádosti.  

Výsledný token bude v následujícím formátu:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Samotný proces zahrnuje stejný šest klíčových kroků:  

1. Výpočet doby platnosti ve formátu [epocha času v systému UNIX](https://en.wikipedia.org/wiki/Unix_time) , což znamená, že počet sekund uplynulý od půlnoci koordinovaného světového času, od 1. ledna 1970.
1. Formátování **ResourceUrl** , které představuje prostředek, ke kterému se pokoušíte získat přístup, je v procentech zakódovaných a malými písmeny. **ResourceUrl** má formu `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Připravuje se **StringToSign**, který je `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`formátovaný jako.
1. Výpočet a kódování Base64 – **Signatura** pomocí algoritmu hash HMAC-SHA256 hodnoty **StringToSign** . Hodnota hash se používá spolu s **klíčovou** částí **připojovacího řetězce** pro příslušné **autorizační pravidlo**.
1. Formátování kódovaného formátu **Base64 tak, aby bylo** zakódováno v procentech.
1. Vytvoření tokenu v očekávaném formátu pomocí hodnot **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**a **UrlEncodedResourceUrl** .

Podrobné informace o sdíleném přístupovém podpisu a o tom, jak se Azure Service Bus a Notification Hubs používají, najdete v [dokumentaci k Azure Service Bus](../service-bus-messaging/service-bus-sas.md) .

Pro účely tohoto příkladu SWIFT budete pomocí knihovny **CommonCrypto** Open Source společnosti Apple pomáhat s hashací signatury. V případě knihovny jazyka C není v rychlém přístupu k dispozici v poli SWIFT. Knihovnu je možné zpřístupnit pomocí přemostění hlavičky.

Postup přidání a konfigurace přemostění hlavičky:

1. V Xcode vyberte **soubor** > **hlavičkového**souboru**nového** > **souboru** > . Pojmenujte hlavičkový soubor **BridgingHeader. h**.

1. Upravte soubor tak, aby importoval **CommonHMAC. h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aktualizujte **nastavení sestavení** cíle tak, aby odkazovalo na hlavičku přemostění:

   1. Otevřete kartu **Nastavení** budovy a přejděte dolů k části **kompilátor** SWIFT.

   1. Zajistěte, aby byla možnost  **Hlavička kompatibility cíl-C**nastavená na **hodnotu Ano**.

   1. Zadejte cestu `'<ProjectName>/BridgingHeader.h'` k souboru do možnosti **hlavičkového** mostu cíl-C. Toto je cesta k souboru v naší hlavičce přemostění.

   Pokud tyto možnosti nemůžete najít, **Ujistěte se,** že máte vybrané zobrazení místo **základní** nebo **přizpůsobené**.

   K dispozici je spousta Open Source knihoven, které mohou využívat **CommonCrypto** a mnohem jednodušší. Diskuse takových knihoven je ale nad rámec tohoto článku.

1. Do složky **nástrojů** přidejte nový soubor SWIFT s názvem **TokenUtility. SWIFT** a přidejte následující kód:

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

   Tento nástroj zapouzdřuje logiku odpovědnou za generování tokenu SAS.

   Jak bylo uvedeno dříve, funkce **getSasToken** orchestruje kroky vysoké úrovně vyžadované pro přípravu tokenu. Funkce bude volána instalační službou dále v tomto kurzu.

   Další dvě funkce jsou volány funkcí **getSasToken** : **sha256HMac** pro výpočet podpisu a **urlEncodedString** pro kódování přidruženého řetězce adresy URL. Funkce **urlEncodedString** je povinná, protože není možné dosáhnout požadovaného výstupu pomocí integrované funkce **addingPercentEncoding** .

   [Sada Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) je skvělým příkladem, jak přistupovat k těmto operacím v cíli – C. Další informace o Azure Service Bus tokeny SAS najdete v dokumentaci k [Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Ověření tokenu SAS

Před implementací instalační služby v klientovi ověřte, že je v naší aplikaci správně vygenerován token SAS pomocí vašeho vlastního nástroje HTTP. Pro účely tohoto kurzu bude náš nástroj na výběr **dodatečně**.

Použijte vhodně umístěný příkaz Print nebo zarážku, abyste si poznamenali **installationId** a hodnoty **tokenu** , které aplikace generuje.

Postupujte podle těchto kroků a zavolejte rozhraní API **instalace** :

1. V **příspěvku**otevřete novou kartu.

1. Nastavte požadavek na **získání** a zadání následující adresy:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Hlavičky požadavku nakonfigurujte takto:

   | Klíč           | Value            |
   | ------------- | ---------------- |
   | Typ obsahu  | application/json |
   | Authorization | \<sasToken >       |
   | x-ms-version  | 2015-01          |

1. Vyberte tlačítko **Code (kód** ), které se zobrazí v pravém horním rohu pod tlačítkem **Uložit** . Požadavek by měl vypadat podobně jako v následujícím příkladu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Vyberte **odeslat** tlačítko.

V tuto chvíli neexistují žádné registrace pro zadaný **installationId** . Ověřování by mělo mít za následek odpověď "404 nebylo nalezeno", nikoli odpověď "401 neautorizovaného". Tento výsledek by měl potvrdit, že se token SAS přijal.

### <a name="implement-the-installation-service-class"></a>Implementace třídy instalační služby

V dalším kroku implementujete naši základní obálku pro [REST API instalací](/rest/api/notificationhubs/create-overwrite-installation).  

Do složky **Services** přidejte nový soubor SWIFT s názvem **NotificationRegistrationService. SWIFT** a potom do tohoto souboru přidejte následující kód:

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
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
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

Podrobnosti o požadavcích jsou k dispozici jako součást inicializace. Značky a šablony jsou volitelně předány do funkce **Register** , aby bylo možné vytvořit součást datové části JSON pro **instalaci zařízení** .  

Funkce **Register** volá další soukromé funkce pro přípravu žádosti. Po přijetí odpovědi se zavolá dokončování a indikuje, jestli se registrace úspěšně dokončila.  

Koncový bod žádosti je vytvořen funkcí **getbaseaddress –** . Konstrukce používá *obor názvů* parametrů centra oznámení a *název* , které byly zadány během inicializace.  

Funkce **getSasToken** zkontroluje, jestli je aktuálně uložený token platný. Pokud token není platný, funkce volá **TokenUtility** k vygenerování nového tokenu a pak ho uloží před vrácením hodnoty.

Nakonec **encodeToJson** převede příslušné objekty modelu na JSON, aby je bylo možno použít jako součást textu žádosti.

### <a name="invoke-the-notification-hubs-rest-api"></a>Vyvolat Notification Hubs REST API

Poslední krok aktualizuje **AppDelegate** na použití **NotificationRegistrationService** k registraci v našem **NotificationHub**.

1. Otevřete **AppDelegate. SWIFT** a přidejte proměnnou na úrovni třídy pro uložení odkazu na **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. Ve stejném souboru aktualizujte funkci **didRegisterForRemoteNotificationsWithDeviceToken** pro inicializaci **NotificationRegistrationService** s parametry požadavků a pak zavolejte funkci **Register** .

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

    Aby se zachovala jednoduchá, použijete několik příkazů Print k aktualizaci okna výstup s výsledkem operace **registrace** .

1. Nyní Sestavte a spusťte aplikaci na fyzickém zařízení. V okně výstup by se měla zobrazit položka registrováno.

## <a name="test-the-solution"></a>Testování řešení

Naše aplikace v této fázi je zaregistrovaná ve službě **NotificationHub** a může dostávat nabízená oznámení. V Xcode ukončete ladicí program a zavřete aplikaci, pokud je aktuálně spuštěná. Pak ověřte, že podrobnosti o **instalaci zařízení** jsou očekávané, a že naše aplikace teď může dostávat nabízená oznámení.  

### <a name="verify-the-device-installation"></a>Ověření instalace zařízení

Nyní můžete vytvořit stejný požadavek, jako jste předtím používali **metodu post** pro [ověření tokenu SAS](#verify-the-sas-token). Za předpokladu, že platnost tokenu SAS nevypršela, měla by nyní odpověď obsahovat podrobnosti o instalaci, které jste zadali, například šablony a značky.

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

### <a name="send-a-test-notification-azure-portal"></a>Odeslat zkušební oznámení (Azure Portal)

Nejrychlejší způsob, jak otestovat, že teď můžete dostávat oznámení, je přejít do centra oznámení v Azure Portal:

1. V Azure Portal přejděte na kartu **Přehled** v centru oznámení.

1. V levém horním rohu okna portálu vyberte **Odeslat test**, který je nad souhrnem **základních** údajů:

    ![Tlačítko pro odeslání souhrnného testu Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. V seznamu **platformy** vyberte možnost **vlastní šablona** .

1. Jako **výraz odeslat do značky**zadejte **12345** . Tuto značku jste předtím zadali v naší instalaci.

1. Volitelně můžete upravit **zprávu** v datové části JSON:

    ![Odeslat Notification Hubs testu](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Vyberte **Poslat**. Portál by měl uvádět, zda bylo oznámení úspěšně odesláno do zařízení:

    ![Výsledky odeslání Notification Hubs testu](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Za předpokladu, že aplikace není spuštěná v popředí, by se v **centru oznámení** na vašem zařízení mělo zobrazit také oznámení. Klepnutím na oznámení by se měla aplikace otevřít a zobrazit výstrahu.

    ![Příklad přijetí oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Odeslat zkušební oznámení (poštovní operátor)

Oznámení můžete odesílat prostřednictvím [REST API](/rest/api/notificationhubs/) pomocí **post**, což může být pohodlnější způsob testování.

1. Otevře novou kartu v **příspěvku**.

1. Nastavte požadavek na **post**a zadejte následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Hlavičky požadavku nakonfigurujte takto:

   | Klíč                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Typ obsahu                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken >                     |
   | ServiceBusNotification-Format  | Šablony                       |
   | Tags                           | "12345"                        |

1. Nakonfigurujte **text** žádosti na použití **surového-JSON (Application. JSON)** s následující datovou částí JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Vyberte tlačítko **kód** , které je pod tlačítkem **Uložit** v pravém horním rohu okna. Požadavek by měl vypadat podobně jako v následujícím příkladu:

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

1. Vyberte **odeslat** tlačítko.

Měli byste získat stavový kód úspěchu a obdržet oznámení v klientském zařízení.

## <a name="next-steps"></a>Další kroky
Teď máte základní aplikaci iOS SWIFT připojenou k centru oznámení prostřednictvím [REST API](/rest/api/notificationhubs/) a můžete odesílat a přijímat oznámení. Další informace najdete v následujících článcích:

- [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Rozhraní REST API pro Notification Hubs](/rest/api/notificationhubs/)
- [Sada SDK Notification Hubs pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK na GitHubu](https://github.com/Azure/azure-notificationhubs)
- [Registrovat do back-endu aplikace](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Práce s značkami](notification-hubs-tags-segment-push-message.md) 
- [Práce s vlastními šablonami](notification-hubs-templates-cross-platform-push-messages.md)
- [Řízení přístupu Service Bus pomocí sdílených přístupových podpisů](../service-bus-messaging/service-bus-sas.md)
- [Generování tokenů SAS prostřednictvím kódu programu](/rest/api/eventhub/generate-sas-token)
- [Apple Security: běžné šifrování](https://developer.apple.com/security/)
- [Čas v epocha systému UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
