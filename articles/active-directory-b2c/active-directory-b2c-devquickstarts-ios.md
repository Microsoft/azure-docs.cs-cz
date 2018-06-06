---
title: Použití AppAuth v aplikace pro iOS v Azure Active Directory B2C | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit aplikaci iOS, která používá AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1da0187fc4ffa139eb7b245dd20d708eb98e0cf5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710303"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Přihlaste se pomocí aplikace pro iOS

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Pomocí otevřený standardní protokol nabízí více možností pro vývojáře, při výběru knihovnu, kterou chcete integrovat s našich služeb. Nabízíme tento návod a ostatní jako ho a usnadňuje vývojářům s psaní aplikací, které se připojují k platformě Microsoft Identity. Většina knihovny, které implementují [specifikace RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) dokážou připojit k platformě Microsoft Identity.

> [!WARNING]
> Společnost Microsoft neposkytuje opravy pro knihovny třetích stran a nebyl provádí kontrolu těchto knihoven. Tato ukázka je použití knihovny třetích stran volat AppAuth, který byl testován pro kompatibilitu v základní scénáře s Azure AD B2C. Problémy a žádosti o funkce se mají směrovat knihovny open-source projekt. Další informace najdete v [tomto článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Pokud jste ještě OAuth2 nebo OpenID Connect, mnohem této ukázkové konfigurace nemusí mít mnoho smysl pro vás. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C
Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](active-directory-b2c-get-started.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace
Dále musíte vytvořit aplikaci v adresáři B2C. Registrace aplikací poskytuje Azure AD informace potřebné k bezpečné komunikaci s vaší aplikace. Při vytváření mobilních aplikací, postupujte podle [tyto pokyny](active-directory-b2c-app-registration.md). Ujistěte se, že:

* Zahrnout **nativního klienta** v aplikaci.
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Tento identifikátor GUID potřebovat později.
* Nastavení **identifikátor URI pro přesměrování** s vlastní schéma (například com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). Tento identifikátor URI potřebovat později.

## <a name="create-your-policies"></a>Vytvořte svoje zásady
V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato aplikace obsahuje jeden prostředí identit: například společné přihlášení a odhlášení. Vytvořte tuto zásadu, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Při vytváření zásady nezapomeňte na následující:

* V části **atributy registrace**, vyberte atribut **zobrazovaný název**.  Můžete vybrat i další atributy.
* V části **deklarace identity aplikace**, vyberte deklarace identity **zobrazovaný název** a **ID objektu uživatele**. Můžete vybrat i další deklarace identity.
* Po vytvoření každé zásady si poznamenejte její **Název**. Je název vaší zásady předponu `b2c_1_` při ukládání zásad.  Název zásady potřebovat později.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po vytvoření zásad jste připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu
Uvádíme pracovní vzorku, který používá AppAuth s Azure AD B2C [na Githubu](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Můžete stáhnout kód a spusťte ho. Vlastního klienta Azure AD B2C, postupujte podle pokynů v [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Tato ukázka byla vytvořena podle těchto pokynů README pomocí [iOS AppAuth projektu na Githubu](https://github.com/openid/AppAuth-iOS). Další informace o fungování vzorku a knihovně odkazovat v souboru README AppAuth na Githubu.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikaci pomocí Azure AD B2C AppAuth

> [!NOTE]
> AppAuth podporuje iOS 7 a vyšší.  Ale pro podporu sociálních přihlášení na webu Google, je potřeba SFSafariViewController což vyžaduje, aby iOS 9 nebo vyšší.
>

### <a name="configuration"></a>Konfigurace

Komunikaci s Azure AD B2C můžete nakonfigurovat tak, že zadáte koncový bod autorizace i koncový bod tokenu identifikátory URI.  Chcete-li vygenerovat tyto identifikátory URI, je třeba následující informace:
* ID klienta (například contoso.onmicrosoft.com)
* Název zásady (například B2C\_1\_SignUpIn)

Koncový bod tokenu může být generována URI nahrazení klienta\_ID a zásady\_názvem v adrese URL následující:

```objc
static NSString *const tokenEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Koncový bod autorizace URI může být generována nahrazení klienta\_ID a zásady\_názvem v adrese URL následující:

```objc
static NSString *const authorizationEndpoint = @"https://login.microsoftonline.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Spusťte následující kód k vytvoření objektu AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Probíhá autorizace.

Po konfiguraci nebo načítání konfigurace povolení služby, lze sestavit požadavek autorizace. Chcete-li vytvořit požadavek, je třeba následující informace:  
* ID klienta (například 00000000-0000-0000-0000-000000000000)
* Identifikátor URI pro přesměrování s vlastní schéma (například com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

Obě položky by byly uloženy když jste byli [aplikaci zaregistrujete](#create-an-application).

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

Pokud chcete nastavit aplikaci pro zpracování přesměrování na identifikátor URI s vlastní schéma, budete muset aktualizovat seznam URL schémata ve vaší Info.pList:
* Otevřete Info.pList.
* Pozastavte ukazatel myši nad řádek jako kód sady operačního systému typu a klikněte na \+ symbol.
* Přejmenujte nový řádek "Adresa URL typů".
* Klikněte na šipku nalevo od "Adresa URL typů" otevřete stromu.
* Klikněte na šipku nalevo od ' položky 0' otevřete stromu.
* První položka pod položky 0, schémata URL, přejmenujte.
* Klikněte na šipku nalevo od adresy URL schémata otevřete stromu.
* Ve sloupci 'Hodnota' je prázdné pole nalevo od 'Položky 0' pod 'Adresy URL Schemes'.  Nastavte hodnotu na jedinečné schéma vaší aplikace.  Hodnota musí odpovídat používané v redirectURL při vytváření objektu OIDAuthorizationRequest schéma.  V ukázce se používá schéma 'com.onmicrosoft.fabrikamb2c.exampleapp'.

Odkazovat [AppAuth průvodce](https://openid.github.io/AppAuth-iOS/) o tom, jak dokončete proces. Pokud budete potřebovat rychle začít s pracovní aplikace, podívejte se na [vzorku](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Postupujte podle kroků v [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) k zadání vlastní konfigurace Azure AD B2C.

Snažíme se vždy otevřený a názory a návrhy! Pokud máte jakékoli problémy s tímto článkem nebo doporučení pro zlepšení tohoto obsahu, by nám chcete sdělit svůj názor v dolní části stránky. Pro žádosti o funkce, přidejte je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
