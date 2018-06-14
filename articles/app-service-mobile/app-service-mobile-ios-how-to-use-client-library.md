---
title: Postup použití iOS SDK pro Azure Mobile Apps
description: Postup použití iOS SDK pro Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: 63dd283605553297a7dc8feab90c8bcbd716d5de
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2017
ms.locfileid: "26660137"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Postup použití iOS Klientská knihovna pro Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Tato příručka je určena můžete provádět běžné scénáře s využitím nejnovější [Azure Mobile Apps iOS SDK][1]. Pokud jste ještě Azure Mobile Apps, nejprve dokončit [Azure Mobile Apps rychlý Start] vytvořte back-end, vytvořit tabulku a stažení projektu Xcode předdefinovaných iOS. V této příručce se zaměříme na straně klienta iOS SDK. Další informace o sadě SDK na straně serveru pro back-end najdete v tématu HOWTOs sady SDK serveru.

## <a name="reference-documentation"></a>Referenční dokumentace
Referenční dokumentaci k nástroji pro klienta iOS SDK se nachází zde: [Azure Mobile Apps iOS odkaz klienta][2].

## <a name="supported-platforms"></a>Podporované platformy
Sada SDK iOS podporuje jazyka Objective-C projekty – projekty Swift 2.2 a Swift 2.3 projekty pro systém iOS verze 8.0 nebo novější.

"Serveru pohybu" ověřování používá webové zobrazení pro vidění uživatelského rozhraní.  Pokud zařízení není možné zobrazení webové zobrazení uživatelského rozhraní, se vyžaduje jinou metodu ověřování, který je mimo rozsah produktu.  
Tato sada SDK není proto vhodný pro sledování type nebo podobně jako zařízení s omezeným přístupem.

## <a name="Setup"></a>Instalační program a požadavky
Tato příručka předpokládá, že jste vytvořili back-end s tabulkou. Tato příručka předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tento průvodce také předpokládá, že ve vašem kódu odkazujete `MicrosoftAzureMobile.framework` a import `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Postupy: vytvoření klienta
Chcete-li získat přístup back-end Azure Mobile Apps ve vašem projektu, vytvořte `MSClient`. Nahraďte `AppUrl` s adresu URL aplikace. Můžete ponechat `gatewayURLString` a `applicationKey` prázdný. Pokud jste nastavili bránu pro ověřování, naplnit `gatewayURLString` s adresou URL brány.

**Jazyka Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Kód SWIFT**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Postupy: vytvoření odkaz na tabulku
Pro přístup k datům a jejich aktualizaci vytvořte odkaz na back-endovou tabulku. Nahraďte `TodoItem` názvem vaší tabulky.

**Jazyka Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Kód SWIFT**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Postupy: dotaz na Data
Chcete-li vytvořit dotaz na databázi, zadejte dotaz `MSTable` objektu. Následující dotaz načte všechny položky `TodoItem` a protokoly text jednotlivých položek.

**Jazyka Objective-C**:

```
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

```
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

## <a name="filtering"></a>Postupy: Filtr nevrátil Data
Chcete-li filtrovat výsledky, existuje mnoho možností k dispozici.

Chcete-li filtrovat pomocí predikátu, použijte `NSPredicate` a `readWithPredicate`. Následující filtry vrácená data a vyhledávat pouze neúplné položky Todo.

**Jazyka Objective-C**:

```
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

```
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
K provedení dotazu komplexní (včetně řazení a stránkování), vytvořte `MSQuery` objektu, přímo nebo pomocí predikátu:

**Jazyka Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Kód SWIFT**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`můžete nastavit různé chování dotazu.

* Určení pořadí výsledků
* Omezení, která pole se vrátí
* Omezit, kolik zaznamenává vrátit
* Zadejte celkový počet v odpovědi
* Zadejte parametry řetězec vlastního dotazu v žádosti
* Použít další funkce

Spuštění `MSQuery` dotazu voláním `readWithCompletion` objektu.

## <a name="sorting"></a>Postupy: řazení dat s MSQuery
Chcete-li seřadit výsledky, podíváme se na příklad. Seřadit podle pole 'text' vzestupně a pak podle 'dokončení' sestupném, vyvolání `MSQuery` takto:

**Jazyka Objective-C**:

```
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

```
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
Chcete-li omezit polí, která mají být vráceny v dotazu, zadejte názvy polí v **selectFields** vlastnost. Tento příklad vrátí jenom text a dokončené pole:

**Jazyka Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Kód SWIFT**:

```
query.selectFields = ["text", "complete"]
```

Chcete-li zahrnout parametrů řetězce dotazu další požadavek serveru (například, protože je používá vlastní skript na straně serveru), naplnit `query.parameters` takto:

**Jazyka Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Kód SWIFT**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Postupy: Konfigurace velikost stránky
Velikost stránky s Azure Mobile Apps, řídí počet záznamů, které jsou vyžádány současně z tabulky back-end. Volání `pull` dat by pak dávky dat, podle této velikosti stránky, dokud nejsou žádné další záznamy pro vyžádání obsahu.

Je možné nakonfigurovat pomocí velikost stránky **MSPullSettings** jak je uvedeno níže. Výchozí velikost stránky je 50 a následující příklad změní na 3.

Můžete nakonfigurovat velikost různé stránky z důvodů výkonu. Pokud máte velké množství malých datových záznamů, snižuje velikost vysoké stránky dobu odezvy serveru.

Toto nastavení řídí pouze velikost stránky na straně klienta. Pokud klient požádá o větší velikosti stránky než back-end mobilní aplikace podporuje, je velikost stránky limitován maximální back-end je nakonfigurován pro podporu.

Toto nastavení je také *číslo* datových záznamů, ne *velikost v bajtech*.

Pokud zvýšíte velikost stránky klienta, měli také zvýšit velikost stránky na serveru. V tématu ["postupy: přizpůsobení velikosti stránkování tabulky"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pokyny k tomu.

**Jazyka Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**Kód SWIFT**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Postupy: vkládání dat
Chcete-li vložit nový řádek tabulky, vytvořte `NSDictionary` a vyvolání `table insert`. Pokud [dynamické schématu] je povoleno, back-end mobilní služby Azure App Service automaticky vytvoří nové sloupce na základě `NSDictionary`.

Pokud `id` není uvedeno, back-end generuje automaticky nový jedinečný identifikátor. Zadejte vlastní `id` používat e-mailové adresy, uživatelská jména, nebo vlastní vlastní hodnoty jako ID. Poskytnutí vlastní ID může usnadňují spojení a logiku orientovanými na firmu databáze.

`result` Obsahuje novou položku, která byla vložena. V závislosti na logice server může mít další nebo upravených datových ve srovnání s co byl předán serveru.

**Jazyka Objective-C**:

```
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

```
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
Pokud chcete aktualizovat existující řádek, upravit položky a volání `update`:

**Jazyka Objective-C**:

```
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

```
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

Můžete taky zadejte identifikátor řádku a aktualizované pole:

**Jazyka Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Kód SWIFT**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Minimálně `id` musí být nastaven při provádění aktualizací.

## <a name="deleting"></a>Postupy: odstranění dat
Pokud chcete odstranit položku, vyvolání `delete` s položkou:

**Jazyka Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kód SWIFT**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Můžete taky odstraňte tím, že poskytuje ID řádku:

**Jazyka Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Kód SWIFT**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Minimálně `id` musí být nastaven při provádění odstraní.

## <a name="customapi"></a>Postupy: volání vlastního rozhraní API
Pomocí vlastního rozhraní API můžou zpřístupnit žádné funkce back-end. Nemá k mapování na operace tabulka. Nejen získáte větší kontrolu nad zasílání zpráv, můžete dokonce i pro čtení nebo nastavení hlavičky a změňte formát textu odpovědi. Chcete-li zjistit, jak vytvořit vlastní rozhraní API na back-end, přečtěte si [vlastní rozhraní API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Pro volání vlastní rozhraní API, volání `MSClient.invokeAPI`. Požadavek a odpověď obsahu jsou považovány za JSON. Používat jiné typy médií [, použijte jiné přetížení `invokeAPI` ] [ 5].  Chcete-li `GET` žádosti místo `POST` požádat, parametr sady `HTTPMethod` k `"GET"` a parametr `body` k `nil` (protože požadavky GET ještě nemá obsah zpráv.) Pokud vaše vlastní rozhraní API podporuje další příkazy HTTP, změňte `HTTPMethod` správně.

**Jazyka Objective-C**:

```
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

```
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

## <a name="templates"></a>Postupy: registrace nabízených šablony k odesílání oznámení napříč platformami
Chcete-li zaregistrovat šablony, předejte šablon s vaší **client.push registerDeviceToken** metoda ve vaší klientské aplikace.

**Jazyka Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Kód SWIFT**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Vaše šablony typu NSDictionary a může obsahovat několik šablon v následujícím formátu:

**Jazyka Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Kód SWIFT**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Všechny značky se odstraní ze žádosti o zabezpečení.  Přidání značek na zařízení nebo šablony v rámci instalace naleznete v tématu [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps][4].  K odeslání oznámení pomocí těchto registrovaných šablon, pracovat s [rozhraní API centra oznámení][3].

## <a name="errors"></a>Postupy: zpracování chyb
Při volání Azure App Service mobilního back-endu blok dokončení obsahuje `NSError` parametr. Když dojde k chybě, tento parametr je nemá hodnotu nil. V kódu zkontrolujte tento parametr a zpracování chyb podle potřeby, jak je předvedeno v předchozích fragmentů kódu.

Soubor [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] definuje konstanty `MSErrorResponseKey`, `MSErrorRequestKey`, a `MSErrorServerItemKey`. Pokud chcete získat další data týkající se chyby:

**Jazyka Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Kód SWIFT**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Kromě toho soubor definuje konstanty pro každý kód chyby:

**Jazyka Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Kód SWIFT**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Postupy: ověřuje uživatele pomocí knihovny Active Directory Authentication Library
Active Directory Authentication Library (ADAL) můžete použít pro přihlášení uživatelů do vaší aplikace pomocí Azure Active Directory. Tok ověřování klientů pomocí zprostředkovatele identity SDK je vhodnější než použít `loginWithProvider:completion:` metoda.  Tok ověření klienta poskytuje více nativní UX chování a umožňuje další přizpůsobení.

1. Podle konfigurace váš back-end mobilní aplikace při přihlášení AAD [jak nakonfigurovat App Service pro přihlášení služby Active Directory] [ 7] kurzu. Ujistěte se, že dokončení volitelný krok registrace nativní klientskou aplikaci. Pro iOS, doporučujeme, aby přesměrování je identifikátor URI ve tvaru `<app-scheme>://<bundle-id>`. Další informace najdete v tématu [rychlý start ADAL iOS][8].
2. Nainstalujte ADAL pomocí Cocoapods. Upravit vaše Podfile zahrnout následující definice nahrazení **YOUR PROJECT** s názvem projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   a Pod:

        pod 'ADALiOS'
3. Pomocí terminálu, spusťte `pod install` z adresáře obsahující projekt a pak otevřete generovaný pracovní prostor Xcode (nikoli projekt).
4. Přidejte následující kód do aplikace podle jazyka, který používáte. V každé udělejte tyto náhrady:

   * Nahraďte **INSERT. AUTORITY zde** s názvem klienta, ve kterém jste zřídili vaší aplikace. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com. Tuto hodnotu lze kopírovat z karty domény v Azure Active Directory v [portál Azure].
   * Nahraďte **INSERT-RESOURCE-ID-zde** s ID klienta pro váš back-end mobilní aplikace. Můžete získat ID klienta z **Upřesnit** v části **nastavení Azure Active Directory** na portálu.
   * Nahraďte **INSERT klienta ID zde** s ID klienta, který jste zkopírovali z nativní klientskou aplikaci.
   * Nahraďte **vložení PŘESMĚROVÁNÍ URI zde** s vaší lokality */.auth/login/done* koncový bod, pomocí schéma HTTPS. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done*.

**Jazyka Objective-C**:

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


**Kód SWIFT**:

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

## <a name="facebook-sdk"></a>Postupy: ověřuje uživatele pomocí služby Facebook SDK pro iOS
Facebook SDK pro iOS můžete použít pro přihlášení uživatelů do vaší aplikace pomocí služby Facebook.  Použití tok ověření klienta je vhodnější než použít `loginWithProvider:completion:` metoda.  Tok ověření klienta poskytuje více nativní UX chování a umožňuje další přizpůsobení.

1. Konfigurace váš back-end mobilní aplikace pro Facebook přihlásit pomocí následujících [jak nakonfigurovat App Service pro Facebook přihlášení] [ 9] kurzu.
2. Instalace sady SDK Facebook pro iOS pomocí následujících [Facebook SDK pro iOS – Začínáme] [ 10] dokumentaci. Místo vytváření aplikace, můžete přidat platformu iOS vaší existující registrace.
3. Dokumentace pro Facebook obsahuje nějaký kód jazyka Objective-C v delegáta aplikace. Pokud používáte **Swift**, můžete použít následující překladů pro AppDelegate.swift:

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
4. Kromě přidání `FBSDKCoreKit.framework` do projektu, také přidat odkaz na `FBSDKLoginKit.framework` stejným způsobem.
5. Přidejte následující kód do aplikace podle jazyka, který používáte.

**Jazyka Objective-C**:

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

**Kód SWIFT**:

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

## <a name="twitter-fabric"></a>Postupy: ověřuje uživatele pomocí služby Twitter prostředků infrastruktury pro iOS
Prostředky infrastruktury pro iOS můžete použít pro přihlášení uživatelů do vaší aplikace pomocí služby Twitter. Tok ověření klienta je vhodnější než použít `loginWithProvider:completion:` metoda, protože poskytuje více nativní UX chování a umožňuje další přizpůsobení.

1. Konfigurace váš back-end mobilní aplikace pro Twitter přihlásit pomocí následujících [jak nakonfigurovat App Service pro přihlášení služby Twitter](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) kurzu.
2. Do projektu přidejte prostředků infrastruktury pomocí následujících [prostředků infrastruktury pro iOS – Začínáme] dokumentace a nastavení TwitterKit.

   > [!NOTE]
   > Ve výchozím nastavení prostředků infrastruktury pro vás vytvoří aplikaci služby Twitter. Vytváření aplikací tak, že zaregistrujete uživatelský klíč a uživatelský tajný klíč, které jste vytvořili dříve pomocí následující fragmenty kódu se můžete vyhnout.    Alternativně můžete nahradit uživatelský klíč a uživatelským utajením hodnoty, které zadáte do služby App Service s hodnotami, můžete vidět v [řídicí panel infrastruktury]. Pokud zvolíte tuto možnost, nezapomeňte nastavit adresu URL zpětné volání na hodnotu zástupného symbolu, jako například `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Pokud chcete použít tajné klíče, které jste vytvořili dříve, přidejte následující kód do vaší aplikace delegáta:

    **Jazyka Objective-C**:

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

    **Kód SWIFT**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Přidejte následující kód do aplikace podle jazyka, který používáte.

**Jazyka Objective-C**:

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

**Kód SWIFT**:

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

## <a name="google-sdk"></a>Postupy: ověřuje uživatele pomocí služby Google přihlášení SDK pro iOS
Google přihlášení SDK pro iOS můžete použít pro přihlášení uživatelů do vaší aplikace pomocí účtu Google.  Google nedávno vydal změny zásad zabezpečení jejich OAuth.  Tyto změny zásad v budoucnu vyžaduje použití sady Google SDK.

1. Konfigurace váš back-end mobilní aplikace pro Google přihlásit pomocí následujících [jak nakonfigurovat App Service pro přihlášení Google](../app-service/app-service-mobile-how-to-configure-google-authentication.md) kurzu.
2. Nainstalujte podle Google SDK pro iOS [přihlášení Google pro iOS – spuštění integrace](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentaci. V části "Ověření s back-end Server", můžete vynechat.
3. Přidejte následující tohoto delegáta `signIn:didSignInForUser:withError:` metoda, podle jazyka, kterou používáte.

**Jazyka Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Kód SWIFT**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Ujistěte se také přidat následující `application:didFinishLaunchingWithOptions:` v aplikaci delegátovi, nahraďte "SERVER_CLIENT_ID" se stejným ID, který můžete použít ke konfiguraci služby App Service v kroku 1.

**Jazyka Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **Kód SWIFT**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Přidejte následující kód do vaší aplikace v UIViewController, který implementuje `GIDSignInUIDelegate` protokolu podle jazyka, kterou používáte.  Jste se odhlásili před se přihlášení znovu a i když nemusíte znovu zadejte své přihlašovací údaje, se zobrazí dialogové okno souhlasu.  Tuto metodu volejte pouze tokenu relace vypršela.

   **Jazyka Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **Kód SWIFT**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

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
[portál Azure]: https://portal.azure.com/
[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[dynamické schématu]: http://go.microsoft.com/fwlink/p/?LinkId=296271
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
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
