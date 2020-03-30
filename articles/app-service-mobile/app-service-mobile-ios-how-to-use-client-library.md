---
title: Použití sady iOS SDK
description: Jak používat iOS SDK pro mobilní aplikace Azure
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249344"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Jak používat klientskou knihovnu iOS pro mobilní aplikace Azure

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Přehled
Tato příručka vás naučí provádět běžné scénáře pomocí nejnovějších [Azure Mobile Apps iOS SDK][1]. Pokud s Azure Mobile Apps teprve začínáte, nejprve dokončete [rychlý start pro Mobilní aplikace Azure] a vytvořte back-end, vytvořte tabulku a stáhněte si předem sestavený projekt IOS Xcode. V této příručce se zaměřujeme na klienta iOS SDK. Další informace o sadě SDK na straně serveru pro back-end najdete v tématu server SDK HOWTO.

## <a name="reference-documentation"></a>Referenční dokumentace

Referenční dokumentace pro sdk klienta iOS se nachází zde: [Azure Mobile Apps iOS Client Reference][2].

## <a name="supported-platforms"></a>Podporované platformy

Sada iOS SDK podporuje projekty Objective-C, projekty Swift 2.2 a Projekty Swift 2.3 pro iOS verze 8.0 nebo novější.

Ověřování "servertok" používá WebView pro prezentované uživatelské ho dohledu.  Pokud zařízení není schopno prezentovat uživatelské rozhraní WebView, je vyžadována jiná metoda ověřování, která je mimo rozsah produktu.  
Tato sada SDK tedy není vhodná pro zařízení typu Watch nebo podobně omezená zařízení.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Nastavení a požadavky

Tato příručka předpokládá, že jste vytvořili back-end s tabulkou. Tato příručka předpokládá, že tabulka má stejné schéma jako tabulky v těchto kurzech. Tato příručka také předpokládá, že `MicrosoftAzureMobile.framework` v `MicrosoftAzureMobile/MicrosoftAzureMobile.h`kódu odkazujete a importujete .

## <a name="how-to-create-client"></a><a name="create-client"></a>Postup: Vytvoření klienta

Chcete-li získat přístup k back-endu `MSClient`mobilních aplikací Azure ve vašem projektu, vytvořte . Nahraďte `AppUrl` ji adresou URL aplikace. Můžete odejít `gatewayURLString` `applicationKey` a vyprázdnit. Pokud nastavíte bránu pro `gatewayURLString` ověřování, naplňte ji adresou URL brány.

**Cíl C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Rychlý**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="how-to-create-table-reference"></a><a name="table-reference"></a>Postup: Vytvoření odkazu na tabulku

Pro přístup k datům a jejich aktualizaci vytvořte odkaz na back-endovou tabulku. Nahraďte `TodoItem` názvem vaší tabulky.

**Cíl C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Rychlý**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="how-to-query-data"></a><a name="querying"></a>Postup: Dotazovat se na data

Chcete-li vytvořit databázový `MSTable` dotaz, dotaz na objekt. Následující dotaz získá všechny `TodoItem` položky a přihlásí text každé položky.

**Cíl C**:

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

**Rychlý**:

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

## <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Postup: Filtrování vrácených dat

Chcete-li filtrovat výsledky, existuje mnoho dostupných možností.

Chcete-li filtrovat pomocí predikátu, použijte a `NSPredicate` . `readWithPredicate` Následující filtry vrátily data, aby nalezly pouze neúplné položky todo.

**Cíl C**:

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

**Rychlý**:

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

## <a name="how-to-use-msquery"></a><a name="query-object"></a>Postup: Použití msquery

Chcete-li provést složitý dotaz (včetně řazení `MSQuery` a stránkování), vytvořte objekt přímo nebo pomocí predikátu:

**Cíl C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Rychlý**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`umožňuje řídit několik chování dotazů.

* Zadat pořadí výsledků
* Omezit, která pole mají být vrácena
* Omezit počet záznamů, které mají být vráceny
* Určit celkový počet v odpovědi
* Určení vlastních parametrů řetězce dotazu v požadavku
* Použití dalších funkcí

Spusťte `MSQuery` dotaz `readWithCompletion` voláním objektu.

## <a name="how-to-sort-data-with-msquery"></a><a name="sorting"></a>Postup: Řazení dat pomocí msquery

Chcete-li seřadit výsledky, podívejme se na příklad. Chcete-li seřadit podle pole 'text' vzestupně, `MSQuery` pak 'kompletní' sestupně, vyvolat takto:

**Cíl C**:

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

**Rychlý**:

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

## <a name="how-to-limit-fields-and-expand-query-string-parameters-with-msquery"></a><a name="selecting"></a><a name="parameters"></a>Postup: Omezení polí a rozbalení parametrů řetězce dotazu pomocí msquery

Chcete-li omezit pole, která mají být vrácena v dotazu, zadejte názvy polí ve vlastnosti **selectFields.** Tento příklad vrátí pouze text a vyplněná pole:

**Cíl C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Rychlý**:

```swift
query.selectFields = ["text", "complete"]
```

Chcete-li do požadavku serveru zahrnout další parametry řetězce dotazu (například `query.parameters` proto, že je používá vlastní skript na straně serveru), naplňte takto:

**Cíl C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Rychlý**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="how-to-configure-page-size"></a><a name="paging"></a>Postup: Konfigurace velikosti stránky

S Azure Mobile Apps velikost stránky řídí počet záznamů, které jsou vytahované najednou z back-endtabulek. Volání `pull` dat by pak dávkově data, na základě této velikosti stránky, dokud neexistují žádné další záznamy k vyžádat.

Velikost stránky je možné nakonfigurovat pomocí **mspullsettings,** jak je znázorněno níže. Výchozí velikost stránky je 50 a následující příklad ji změní na 3.

Z důvodů výkonu můžete nakonfigurovat jinou velikost stránky. Pokud máte velký počet malých datových záznamů, vysoká velikost stránky snižuje počet serverů round-trips.

Toto nastavení řídí pouze velikost stránky na straně klienta. Pokud klient požádá o větší velikost stránky, než podporuje back-end mobilních aplikací, je velikost stránky omezena na maximum, které je back-end nakonfigurován tak, aby podporoval.

Toto nastavení je také *počet* datových záznamů, nikoli *velikost bajtu*.

Pokud zvětšíte velikost stránky klienta, měli byste také zvětšit velikost stránky na serveru. Postup: [Úprava velikosti stránkování tabulky naleznete](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) v krocích k tomu, jak to provést.

**Cíl C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Rychlý**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="how-to-insert-data"></a><a name="inserting"></a>Postup: Vložení dat

Chcete-li vložit nový řádek `NSDictionary` tabulky, vytvořte a vyvolat `table insert`. Pokud je povoleno [dynamické schéma,] mobilní back-end služby Azure App `NSDictionary`Service automaticky vygeneruje nové sloupce na základě rozhraní .

Pokud `id` není k dispozici, back-end automaticky generuje nové jedinečné ID. Zadejte `id` své vlastní používat e-mailové adresy, uživatelská jména, nebo vlastní hodnoty jako ID. Poskytnutí vlastního ID může usnadnit spojení a obchodní databázovou logiku.

Obsahuje `result` novou položku, která byla vložena. V závislosti na logice serveru může mít další nebo upravená data ve srovnání s tím, co bylo předáno serveru.

**Cíl C**:

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

**Rychlý**:

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

## <a name="how-to-modify-data"></a><a name="modifying"></a>Postup: Změna dat

Chcete-li aktualizovat existující řádek, `update`upravte položku a volejte :

**Cíl C**:

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

**Rychlý**:

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

Případně zadeji ID řádku a aktualizované pole:

**Cíl C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Rychlý**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Minimálně `id` atribut musí být nastavena při provádění aktualizací.

## <a name="how-to-delete-data"></a><a name="deleting"></a>Postup: Odstranění dat

Chcete-li odstranit `delete` položku, vyvolat s položkou:

**Cíl C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Rychlý**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Případně odstraňte zadáním ID řádku:

**Cíl C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Rychlý**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Minimálně `id` atribut musí být nastavena při provádění odstraní.

## <a name="how-to-call-custom-api"></a><a name="customapi"></a>Postup: Volání vlastního rozhraní API

Pomocí vlastního rozhraní API můžete vystavit všechny funkce back-endu. Nemusí se mapovat na operaci stolu. Nejen, že získáte větší kontrolu nad zasíláním zpráv, můžete dokonce číst / nastavit záhlaví a změnit formát těla odezvy.

Chcete-li volat vlastní `MSClient.invokeAPI`rozhraní API, volejte . Obsah požadavku a odpovědi jsou považovány za JSON. Chcete-li použít jiné typy médií, [použijte jiné přetížení `invokeAPI` ][5].  Chcete-li `GET` vytvořit požadavek namísto `POST` požadavku, nastavte `HTTPMethod` parametr a `"GET"` parametr `body` na `nil` (protože požadavky GET nemají těla zpráv.) Pokud vaše vlastní rozhraní API podporuje `HTTPMethod` jiná slovesa HTTP, změňte odpovídajícím způsobem.

**Cíl C**:

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

**Rychlý**:

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

## <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="templates"></a>Postup: Registrace nabízených šablon pro odesílání oznámení pro různé platformy

Chcete-li zaregistrovat šablony, předejte šablony pomocí metody **client.push registerDeviceToken** ve vaší klientské aplikaci.

**Cíl C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Rychlý**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Šablony jsou typu NSDictionary a mohou obsahovat více šablon v následujícím formátu:

**Cíl C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Rychlý**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Všechny značky jsou odstraněny z žádosti o zabezpečení.  Informace o přidání značek do instalací nebo šablon v rámci instalací naleznete v [tématu Práce s back-endovým serverem .NET SDK pro Azure Mobile Apps][4].  Chcete-li odesílat oznámení pomocí těchto registrovaných šablon, pracujte s [api centra oznámení][3].

## <a name="how-to-handle-errors"></a><a name="errors"></a>Postup: Zpracování chyb

Když zavoláte mobilní back-end služby Azure `NSError` App Service, blok dokončení obsahuje parametr. Dojde-li k chybě, tento parametr není nulová. V kódu byste měli zkontrolovat tento parametr a zpracovat chybu podle potřeby, jak je znázorněno v předchozích fragmentech kódu.

Soubor [`<WindowsAzureMobileServices/MSError.h>`][6] definuje `MSErrorResponseKey`konstanty , `MSErrorRequestKey`a `MSErrorServerItemKey`. Chcete-li získat další data související s chybou:

**Cíl C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Rychlý**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Soubor navíc definuje konstanty pro každý kód chyby:

**Cíl C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Rychlý**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="how-to-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Postup: Ověření uživatelů pomocí knihovny ověřování služby Active Directory

Pomocí knihovny ADAL (Active Directory Authentication Library) můžete uživatele přihlásit do aplikace pomocí služby Azure Active Directory. Ověřování toku klienta pomocí zprostředkovatele identity SDK je vhodnější pomocí `loginWithProvider:completion:` metody.  Ověřování toku klienta poskytuje více nativní uživatelské rozhraní a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení do Služby AAD podle kurzu [Jak nakonfigurovat službu App Service pro přihlášení do služby Active Directory.][7] Ujistěte se, že dokončíte volitelný krok registrace nativní klientské aplikace. Pro iOS doporučujeme, aby identifikátor URI `<app-scheme>://<bundle-id>`přesměrování byl formuláře . Další informace naleznete v [rychlém startu ADAL iOS][8].
2. Nainstalujte ADAL pomocí kakaopodů. Upravte podfile tak, aby zahrnoval následující definici a nahradil **váš projekt** názvem projektu Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   a pod:

        pod 'ADALiOS'

3. Pomocí terminálu `pod install` spusťte z adresáře obsahujícího váš projekt a otevřete vygenerovaný pracovní prostor Xcode (nikoli projekt).
4. Přidejte do aplikace následující kód podle jazyka, který používáte. V každém z nich proveďte tyto náhrady:

   * Nahraďte **INSERT-AUTHORITY-HERE** názvem klienta, ve kterém jste zřídit svou aplikaci. Formát by https://login.microsoftonline.com/contoso.onmicrosoft.comměl být . Tuto hodnotu lze zkopírovat z karty Doména ve službě Azure Active Directory na [webu Azure Portal].
   * Nahraďte **INSERT-RESOURCE-ID-ZDE** ID klienta pro back-end mobilní aplikace. ID klienta můžete získat na kartě **Upřesnit** v části **Nastavení služby Azure Active Directory** na portálu.
   * Nahraďte **INSERT-CLIENT-ID-ZDE** ID klienta, které jste zkopírovali z nativní klientské aplikace.
   * Nahraďte **rozhraní INSERT-REDIRECT-URI-ZDE** koncovým bodem vašeho webu */.auth/login/done* pomocí schématu HTTPS. Tato hodnota by *https://contoso.azurewebsites.net/.auth/login/done*měla být podobná .

**Cíl C**:

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

**Rychlý**:

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

## <a name="how-to-authenticate-users-with-the-facebook-sdk-for-ios"></a><a name="facebook-sdk"></a>Postup: Ověření uživatelů pomocí sady Facebook SDK pro iOS

Pomocí sady Facebook SDK pro iOS můžete uživatele přihlásit do aplikace pomocí Facebooku.  Použití ověřování toku klienta je `loginWithProvider:completion:` vhodnější použít metodu.  Ověřování toku klienta poskytuje více nativní uživatelské rozhraní a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení na Facebooku podle kurzu [Jak nakonfigurovat službu App Service pro Facebook.][9]
2. Nainstalujte facebookovou sdk pro iOS podle dokumentace [ke službě Facebook SDK pro iOS – Začínáme.][10] Místo vytváření aplikace můžete přidat platformu iOS do stávající registrace.
3. Dokumentace facebooku obsahuje nějaký kód Objective-C v delegátovi aplikace. Pokud používáte **Swift**, můžete použít následující překlady pro AppDelegate.swift:

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
4. Kromě přidání `FBSDKCoreKit.framework` do projektu, také přidat `FBSDKLoginKit.framework` odkaz stejným způsobem.
5. Přidejte do aplikace následující kód podle jazyka, který používáte.

    **Cíl C**:

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

    **Rychlý**:

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

## <a name="how-to-authenticate-users-with-twitter-fabric-for-ios"></a><a name="twitter-fabric"></a>Postup: Ověření uživatelů pomocí aplikace Twitter Fabric pro iOS

Fabric pro iOS můžete použít k přihlášení uživatelů do aplikace pomocí Twitteru. Ověřování toku klienta je `loginWithProvider:completion:` vhodnější než použití metody, protože poskytuje nativní uživatelské rozhraní a umožňuje další přizpůsobení.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení na Twitteru podle kurzu [Jak nakonfigurovat službu App Service pro přihlášení na Twitteru.](../app-service/configure-authentication-provider-twitter.md)
2. Přidejte fabric do projektu podle [dokumentace Fabric pro iOS - Začínáme] a nastavení TwitterKit.

   > [!NOTE]
   > Ve výchozím nastavení fabric vytvoří aplikaci Twitter pro vás. Vytvoření aplikace se můžete vyhnout registrací spotřebitelského klíče a tajného klíče spotřebitele, které jste vytvořili dříve, pomocí následujících fragmentů kódu.    Případně můžete nahradit hodnoty spotřebitelského klíče a tajného spotřebitele, které poskytujete službě App Service, hodnotami, které se zobrazí na [řídicím panelu prostředků infrastruktury]. Pokud zvolíte tuto možnost, nezapomeňte nastavit adresu URL zpětného volání `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`na zástupnou hodnotu, například .

    Pokud se rozhodnete použít tajné kódy, které jste vytvořili dříve, přidejte do delegáta aplikace následující kód:

    **Cíl C**:

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

    **Rychlý**:

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

3. Přidejte do aplikace následující kód podle jazyka, který používáte.

    **Cíl C**:

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

    **Rychlý**:

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

## <a name="how-to-authenticate-users-with-the-google-sign-in-sdk-for-ios"></a><a name="google-sdk"></a>Postup: Ověření uživatelů pomocí sady Google Sign-In SDK pro iOS

Pomocí sady SDK pro přihlášení google pro iOS můžete uživatele přihlásit do aplikace pomocí účtu Google.  Google nedávno oznámil změny svých bezpečnostních zásad OAuth.  Tyto změny zásad budou v budoucnu vyžadovat používání sady Google SDK.

1. Nakonfigurujte back-end mobilní aplikace pro přihlášení Google podle kurzu [Jak nakonfigurovat službu app service pro přihlášení Google.](../app-service/configure-authentication-provider-google.md)
2. Nainstalujte google sdk pro iOS podle [přihlášení Google pro iOS - Začněte integrovat](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentaci. Můžete přeskočit část Ověření pomocí back-endového serveru.
3. Přidejte následující metodu `signIn:didSignInForUser:withError:` delegáta podle jazyka, který používáte.

    **Cíl C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Rychlý**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Ujistěte se, že `application:didFinishLaunchingWithOptions:` jste také přidat následující v aplikaci delegáta, nahradí "SERVER_CLIENT_ID" se stejným ID, které jste použili ke konfiguraci app service v kroku 1.

    **Cíl C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Rychlý**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Přidejte následující kód do aplikace v UIViewController, který implementuje `GIDSignInUIDelegate` protokol, podle jazyka, který používáte.  Před znovupřihlášením jste odhlášeni, a i když nemusíte znovu zadávat přihlašovací údaje, zobrazí se dialogové okno souhlasu.  Tuto metodu zavolejte pouze v případě, že vypršela platnost tokenu relace.

   **Cíl C**:

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

   **Rychlý**:

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
[Rychlý start mobilních aplikací Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Portál Azure]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamické schéma]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Řídicí panel infrastruktury]: https://www.fabric.io/home
[Fabric pro iOS – začínáme]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v2-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
