---
title: Konfigurace poskytovatele OpenID Connect (Preview)
description: Naučte se nakonfigurovat poskytovatele OpenID Connect jako poskytovatele identity pro vaše App Service nebo Azure Functions aplikaci.
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.openlocfilehash: e8112f2dc20175e81cfa8388440b2d9aef6a419c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983868"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Konfigurace přihlašování pomocí poskytovatele OpenID Connect v aplikaci App Service nebo Azure Functions (Preview)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure App Service nebo Azure Functions, aby používaly vlastního zprostředkovatele ověřování, který dodržuje [specifikaci OpenID Connect](https://openid.net/connect/). OpenID Connect (OIDC) je průmyslový standard používaný mnoha zprostředkovateli identity (zprostředkovatelů identity). Nemusíte porozumět podrobnostem specifikace, aby bylo možné aplikaci nakonfigurovat tak, aby používala IDP adherent.

Vaše aplikace může nakonfigurovat, aby používala jednoho nebo více zprostředkovatelů OIDC. Každé z nich musí být přiřazen jedinečný název a jako výchozí cíl přesměrování může sloužit pouze jeden.

> [!CAUTION]
> Povolení poskytovatele OpenID Connect zakáže správu funkce App Service ověřování/autorizace pro vaši aplikaci prostřednictvím některých klientů, jako je například Azure Portal, Azure CLI a Azure PowerShell. Tato funkce spoléhá na nové prostředí API, které ve verzi Preview ještě není ve všech prostředích správy k discentrálnímu účtu.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registrace aplikace pomocí zprostředkovatele identity

Váš poskytovatel bude vyžadovat, abyste v rámci své aplikace zaregistrovali podrobnosti. Podívejte se prosím na pokyny týkající se tohoto poskytovatele. Pro vaši aplikaci budete potřebovat shromáždit **ID klienta** a **tajný klíč klienta** .

> [!IMPORTANT]
> Tajný klíč aplikace je důležité bezpečnostní pověření. Nesdílejte tento tajný klíč s kýmkoli ani ho distribuujte v klientské aplikaci.
>

> [!NOTE]
> Někteří poskytovatelé můžou vyžadovat další kroky pro jejich konfiguraci a způsob použití hodnot, které poskytují. Například společnost Apple poskytuje privátní klíč, který se nepoužívá jako tajný klíč klienta OIDC, a místo toho je třeba ho použít k vygenerování tokenu JWT, který se považuje za tajný klíč, který zadáte v konfiguraci aplikace (viz část vytvoření tajného klíče klienta v tématu [přihlášení pomocí dokumentace společnosti Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)).
>

Přidejte tajný klíč klienta jako [nastavení aplikace](./configure-common.md#configure-app-settings) pro aplikaci s použitím názvu nastavení dle vašeho výběru. Poznamenejte si tento název pro pozdější účely.

Kromě toho budete pro poskytovatele potřebovat metadata OpenID Connect. To se často zveřejňuje prostřednictvím [dokumentu metadat konfigurace](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), což je adresa URL vystavitele zprostředkovatele s příponou `/.well-known/openid-configuration` . Shromážděte tuto adresu URL konfigurace.

Pokud nemůžete použít dokument metadat konfigurace, budete muset shromáždit následující hodnoty samostatně:

- Adresa URL vystavitele (někdy zobrazená jako `issuer` )
- [Koncový bod autorizace OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.1) (někdy zobrazený jako `authorization_endpoint` )
- [Koncový bod tokenu OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-3.2) (někdy zobrazený jako `token_endpoint` )
- Adresa URL dokumentu [sady webových klíčů OAuth 2,0 OAuth](https://tools.ietf.org/html/rfc8414#section-2) (někdy zobrazeného jako `jwks_uri` )

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Přidání informací o poskytovateli do aplikace

> [!NOTE]
> Požadovaná konfigurace je ve formátu nového rozhraní API, který je aktuálně podporovaný jenom [konfigurací založenou na souborech (Preview)](.\app-service-authentication-how-to.md#config-file). Pomocí takového souboru budete muset postupovat podle následujících kroků.

V této části se dozvíte, jak aktualizovat konfiguraci, aby zahrnovala vaše nové IDP. Následuje příklad konfigurace.

1. V rámci `identityProviders` objektu přidejte objekt, `openIdConnectProviders` Pokud ještě neexistuje.
1. Do `openIdConnectProviders` objektu přidejte klíč pro nového poskytovatele. Toto je popisný název, který se používá k odkazování poskytovatele ve zbývající části konfigurace. Pokud byste například chtěli vyžadovat ověření všech požadavků u tohoto poskytovatele, měli byste nastavit `globalValidation.unauthenticatedClientAction` na "RedirectToLoginPage" a nastavit `redirectToProvider` na stejný popisný název.
1. Přiřaďte objekt k tomuto klíči s `registration` objektem, který je v něm, a volitelně `login` objekt:
    
    ```json
    "myCustomIDP" : {
       "registration" : {},
       "login": {
             "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
             "scope": [],
             "loginParameterNames": [],
       }
    }
    ```

1. V rámci objektu registrace nastavte na `clientId` ID klienta, které jste shromáždili, nastavte `clientCredential.secretSettingName` název nastavení aplikace, kam jste uložili tajný klíč klienta, a vytvořte `openIdConnectConfiguration` objekt:

    ```json
    "registration": {
      "clientId": "bd96cf8a-3f2b-4806-b180-d3c5fd11a2be",
      "clientCredential": {
         "secretSettingName": "IDP_CLIENT_SECRET"
      },
      "openIdConnectConfiguration" : {}
    }
    ```

1. V rámci `openIdConnectConfiguration` objektu zadejte metadata OpenID Connect, která jste shromáždili dříve. Existují dvě možnosti, které jsou založené na shromažďovaných informacích:

    - Nastavte `wellKnownOpenIdConfiguration` vlastnost na adresu URL metadat konfigurace, kterou jste shromáždili dříve.
    - Případně můžete nastavit čtyři jednotlivé hodnoty shromážděné následujícím způsobem:
        - Nastavit `issuer` na adresu URL vystavitele
        - Nastavit `authorizationEndpoint` na koncový bod autorizace
        - Nastavit `tokenEndpoint` na koncový bod tokenu
        - Nastavte `certificationUri` na adresu URL dokumentu sady webových klíčů JSON.

    Tyto dvě možnosti se vzájemně vylučují.

Po nastavení této konfigurace jste připraveni použít poskytovatele OpenID Connect pro ověřování ve vaší aplikaci.

Příklad konfigurace může vypadat jako v následujícím příkladu (při použití přihlašování pomocí Apple jako příklad, kdy nastavení APPLE_GENERATED_CLIENT_SECRET odkazuje na vygenerovaný token JWT podle [Dokumentace společnosti Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "openIdConnectProviders": {
            "apple": {
                "registration": {
                    "clientId": "com.contoso.example.client",
                    "clientCredential": {
                        "secretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
                    },
                    "openIdConnectConfiguration": {
                        "wellKnownOpenIdConfiguration": "https://appleid.apple.com/.well-known/openid-configuration"
                    }
                },
                "login": {
                    "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
                    "scope": [],
                    "loginParameterNames": []
                }
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
