---
title: Konfigurace klíčenky
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak nakonfigurovat řetězec klíčů tak, aby vaše aplikace mohla ukládat tokeny do mezipaměti v řetězci klíčů.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085863"
---
# <a name="configure-keychain"></a>Konfigurace klíčenky

Když se v uživateli přihlásí [knihovna ověřování Microsoft pro iOS a macOS](msal-overview.md) (MSAL) nebo aktualizuje token, pokusí se ukládat tokeny do mezipaměti v řetězci klíčů. Ukládání tokenů do mezipaměti v řetězci klíčů umožňuje službě MSAL poskytovat tiché jednotné přihlašování (SSO) mezi více aplikacemi, které jsou distribuovány stejným vývojářem Apple. SSO je dosaženo prostřednictvím funkce přístupové skupiny klíčenky. Další informace naleznete v [dokumentaci k položkám klíčenky](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)společnosti Apple .

Tento článek popisuje, jak nakonfigurovat nároky aplikací tak, aby MSAL můžete psát tokeny uložené v mezipaměti pro iOS a macOS klíčenky.

## <a name="default-keychain-access-group"></a>Výchozí přístupová skupina řetězce klíčů

### <a name="ios"></a>iOS

MSAL v systému `com.microsoft.adalcache` iOS používá přístupovou skupinu ve výchozím nastavení. Toto je sdílená přístupová skupina používaná sadami SDK knihovny MSAL i Azure AD Authentication Library (ADAL) a zajišťuje nejlepší jednotné přihlašování (SSO) mezi více aplikacemi od stejného vydavatele.

V iOS přidejte `com.microsoft.adalcache` skupinu klíčů ke svému oprávnění aplikace v XCode v části Nastavení **projektu** > **Sdílení** > **klíčů.**

### <a name="macos"></a>macOS

MSAL na macOS používá `com.microsoft.identity.universalstorage` přístupovou skupinu ve výchozím nastavení.

Z důvodu omezení řetězce klíčů macOS se `access group` MSAL přímo nepřekládá do atributu skupiny přístupových řetězců klíčů (viz [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)v macOS 10.14 a starších. Však chová podobně z hlediska spřimhou přihlašování tím, že zajišťuje, že více aplikací distribuovaných stejným vývojářem Apple může mít tiché přihlašování.

V macOS 10.15 a dále (macOS Catalina), MSAL používá atribut skupiny přístupu klíčenky k dosažení tichého sso, podobně jako iOS.

## <a name="custom-keychain-access-group"></a>Vlastní přístupová skupina řetězce klíčů

Pokud chcete použít jinou skupinu přístupových řetězců klíčů, můžete `MSALPublicClientApplicationConfig` před `MSALPublicClientApplication`vytvořením předat vlastní skupinu takto:

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

## <a name="disable-keychain-sharing"></a>Zakázat sdílení klíčenky

Pokud nechcete sdílet stav přihlašovacího systému mezi více aplikacemi nebo použít libovolnou skupinu přístupových řetězců klíčů, zakažte sdílení řetězce klíčů předáním ID sady aplikací jako skupiny klíčů:

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

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Popisovač -34018 chyba (nepodařilo se nastavit položku do klíčenky)

Chyba -34018 obvykle znamená, že klíčenka nebyla správně nakonfigurována. Ujistěte se, že přístupová skupina řetězce klíčů, která byla nakonfigurována v msal, odpovídá skupině nakonfigurované v oprávněních.

## <a name="ensure-your-application-is-properly-signed"></a>Ujistěte se, že je vaše žádost řádně podepsána

V macOS se aplikace můžou spouštět, aniž by je vývojář podepsal. Zatímco většina funkcí MSAL bude i nadále fungovat, spřijit pomocí přístupu řetězce klíčů vyžaduje, aby byla aplikace podepsána. Pokud se u vás vyskytne více výzev řetězce klíčů, ujistěte se, že podpis aplikace je platný.

## <a name="next-steps"></a>Další kroky

Další informace o přístupových skupinách pro klíčenky najdete v článku [Sdílení přístupu apple k položkám klíčenky v kolekci aplikací.](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)
