---
title: Jak používat sadu iOS SDK pro Azure Mobile Apps
description: Jak používat sadu iOS SDK pro Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: elamalani
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 898bf082874a1e9bf26dd094a6a0fe55417c9d8e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851063"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu iOS pro Azure Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center je investována v nových a integrovaných službách z centrálního vývoje mobilních aplikací. Vývojáři mohou pomocísestavování, **testování** a **distribuce** služeb nastavit kanál průběžné integrace a doručování. Po nasazení aplikace mohou vývojáři sledovat stav a využití své aplikace pomocí **analytických** a diagnostických služeb a spolupracovat s uživateli pomocí služby **push** . Vývojáři můžou také využít **ověřování** k ověřování uživatelů a **datových** služeb, aby zachovaly a synchronizovaly data aplikací v cloudu. Podívejte se [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=/app-service-mobile-ios-how-to-use-client-library) ještě dnes.
>

## <a name="overview"></a>Přehled
V této příručce se naučíte, jak provádět běžné scénáře pomocí nejnovější [sady Azure Mobile Apps iOS SDK][1]. Pokud s Azure Mobile Apps začínáte, nejdřív dokončete [rychlé zprovoznění Mobile Apps Azure] a vytvořte back-end, vytvořte tabulku a stáhněte předem sestavený projekt Xcode pro iOS. V tomto průvodci se zaměříme na sadu iOS SDK na straně klienta. Další informace o sadě SDK na straně serveru pro back-end najdete v tématu sada SDK pro server HOWTOs.

## <a name="reference-documentation"></a>Referenční dokumentace

Referenční dokumentace k sadě Client SDK pro iOS se nachází tady: [Odkaz na klienta Azure Mobile Apps iOS][2].

## <a name="supported-platforms"></a>Podporované platformy

Sada iOS SDK podporuje projekty s cíli C, SWIFT 2,2 a projekty SWIFT 2,3 pro verze iOS 8,0 nebo novější.

Ověřování "Server-Flow" používá pro prezentované uživatelské rozhraní WebView.  Pokud zařízení není schopné prezentovat uživatelské rozhraní WebView, vyžaduje se další metoda ověřování, která je mimo rozsah produktu.  
Tato sada SDK není vhodná pro zařízení s omezením typu kukátka nebo podobně.

## <a name="Setup"></a>Nastavení a předpoklady

V tomto průvodci se předpokládá, že jste vytvořili back-end s tabulkou. V tomto průvodci se předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tato příručka také předpokládá, že ve vašem kódu odkazujete `MicrosoftAzureMobile.framework` a importujete `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Jak: Vytvořit klienta

Pokud chcete získat přístup k back-endu Azure Mobile Apps v projektu `MSClient`, vytvořte. Nahraďte `AppUrl` adresou URL aplikace. Můžete nechat zůstat `gatewayURLString` a `applicationKey` prázdné. Pokud nastavíte bránu pro ověřování, naplňte `gatewayURLString` ji adresou URL brány.

**Cíl-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Jak: Vytvořit odkaz na tabulku

Pro přístup k datům a jejich aktualizaci vytvořte odkaz na back-endovou tabulku. Nahraďte `TodoItem` názvem vaší tabulky.

**Cíl-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Jak: Dotazování dat

Chcete-li vytvořit databázový dotaz, dotaz `MSTable` na objekt. Následující dotaz načte všechny položky v `TodoItem` a zaznamená text každé položky.

**Cíl-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

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

## <a name="filtering"></a>Jak: Filtrovat vrácená data

K filtrování výsledků je dostupné mnoho možností.

Chcete-li filtrovat pomocí predikátu, `NSPredicate` použijte `readWithPredicate`a. Následující filtry vrátí data pro vyhledání pouze neúplných položek todo.

**Cíl-C**:

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

**Swift**:

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

## <a name="query-object"></a>Jak: Použití MSQuery

Chcete-li provést složitý dotaz (včetně řazení a stránkování), vytvořte `MSQuery` objekt přímo nebo pomocí predikátu:

**Cíl-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`umožňuje řídit několik chování dotazů.

* Zadat pořadí výsledků
* Omezení, která pole se mají vrátit
* Omezit počet záznamů, které se mají vrátit
* Zadejte celkový počet v odpovědi.
* Zadat vlastní parametry řetězce dotazu v žádosti
* Použití dalších funkcí

Spusťte dotaz voláním `readWithCompletion`objektu. `MSQuery`

## <a name="sorting"></a>Jak: Řazení dat pomocí MSQuery

Pokud chcete výsledky seřadit, Podívejme se na příklad. Pokud chcete řadit podle pole ' text ' vzestupně, pak podle ' dokončeno ' sestupně `MSQuery` , vyvolat jako:

**Cíl-C**:

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

**Swift**:

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

## <a name="selecting"></a><a name="parameters"></a>Jak: Omezení polí a rozbalení parametrů řetězce dotazu pomocí MSQuery

Chcete-li omezit pole, která mají být vrácena v dotazu, zadejte názvy polí ve vlastnosti **selectFields** . Tento příklad vrátí pouze text a dokončená pole:

**Cíl-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Chcete-li do žádosti serveru zahrnout další parametry řetězce dotazu (například proto, že je používá vlastní skript na straně serveru), naplnit `query.parameters` jako příklad:

**Cíl-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Jak: Konfigurovat velikost stránky

V případě Azure Mobile Apps určuje velikost stránky počet záznamů, které se v tabulkách back-endu vyžádaly současně. Volání `pull` dat by pak mohla dávkovat data na základě této velikosti stránky, dokud nebudou existovat žádné další záznamy k vyžádání.

Velikost stránky je možné nakonfigurovat pomocí **MSPullSettings** , jak je znázorněno níže. Výchozí velikost stránky je 50 a následující příklad ho změní na 3.

Z důvodů výkonu můžete nakonfigurovat jinou velikost stránky. Pokud máte velký počet malých datových záznamů, vysoká velikost stránky snižuje počet zpátečních cest serveru.

Toto nastavení řídí pouze velikost stránky na straně klienta. Pokud klient požaduje větší velikost stránky než Mobile Apps podporuje back-end, velikost stránky je omezené na maximálním počtu back-endu, který je nakonfigurován pro podporu.

Toto nastavení je také *počet* datových záznamů, nikoli *Velikost bajtu*.

Pokud zvětšíte velikost stránky klienta, měli byste také zvětšit velikost stránky na serveru. Viz ["Jak: Upravte velikost](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) stránkování tabulky, pokud chcete tento postup provést.

**Cíl-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Jak: Vložit data

Chcete-li vložit nový řádek tabulky, vytvořte `NSDictionary` a zavolejte `table insert`. Pokud je povolené [dynamické schéma] , Azure App Service mobilní back-end automaticky vygeneruje nové sloupce založené `NSDictionary`na.

Pokud `id` není zadaný, back-end automaticky vytvoří nové jedinečné ID. Poskytněte vlastní `id` použití e-mailových adres, uživatelských jmen nebo vlastních hodnot jako ID. Poskytnutí vlastního ID může usnadnit spojení a obchodní logiku databáze.

`result` Obsahuje novou položku, která byla vložena. V závislosti na logice serveru může být v porovnání s daty, která byla předána serveru, k dispozici další nebo změněná data.

**Cíl-C**:

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

**Swift**:

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

## <a name="modifying"></a>Jak: Úprava dat

Chcete-li aktualizovat existující řádek, upravte položku a zavolejte `update`:

**Cíl-C**:

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

**Swift**:

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

Případně zadejte ID řádku a aktualizované pole:

**Cíl-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Při provádění aktualizací musí `id` být nastaven minimálně atribut.

## <a name="deleting"></a>Jak: Odstranit data

Chcete-li odstranit položku, `delete` volejte s položkou:

**Cíl-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Případně můžete odstranit zadáním ID řádku:

**Cíl-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Při odstraňování musí být `id` atribut nastaven na minimum.

## <a name="customapi"></a>Jak: Volání vlastního rozhraní API

Pomocí vlastního rozhraní API můžete vystavit jakékoli funkce back-endu. Není nutné namapovat na operaci tabulky. Nejenom máte větší kontrolu nad zasíláním zpráv, můžete dokonce číst a nastavovat záhlaví a měnit formát textu odpovědi.

Chcete-li volat vlastní rozhraní API `MSClient.invokeAPI`, zavolejte. Obsah žádosti a odpovědi se považuje za JSON. Chcete-li použít jiné typy médií, [použijte jiné přetížení `invokeAPI` ][5].  Chcete-li `GET` vytvořit požadavek namísto `POST` požadavku, nastavte parametr `HTTPMethod` na `"GET"` hodnotu a parametr `body` na `nil` (protože požadavky GET nemají tělo zprávy.) Pokud vaše vlastní rozhraní API podporuje jiné příkazy HTTP, proveďte `HTTPMethod` odpovídající změnu.

**Cíl-C**:

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

**Swift**:

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

## <a name="templates"></a>Jak: Registrace šablon nabízených oznámení pro odesílání oznámení pro různé platformy

Chcete-li registrovat šablony, předejte šablony pomocí metody **Client. push registerDeviceToken** ve vaší klientské aplikaci.

**Cíl-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Šablony jsou typu NSDictionary a mohou obsahovat více šablon v následujícím formátu:

**Cíl-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Všechny značky jsou z požadavku na zabezpečení odstraněny.  Pokud chcete přidat značky k instalacím nebo šablonám v rámci instalací, přečtěte si téma [práce s back-end serverem .NET SDK pro Azure Mobile Apps][4].  Pokud chcete odesílat oznámení pomocí těchto registrovaných šablon, pracujte s [rozhraními api Notification Hubs][3].

## <a name="errors"></a>Jak: Zpracování chyb

Když zavoláte Azure App Service mobilní back-end, blok dokončení obsahuje `NSError` parametr. Pokud dojde k chybě, má tento parametr hodnotu non Nil. V kódu byste měli kontrolovat tento parametr a zpracovat chybu podle potřeby, jak je znázorněno v předchozích fragmentech kódu.

Soubor [`<WindowsAzureMobileServices/MSError.h>`][6] definuje konstanty `MSErrorResponseKey`, `MSErrorRequestKey`, a `MSErrorServerItemKey`. Chcete-li získat další data související s touto chybou:

**Cíl-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Kromě toho soubor definuje konstanty pro každý kód chyby:

**Cíl-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Jak: Ověřování uživatelů pomocí Active Directory Authentication Library

K podepisování uživatelů do aplikace pomocí Azure Active Directory můžete použít Active Directory Authentication Library (ADAL). Ověřování klientského toku pomocí sady SDK zprostředkovatele identity je vhodnější použít `loginWithProvider:completion:` metodu.  Ověřování toku klienta poskytuje více nativního uživatelského prostředí a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení AAD pomocí [postupu konfigurace App Service v kurzu přihlášení ke službě Active Directory][7] . Ujistěte se, že jste dokončili volitelný krok registrace nativní klientské aplikace. Pro iOS doporučujeme, aby identifikátor URI přesměrování byl ve formátu `<app-scheme>://<bundle-id>`. Další informace najdete v tématu [rychlý Start][8]pro rozhraní ADAL pro iOS.
2. Nainstalujte ADAL pomocí Cocoapods. Upravte souboru podfile tak, aby zahrnoval následující definici, a nahraďte **svůj projekt** názvem vašeho projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   a pod:

        pod 'ADALiOS'

3. Pomocí terminálu spusťte `pod install` z adresáře, který obsahuje váš projekt, a pak otevřete vygenerovaný pracovní prostor Xcode (ne projekt).
4. Do aplikace přidejte následující kód podle jazyka, který používáte. V každé z nich proveďte Tato nahrazení:

   * V části pro **vložení autority** nahraďte název tenanta, ve kterém jste aplikaci zřídili. Formát by měl být https://login.microsoftonline.com/contoso.onmicrosoft.com. Tuto hodnotu lze zkopírovat z karty doména v Azure Active Directory [Azure Portal].
   * Pro back-end mobilní aplikace nahraďte **INSERT-Resource-ID – tady** ID klienta. ID klienta můžete získat z karty **Upřesnit** v části **Nastavení Azure Active Directory** na portálu.
   * Pomocí ID klienta, které jste zkopírovali z nativní klientské aplikace, nahraďte **INSERT-Client-ID** .
   * Pomocí schématu HTTPS nahraďte **text INSERT-redirect-URI – tady** s koncovým bodem */.auth/Login/Done* vašeho webu. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done* .

**Cíl-C**:

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

**Swift**:

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

## <a name="facebook-sdk"></a>Jak: Ověřování uživatelů pomocí sady Facebook SDK pro iOS

Můžete použít sadu Facebook SDK pro iOS k podepisování uživatelů do vaší aplikace pomocí Facebooku.  Použití ověřování toku klienta je vhodnější pro použití `loginWithProvider:completion:` metody.  Ověřování toku klienta poskytuje nativní prostředí pro UX a umožňuje další přizpůsobení.

1. Konfigurací back-endu mobilní aplikace pro aplikaci Facebook Přihlaste podle pokynů v tématu [Postup konfigurace App Service pro přihlášení na Facebooku][9] .
2. Nainstalujte sadu Facebook SDK pro iOS pomocí [sady Facebook SDK pro iOS – Začínáme][10] dokumentaci. Místo vytvoření aplikace můžete přidat platformu iOS do stávající registrace.
3. Dokumentace ke službě Facebook obsahuje v delegátu aplikace nějaký účelový kód v jazyce C. Pokud používáte **SWIFT**, můžete použít následující překlady pro AppDelegate. SWIFT:

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
4. Kromě přidání `FBSDKCoreKit.framework` do projektu přidejte také odkaz na `FBSDKLoginKit.framework` stejný způsob.
5. Do aplikace přidejte následující kód podle jazyka, který používáte.

    **Cíl-C**:

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

    **Swift**:

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

## <a name="twitter-fabric"></a>Jak: Ověřování uživatelů pomocí prostředků infrastruktury Twitteru pro iOS

K podepisování uživatelů do aplikace pomocí služby Twitter můžete použít prostředky infrastruktury pro iOS. Ověřování toku klienta je vhodnější použít `loginWithProvider:completion:` metodu, protože poskytuje více nativního uživatelského prostředí a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení k Twitteru pomocí [postupu konfigurace App Service pro přihlášení k Twitteru](../app-service/configure-authentication-provider-twitter.md) .
2. Pomocí [Prostředky infrastruktury pro iOS – Začínáme] dokumentaci a nastavením TwitterKit přidejte do projektu prostředky infrastruktury.

   > [!NOTE]
   > Ve výchozím nastavení vytvoří Fabric pro vás aplikaci Twitter. Vytvoření aplikace můžete zabránit tak, že zaregistrujete klíč příjemce a tajný klíč příjemce, který jste vytvořili dříve, pomocí následujících fragmentů kódu.    Alternativně můžete nahradit klíč příjemce a tajné hodnoty příjemce, které poskytnete, aby App Service s hodnotami, které vidíte na [Řídicí panel prostředků infrastruktury]. Pokud zvolíte tuto možnost, nezapomeňte nastavit adresu URL zpětného volání na zástupnou hodnotu, například `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Pokud se rozhodnete použít tajné klíče, které jste vytvořili dříve, přidejte do delegáta aplikace následující kód:

    **Cíl-C**:

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

    **Swift**:

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

3. Do aplikace přidejte následující kód podle jazyka, který používáte.

    **Cíl-C**:

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

    **Swift**:

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

## <a name="google-sdk"></a>Jak: Ověřování uživatelů pomocí přihlašování k webu Google SDK pro iOS

K podepisování uživatelů do vaší aplikace pomocí účtu Google můžete použít sadu přihlašovacích SDK Google pro iOS.  Společnost Google nedávno oznámila změny svých zásad zabezpečení OAuth.  Tyto změny zásad budou v budoucnu vyžadovat použití sady Google SDK.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení Google podle pokynů v tématu [Postup konfigurace App Service pro přihlášení Google](../app-service/configure-authentication-provider-google.md) .
2. Nainstalujte sadu Google SDK pro iOS podle pokynů v části [přihlášení Google pro iOS – začněte](https://developers.google.com/identity/sign-in/ios/start-integrating) s integrací dokumentace. Můžete přeskočit část ověřování pomocí back-endu serveru.
3. Do `signIn:didSignInForUser:withError:` metody delegáta přidejte následující, podle jazyka, který používáte.

    **Cíl-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Nezapomeňte také do delegáta aplikace přidat následující `application:didFinishLaunchingWithOptions:` : nahraďte "SERVER_CLIENT_ID" stejným ID, které jste použili ke konfiguraci App Service v kroku 1.

    **Cíl-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Přidejte do aplikace následující kód v UIViewController, který implementuje `GIDSignInUIDelegate` protokol, podle jazyka, který používáte.  Před opětovným přihlášením jste se odhlásili a i když nebudete muset znovu zadávat svoje přihlašovací údaje, zobrazí se dialogové okno pro vyjádření souhlasu.  Tuto metodu volejte pouze v případě, že vypršela platnost tokenu relace.

   **Cíl-C**:

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

   **Swift**:

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
[rychlé zprovoznění Mobile Apps Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamické schéma]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Řídicí panel prostředků infrastruktury]: https://www.fabric.io/home
[Prostředky infrastruktury pro iOS – Začínáme]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
