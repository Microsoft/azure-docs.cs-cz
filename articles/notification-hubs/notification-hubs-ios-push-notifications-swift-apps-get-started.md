---
title: Nabízená oznámení do aplikace pro systém iOS Swift používat Azure Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak nabízená oznámení do aplikace pro systém iOS Swift používat Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: c35044918876b2c7710e26f6b868bc1096c2f538
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340390"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Kurz: Odesílání nabízených oznámení do aplikace pro systém iOS Swift pomocí rozhraní REST API Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

V tomto kurzu jste pomocí Azure Notification Hubs k odesílání nabízených oznámení do aplikace iOS založené na Swift [rozhraní REST API](/rest/api/notificationhubs/). Vytvoříte prázdnou aplikaci pro iOS, která přijímá nabízená oznámení pomocí [služby Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Tento kurz vás provede následující kroky:

> [!div class="checklist"]
> * Generovat soubor žádosti o podepsání certifikátu.
> * Požádat o vaši aplikaci pro nabízená oznámení.
> * Vytvoření zřizovacího profilu aplikace.
> * Vytvoříte centrum oznámení.
> * Konfigurace centra oznámení s informacemi služby APN.
> * Připojte aplikaci iOS k centru oznámení.
> * Otestování řešení.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat s námi, budete potřebovat:

- Kvůli tomu provádět [přehled Azure Notification Hubs](notification-hubs-push-notification-overview.md) Pokud nejste obeznámeni s službu.
- Další informace o [registrace a instalace](notification-hubs-push-notification-registration-management.md).
- Aktivní [vývojářského účtu Apple](https://developer.apple.com).
- Počítač Mac s Xcode spolu s platnou vývojářskou certifikát nainstalován do své klíčenky.
- Zařízení iPhone fyzické můžete spustit a ladit pomocí, jak pomocí simulátoru nelze otestovat nabízená oznámení.
- IPhone fyzické zařízení zaregistrované v [Apple Portal](https://developer.apple.com) a přidružené k certifikátu.
- [Předplatného Azure](https://portal.azure.com) ve kterém můžete vytvářet a spravovat prostředky.

I v případě, že nemáte žádné předchozí zkušenosti s vývojem pro iOS, byste měli moct projít kroky pro vytvoření v tomto příkladu první zásady. Budete ale využívat znalost následujících konceptů:

- Vytváření aplikací pro iOS s Xcode a Swift.
- Konfigurace [centra oznámení Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro iOS.
- [Portálu Apple Developer](https://developer.apple.com) a [webu Azure portal](https://portal.azure.com).

> [!NOTE]
> Centrum oznámení se byly konfigurovány k použití **izolovaného prostoru** pouze v režimu ověřování. Tento režim ověřování byste neměli používat pro produkční úlohy.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Připojte aplikaci iOS k centru oznámení.

V této části sestavíte aplikaci pro iOS, který se připojí k centru oznámení.  

### <a name="create-an-ios-project"></a>Vytvoření projektu pro iOS

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu **Jediné zobrazení aplikace**.

1. Když nastavujete možnosti pro nový projekt:

   1. Zadejte **název produktu** (PushDemo) a **identifikátor organizace** (`com.<organization>`), který jste použili při nastavení **identifikátor sady prostředků** na portálu pro vývojáře Apple.

   1. Zvolte **týmu** , který **ID aplikace** byla nastavena pro.

   1. Nastavte **jazyk** k **Swift**.

   1. Vyberte **Další**.

1. Vytvořit novou složku s názvem **SupportingFiles**.

1. Vytvořte nový soubor p-list s názvem **devsettings.plist** v **SupportingFiles** složky. Nezapomeňte si přidat tuto složku do vaší **gitignore** souboru není potvrzená při práci s úložištěm git. V produkční aplikace bude by pravděpodobně být podmíněně nastavení těchto tajných kódů jako součást automatizovaného procesu sestavení. Tato nastavení nejsou pokryté v tomto názorném postupu.

1. Aktualizace **devsettings.plist** pomocí vlastní hodnoty z centra oznámení, kterou jste zřídili zahrnout následující položky konfigurace:

   | Klíč                            | Type                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   Požadované hodnoty najdete tak, že přejdete na prostředek centra oznámení na webu Azure Portal. Konkrétně se **notificationHubName** a **notificationHubNamespace** hodnoty jsou v pravém horním rohu **Essentials** souhrn v rámci **Přehled** stránky.

   ![Notification Hubs Essentials souhrn](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Můžete také vyhledat **notificationHubKeyName** a **notificationHubKey** hodnoty tak, že přejdete do **zásady přístupu** a výběrem příslušné  **Zásady přístupu**, jako například `DefaultFullSharedAccessSignature`. Potom zkopírujte z **primární připojovací řetězec** předponu hodnotu `SharedAccessKeyName=` pro `notificationHubKeyName` a hodnota předponu `SharedAccessKey=` pro `notificationHubKey`.

   Připojovací řetězec by měl být v následujícím formátu:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Chcete-li zachovat jednoduché, zadejte `DefaultFullSharedAccessSignature` tak token, který můžete použít k odesílání oznámení. V praxi `DefaultListenSharedAccessSignature` by bylo vhodnější pro situace, ve kterém chcete přijímat oznámení.

1. V části **navigátoru projektů**, vyberte **název projektu** a pak vyberte **Obecné** kartu.

1. Najít **Identity** a pak nastavte **identifikátor sady prostředků** hodnotu tak, aby odpovídalo `com.<organization>.PushDemo`, hodnota sloužící pro **ID aplikace** z předchozího kroku.

1. Najít **podepisování**a pak vyberte příslušné **týmu** pro vaše **vývojářského účtu Apple**. **Týmu** hodnota by měla odpovídat ten, ve kterém jste vytvořili, certifikátů a profilů.

1. Xcode by měl automaticky stahují odpovídající **zřizovací profil** na základě hodnoty **identifikátor sady prostředků**. Pokud nevidíte nový **zřizovací profil** hodnoty, pokuste se aktualizovat profily pro **Podpisová identita** tak, že vyberete **Xcode**  >  **Předvolby** > **účet** > **podrobnosti**. Vyberte **Podpisová identita**a pak vyberte **aktualizovat** tlačítko v pravé dolní části stáhnout profily.

1. Vyberte **možnosti** kartu a ujistěte se, že **nabízená oznámení** jsou povolené.

1. Otevřete váš **AppDelegate.swift** soubor k implementaci **UNUserNotificationCenterDelegate** protokolu a přidejte následující kód do horní části třídy:

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

    Tyto členy, budete používat později. Konkrétně budete používat **značky** a **genericTemplate** členy jako součást registrace. Další informace o značek, naleznete v tématu [značky pro registrace](notification-hubs-tags-segment-push-message.md) a [registrace šablon](notification-hubs-templates-cross-platform-push-messages.md).

1. Ve stejném souboru přidejte následující kód, který **didFinishLaunchingWithOptions** funkce:

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

    Tento kód načte nastavení hodnot z **devsettings.plist**, nastaví **AppDelegate** třídy jako **UNUserNotificationCenter** delegovat, žádosti o autorizaci pro nabízená oznámení a poté zavolá **registerForRemoteNotifications**.

    Aby byl jednoduchý, kód podporuje *iOS 10 nebo novější*. Podmíněně pomocí příslušných rozhraní API a přístupy, jako byste to obvykle udělali, můžete přidat podporu pro předchozí verze operačního systému.

1. Ve stejném souboru přidejte následující funkce:

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

    Tento kód použije **installationId** a **pushChannel** hodnoty pro registraci do centra oznámení. V tomto případě používáte **UIDevice.current.identifierForVendor** poskytnout jedinečnou hodnotu pro identifikaci zařízení a pak formátování **deviceToken** poskytnout požadovaný  **pushChannel** hodnotu. **ShowAlert** funkce existuje. stačí k zobrazení text zprávy pro demonstrační účely.

1. Pořád ještě v **AppDelegate.swift** přidejte **willPresent** a **didReceive** fungovat tak, aby **UNUserNotificationCenterDelegate**. Tyto funkce Zobrazit upozornění, když jste upozornění, že aplikace je spuštěna v popředí nebo pozadí.

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

1. Přidejte tisku příkazy k dolnímu okraji **didRegisterForRemoteNotificationsWithDeviceToken** funkce a ověřte, zda **installationId** a **pushChannel** probíhá přiřazené hodnoty.

1. Vytvořte **modely**, **služby**, a **nástroje** složky pro základní součásti můžete přidávat do projektu později.

1. Zkontrolujte, zda projekt sestavení a běží na fyzickém zařízení. Nabízená oznámení nemůže být testován pomocí simulátoru.

### <a name="create-models"></a>Vytváření modelů

V tomto kroku vytvoříte sadu modely, které představují [rozhraní REST API pro Notification Hubs](/rest/api/notificationhubs/) datových částí a k uložení požadovaných sdíleného přístupového podpisu (SAS) token data.

1. Přidat nový Swift soubor s názvem **PushTemplate.swift** k **modely** složky. Tento model poskytuje strukturu představující **tělo** jednotlivé šablony jako součást **DeviceInstallation** datové části.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Přidat nový Swift soubor s názvem **DeviceInstallation.swift** k **modely** složky. Tento soubor definuje strukturu představující datovou část pro vytváření nebo aktualizaci **instalace zařízení**. Přidejte následující kód do souboru:

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

1. Přidat nový Swift soubor s názvem **TokenData.swift** k **modely** složky. Tento model se použije k ukládání spolu s jeho vypršení platnosti tokenu SAS.

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

### <a name="generate-a-sas-token"></a>Vygenerování tokenu SAS

Notification Hubs pomocí stejné zabezpečení infrastruktury jako služby Azure Service Bus. Pro volání rozhraní REST API, je potřeba [prostřednictvím kódu programu vytvořit SAS token](/rest/api/eventhub/generate-sas-token) , který je možné v **autorizace** záhlaví požadavku.  

Výsledný token bude v následujícím formátu:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Samotný proces zahrnuje stejné šest klíčové kroky:  

1. Výpočetní vypršení platnosti v [UNIX Unixový čas](https://en.wikipedia.org/wiki/Unix_time) formátu, což znamená, že dobu v sekundách uplynulých od půlnoci koordinovaný světový čas 1. ledna 1970.
1. Formátování **ResourceUrl** , který představuje prostředek, který se snažíte získat přístup tak, aby byl procentuálně zakódovaný a malá písmena. **ResourceUrl** má tvar `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Příprava **StringToSign**, který je formátován jako `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Výpočetní a kódování Base64 **podpis** pomocí hodnota hash HMAC SHA256 **StringToSign** hodnotu. Hodnota hash se používá s **klíč** součástí **připojovací řetězec** pro příslušné **autorizační pravidlo**.
1. Formátování kódovanou jako Base64 **podpis** tak, aby byl kódovaný procent.
1. Vytváření pomocí tokenu v očekávaném formátu **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, a **UrlEncodedResourceUrl** hodnoty.

Zobrazit [dokumentace ke službě Azure Service Bus](../service-bus-messaging/service-bus-sas.md) důkladnější přehled sdíleného přístupového podpisu a jak ho používat Azure Service Bus a Notification Hubs.

Pro účely tohoto příkladu Swift, budete používat open source od společnosti Apple **CommonCrypto** knihovny, které vám pomůžou s hodnotami hash podpisu. Protože je knihovny jazyka C, není přístupná ve Swiftu úprav. Knihovny můžete zpřístupnit pomocí hlavičku přemostění Datacenter.

Přidání a konfigurace hlavičku přemostění Datacenter:

1. V Xcode, vyberte **souboru** > **nový** > **souboru** > **hlavičkový soubor**. Název souboru hlaviček **BridgingHeader.h**.

1. Upravte soubor k importu **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Aktualizovat cíle **nastavení sestavení** odkazovat hlavičku přemostění Datacenter:

   1. Otevřít **nastavení vytváření** kartu a přejděte dolů k položce **Swift kompilátoru** oddílu.

   1. Ujistěte se, že **nainstalovat záhlaví kompatibility Objective-C** je možnost nastavená na **Ano**.

   1. Zadejte cestu k souboru `'<ProjectName>/BridgingHeader.h'` do **Objective-C hlavičky přemostění** možnost. Toto je cesta k souboru do našich hlavičky přemostění Datacenter.

   Pokud nemůžete najít tyto možnosti, ujistěte se, že máte **všechny** zobrazení vybrané spíše než **základní** nebo **vlastní**.

   Existuje mnoho obálky open source třetích stran dostupné knihovny, které by mohlo způsobit nepoužitelnost pomocí **CommonCrypto** trochu snazší. Diskuzi o tyto knihovny je ale nad rámec tohoto článku.

1. Přidat nový Swift soubor s názvem **TokenUtility.swift** v rámci **nástroje** složky a přidejte následující kód:

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

   Tento nástroj zapouzdří logiku, která je zodpovědná za generování tokenu SAS.

   Jak je uvedeno dříve, **getSasToken** funkce orchestruje postup vysoké úrovně, které jsou potřebné k přípravě token. Funkce se volá služba instalaci později v tomto kurzu.

   Tyto dvě funkce jsou volány **getSasToken** funkce: **sha256HMac** pro výpočet podpisu a **urlEncodedString** pro kódování přidružené adresy URL řetězec. **UrlEncodedString** funkce není nutné, protože není možné dosáhnout požadovaných výstup s použitím integrovaného **addingPercentEncoding** funkce.

   [Služby Azure Storage SDK pro iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) je vynikající příklad toho, jak přistupovat ke těchto operací v jazyce Objective-C. Další informace týkající se tokeny Azure Service Bus SAS najdete v [dokumentace ke službě Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Ověřit SAS token

Před implementací služby instalace v klientovi, zkontrolujte, že naše aplikace správně generuje SAS token pomocí vašeho nástroje pro HTTP podle výběru. Pro účely tohoto kurzu bude náš nástroj podle výběru **Postman**.

Použít příkaz správně umístěný tiskový nebo zarážka poznamenat **installationId** a **token** hodnot generovaných aplikací.

Následující postup použijte k volání **instalace** rozhraní API:

1. V **Postman**, otevřete novou kartu.

1. Nastavte požadavek na **získat** a zadejte následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Konfigurace hlaviček žádosti následujícím způsobem:

   | Klíč           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Authorization | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Vyberte **kód** tlačítko, které se zobrazí v pravém horním rohu pod **Uložit** tlačítko. Požadavek by měl vypadat podobně jako v následujícím příkladu:

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

Neexistuje žádná registrace pro zadaný rozbočovač **installationId** v tomto okamžiku. Ověření by měla za následek odpověď "404 nebyl nalezen" místo "401 Neautorizováno" odpověď. Tento výsledek by měl potvrďte, že byl přijat tokenu SAS.

### <a name="implement-the-installation-service-class"></a>Implementace třídy instalace služby

Dále budete implementovat naše základní obálku kolem [rozhraní REST API služby instalace](/rest/api/notificationhubs/create-overwrite-installation).  

Přidat nový Swift soubor s názvem **NotificationRegistrationService.swift** pod **služby** složky a potom přidejte následující kód k tomuto souboru:

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

Podrobnosti požadavku jsou k dispozici jako součást inicializace. Značky a šablony jsou volitelně předán **zaregistrovat** funkce součástí **instalace zařízení** datovou část JSON.  

**Zaregistrovat** funkce volá soukromé funkce k přípravě žádosti. Po přijetí odpovědi se nazývá dokončení a určuje, zda byla registrace úspěšná.  

Koncový bod žádosti je vytvářený **getbaseaddress –** funkce. Konstrukce používá parametry centra oznámení *obor názvů* a *název* , které bylo zadáno během inicializace.  

**GetSasToken** funkce zkontroluje, zda je aktuálně uložené token platný. Pokud token není platný, zavolá funkci **TokenUtility** chcete vygenerovat nový token a uloží jej před vrácením hodnoty.

Nakonec **encodeToJson** převede příslušný model objekty do formátu JSON pro použití jako část obsahu žádosti.

### <a name="invoke-the-notification-hubs-rest-api"></a>Volání rozhraní REST API pro Notification Hubs

Posledním krokem je aktualizace **AppDelegate** používat **NotificationRegistrationService** zaregistrovat u našich **NotificationHub**.

1. Otevřít **AppDelegate.swift** a přidat proměnnou úrovni třídy pro uložení odkazu na **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. Ve stejném souboru, aktualizujte **didRegisterForRemoteNotificationsWithDeviceToken** funkce lze inicializovat **NotificationRegistrationService** s požadovanými parametry a poté zavolejte **zaregistrovat** funkce.

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

    Aby byl jednoduchý, se chystáte aktualizovat v okně výstupu výsledek pomocí několika tisku příkazy **zaregistrovat** operace.

1. Nyní sestavte a spusťte aplikaci na fyzickém zařízení. Měli byste vidět "Registrováno" v okně výstup.

## <a name="test-the-solution"></a>Otestování řešení

Naše aplikace v této fázi je registrovaný pomocí **NotificationHub** a může přijímat nabízená oznámení. V prostředí Xcode zastavení ladicího programu a zavřete aplikaci, pokud aktuálně běží. V dalším kroku zkontrolujte, že **instalace zařízení** podrobnosti jsou podle očekávání a že naší aplikace teď můžou přijímat nabízená oznámení.  

### <a name="verify-the-device-installation"></a>Ověření instalace zařízení

Nyní můžete provést stejný požadavek jako jste to udělali dříve pomocí **Postman** pro [ověření tokenu SAS](#verify-the-sas-token). Za předpokladu, že nevypršela platnost tokenu SAS, odpověď by teď měl obsahovat podrobné informace o instalaci, které jste zadali, jako jsou šablony a značky.

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

### <a name="send-a-test-notification-azure-portal"></a>Poslat testovací oznámení (portál Azure portal)

Nejrychlejší způsob, jak otestovat, teď můžou přijímat oznámení je a přejděte do centra oznámení na webu Azure Portal:

1. Na webu Azure Portal, přejděte **přehled** karty na vaše Centrum oznámení.

1. Vyberte **poslat na zkoušku**, což je víc než **Essentials** souhrnu v levém horním rohu okna portálu:

    ![Souhrn testů odeslat tlačítko Essentials centra oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Zvolte **vlastní šablony** z **platformy** seznamu.

1. Zadejte **12345** pro **výraz odeslání na značku**. Tato značka bylo dříve zadanou v našich instalaci.

1. Volitelně můžete upravit **zpráva** v datové části JSON:

    ![Notification Hubs Test Send](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Vyberte **Poslat**. Na portálu hlásí, zda na zařízení byla úspěšně odeslána oznámení:

    ![Notification Hubs testů odeslat výsledky](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Za předpokladu, že aplikace není spuštěná v popředí, by se měla zobrazit v oznámení **centrum oznámení** na vašem zařízení. Po klepnutí na oznámení by měla otevřít aplikaci a zobrazit výstrahu.

    ![Příklad přijato oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Poslat testovací oznámení (poskytovatel e-mailu)

Může odeslat oznámení prostřednictvím [rozhraní REST API](/rest/api/notificationhubs/) pomocí **Postman**, což může být více pohodlný způsob, jak otestovat.

1. Otevřít na nové kartě v **Postman**.

1. Nastavte požadavek na **příspěvek**a zadejte následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Konfigurace hlaviček žádosti následujícím způsobem:

   | Klíč                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | Šablony                       |
   | Tags                           | "12345"                        |

1. Konfigurace žádost **tělo** používat **RAW - JSON (application.json)** s následující datové části JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Vyberte **kód** tlačítko, které je pod **Uložit** tlačítko v pravém horním rohu okna. Požadavek by měl vypadat podobně jako v následujícím příkladu:

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

Měli byste získat stavový kód úspěchu a, dostanete oznámení na klientském zařízení.

## <a name="next-steps"></a>Další postup
Teď máte základní iOS Swift aplikaci připojenou k centru oznámení prostřednictvím [rozhraní REST API](/rest/api/notificationhubs/) a mohla odesílat a přijímat oznámení. Další informace najdete v následujících článcích:

- [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Rozhraní REST API Notification Hubs](/rest/api/notificationhubs/)
- [Sadu SDK služby Notification Hubs pro back endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK na Githubu](https://github.com/Azure/azure-notificationhubs)
- [Zaregistrovat s back-endu aplikace](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Práce se značkami](notification-hubs-tags-segment-push-message.md) 
- [Práce s využitím vlastních šablon](notification-hubs-templates-cross-platform-push-messages.md)
- [Řízení přístupu služby Service Bus pomocí signatur sdíleného přístupu](../service-bus-messaging/service-bus-sas.md)
- [Generovat tokeny SAS prostřednictvím kódu programu](/rest/api/eventhub/generate-sas-token)
- [Zabezpečení společnosti Apple: běžné šifrování](https://developer.apple.com/security/)
- [UNIX Unixový čas](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
