---
title: Konfigurace klíčenky
titleSuffix: Microsoft identity platform
description: Naučte se konfigurovat řetězce klíčů tak, aby vaše aplikace mohla ukládat tokeny do mezipaměti v řetězci klíčů.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 06e197a6e445c7dc1179be696318905f2132ee36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85477732"
---
# <a name="configure-keychain"></a>Konfigurace klíčenky

Když se v rámci uživatele v [knihovně pro ověřování pro iOS a MacOS](msal-overview.md) (MSAL) přihlásí nebo aktualizuje token, pokusí se v řetězci klíčů ukládat tokeny do mezipaměti. Ukládání tokenů do mezipaměti v řetězci klíčů umožňuje MSAL poskytovat tiché jednotné přihlašování (SSO) mezi více aplikacemi, které distribuuje stejný vývojář Apple. Jednotné přihlašování se dosahuje prostřednictvím funkcí přístupových skupin pro řetězce klíčů. Další informace najdete v [dokumentaci k položkám řetězce klíčů](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)společnosti Apple.

Tento článek popisuje, jak nakonfigurovat nároky aplikace tak, aby MSAL mohli zapisovat tokeny do mezipaměti pro iOS a macOS řetězce klíčů.

## <a name="default-keychain-access-group"></a>Výchozí přístupová skupina pro řetězce klíčů

### <a name="ios"></a>iOS

MSAL v systému iOS používá `com.microsoft.adalcache` ve výchozím nastavení přístupovou skupinu. Jedná se o sdílenou přístupovou skupinu, kterou používají sady SDK MSAL i Azure AD Authentication Library (ADAL), a zajišťuje nejlepší jednotné přihlašování (SSO) mezi více aplikacemi od stejného vydavatele.

V systému iOS přidejte `com.microsoft.adalcache` skupinu řetězce klíčů do nároku vaší aplikace v Xcode v části **Možnosti nastavení projektu**  >  **Capabilities**  >  **sdílení řetězce klíčů** .

### <a name="macos"></a>macOS

MSAL v macOS používá `com.microsoft.identity.universalstorage` ve výchozím nastavení přístupovou skupinu.

Z důvodu omezení řetězce klíčů macOS se MSAL `access group` nepřeloží přímo na atribut přístupové skupiny pro řetězce klíčů (viz [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) v MacOS 10,14 a starších. Nicméně se chová podobně jako v perspektivě jednotného přihlašování. zajišťuje, že více aplikací distribuovaných stejným vývojářem Apple může mít tiché jednotné přihlašování.

V macOS 10,15 a vyšším (macOS Catalina) využívá MSAL k zajištění tichého přihlašování () atribut přístupovou skupinu pro řetězce klíčů, podobně jako iOS.

## <a name="custom-keychain-access-group"></a>Vlastní přístupová skupina pro řetězce klíčů

Pokud chcete použít jinou přístupovou skupinu pro řetězce klíčů, můžete vlastní skupinu předat při vytváření `MSALPublicClientApplicationConfig` před vytvořením, například takto `MSALPublicClientApplication` :

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Zakázat sdílení řetězce klíčů

Pokud nechcete sdílet stav jednotného přihlašování mezi více aplikacemi nebo použít jakoukoli přístupovou skupinu pro řetězce klíčů, zakažte sdílení řetězce klíčů předáním ID sady prostředků aplikace jako své skupiny klíčů:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Chyba popisovače-34018 (Nepodařilo se nastavit položku na řetězec klíčů)

Chyba-34018 obvykle znamená, že řetězec klíčů nebyl správně nakonfigurován. Ujistěte se, že přístupová skupina pro řetězce klíčů, která je nakonfigurovaná v MSAL, odpovídá hodnotě nakonfigurované v oprávněních.

## <a name="ensure-your-application-is-properly-signed"></a>Ujistěte se, že je aplikace správně podepsaná.

V macOS můžou být aplikace spouštěné bez podpisu vývojářem. I když většina funkcí MSAL bude i nadále fungovat, SSO prostřednictvím přístupového řetězce klíčů vyžaduje, aby byla aplikace podepsaná. Pokud máte více výzev pro řetězce klíčů, ujistěte se, že signatura aplikace je platná.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o přístupových skupinách pro řetězce klíčů v tématu [přístup k položkám řetězce klíčů](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) v rámci kolekce článků v aplikacích v Apple.
