---
title: Použití AppAuth v aplikaci pro iOS
titleSuffix: Azure AD B2C
description: Jak vytvořit aplikaci pro iOS, která používá AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186824"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Přihlášení pomocí aplikace pro iOS

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Použití otevřeného standardního protokolu nabízí větší výběr pro vývojáře při výběru knihovny pro integraci s našimi službami. Poskytli jsme tento návod a další, jako je to pomoci vývojářům s psaní mandatorní aplikace, které se připojují k platformě Microsoft Identity. Většina knihoven, které [implementují specifikace RFC6749 OAuth2,](https://tools.ietf.org/html/rfc6749) se může připojit k platformě Microsoft Identity.

> [!WARNING]
> Společnost Microsoft neposkytuje opravy pro knihovny jiných výrobců a neprovedla kontrolu těchto knihoven. Tato ukázka používá knihovnu jiného výrobce s názvem AppAuth, která byla testována na kompatibilitu v základních scénářích s Azure AD B2C. Problémy a požadavky na funkce by měly být směrovány do open source projektu knihovny. Další informace najdete v [tomto článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Pokud jste novým OAuth2 nebo OpenID Connect, velká část této ukázkové konfigurace nemusí mít velký smysl pro vás. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C
Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](tutorial-create-tenant.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

Dále zaregistrujte aplikaci v tenantovi Azure AD B2C. To poskytuje Azure AD informace, které potřebuje ke komunikaci bezpečně s vaší aplikací.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

Také zaznamenejte vlastní identifikátor URI přesměrování pro pozdější krok. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Vytvoření uživatelských toků
Ve službě Azure AD B2C je každé uživatelské prostředí definováno [tok uživatele](user-flow-overview.md). Tato aplikace obsahuje jednu identitu: kombinované přihlášení a přihlášení. Při vytváření toku uživatele nezapomeňte:

* V části **Atributy registrace**vyberte atribut **Zobrazovaný název**.  Můžete vybrat i další atributy.
* V části **Deklarace identity**vyberte **deklarace identity Zobrazované jméno** a **ID objektu uživatele**. Můžete vybrat i další nároky.
* Zkopírujte **název** každého toku uživatele po jeho vytvoření. Název toku uživatele je `b2c_1_` předponou s při uložení toku uživatele.  Později budete potřebovat název toku uživatele.

Po vytvoření toků uživatelů jste připraveni vytvořit aplikaci.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu
Poskytli jsme pracovní ukázku, která používá AppAuth s Azure AD B2C [na GitHubu](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Můžete si stáhnout kód a spustit jej. Chcete-li použít vlastního klienta Azure AD B2C, postupujte podle pokynů v [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Tato ukázka byla vytvořena podle pokynů README [v projektu iOS AppAuth na GitHubu](https://github.com/openid/AppAuth-iOS). Další podrobnosti o tom, jak ukázka a knihovna fungují, načláneknete appauth README na GitHubu.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikace tak, aby používala Azure AD B2C s AppAuth

> [!NOTE]
> AppAuth podporuje iOS 7 a vyšší.  Nicméně, pro podporu sociální přihlášení na Google, SFSafariViewController je potřeba, který vyžaduje iOS 9 nebo vyšší.
>

### <a name="configuration"></a>Konfigurace

Můžete nakonfigurovat komunikaci s Azure AD B2C zadáním koncový bod autorizace a token koncový bod URI.  Chcete-li generovat tyto identifikátory URI, potřebujete následující informace:
* ID klienta (například contoso.onmicrosoft.com)
* Název toku uživatele (například\_\_B2C 1 SignUpIn)

Identifikátor URI koncového bodu tokenu lze\_vygenerovat nahrazením ID klienta a názvu zásady\_v následující adrese URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Identifikátor URI koncového bodu autorizace lze\_vygenerovat nahrazením ID klienta a názvu zásady\_v následující adrese URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Spusťte následující kód a vytvořte objekt AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizace

Po konfiguraci nebo načtení konfigurace autorizační služby lze sestavit žádost o autorizaci. Chcete-li vytvořit požadavek, potřebujete následující informace:

* ID klienta (ID aplikace), které jste zaznamenali dříve. Například, `00000000-0000-0000-0000-000000000000`.
* Vlastní identifikátor URI přesměrování, který jste zaznamenali dříve. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Obě položky měly být uloženy při [registraci aplikace](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Chcete-li nastavit aplikaci pro zpracování přesměrování na identifikátor URI pomocí vlastního schématu, je třeba aktualizovat seznam schémat adres URL ve vašem seznamu Info.pList:
* Otevřete soubor Info.pList.
* Najeďte přes řádek, jako je 'Bundle \+ OS Typový kód' a klikněte na symbol.
* Přejmenujte nový řádek "Typy adres URL".
* Kliknutím na šipku vlevo od "typů adres URL" otevřete strom.
* Kliknutím na šipku vlevo od položky 0 otevřete strom.
* Přejmenujte první položku pod položkou 0 na "Schémata adres URL".
* Kliknutím na šipku vlevo od "Schémata adres URL" otevřete strom.
* Ve sloupci Hodnota je prázdné pole vlevo od položky 0 pod položkou Schémata ADRES URL.  Nastavte hodnotu jedinečného schématu aplikace.  Hodnota musí odpovídat schématu použitému v redirectURL při vytváření objektu OIDAuthorizationRequest.  Ve vzorku se používá schéma 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Naleznete [appauth průvodce](https://openid.github.io/AppAuth-iOS/) o dokončení zbytku procesu. Pokud potřebujete rychle začít pracovat s pracovní aplikací, podívejte se [na ukázku](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Podle pokynů v [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) zadejte vlastní konfiguraci Azure AD B2C.

Jsme vždy otevřeni zpětné vazbě a návrhům! Pokud máte potíže s tímto článkem nebo máte doporučení pro zlepšení tohoto obsahu, ocenili bychom vaši zpětnou vazbu v dolní části stránky. Pro požadavky na funkce je přidejte do [uservoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
