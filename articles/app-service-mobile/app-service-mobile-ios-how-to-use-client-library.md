---
title: Postup použití iOS SDK pro Azure Mobile Apps
description: Postup použití iOS SDK pro Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: conceptdev
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 0de561b177a1474b0ce4f0f203803e8265db5e7a
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818342"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Jak iOS použijte klientskou knihovnu pro Azure Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tento průvodce vás naučí, provádět běžné scénáře pomocí nejnovější [Azure Mobile Apps iOS SDK][1]. Pokud jste ještě na službu Azure Mobile Apps, nejprve dokončit [Azure Mobile Apps rychlý Start] Pokud chcete vytvořit back-endu, vytvoření tabulky a stáhnete projekt Xcode předem sestavených s Iosem. V této příručce se zaměříme na sadu SDK pro iOS na straně klienta. Další informace o sadě SDK na straně serveru pro back-endu, najdete v článku HOWTOs SDK serveru.

## <a name="reference-documentation"></a>Referenční dokumentace

Referenční dokumentace pro iOS Klientská sada SDK je umístěná tady: [Azure Mobile Apps iOS odkaz klienta][2].

## <a name="supported-platforms"></a>Podporované platformy

IOS SDK podporuje projekty jazyka Objective-C, Swift 2.2 projekty a projekty Swift 2.3 pro iOS verze 8.0 nebo novější.

"Serveru tok" ověřování používá komponenty WebView pro zobrazené uživatelské rozhraní.  Pokud zařízení není umožnilo předkládat WebView uživatelského rozhraní, pak se vyžaduje jinou metodu ověřování, která je mimo rozsah tohoto produktu.  
Tato sada SDK není proto vhodný pro Watch-type nebo podobně jako zařízení s omezeným přístupem.

## <a name="Setup"></a>Instalace a požadavky

Tato příručka předpokládá, že vytvoříte back-end s tabulkou. Tento průvodce to předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tento průvodce to předpokládá také, že ve vašem kódu, které odkazují na `MicrosoftAzureMobile.framework` a importovat `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Postupy: vytvoření klienta

Pokud chcete získat přístup k back-endu Azure Mobile Apps ve vašem projektu, vytvořte `MSClient`. Nahraďte `AppUrl` pomocí adresy URL aplikace. Můžete ponechat `gatewayURLString` a `applicationKey` prázdný. Pokud jste nastavili Brána pro ověřování, naplnění `gatewayURLString` s adresou URL brány.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Kód SWIFT**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Postupy: vytvoření odkazu na tabulku

Pro přístup k datům a jejich aktualizaci vytvořte odkaz na back-endovou tabulku. Nahraďte `TodoItem` názvem vaší tabulky.

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Kód SWIFT**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Postupy: dotazování na Data

Chcete-li vytvořit dotaz na databázi, dotazování `MSTable` objektu. Následující dotaz načte všechny položky v `TodoItem` a protokolovat text každé položky.

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Kód SWIFT**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>Postupy: Filtr vrátil Data

Chcete-li filtrovat výsledky, existuje mnoho možností k dispozici.

Chcete-li filtrovat pomocí predikátu, použijte `NSPredicate` a `readWithPredicate`. Následující filtry vrátil data k vyhledání pouze neúplných položek Todo.

**Objective-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Kód SWIFT**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>Postupy: použití MSQuery

K provedení složitých dotazů (včetně řazení a stránkování), vytvořte `MSQuery` přímo nebo pomocí predikátu objektu:

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Kód SWIFT**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` umožňuje ovládat různé chování dotazu.

* Určit pořadí výsledků
* Omezení pole, která chcete vrátit
* Omezení, kolik záznamů se má vrátit
* Zadejte celkový počet v odpovědi
* Zadejte parametry řetězce vlastního dotazu v žádosti
* Použít další funkce

Spuštění `MSQuery` dotazu voláním `readWithCompletion` objektu.

## <a name="sorting"></a>Postupy: řazení dat s MSQuery

Chcete-li seřadit výsledky, Podívejme se na příklad. Seřadit podle pole text a pak podle sestupných "úplné", vyvolat `MSQuery` takto:

**Objective-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Kód SWIFT**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="selecting"></a><a name="parameters"></a>Postupy: omezení polí a parametrů řetězce dotazu s MSQuery rozbalte

Chcete-li omezit pole, který se má vrátit v dotazu, zadejte názvy polí v **selectFields** vlastnost. V tomto příkladu vrátí pouze text a dokončené pole:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Kód SWIFT**:

```swift
query.selectFields = ["text", "complete"]
```

Chcete-li zahrnout parametrů řetězce dotazu další požadavek serveru (například, protože je používá vlastní skript na straně serveru), naplnění `query.parameters` takto:

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Kód SWIFT**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Postupy: Konfigurace velikosti stránky

S Azure Mobile Apps velikost stránky určuje počet záznamů, které se berou v době z tabulek back-endu. Volání `pull` dat by pak dávky dat na základě této stránky velikosti, až nebudou existovat žádné další záznamy o přijetí změn.

Je možné nakonfigurovat velikost stránky pomocí **MSPullSettings** jak je znázorněno níže. Výchozí velikost stránky je 50 a následující příklad změní na 3.

Můžete nakonfigurovat velikost různé stránky z důvodů výkonu. Pokud máte velký počet malých datových záznamů, velikost stránky vysokou snižuje dobu odezvy serveru.

Toto nastavení řídí pouze velikost stránky na straně klienta. Pokud klient požádá o větší velikosti stránky než back-end Mobile Apps podporuje, je velikost stránky je limitována maximální back-end je nakonfigurován na podporu.

Toto nastavení je také *číslo* datových záznamů, ne *nadměrná velikost v bajtech*.

Pokud zvýšíte velikost stránky klienta, by měl také zvýšit velikost stránky na serveru. V tématu ["postupy: přizpůsobení velikosti stránkovacího tabulky"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pokyny k tomu.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Kód SWIFT**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Postupy: vkládání dat

Chcete-li vložit nový řádek tabulky, vytvořte `NSDictionary` a vyvolání `table insert`. Pokud [dynamické schéma] je povoleno, mobilních back-endu Azure App Service automaticky vygeneruje nové sloupce na základě `NSDictionary`.

Pokud `id` není zadán, back-endu se generuje automaticky nové jedinečné ID. Zadejte vlastní `id` používat e-mailové adresy, uživatelská jména, nebo vlastní hodnoty jako ID. Poskytuje vlastní ID může usnadňují spojení a databáze orientované obchodní logiku.

`result` Obsahuje novou položku, která byla vložena. V závislosti na logice server může mít další nebo upravených dat v porovnání s co bylo předáno do serveru.

**Objective-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Kód SWIFT**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>Postupy: Změna dat

Aby se aktualizoval stávající řádek, upravte položky a volání `update`:

**Objective-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Kód SWIFT**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Můžete také zadejte ID řádku a aktualizované pole:

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Kód SWIFT**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Minimálně `id` atribut musí být nastaven při provádění aktualizací.

## <a name="deleting"></a>Postupy: odstranění dat

Odstranit položku, vyvolat `delete` s položkou:

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kód SWIFT**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Můžete také odstraňte zadáním ID řádku:

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kód SWIFT**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Minimálně `id` atribut musí být nastaven při provádění odstraní.

## <a name="customapi"></a>Postupy: volání vlastních rozhraní API

Vlastní rozhraní API můžete zveřejnit žádné funkce back-endu. Není třeba mapují na operace tabulky. Nejen získáte větší kontrolu nad zasílání zpráv, můžete dokonce pro čtení nebo nastaví záhlaví a změnit formát těla zprávy odpovědi. Zjistěte, jak vytvořit vlastní rozhraní API na back-endu, přečtěte si téma [vlastních rozhraní API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Chcete-li volat vlastní API, zavolejte `MSClient.invokeAPI`. Žádost a odpověď obsahu jsou považovány za JSON. Používat jiné typy médií [, použijte jiné přetížení `invokeAPI` ] [ 5].  Aby `GET` žádost o místo `POST` požadavku sadu parametrů `HTTPMethod` k `"GET"` a parametr `body` k `nil` (protože požadavky GET nemají těla zprávy.) Pokud vaše vlastní rozhraní API podporuje další příkazy HTTP, změňte `HTTPMethod` odpovídajícím způsobem.

**Objective-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Kód SWIFT**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>Postupy: registrace nabízených oznámení šablon k odesílání oznámení napříč platformami

Chcete-li zaregistrovat šablony, předejte šablony s vaší **client.push registerDeviceToken** metoda ve vaší klientské aplikace.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Kód SWIFT**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Šablony jsou typu NSDictionary a může obsahovat více šablon v následujícím formátu:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Kód SWIFT**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Všechna klíčová slova jsou odebrána z požadavku pro zabezpečení.  Přidání značek na zařízení nebo šablony v rámci instalace naleznete v tématu [pracovat s back-end .NET server SDK pro Azure Mobile Apps][4].  K odesílání oznámení pomocí těchto registrovaných šablon, pracovat s [rozhraní API pro Notification Hubs][3].

## <a name="errors"></a>Postupy: zpracování chyb

Při volání back-endu mobilní služby Azure App Service obsahuje blok dokončení `NSError` parametru. Pokud dojde k chybě, je tento parametr nemá hodnotu nil. Ve vašem kódu zkontrolujte tento parametr a zpracování chyb podle potřeby, jak je ukázáno v předchozích fragmentů kódu.

Soubor [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] definuje konstanty `MSErrorResponseKey`, `MSErrorRequestKey`, a `MSErrorServerItemKey`. Pokud chcete získat další data související s chybou:

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Kód SWIFT**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Kromě toho soubor definuje konstanty pro každý kód chyby:

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Kód SWIFT**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Postupy: ověřování uživatelů pomocí Active Directory Authentication Library

Můžete používat Active Directory Authentication Library (ADAL) pro přihlášení uživatelů do vaší aplikace pomocí Azure Active Directory. Tok ověřování klientů pomocí zprostředkovatele identity sada SDK je vhodnější než použít `loginWithProvider:completion:` metody.  Tok ověření klienta obsahuje více přirozený chování uživatelského prostředí a umožňuje další přizpůsobení.

1. Konfigurace back-endu mobilní aplikace pro přihlášení k AAD pomocí následujících [konfigurace služby App Service pro přihlášení služby Active Directory] [ 7] kurzu. Ujistěte se, že k dokončení volitelný krok registrace nativní klientské aplikace. Pro iOS, doporučujeme vám, která má formát identifikátoru URI pro přesměrování `<app-scheme>://<bundle-id>`. Další informace najdete v tématu [rychlý start ADAL iOS][8].
2. Nainstalujte knihovnu ADAL pomocí Cocoapods. Upravit Podfile zahrnout následující definice nahrazení **váš projekt** s názvem projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   a pokud chcete Pod:

        pod 'ADALiOS'

3. Pomocí terminálu spusťte `pod install` z adresáře obsahující váš projekt a potom otevřete vygenerovaný pracovní prostor Xcode (nikoli projekt).
4. Přidejte následující kód do vaší aplikace, podle jazyka, který používáte. V každém proveďte tyto náhrady odkazuje:

   * Nahraďte **INSERT-AUTORITY-KORENOVA** s názvem tenanta, ve kterém jste zřídili vaší aplikace. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com. Tuto hodnotu je možné zkopírovat ze záložky domény ve službě Azure Active Directory v webu [Azure Portal].
   * Nahraďte **INSERT-RESOURCE-ID – TADY** s ID klienta pro back-endu mobilní aplikace. Můžete získat ID klienta z **Upřesnit** kartu **nastavení služby Azure Active Directory** na portálu.
   * Nahraďte **vložit klienta ID TADY** s ID klienta, který jste zkopírovali z nativní klientskou aplikaci.
   * Nahraďte **vložení – PŘESMĚROVÁNÍ-URI-TADY** s vaší lokality */.auth/login/done* koncový bod, používat schéma HTTPS. Tato hodnota by měl být podobný *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Kód SWIFT**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="facebook-sdk"></a>Postupy: ověřování uživatelů pomocí sady SDK Facebooku pro iOS

Sady SDK Facebooku pro iOS můžete použít pro přihlášení uživatelů do vaší aplikace pomocí služby Facebook.  Tok ověřování klienta je vhodnější než použít `loginWithProvider:completion:` metody.  Tok ověření klienta obsahuje více přirozený chování uživatelského prostředí a umožňuje další přizpůsobení.

1. Konfigurace back-endu mobilní aplikace pro Facebook přihlásit pomocí následujících [konfigurace služby App Service pro přihlášení k Facebooku] [ 9] kurzu.
2. Nainstalujte sadu SDK Facebooku pro iOS pomocí následujících [Facebook SDK pro iOS – Začínáme] [ 10] dokumentaci. Místo vytváření aplikace, můžete přidat na platformu iOS na existující registraci.
3. Dokumentace ke službě Facebook obsahuje kód Objective-C v delegáta aplikace. Pokud používáte **Swift**, můžete použít následující překlady pro AppDelegate.swift:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. Kromě přidání `FBSDKCoreKit.framework` do projektu přidat také odkaz na `FBSDKLoginKit.framework` stejným způsobem.
5. Přidejte následující kód do vaší aplikace, podle jazyka, který používáte.

    **Objective-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Kód SWIFT**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="twitter-fabric"></a>Postupy: ověřování uživatelů pomocí Twitteru prostředků infrastruktury pro iOS

Prostředky infrastruktury pro iOS můžete použít pro přihlášení uživatelů do vaší aplikace pomocí služby Twitter. Tok ověřování klientů je vhodnější než použít `loginWithProvider:completion:` metody, protože obsahuje více přirozený chování uživatelského prostředí a umožňuje další přizpůsobení.

1. Konfigurace back-endu mobilní aplikace pro Twitter přihlásit pomocí následujících [konfigurace služby App Service pro přihlášení k Twitteru](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) kurzu.
2. Přidejte do projektu prostředků infrastruktury pomocí následujících [prostředků infrastruktury pro iOS – Začínáme] dokumentace ke službě a nastavení TwitterKit.

   > [!NOTE]
   > Ve výchozím nastavení Fabric vytvoří aplikaci Twitter. Vytvoření aplikace, když si zaregistrujete uživatelským klíčem a uživatelský tajný klíč, které jste předtím vytvořili pomocí následující fragmenty kódu se můžete vyhnout.    Alternativně můžete nahradit uživatelským klíčem a uživatelským utajením hodnoty, které zadáte do služby App Service hodnotami, které se zobrazí v [řídicí panel infrastruktury]. Pokud zvolíte tuto možnost, nezapomeňte nastavit adresu URL zpětného volání na hodnotu zástupného symbolu, jako například `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Pokud se rozhodnete pro použití tajných kódů, které jste vytvořili dříve, přidejte následující kód do vaší aplikace delegáta:

    **Objective-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Kód SWIFT**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Přidejte následující kód do vaší aplikace, podle jazyka, který používáte.

    **Objective-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Kód SWIFT**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="google-sdk"></a>Postupy: ověřování uživatelů pomocí služby Google přihlášení SDK pro iOS

Google přihlášení SDK pro iOS můžete použít pro přihlášení uživatelů do vaší aplikace pomocí účtu Google.  Google nedávno jsme oznámili změny zásad zabezpečení jejich OAuth.  Tyto změny zásad bude v budoucnu vyžadovat použití sady SDK služby Google.

1. Konfigurace back-endu mobilní aplikace pro Google přihlásit pomocí následujících [konfigurace služby App Service pro Google přihlášení](../app-service/app-service-mobile-how-to-configure-google-authentication.md) kurzu.
2. Instalace sady SDK služby Google pro iOS pomocí následujících [přihlášení Google pro iOS – začněte s integrací](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentaci. V části "Ověření se a back-end Server", můžete vynechat.
3. Přidejte následující tohoto delegáta `signIn:didSignInForUser:withError:` metoda podle jazyka, který používáte.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Kód SWIFT**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Ujistěte se, že přidáte následující `application:didFinishLaunchingWithOptions:` v aplikaci delegátu, nahraďte "SERVER_CLIENT_ID" se stejným ID, které jste použili ke konfiguraci služby App Service v kroku 1.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Kód SWIFT**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Přidejte následující kód do vaší aplikace v UIViewController, který implementuje `GIDSignInUIDelegate` protokol, podle jazyka, který používáte.  Odhlášení před Probíhá přihlašování znovu, a i když není nutné znovu zadejte svoje přihlašovací údaje, se zobrazí dialogové okno souhlasu.  Tuto metodu volejte, pouze pokud vypršela platnost tokenu relace.

   **Objective-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Kód SWIFT**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps rychlý Start]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamické schéma]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[řídicí panel infrastruktury]: https://www.fabric.io/home
[prostředků infrastruktury pro iOS – Začínáme]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
