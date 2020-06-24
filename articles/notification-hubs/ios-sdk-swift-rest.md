---
title: Posílání nabízených oznámení do aplikací pro iOS ve službě SWIFT pomocí Azure Notification Hubs a rozhraní REST API
description: V tomto kurzu se naučíte používat Azure Notification Hubs a rozhraní REST API k odesílání nabízených oznámení do zařízení s iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126278"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Kurz: odesílání nabízených oznámení do aplikací pro iOS pomocí Notification Hubs rozhraní REST API

V tomto kurzu se dozvíte, jak používat Azure Notification Hubs k posílání nabízených oznámení do aplikace pro iOS pomocí rozhraní REST API.

Tento kurz se zabývá následujícími kroky:

- Vytvořte si ukázkovou aplikaci pro iOS.
- Připojte svoji aplikaci pro iOS k Azure Notification Hubs.
- Odesílat testovací nabízená oznámení.
- Ověřte, že aplikace přijímá oznámení.

Úplný kód tohoto kurzu si můžete stáhnout z [GitHubu](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.

- IPhone nebo iPad se systémem iOS verze 10 nebo novější.

- Vaše fyzické zařízení je zaregistrované na [portálu Apple](https://developer.apple.com/)   a přidruženo k vašemu certifikátu.

Než budete pokračovat, přečtěte si předchozí kurz na webu Začínáme s [Azure Notification Hubs pro aplikace pro iOS](https://go.microsoft.com/fwlink/?linkid=2129801) , který umožňuje nastavit a nakonfigurovat přihlašovací údaje pro nabízené oznámení v centru oznámení. I když nemáte žádné předchozí zkušenosti s vývojem pro iOS, měli byste být schopni postupovat podle těchto kroků.

> [!NOTE]
> Z důvodu požadavků na konfiguraci pro nabízená oznámení musíte místo emulátoru iOS nasadit a otestovat nabízená oznámení na fyzickém zařízení iOS (iPhone nebo iPad).

V následujících částech vytvoříte aplikaci pro iOS, která se připojuje k centru oznámení.

## <a name="create-an-ios-project"></a>Vytvoření projektu pro iOS

1. V Xcode vytvořte nový projekt iOS a vyberte šablonu aplikace s **jedním zobrazením**   .

2. Při nastavování možností pro nový projekt:
   - Zadejte **název produktu**   (PushDemo) a **identifikátor organizace**   ( `com.<organization>` ), který jste použili při nastavení **identifikátoru sady prostředků**na   portálu pro vývojáře Apple.
   - Vyberte **tým**,   pro který se **App ID**   nastavilo ID aplikace.
   - Nastavte **jazyk**   na **SWIFT**.
   - Vyberte **Další**.

3. Vytvořte novou složku s názvem **SupportingFiles**.

4. Vytvořte nový soubor v seznamu s názvem **devsettings. plist**   ve složce **SupportingFiles**   . Nezapomeňte tuto složku přidat do souboru **gitignore**   , aby se při práci s úložištěm Git netvrdila. V produkční aplikaci můžou být tyto tajné kódy podmíněně nastavované jako součást procesu automatizovaného sestavení. Tato nastavení se nevztahují v tomto kurzu.

5. Aktualizujte **devsettings. plist**   tak, aby zahrnovaly následující položky konfigurace pomocí vlastních hodnot z centra oznámení, které jste zřídili:

   | **Klíč**                  | **Typ** | **Hodnota**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | Řetězec   | `<hubKey>`       |
   | notificationHubKeyName   | Řetězec   | `<hubKeyName>`   |
   | notificationHubName      | Řetězec   | `<hubName>`      |
   | notificationHubNamespace | Řetězec   | `<hubNamespace>` |

   Požadované hodnoty můžete najít tak, že přejdete na prostředek centra oznámení v Azure Portal. Zejména hodnoty **notificationHubName**   a **notificationHubNamespace**   jsou v pravém horním rohu souhrnu **Essentials**na   stránce **Přehled**   .

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Požadované hodnoty":::

   Hodnoty **notificationHubKeyName**   a notificationHubKey můžete najít také **notificationHubKey**   tak, že přejdete na **zásady přístupu**   a vyberete příslušné **zásady přístupu**, jako je například **DefaultFullSharedAccessSignature**. Potom zkopírujte z **primárního připojovacího řetězce**   hodnotu s předponou  `SharedAccessKeyName=`   pro **notificationHubKeyName**a hodnotu s předponou  `SharedAccessKey=`   pro **notificationHubKey**. Připojovací řetězec by měl být v následujícím formátu:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Pro jednoduchost zadejte **DefaultFullSharedAccessSignature**, abyste mohli použít token k odesílání oznámení. V praxi je **DefaultListenSharedAccessSignature**   lepší volbou pro situace, kdy chcete dostávat jenom oznámení.

6. V části **Navigátor projektu**vyberte **název projektu**   a pak vyberte kartu **Obecné**   .

7. Najděte **identitu**   a pak nastavte hodnotu **identifikátoru sady prostředků**   tak, aby odpovídala  `com.<organization>.PushDemo` hodnotě, která je použitá pro **ID aplikace**   z předchozího kroku.

8. Najděte **možnosti podepisování &** a pak vyberte příslušný **tým**   pro svůj **účet Apple Developer**. Hodnota **týmu**   by měla odpovídat tomu, ve kterém jste vytvořili certifikáty a profily.

9. Xcode by měl automaticky stáhnout příslušnou hodnotu **zřizovacího profilu**   na základě **identifikátoru sady prostředků**. Pokud nevidíte novou hodnotu **zřizovacího profilu**   , zkuste obnovit profily pro **podpisovou identitu**   tak, že vyberete **Xcode**, potom **Předvolby**, **účet**a potom kliknutím na tlačítko **Stáhnout ruční profily**   Stáhněte profily.

10. Pořád na kartě **možnosti podepisování &**   klikněte na tlačítko **+ Možnosti**   a dvojím klepnutím na **nabízená oznámení**   ze seznamu zajistěte, aby byla povolená **nabízená oznámení**   .

11. Otevřete soubor **AppDelegate. SWIFT**   pro implementaci protokolu **UNUserNotificationCenterDelegate**   a přidejte následující kód do horní části třídy:

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

    Tyto členy budete používat později. Konkrétně použijete člena **značek**   jako součást registrace pomocí **vlastní šablony**. Další informace o značkách naleznete v tématu [značky pro registrace](notification-hubs-tags-segment-push-message.md)   a [Registrace šablon](notification-hubs-templates-cross-platform-push-messages.md).

12. Do stejného souboru přidejte následující kód do funkce **didFinishLaunchingWithOptions** :

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

    Tento kód načte nastavení z **devsettings. plist**, nastaví třídu **AppDelegate**   jako **UNUserNotificationCenter**   delegáta UNUserNotificationCenter, vyžádá autorizaci pro nabízená oznámení a potom zavolá **registerForRemoteNotifications**.

    Pro jednoduchost kódu podporuje pouze iOS 10 a novější. Podporu pro předchozí verze iOS můžete přidat podmíněně pomocí příslušných rozhraní API a přístupů, jak by to bylo obvyklé.

13. Do stejného souboru přidejte následující kód:

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

    Tento kód používá hodnoty **installationId**   a **pushChannel**   k registraci v centru oznámení. V tomto případě používáte **UIDevice. Current. identifierForVendor**   k poskytnutí jedinečné hodnoty k identifikaci zařízení a pak formátování **deviceToken**   pro poskytnutí požadované hodnoty **pushChannel**   . Funkce **showAlert**   existuje jednoduše k zobrazení textu zprávy pro demonstrační účely.

14. Stále v souboru **AppDelegate. SWIFT**   přidejte funkce **WillPresent**   a **didReceive**   do **UNUserNotificationCenterDelegate**. Tyto funkce zobrazí výstrahu, když jsou informováni o tom, že aplikace běží buď v popředí, nebo na pozadí.

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

15. Přidáním `print` příkazů do dolní části funkce **didRegisterForRemoteNotificationsWithDeviceToken**   Ověřte, zda **installationId**   jsou přiřazeny hodnoty installationId a **pushChannel**   :

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Vytvořte si **modely**, **služby**a složky **nástrojů**   pro základní komponenty, které do projektu přidáte později.

17. Ověřte, že se projekt sestaví a spouští na fyzickém zařízení. Nabízená oznámení nelze testovat pomocí simulátoru.

## <a name="create-models"></a>Vytváření modelů

V tomto kroku vytvoříte sadu modelů reprezentujících datové části [Notification Hubs REST API](/rest/api/notificationhubs/)   a uložíte požadovaná data tokenu sdíleného přístupového podpisu (SAS).

1. Do složky modely přidejte nový soubor SWIFT s názvem **PushTemplate. SWIFT**    **Models**   . Tento model definuje strukturu, která představuje **tělo**   jednotlivé šablony jako součást **DeviceInstallation**   datové části DeviceInstallation.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Do složky modely přidejte nový soubor SWIFT s názvem **DeviceInstallation. SWIFT**    **Models**   . Tento soubor definuje strukturu, která představuje datovou část pro vytvoření nebo aktualizaci **instalace zařízení**. Do souboru přidejte následující kód:

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

3. Do složky modely přidejte nový soubor SWIFT s názvem **TokenData. SWIFT**    **Models**   . Tento model se používá k uložení tokenu SAS spolu s jeho vypršením platnosti. Do souboru přidejte následující kód:

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

## <a name="generate-a-sas-token"></a>Vygenerovat token SAS

Notification Hubs používá stejnou infrastrukturu zabezpečení jako Azure Service Bus. Chcete-li volat REST API, [programově vygenerujte token SAS](/rest/api/eventhub/generate-sas-token)   , který lze použít v **autorizační**   hlavičce žádosti.

Výsledný token bude v následujícím formátu:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Samotný proces zahrnuje stejný šest kroků:

1. Vypočítá vypršení platnosti ve formátu [epocha času v systému UNIX](https://en.wikipedia.org/wiki/Unix_time)   , což znamená, že počet sekund uplynulý od půlnoci světového koordinovaného času 1. ledna 1970.

2. Naformátujte **ResourceUrl**   , který představuje prostředek, ke kterému se pokoušíte získat přístup, aby byl v procentech zakódovaný a malými písmeny.  **ResourceUrl**   má formát `https://<namespace>.servicebus.windows.net/<hubName>` .

3. Připravte **StringToSign**, který je formátovaný jako `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` .

4. Výpočty a kódování Base64: **podpis**   pomocí hodnoty hash HMAC-SHA256 hodnoty **StringToSign**   . Hodnota hash se používá spolu s **klíčovou**   částí **připojovacího řetězce**   pro příslušné **autorizační pravidlo**.

5. Naformátujte **podpis**kódovaný v kódování Base64   tak, aby byl zakódovaný jako procentuální.

6. Sestavte token v očekávaném formátu pomocí hodnot **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**a **UrlEncodedResourceUrl**   .

Podrobnější [Azure Service Bus documentation](../service-bus-messaging/service-bus-sas.md)   Přehled podpisů sdíleného přístupu a jeho použití Azure Service Bus a Notification Hubs najdete v dokumentaci k Azure Service Bus.

Pro účely tohoto příkladu SWIFT pomocí knihovny **CommonCrypto**Apple Open Source můžete   pomáhat s hashací signatury. Stejně jako v případě knihovny jazyka C není v rychlém přístupu k dispozici v poli SWIFT. Knihovnu je možné zpřístupnit pomocí přemostění hlavičky.

Postup přidání a konfigurace přemostění hlavičky:

1. V Xcode vyberte **soubor**, **Nový**a pak **soubor**a pak vyberte **hlavičkový soubor**. Pojmenujte hlavičkový soubor **BridgingHeader. h**.

2. Upravte soubor tak, aby importoval **CommonHMAC. h**:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Aktualizujte **nastavení cílového sestavení**   tak, aby odkazovalo na hlavičku přemostění:

   1. Vyberte projekt **PushDemo**   a přejděte dolů k části **kompilátor SWIFT**   .

   2. Zajistěte, aby byla možnost **Hlavička kompatibility cíl-C**   nastavená na **hodnotu Ano**.

   3. Zadejte cestu k souboru  `<ProjectName>/BridgingHeader.h`   v možnosti **hlavičky přemostění v cíli-C**   . Jedná se o cestu k přemostění hlavičky.

   Pokud tyto možnosti nemůžete najít, ujistěte se, že jste vybrali **vše**pro   zobrazení, nikoli pro **základní**   nebo **vlastní**.

   K dispozici je spousta Open Source knihoven, které mohou využívat **CommonCrypto**   a mnohem jednodušší. Diskuse o těchto knihovnách je ale nad rámec tohoto článku.

4. Do složky nástrojů přidejte nový soubor SWIFT s názvem **TokenUtility. SWIFT**    **Utilities**   a přidejte následující kód:

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

   Jak bylo uvedeno dříve, funkce **getSasToken**   orchestruje kroky vysoké úrovně vyžadované pro přípravu tokenu. Funkce bude volána instalační službou dále v tomto kurzu.

   Další dvě funkce jsou volány funkcí **getSasToken**   : **sha256HMac**   pro výpočet podpisu a **urlEncodedString**   pro kódování přidruženého řetězce adresy URL. Funkce **urlEncodedString**   je povinná, protože není možné dosáhnout požadovaného výstupu pomocí integrované funkce **addingPercentEncoding**   .

    [Sada Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)   je skvělým příkladem, jak přistupovat k těmto operacím v cíli – C. Další informace o Azure Service Bus tokeny SAS najdete v dokumentaci k [Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

5. V **AppDelegate. SWIFT**přidejte do funkce následující kód  `didRegisterForRemoteNotificationsWithDeviceToken`   pro ověření, že **TokenUtility. getSasToken**   generuje platný token.

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Nezapomeňte nahradit zástupné symboly v řetězci **BaseAddress**   vlastními hodnotami.

## <a name="verify-the-sas-token"></a>Ověření tokenu SAS

Před implementací instalační služby v klientovi ověřte, že aplikace správně generuje token SAS pomocí nástroje HTTP. Pro účely tohoto **kurzu je náš**nástroj na výběr.

Poznamenejte si hodnoty **installationId**   a **token**   vygenerované aplikací.

Postupujte podle těchto kroků a zavolejte **installations**   rozhraní API instalace:

1. V **příspěvku**otevřete novou kartu.
2. Nastavte požadavek na **získání**   a zadání následující adresy:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Hlavičky požadavku nakonfigurujte takto:

   | **Klíč**       | **Hodnota**        |
   | ------------- | ---------------- |
   | Typ obsahu  | application/json |
   | Autorizace | \<sasToken\>     |
   | x-MS-Version  | 2015-01          |

4. Vyberte tlačítko **Code (kód**)   , které se zobrazí v pravém horním rohu pod tlačítkem **Uložit**   . Požadavek by měl vypadat podobně jako v následujícím příkladu:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Klikněte na tlačítko **Odeslat**   .

V tuto chvíli neexistují žádné registrace pro zadaný **installationId**   . Ověřování by mělo mít za následek odpověď "404 nebylo nalezeno", nikoli odpověď "401 neautorizovaného". Tento výsledek by měl potvrdit, že se token SAS přijal.

## <a name="implement-the-installation-service-class"></a>Implementace třídy instalační služby

Potom implementujte základní obálku kolem [instalací REST API](/rest/api/notificationhubs/create-overwrite-installation).

Do složky Services přidejte nový soubor SWIFT s názvem **NotificationRegistrationService. SWIFT**    **Services**   a potom do tohoto souboru přidejte následující kód:

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

Podrobnosti o požadavcích jsou k dispozici jako součást inicializace. Značky a šablony jsou volitelně předány do funkce **Register**   , aby bylo možné vytvořit součást datové části JSON pro **instalaci zařízení**   .

Funkce **Register**   volá další soukromé funkce pro přípravu žádosti. Po přijetí odpovědi se zavolá dokončování a indikuje, jestli se registrace úspěšně dokončila.

Koncový bod žádosti je vytvořen funkcí **getbaseaddress –**   . Konstrukce používá *obor názvů*parametrů centra oznámení   a *název*   , které byly zadány během inicializace.

Funkce **getSasToken**   zkontroluje, jestli je aktuálně uložený token platný. Pokud token není platný, funkce volá **TokenUtility**   k vygenerování nového tokenu a pak ho uloží před vrácením hodnoty.

Nakonec **encodeToJson**   převede příslušné objekty modelu na JSON, aby je bylo možno použít jako součást textu žádosti.

## <a name="invoke-the-notification-hubs-rest-api"></a>Vyvolat Notification Hubs REST API

Poslední krok aktualizuje **AppDelegate**   na použití **NotificationRegistrationService**   k registraci v **NotificationHub**.

1. Otevřete **AppDelegate. SWIFT**   a přidejte proměnné na úrovni třídy pro uložení odkazu na **NoficiationRegistrationService**   a obecné **PushTemplate**:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. Ve stejném souboru aktualizujte funkci **didRegisterForRemoteNotificationsWithDeviceToken**   pro inicializaci **NotificationRegistrationService**   s parametry požadavků a pak zavolejte funkci **Register**   .

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

   Pro jednoduchost kód používá `print` příkazy k aktualizaci okna výstup s výsledkem operace **registrace**   .

3. Sestavte a spusťte aplikaci na fyzickém zařízení. V okně výstup by se měla zobrazit **zaregistrovaná** .

## <a name="test-the-solution"></a>Testování řešení

V tomto okamžiku je aplikace zaregistrovaná ve službě **NotificationHub**   a může přijímat nabízená oznámení. V Xcode ukončete ladicí program a zavřete aplikaci, pokud je aktuálně spuštěná. Dále ověřte, že **Device Installation**   se zobrazují podrobnosti o instalaci zařízení podle očekávání a že aplikace teď může dostávat nabízená oznámení.

### <a name="verify-the-device-installation"></a>Ověření instalace zařízení

Nyní můžete stejný požadavek provést jako dříve, a to pomocí **metody post**   pro [ověření tokenu SAS](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Za předpokladu, že platnost tokenu SAS nevypršela, měla by nyní odpověď obsahovat podrobnosti o instalaci, které jste zadali, například šablony a značky.

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

Pokud vypršela platnost předchozího tokenu SAS, můžete přidat zarážku na řádek 24 třídy **TokenUtility**   a získat nový token SAS a aktualizovat **autorizační**   hlavičku touto novou hodnotou.

### <a name="send-a-test-notification-azure-portal"></a>Odeslat zkušební oznámení (Azure Portal)

Nejrychlejší způsob, jak otestovat, že teď můžete dostávat oznámení, je přejít do centra oznámení v Azure Portal:

1. V Azure Portal přejděte na kartu **Přehled**   v centru oznámení.

2. V levém horním rohu okna portálu vyberte **Essentials** **Odeslat test**, který je nad   souhrnem základních údajů:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Odeslání souhrnného testu z Notification Hubs Essentials":::

3. V seznamu platformy vyberte možnost **vlastní šablona**    **Platforms**   .

4. Jako **12345**    **výraz odeslat do značky**zadejte 12345. Tuto značku jste předtím zadali v instalaci.

5. Volitelně můžete upravit **zprávu**   v datové části JSON:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Odeslat Notification Hubs testu":::

6. Vyberte **Odeslat**. Portál by měl uvádět, zda bylo oznámení úspěšně odesláno do zařízení:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Výsledek odeslání testu":::

   Za předpokladu, že aplikace není spuštěná v popředí, by se v **centru oznámení**   na vašem zařízení mělo zobrazit také oznámení. Klepnutím na oznámení by se měla aplikace otevřít a zobrazit výstrahu.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Testovací oznámení":::

### <a name="send-a-test-notification-mail-carrier"></a>Odeslat zkušební oznámení (poštovní operátor)

Oznámení můžete odesílat prostřednictvím [REST API](/rest/api/notificationhubs/)   pomocí **post**, což může být pohodlnější způsob testování.

1. Otevře novou kartu v **příspěvku**.

2. Nastavte požadavek na **post**a zadejte následující adresu:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Hlavičky požadavku nakonfigurujte takto:

   | Klíč                           | Hodnota                          |
   | ----------------------------- | ------------------------------ |
   | Typ obsahu                  | Application/JSON; charset = UTF-8 |
   | Autorizace                 | \<sasToken\>                   |
   | ServiceBusNotification – formát | šablona                       |
   | Značky                          | "12345"                        |

4. Nakonfigurujte tělo požadavku **BODY**   na použití **surového-JSON (application.json)**   s následující datovou částí JSON:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Vyberte tlačítko **kód**   , které je pod tlačítkem **Uložit**   v pravém horním rohu okna. Požadavek by měl vypadat podobně jako v následujícím příkladu:

   ```xml
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

6. Klikněte na tlačítko **Odeslat**   .

Měli byste získat **201 Created**   stavový kód úspěchu 201 a obdržet oznámení v klientském zařízení.

## <a name="next-steps"></a>Další kroky

Teď máte základní aplikaci iOS SWIFT připojenou k centru oznámení prostřednictvím [Notification Hubs REST API](/rest/api/notificationhubs/)a můžete odesílat a přijímat oznámení. Pokud se chcete dozvědět víc o tom, jak odesílat oznámení na konkrétní zařízení, přejděte k následujícímu kurzu:

- [Kurz: nabízená oznámení na konkrétní zařízení](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Další informace najdete v následujících článcích:

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
