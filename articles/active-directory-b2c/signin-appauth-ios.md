---
title: Použití AppAuth v aplikaci pro iOS
titleSuffix: Azure AD B2C
description: Jak vytvořit aplikaci pro iOS, která používá AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 558e6cea4d5e0c9bd0f6222f9070d2b867a5bf44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384936"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: přihlášení pomocí aplikace pro iOS

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Použití otevřeného standardního protokolu nabízí při výběru knihovny pro integraci s našimi službami více možností vývojářů. Tento názorný postup a další uživatelé mají za to, aby mohli snadněji psát aplikace, které se připojují k platformě Microsoft identity. Většina knihoven, které implementují [specifikaci RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) , se může připojit k platformě Microsoft identity.

> [!WARNING]
> Microsoft neposkytuje opravy pro knihovny třetích stran a neprovádí revize těchto knihoven. Tato ukázka používá knihovnu třetí strany s názvem AppAuth, která byla testována z hlediska kompatibility v základních scénářích s Azure AD B2C. Žádosti o problémy a funkce by měly být směrovány na open source projekt knihovny. Další informace najdete v [tomto článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Pokud s OAuth2 nebo OpenID připojením začínáte, tato Ukázková konfigurace nemusí být pro vás velmi vhodná. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C
Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](tutorial-create-tenant.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

V dalším kroku zaregistrujete aplikaci do svého tenanta Azure AD B2C. To poskytuje službě Azure AD informace, které potřebuje pro bezpečnou komunikaci s vaší aplikací.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

Také zaznamenejte vlastní identifikátor URI přesměrování pro použití v pozdějším kroku. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Vytvoření uživatelských toků
V Azure AD B2C je každé uživatelské prostředí definované [uživatelským tokem](user-flow-overview.md). Tato aplikace obsahuje jedno prostředí identity: kombinované přihlášení a registrace. Při vytváření toku uživatele nezapomeňte:

* V části **atributy registrace**vyberte **Zobrazovaný název**atributu.  Můžete také vybrat jiné atributy.
* V části **deklarace identity aplikace**vyberte **Zobrazovaný název** deklarací identity a **ID objektu uživatele**. Můžete vybrat i další deklarace identity.
* Po vytvoření si zkopírujte **název** každého toku uživatele. Název uživatelského toku je předponou `b2c_1_` při ukládání toku uživatele.  Název toku uživatele budete potřebovat později.

Po vytvoření toků uživatelů budete připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu
Poskytujeme pracovní ukázku, která používá AppAuth s Azure AD B2C [na GitHubu](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Můžete stáhnout kód a spustit ho. Pokud chcete použít vlastního tenanta Azure AD B2C, postupujte podle pokynů v [Readme.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Tuto ukázku vytvořil [projekt iOS AppAuth na GitHubu](https://github.com/openid/AppAuth-iOS)podle pokynů v souboru Readme. Další podrobnosti o tom, jak ukázka a knihovna fungují, najdete v souboru READme AppAuth na GitHubu.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikace pro použití Azure AD B2C s AppAuth

> [!NOTE]
> AppAuth podporuje iOS 7 a novější.  Aby ale podporovala sociální přihlášení na Google, je potřeba SFSafariViewController, který vyžaduje iOS 9 nebo vyšší.
>

### <a name="configuration"></a>Konfigurace

Komunikaci s Azure AD B2C můžete nakonfigurovat zadáním identifikátoru URI koncového bodu autorizace i koncového bodu tokenu.  K vygenerování těchto identifikátorů URI potřebujete následující informace:
* ID tenanta (například contoso.onmicrosoft.com)
* Název toku uživatele (například B2C \_ 1 \_ SignUpIn)

Identifikátor URI koncového bodu tokenu se dá vygenerovat tak, že nahradíte \_ ID tenanta a \_ název zásady v následující adrese URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Identifikátor URI koncového bodu autorizace se dá vygenerovat tak, že nahradíte \_ ID tenanta a \_ název zásady v následující adrese URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Spusťte následující kód pro vytvoření objektu AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizace

Jakmile nakonfigurujete nebo načtete konfiguraci autorizační služby, může být vytvořen požadavek na autorizaci. K vytvoření žádosti potřebujete následující informace:

* ID klienta (ID aplikace), které jste si poznamenali dříve. Například, `00000000-0000-0000-0000-000000000000`.
* Vlastní identifikátor URI pro přesměrování, který jste si poznamenali dříve. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Obě položky by měly být uloženy při [registraci aplikace](#create-an-application).

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

Chcete-li nastavit aplikaci tak, aby zpracovávala přesměrování na identifikátor URI s vlastním schématem, je třeba aktualizovat seznam schémat URL v souboru info. pList:
* Otevřete info. pList.
* Najeďte myší na řádek, jako je například kód typu operačního systému sady, a klikněte na \+ symbol.
* Přejmenujte nový řádek ' typy URL '.
* Kliknutím na šipku nalevo od ' typy URL ' otevřete stromovou strukturu.
* Kliknutím na šipku nalevo od položky ' položka 0 ' otevřete strom.
* Přejmenujte první položku pod položkou 0 na ' schémata URL '.
* Kliknutím na šipku nalevo od ' schémata URL ' otevřete strom.
* Ve sloupci Value (hodnota) se nachází prázdné pole nalevo od položky "položka 0" pod "schématy URL".  Nastavte hodnotu na jedinečné schéma vaší aplikace.  Hodnota se musí shodovat s schématem použitým v redirectURL při vytváření objektu OIDAuthorizationRequest.  V ukázce se používá schéma "com. fabrikamb2c. exampleapp".

Informace o tom, jak dokončit zbytek procesu, najdete v [příručce k AppAuth](https://openid.github.io/AppAuth-iOS/) . Pokud potřebujete rychle začít pracovat s pracovní aplikací, podívejte [se na ukázku](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Postupujte podle kroků v [Readme.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) a zadejte vlastní konfiguraci Azure AD B2C.

Vždycky jsme otevřeli názory a návrhy! Pokud máte v tomto článku nějaké potíže nebo máte doporučení pro zlepšení tohoto obsahu, Děkujeme za váš názor v dolní části stránky. V případě žádostí o funkce je přidejte do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
