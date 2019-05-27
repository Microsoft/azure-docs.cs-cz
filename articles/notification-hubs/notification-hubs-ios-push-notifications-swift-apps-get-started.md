---
title: Nabízená oznámení do aplikace pro Swift iOS pomocí Azure Notification Hubs | Dokumentace Microsoftu
description: Zjistěte, jak nabízená oznámení do aplikace pro Swift iOS pomocí Azure Notification Hubs.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994763"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Kurz: Odesílání nabízených oznámení do aplikace Swift iOS s využitím rozhraní REST API Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

V tomto kurzu budete používat Azure Notification Hubs k odesílání nabízených oznámení do aplikace iOS založené na Swift pomocí [rozhraní REST API](/rest/api/notificationhubs/). Vytvoříte prázdnou aplikaci pro iOS, která přijímá nabízená oznámení [služby Apple Push Notification (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vygenerujete soubor s žádostí o podepsání certifikátu
> * Požádáte aplikaci o nabízená oznámení
> * Vytvoření zřizovacího profilu pro aplikaci
> * Vytvoříte centrum oznámení.
> * Konfigurace centra oznámení s informacemi služby APN
> * Připojíte aplikaci pro iOS ke službě Notification Hubs
> * Otestování řešení

## <a name="prerequisites"></a>Požadavky
Pokud chcete postupovat s námi, budete potřebovat:

- Projděte si [přehled Azure Notification Hubs](notification-hubs-push-notification-overview.md) Pokud nejste obeznámeni s službu. 
- Další informace o registraci a instalaci: [Správa registrací](notification-hubs-push-notification-registration-management.md)
- Aktivní [vývojářský účet Apple.](https://developer.apple.com) 
- Počítač Mac s Xcode spolu s platnou vývojářskou certifikát nainstalován do své klíčenky
- Zařízení iPhone fyzické můžete spustit a ladit pomocí (už není možné otestovat nabízená oznámení pomocí simulátoru)
- IPhone fyzické zařízení zaregistrované v [Apple Portal](https://developer.apple.com) a přidružené k certifikátu
- [Předplatné Azure](https://portal.azure.com) ve kterém můžete vytvářet a spravovat prostředky

By měl být možné postupovat s námi vytvoření v tomto příkladu první zásady bez předchozích zkušeností. Znalost následujících konceptů ale budou užitečné:

- Vytváření aplikací pro iOS s Xcode a Swift
- Konfigurace [centra oznámení Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) pro iOS
- Znalost [portálu Apple Developer](https://developer.apple.com) a [webu Azure portal](https://portal.azure.com)

> [!NOTE]
> Centrum oznámení se byly konfigurovány k použití *izolovaného prostoru* pouze v režimu ověřování. Tento režim ověřování byste neměli používat pro produkční úlohy.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Připojte aplikaci iOS k centru oznámení
V této části sestavíte aplikaci pro iOS, který se připojí k centru oznámení.  

### <a name="create-an-ios-project"></a>Vytvoření projektu pro iOS
1. V **Xcode**, vytvořte nový projekt iOS a vyberte **jediné zobrazení aplikace** šablony.
2. Když nastavujete možnosti pro nový projekt, postupujte podle těchto kroků:
    1. Použijte stejný **název produktu** (to znamená **PushDemo**) a **identifikátor organizace** (to znamená, `com.<organization>`), který jste použili při **sady prostředků Identifikátor** v byl nastaven **portálu Apple Developer**. 
    2. Zvolte **týmu** , který **ID aplikace** byla nastavena pro.
    3. Nastavte **jazyk** k **Swift**.
    4. Klikněte na **Další**
3. Vytvořit novou složku s názvem **SupportingFiles**.
4. Vytvořte nový **plist** soubor s názvem **devsettings.plist** pod **SupportingFiles** složky. Nezapomeňte si přidat tuto složku do vaší **gitignore** souboru není potvrzená při práci s **úložiště git**. V produkční aplikace bude by pravděpodobně být podmíněně nastavení těchto tajných kódů jako součást automatizovaného procesu sestavení. Ale není zahrnut jako součást tohoto názorného postupu.
5. Aktualizace **devsettings.plist** zahrnout následující položky konfigurace (pomocí vlastní hodnoty z **centra oznámení** můžete zřídit):

   | Klíč                            | Type                     | Hodnota                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   Požadované hodnoty najdete tak, že přejdete na **centra oznámení** prostředek **webu Azure portal**. Můžete najít **notificationHubName** a **notificationHubNamespace** hodnoty v pravém horním rohu **Essentials** souhrn v rámci  **Přehled** stránky.

   ![Souhrn Essentials Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Můžete najít **notificationHubKeyName** a **notificationHubKey** hodnoty tak, že přejdete do **zásady přístupu**, pak kliknete na příslušné **přístup Zásady**. Například, `DefaultFullSharedAccessSignature`. Zkopírujte z **primární připojovací řetězec** předponu hodnotu `SharedAccessKeyName=` pro `notificationHubKeyName` a hodnota předponu `SharedAccessKey=` pro `notificationHubKey`. Připojovací řetězec by měl být v následujícím formátu:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Aby byl jednoduchý, použijete *DefaultFullSharedAccessSignature* tak token, který můžete použít k odesílání oznámení také. V praxi však `DefaultListenSharedAccessSignature` by bylo vhodnější pro situace, ve kterém chcete přijímat oznámení.       
6. V části **navigátoru projektů**, klikněte na tlačítko **název projektu**, klikněte **Obecné** kartu
7. Najít **Identity**, nastavte **identifikátor sady prostředků** hodnotu tak, aby odpovídala identifikátoru použitému **ID aplikace** (z předchozích kroků) to znamená, `'com.<organization>.PushDemo'`
8. Najít **podepisování**, pak Ujistěte se, že vyberete příslušný **týmu** pro vaše **vývojářského účtu Apple** (to, ve kterém jste vytvořili certifikátů a profilů dříve).  **Xcode** by měl automaticky stahují odpovídající **zřizovací profil** na základě **identifikátor sady prostředků**. Pokud nevidíte nový **zřizovací profil**, pokuste se aktualizovat profily pro **Podpisová identita** (*Xcode > Předvolby > účet > Zobrazit podrobnosti o*). Kliknutím na **Podpisová identita**, kliknutím **aktualizovat** tlačítko v pravé dolní části měli stáhnout profily.
9. Vyberte **možnosti** kartu a ujistěte se, že **nabízená oznámení** jsou povolené.
10. Otevřete váš **AppDelegate.swift** soubor k implementaci *UNUserNotificationCenterDelegate* protokolu a přidejte následující kód do horní části třídy:
    
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

    Tyto členy, budete používat později. *Značky* a *genericTemplate* se použije jako součást registrace. Další informace o značek, naleznete v tématu [značky pro registrace](notification-hubs-tags-segment-push-message.md) a [registrace šablon](notification-hubs-templates-cross-platform-push-messages.md).
 
11. Ve stejném souboru přidejte následující kód *didFinishLaunchingWithOptions* funkce:

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

    Tento kód načte nastavení hodnot z **devsettings.plist**, nastaví **AppDelegate** třídy jako *UNUserNotificationCenter* delegovat, žádosti o autorizaci pro nabízená oznámení a poté zavolá *registerForRemoteNotifications*.
    
    Aby byl jednoduchý, kód podporuje **iOS 10 a novějších pouze**. Podmíněně pomocí příslušných rozhraní API a přístupy, jako byste to obvykle udělali, můžete přidat podporu pro starší verze operačního systému.

12. Ve stejném souboru přidejte následující funkce:

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

    Tento kód použije *installationId* a *pushChannel* hodnot k registraci ve službě **centra oznámení**. V tomto případě používáte *UIDevice.current.identifierForVendor* k poskytnutí jedinečnou hodnotu k identifikaci zařízení a poté ho naformátujte *deviceToken* k poskytnutí požadovaných *pushChannel* hodnotu. *ShowAlert* funkce je jednoduše zobrazit text zprávy pro demonstrační účely.

13. Pořád ještě v **AppDelegate.swift**, přidejte *willPresent* a *didReceive* **UNUserNotificationCenterDelegate** fungovat tak, aby Zobrazte upozornění, když dostali oznámení, když aplikace je spuštěná v popředí a pozadí
    
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

14. Přidejte tisku příkazy k dolnímu okraji *didRegisterForRemoteNotificationsWithDeviceToken* funkce a ověřte, zda *installationId* a *pushChannel* probíhá přiřazené hodnoty
15. Vytváření složek (**modely**, **služby**, a **nástroje**) pro základní součásti můžete přidávat do projektu později
16. Zkontrolujte, zda projekt sestavení a běží na fyzickém zařízení (oznámení nelze provést test pomocí simulátoru push)

### <a name="create-models"></a>Vytváření modelů
V tomto kroku vytvoříte sadu modely, které představují [rozhraní REST API pro Notification Hubs](/rest/api/notificationhubs/) datových částí a slouží k uložení požadovaných **SAS token** data.


1.  Přidat nový soubor swift, aby **modely** volá **PushTemplate.swift**. Tento model poskytuje strukturu představující **tělo** jednotlivé šablony jako součást **DeviceInstallation** datové části:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Přidat nový soubor swift, aby **modely** složku s názvem **DeviceInstallation.swift**. Tento soubor definuje strukturu představující datovou část pro vytváření nebo aktualizaci **instalace zařízení**. Přidejte následující kód do souboru:
    
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

3.  Přidejte nový soubor swift pod **modely** volá **TokenData.swift**. Tento model se používá k ukládání **SAS token** spolu s jeho vypršení platnosti

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
**Notification Hubs** používat stejnou infrastrukturu zabezpečení jako **Azure Service Bus**. Pro volání rozhraní REST API, je potřeba [prostřednictvím kódu programu vytvořit SAS token](/rest/api/eventhub/generate-sas-token) , který je možné v **autorizace** záhlaví požadavku.  

Výsledný token bude v následujícím formátu: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Samotný proces zahrnuje stejné šest klíčové kroky:  

1.  Výpočetní vypršení platnosti v [UNIX Unixový čas](https://en.wikipedia.org/wiki/Unix_time) formátu (sekundách uplynulých od 00:00:00 UTC 1. ledna 1970)
2.  Formátování **ResourceUrl** (představuje prostředek, který se snažíte získat přístup, tedy `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) tak, aby byl procentuálně zakódovaný a malá písmena
3.  Příprava **StringToSign**, který se skládá z `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Computingu (a kódování base 64) **podpis** pomocí **HMAC SHA256** z **StringToSign** hodnotu **klíč** součástí **Připojovací řetězec** (pro příslušné **autorizační pravidlo**)
5.  Formátování kódování base-64 **podpis** tak, aby byl % kódování
6.  Vytváření **token** v očekávaném formátu pomocí **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**a **UrlEncodedResourceUrl** hodnoty

Najdete v článku [dokumentace ke službě Azure Service Bus](../service-bus-messaging/service-bus-sas.md) důkladnější přehled **sdílený přístupový podpis** a jak se používají v **Azure Service Bus** a  **Notification Hubs**.

Pro účely tohoto příkladu Swift, budete používat open source od společnosti Apple **CommonCrypto** knihovny, které vám pomůžou s hodnotami hash podpisu. Protože je knihovny jazyka C, není přístupná v Swift out-of-the-box. Ale můžete zpřístupnit to pomocí hlavičku přemostění Datacenter. Přidání a konfigurace hlavičku přemostění Datacenter:

1. V **Xcode**, přejděte na stránku **souboru**, pak **nový**, pak **souboru** a vyberte **hlavičkový soubor** pojmenování *"BridgingHeader.h.*
2. Upravte soubor k importu **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Aktualizovat cíle **nastavení sestavení** odkazovat hlavičku přemostění Datacenter. Otevřít **nastavení vytváření** kartu a přejděte dolů k položce **Swift kompilátoru** oddílu. Ujistěte se, že **nainstalovat záhlaví kompatibility Objective-C** možnost nastavená na hodnotu **Ano** a zadejte cestu k naší hlavičky přemostění Datacenter do **hlavičky přemostění Objective-C**   to znamená možnost `'\<ProjectName\>/BridgingHeader.h'`. Pokud nemůžete najít tyto možnosti, ujistěte se, máte **všechny** zobrazení vybrané (spíše než **základní** nebo **vlastní**).
    
   Existuje mnoho obálky open source třetích stran dostupné knihovny, které by mohlo způsobit nepoužitelnost pomocí **CommonCrypto** trochu snazší. Jde nad rámec tohoto článku.

4. Přidejte nový soubor Swift pod **nástroje** složku s názvem **TokenUtility.swift** a přidejte následující kód:

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
    
    Tento nástroj zapouzdří logiku, která je zodpovědná za generování **SAS token**. *GetSasToken* funkce orchestruje postup vysoké úrovně, které jsou potřebné k přípravě tokenu, jak je uvedeno dříve a bude volat službu instalace v krocích později v tomto kurzu. Tyto dvě funkce jsou volány *getSasToken funkce*; *sha256HMac* pro výpočet podpisu a *urlEncodedString* pro šifrování příslušných řetězce adresy Url. *UrlEncodedString* funkce byla požadována, protože nebylo možné získat požadované výstup pomocí integrovaného *addingPercentEncoding* funkce. [Služby Azure Storage SDK pro iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) pracoval jako vynikající příklad toho, jak přistupovat ke tyto operace byť v Objective-C. Další informace o **tokeny Azure Service Bus SAS** najdete v [dokumentace ke službě Azure Service Bus](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Ověřit SAS token
Před implementací služby instalace v klientovi můžete zkontrolovat, že je správně generování naši aplikaci **SAS token** pomocí vaší http užitečnost výběru. Pro účely tohoto příspěvku, bude náš nástroj podle výběru **Postman**.

Poznamenejte si *installationId* a *token* hodnoty generovaná aplikace pomocí správně umístěný vytisknout příkaz nebo zarážku. 

Následující postup použijte k volání *instalace* rozhraní API:

1. V **Postman**, otevřete novou kartu
2. Nastavte požadavek na **získat** a na následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Konfigurace hlaviček žádosti následujícím způsobem:
    
   | Klíč           | Hodnota            |
   | ------------- | ---------------- |
   | Typ obsahu  | application/json |
   | Autorizace | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Klikněte na **kód** tlačítka (v pravé horní části **Uložit** tlačítko). Požadavek by měl vypadat podobně jako v následujícím příkladu:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Klikněte na tlačítko **odeslat** tlačítko

Neexistuje žádná registrace pro zadaný rozbočovač *installationId* v tomto okamžiku ale mělo by být výsledkem **404 Nenalezeno** odpovědi spíše než **401 Neautorizováno**. Tento výsledek by měl ověřte, že **SAS token** byla přijata.

### <a name="implement-the-installation-service-class"></a>Implementace třídy instalace služby
Dále budete implementovat naše základní obálku kolem [rozhraní REST API služby instalace](/rest/api/notificationhubs/create-overwrite-installation).  

Přidejte nový soubor Swift pod **služby** složku s názvem **NotificationRegistrationService.swift**, přidejte následující kód k tomuto souboru:

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
 
Podrobnosti požadavku jsou k dispozici jako součást inicializace. Značky a šablony jsou volitelně předán *zaregistrovat* funkce součástí **instalace zařízení** datovou část JSON.  

*Zaregistrovat* funkce zdůrazňuje na soukromé funkce k přípravě žádosti. Po přijetí odpovědi na dokončení se nazývá označující, zda registrace byla úspěšná, či nikoli.  

Koncový bod žádosti je vytvořená pomocí *getbaseaddress –* použití pracovat **centra oznámení** parametry **obor názvů** a **název**během inicializace k dispozici.  

*GetSasToken* funkce zkontroluje, zda je aktuálně uložené token platný, jinak ho upozorní na **TokenUtility** vygenerovat nový token a uložte ho před vrácením hodnoty. 

Nakonec *encodeToJson* převede na příslušný model objekty do formátu JSON pro použití jako část obsahu žádosti.

### <a name="invoke-the-notification-hubs-rest-api"></a>Volání rozhraní REST API pro Notification Hubs
Posledním krokem je aktualizace **AppDelegate** používat **NotifiationRegistrationService** zaregistrovat u našich **NotificationHub**. 

1. Otevřít **AppDelegate.swift** a přidat proměnnou úrovni třídy pro uložení odkazu na **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. Ve stejném souboru, aktualizujte *didRegisterForRemoteNotificationsWithDeviceToken* funkce lze inicializovat **NotificationRegistrationService** s požadovanými parametry, zavolejte *zaregistrovat* funkce.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Aby byl jednoduchý, se chystáte aktualizovat v okně výstupu výsledek pomocí několika tisku příkazy *zaregistrovat* operace. 

3. Nyní sestavte a spusťte aplikaci (na fyzickém zařízení). Měli byste vidět **"Registrováno"** ve výstupním okně.

## <a name="test-the-solution"></a>Otestování řešení
V této fázi je zaregistrován náš aplikace **NotificationHub** a může přijímat nabízená oznámení. V **Xcode**zastavení ladicího programu a zavřete aplikaci (Pokud je nainstalováno). V dalším kroku budete zkontrolujte, že **instalace zařízení** podrobnosti jsou podle očekávání a že naši aplikaci skutečně teď můžou přijímat nabízená oznámení.  

### <a name="verify-the-device-installation"></a>Ověření instalace zařízení
Nyní můžete provést stejný požadavek jako jste to udělali dříve pomocí **Postman** pro [ověření tokenu SAS](#verify-the-sas-token). Za předpokladu, že **SAS token** nebyl vypršel, odpověď by teď měl obsahovat jste zadali jako jsou šablony a značky podrobné informace o instalaci.  

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
Nejrychlejší způsob, jak otestovat, teď můžou přijímat oznámení je přejděte **centra oznámení** v **webu Azure portal**.

1. V **webu Azure portal**, přejděte **přehled** kartu na vaše **centra oznámení**
2. Klikněte na tlačítko **poslat na zkoušku** (levém) nad **Essentials** souhrnu

    ![Souhrn testů odeslat tlačítko Essentials centra oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Zvolte **vlastní šablony** ze seznamu **platformy**
4. Zadejte **12345** pro **odeslání na značku výraz** (Tato značka bylo zadané v našich instalace)
5. Volitelně můžete upravit **zpráva** v datové části JSON
    
    ![Notification Hubs Test Send](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Klikněte na tlačítko **odeslat** a na portálu hlásí, zda oznámení byl úspěšně odeslán do zařízení

    ![Notification Hubs testů odeslat výsledky](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Také byste měli vidět oznámení v **centrum oznámení** na vašem zařízení (za předpokladu, že aplikace není spuštěná v popředí). Klepnutí na oznámení by měla otevřít aplikaci a zobrazit výstrahu.

    ![Příklad přijato oznámení](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Poslat testovací oznámení (Postman)
Může odeslat oznámení prostřednictvím funkcím [rozhraní REST API](/rest/api/notificationhubs/) prostřednictvím **Postman** jako dobře a že může být více pohodlný způsob, jak otestovat. 

1. V **Postman**, otevřete novou kartu
2. Nastavte požadavek na **příspěvek** a zadejte následující adresu:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Konfigurace hlaviček žádosti následujícím způsobem:
    
   | Klíč                            | Hodnota                          |
   | ------------------------------ | ------------------------------ |
   | Typ obsahu                   | application/json;charset=utf-8 |
   | Autorizace                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | šablona                       |
   | Tags                           | "12345"                        |

4. Konfigurace žádost **tělo** používat **RAW - JSON (application.json)** s následující datové části JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Klikněte na **kód** tlačítka (v pravé horní části **Uložit** tlačítko). Požadavek by měl vypadat podobně jako v následujícím příkladu:

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

5. Klikněte na tlačítko **odeslat** tlačítko

Měli byste získat stavový kód úspěchu a, dostanete oznámení na klientském zařízení.

## <a name="next-steps"></a>Další postup
Teď máte základní iOS Swift aplikaci připojenou k **centra oznámení** prostřednictvím [rozhraní REST API](/rest/api/notificationhubs/) a mohla odesílat a přijímat oznámení. Další informace najdete v následujících článcích: 

- [Přehled služby Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Rozhraní REST API Notification Hubs](/rest/api/notificationhubs/)
- [Sadu SDK služby Notification Hubs pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK na Githubu](https://github.com/Azure/azure-notificationhubs)
- [Zaregistrovat s back-endu aplikací](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Správa registrací](notification-hubs-push-notification-registration-management.md)
- [Práce se značkami](notification-hubs-tags-segment-push-message.md) 
- [Práce s využitím vlastních šablon](notification-hubs-templates-cross-platform-push-messages.md)
- [Řízení přístupu služby Service Bus se sdílenými přístupovými podpisy](../service-bus-messaging/service-bus-sas.md)
- [Generovat tokeny SAS prostřednictvím kódu programu](/rest/api/eventhub/generate-sas-token)
- [Zabezpečení společnosti Apple: běžné šifrování](https://developer.apple.com/security/)
- [UNIX Unixový čas](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
