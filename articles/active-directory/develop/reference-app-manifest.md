---
title: Principy manifestu aplikace Azure Active Directory | Dokumenty společnosti Microsoft
description: Podrobné pokrytí manifestu aplikace Azure Active Directory, který představuje konfiguraci identity aplikace v tenantovi Azure AD a používá se k usnadnění autorizace OAuth, prostředí souhlasu a další.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/23/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 6d9a4af5ee814282589959fcf840c1061358ca18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383935"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikace Azure Active Directory

Manifest aplikace obsahuje definici všech atributů aplikačního objektu v platformě identit microsoftu. Slouží také jako mechanismus pro aktualizaci aplikačního objektu. Další informace o entitě Aplikace a jejím schématu naleznete v [dokumentaci k entitě entity rozhraní API grafu](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Atributy aplikace můžete nakonfigurovat prostřednictvím portálu Azure nebo programově pomocí [rozhraní REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) nebo [PowerShellu](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications). Existují však některé scénáře, kde budete muset upravit manifest aplikace pro konfiguraci atributu aplikace. Mezi tyto scénáře patří:

* Pokud jste aplikaci zaregistrovali jako víceklientské a osobní účty Microsoft Azure AD, nemůžete v uživatelském rozhraní změnit podporované účty Microsoft. Místo toho je nutné použít editor manifestu aplikace ke změně typu podporovaného účtu.
* Pokud potřebujete definovat oprávnění a role, které vaše aplikace podporuje, musíte upravit manifest aplikace.

## <a name="configure-the-app-manifest"></a>Konfigurace manifestu aplikace

Postup konfigurace manifestu aplikace:

1. Přejděte na [portál Azure](https://portal.azure.com). Vyhledejte a vyberte službu **Azure Active Directory.**
1. Vyberte **Registrace aplikací**.
1. Vyberte aplikaci, kterou chcete konfigurovat.
1. Na stránce **Přehled** aplikace vyberte část **Manifest**. Otevře se webový editor manifestu, který umožňuje upravovat manifest v rámci portálu. Volitelně můžete vybrat **Stáhnout** a upravit manifest místně a pak ho použít **pomocí funkce Odeslat** znovu použít ve vaší aplikaci.

## <a name="manifest-reference"></a>Odkaz na manifest

Tato část popisuje atributy nalezené v manifestu aplikace.

### <a name="accesstokenacceptedversion-attribute"></a>atribut accessTokenAcceptedVersion

| Klíč | Typ hodnoty |
| :--- | :--- |
| accessTokenAcceptedVersion | Int32, který lze zrušit |

Určuje verzi přístupového tokenu očekávanou u prostředku. Tento parametr změní verzi a formát JWT vyrobené nezávisle na koncovém bodu nebo klienta použitém k vyžádání přístupového tokenu.

Použitý koncový bod, v1.0 nebo v2.0, je vybrán klientem a má vliv pouze na verzi id_tokens. Prostředky je třeba `accesstokenAcceptedVersion` explicitně nakonfigurovat k označení podporovaného formátu tokenu přístupu.

Možné hodnoty `accesstokenAcceptedVersion` pro jsou 1, 2 nebo null. Pokud je hodnota null, tento parametr výchozí 1, který odpovídá v1.0 koncový bod.

Pokud `signInAudience` `AzureADandPersonalMicrosoftAccount`je , hodnota `2`musí být .

Příklad:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| Doplňky | Kolekce |

Definuje vlastní chování, které může spotřebitelská služba použít k volání aplikace v konkrétních kontextech. Například aplikace, které mohou vykreslit `addIns` datové proudy souborů, mohou nastavit vlastnost pro svou funkci "FileHandler". Tento parametr umožní službám, jako je Office 365, volat aplikaci v kontextu dokumentu, na kterém uživatel pracuje.

Příklad:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>atribut allowPublicClient

| Klíč | Typ hodnoty |
| :--- | :--- |
| allowPublicClient | Logická hodnota |

Určuje typ záložní aplikace. Azure AD odvodí typ aplikace z replyUrlsWithType ve výchozím nastavení. Existují určité scénáře, kde Azure AD nelze určit typ klientské aplikace. Například jeden takový scénář je [tok ROPC,](https://tools.ietf.org/html/rfc6749#section-4.3) kde http požadavek se stane bez přesměrování adresy URL). V těchto případech Azure AD bude interpretovat typ aplikace na základě hodnoty této vlastnosti. Pokud je tato hodnota nastavena na hodnotu true, je typ záložní aplikace nastaven jako veřejný klient, například nainstalovaná aplikace spuštěná na mobilním zařízení. Výchozí hodnota je false, což znamená, že typ záložní aplikace je důvěrný klient, jako je například webová aplikace.

Příklad:

```json
    "allowPublicClient": false,
```

### <a name="availabletoothertenants-attribute"></a>atribut availableToOtherTenants

| Klíč | Typ hodnoty |
| :--- | :--- |
| availableToOtherTenants | Logická hodnota |

Nastavte hodnotu true, pokud je aplikace sdílena s ostatními klienty; jinak false.

> [!NOTE]
> Tento atribut je k dispozici pouze v **prostředí registrace aplikací (Starší verze).** Nahrazeno `signInAudience` v prostředí [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="appid-attribute"></a>atribut appId

| Klíč | Typ hodnoty |
| :--- | :--- |
| appId | Řetězec |

Určuje jedinečný identifikátor aplikace, která je přiřazena k aplikaci azure ad.

Příklad:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>atribut appRoles

| Klíč | Typ hodnoty |
| :--- | :--- |
| appRoles | Kolekce |

Určuje kolekci rolí, které může aplikace deklarovat. Tyto role lze přiřadit uživatelům, skupinám nebo instančním objektům. Další příklady a informace najdete [v tématu Přidání rolí aplikace v aplikaci a jejich přijetí v tokenu](howto-add-app-roles-in-azure-ad-apps.md).

Příklad:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="displayname-attribute"></a>displayName, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| displayName | Řetězec |

Zobrazovaný název aplikace.

> [!NOTE]
> Tento atribut je k dispozici pouze v **prostředí registrace aplikací (Starší verze).** Nahrazeno `name` v prostředí [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="errorurl-attribute"></a>errorUrl, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| errorUrl | Řetězec |

Nepodporovaný.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
|groupMembershipClaims | Řetězec |

Konfiguruje `groups` deklaraci vydanou v uživatelském nebo přístupovém tokenu OAuth 2.0, který aplikace očekává. Chcete-li nastavit tento atribut, použijte jednu z následujících platných hodnot řetězce:

- `"None"`
- `"SecurityGroup"`(pro skupiny zabezpečení a role Azure AD)
- `"All"`(tím získáte všechny skupiny zabezpečení, distribuční skupiny a role adresáře Azure AD, kterých je přihlášený uživatel členem.

Příklad:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="homepage-attribute"></a>atribut domovskou stránku

| Klíč | Typ hodnoty |
| :--- | :--- |
| Domovskou stránku |Řetězec |

Adresa URL domovské stránky aplikace.

> [!NOTE]
> Tento atribut je k dispozici pouze v **prostředí registrace aplikací (Starší verze).** Nahrazeno `signInUrl` v prostředí [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="objectid-attribute"></a>atribut objectId

| Klíč | Typ hodnoty |
| :--- | :--- |
|Objectid | Řetězec |

Jedinečný identifikátor aplikace v adresáři.

To je k dispozici pouze v **prostředí registrace aplikací (Starší verze).** Nahrazeno `id` v prostředí [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)

Příklad:

```json
    "objectId": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="optionalclaims-attribute"></a>optionalClaims atribut optionalClaims atribut optionalClaims atribut optionalClaims

| Klíč | Typ hodnoty |
| :--- | :--- |
| volitelnéNároky | Řetězec |

Volitelné deklarace identity vrácené v tokenu službou tokenu zabezpečení pro tuto konkrétní aplikaci.

V tuto chvíli aplikace, které podporují osobní účty a Azure AD (registrované prostřednictvím portálu pro registraci aplikací) nelze použít volitelné deklarace identity. Aplikace registrované pouze pro Azure AD pomocí koncového bodu v2.0 však můžete získat volitelné deklarace identity, které požadovali v manifestu. Další informace naleznete v [tématu Volitelné deklarace identity](active-directory-optional-claims.md).

Příklad:

```json
    "optionalClaims": null,
```

### <a name="id-attribute"></a>id, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| id | Řetězec |

Jedinečný identifikátor aplikace v adresáři. Toto ID není identifikátor používaný k identifikaci aplikace v jakékoli transakci protokolu. Používá se pro odkazování na objekt v adresářových dotazech.

Příklad:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="identifieruris-attribute"></a>atribut identifierUris

| Klíč | Typ hodnoty |
| :--- | :--- |
| identifikátorUris | Pole řetězců |

Uživatelem definované identifikátory URI, které jednoznačně identifikují webovou aplikaci v rámci svého klienta Azure AD nebo v rámci ověřené vlastní domény, pokud je aplikace víceklientská.

Příklad:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| informationalUrls | Řetězec |

Určuje odkazy na smluvní podmínky aplikace a prohlášení o zásadách ochrany osobních údajů. Podmínky služby a prohlášení o zásadách ochrany osobních údajů jsou uživatelům k dispozici prostřednictvím uživatelského souhlasu. Další informace najdete v [tématu Postup: Přidání smluvních podmínek a prohlášení o zásadách ochrany osobních údajů pro registrované aplikace Azure AD](howto-add-terms-of-service-privacy-statement.md).

Příklad:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| keyCredentials | Kolekce |

Obsahuje odkazy na přihlašovací údaje přiřazené k aplikaci, sdílené tajné klíče založené na řetězecích a certifikáty X.509. Tato pověření se používají při požadování přístupových tokenů (když aplikace funguje jako klient spíše jako prostředek).

Příklad:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>atribut knownClientApplications

| Klíč | Typ hodnoty |
| :--- | :--- |
| známé klientské aplikace | Pole řetězců |

Používá se pro sdružování souhlasu, pokud máte řešení, které obsahuje dvě části: klientskou aplikaci a vlastní webAPI aplikaci. Pokud do této hodnoty zadáte id aplikace klienta, uživatel bude muset souhlasit pouze jednou s klientskou aplikací. Azure AD bude vědět, že souhlas s klientem znamená implicitně souhlas s webovým rozhraním API. Automaticky zřdá instanční objekty pro klienta i webové rozhraní API současně. Klient i webová aplikace rozhraní API musí být registrovány ve stejném klientovi.

Příklad:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| logoUrl | Řetězec |

Hodnota pouze pro čtení, která odkazuje na adresu URL CDN na logo, které bylo nahráno na portálu.

Příklad:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>atribut logoutUrl

| Klíč | Typ hodnoty |
| :--- | :--- |
| adresa url odhlášení | Řetězec |

Adresa URL pro odhlášení z aplikace.

Příklad:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atribut name

| Klíč | Typ hodnoty |
| :--- | :--- |
| jméno | Řetězec |

Zobrazovaný název aplikace.

Příklad:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitflow, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2AllowImplicitflow | Logická hodnota |

Určuje, zda tato webová aplikace může požadovat tokeny přístupu k toku OAuth2.0 implicitní. Výchozí hodnotou je hodnota false. Tento příznak se používá pro aplikace založené na prohlížeči, jako jsou jednostránkové aplikace JavaScriptu. Další informace zadejte do `OAuth 2.0 implicit grant flow` obsahu a přečtěte si témata o implicitním toku.

Příklad:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2AllowIdTokenImplicitflow | Logická hodnota |

Určuje, zda tato webová aplikace může požadovat tokeny Implicitní tok OAuth2.0. Výchozí hodnotou je hodnota false. Tento příznak se používá pro aplikace založené na prohlížeči, jako jsou jednostránkové aplikace JavaScriptu.

Příklad:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2Oprávnění | Kolekce |

Určuje kolekci oborů oprávnění OAuth 2.0, které aplikace webového rozhraní API (prostředek) zpřístupňuje klientským aplikacím. Tyto obory oprávnění mohou být uděleny klientským aplikacím během souhlasu.

Příklad:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2RequiredPostResponse | Logická hodnota |

Určuje, zda jako součást oauth 2.0 požadavky na token Azure AD povolí požadavky POST, na rozdíl od get požadavky. Výchozí hodnota je false, která určuje, že budou povoleny pouze požadavky GET.

Příklad:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| parentalControlSettings | Řetězec |

- `countriesBlockedForMinors`určuje země, ve kterých je aplikace blokována pro nezletilé.
- `legalAgeGroupRule`určuje pravidlo zákonné věkové skupiny, které se vztahuje na uživatele aplikace. Lze nastavit `Allow`na `RequireConsentForPrivacyServices` `RequireConsentForMinors`, `RequireConsentForKids`, `BlockMinors`, , nebo .  

Příklad:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| hesloPověření | Kolekce |

Podívejte se na `keyCredentials` popis vlastnosti.

Příklad:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>atribut preAuthorizedApplications

| Klíč | Typ hodnoty |
| :--- | :--- |
| aplikace preAuthorized | Kolekce |

Zobrazí seznam aplikací a požadovaných oprávnění pro implicitní souhlas. Vyžaduje, aby správce poskytl souhlas s aplikací. preAuthorizedApplications nevyžadují, aby uživatel souhlasil s požadovanými oprávněními. Oprávnění uvedená v preAuthorizedApplications nevyžadují souhlas uživatele. Všechna další požadovaná oprávnění, která nejsou uvedena v preAuthorizedApplications však vyžadují souhlas uživatele.

Příklad:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publicclient-attribute"></a>atribut publicClient

| Klíč | Typ hodnoty |
| :--- | :--- |
| publicClient | Logická hodnota|

Určuje, zda je tato aplikace veřejným klientem (například nainstalovanou aplikací spuštěnou na mobilním zařízení). 

Tato vlastnost je k dispozici pouze v **prostředí registrace aplikací (Starší verze).** Nahrazeno `allowPublicClient` v prostředí [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="publisherdomain-attribute"></a>atribut publisherDomain

| Klíč | Typ hodnoty |
| :--- | :--- |
| vydavatelDoména | Řetězec |

Ověřená doména vydavatele pro aplikaci. Jen pro čtení.

Příklad:

```json
    "publisherDomain": "https://www.contoso.com",
````

### <a name="replyurls-attribute"></a>atribut replyUrls

| Klíč | Typ hodnoty |
| :--- | :--- |
| replyUrls | Pole řetězců |

Tato vlastnost s více hodnotami obsahuje seznam registrovaných hodnot redirect_uri, které služba Azure AD přijme jako cíle při vracení tokenů.

Tato vlastnost je k dispozici pouze v **prostředí registrace aplikací (Starší verze).** Nahrazeno `replyUrlsWithType` v prostředí [registrace aplikací.](https://go.microsoft.com/fwlink/?linkid=2083908)

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| replyUrlsWithType | Kolekce |

Tato vlastnost s více hodnotami obsahuje seznam registrovaných hodnot redirect_uri, které služba Azure AD přijme jako cíle při vracení tokenů. Každá hodnota identifikátoru URI by měla obsahovat přidruženou hodnotu typu aplikace. Podporované hodnoty typu jsou:

- `Web`
- `InstalledClient`

Další informace naleznete v [tématu replyUrl omezení a omezení](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

Příklad:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>atribut requiredResourceAccess

| Klíč | Typ hodnoty |
| :--- | :--- |
| requiredResourceAccess | Kolekce |

S dynamickým `requiredResourceAccess` souhlasem řídí prostředí souhlasu správce a uživatelské prostředí pro uživatele, kteří používají statický souhlas. Tento parametr však neřídí uživatelské ho souhlasu pro obecný případ.

- `resourceAppId`je jedinečný identifikátor prostředku, ke kterému aplikace vyžaduje přístup. Tato hodnota by měla být rovna appId deklarované v cílové množiny prostředků.
- `resourceAccess`je pole, které obsahuje seznam oborů oprávnění OAuth2.0 a rolí aplikací, které aplikace vyžaduje od zadaného prostředku. Obsahuje `id` hodnoty `type` a zadané prostředky.

Příklad:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>atribut samlMetadataUrl

| Klíč | Typ hodnoty |
| :--- | :--- |
| samlMetadataUrl | Řetězec |

Adresa URL metadat SAML pro aplikaci.

Příklad:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| signInUrl | Řetězec |

Určuje adresu URL domovské stránky aplikace.

Příklad:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience, atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| signInAudience | Řetězec |

Určuje, jaké účty Microsoft jsou podporovány pro aktuální aplikaci. Podporované hodnoty jsou:
- `AzureADMyOrg`- Uživatelé s pracovním nebo školním účtem Microsoftu v tenantovi Azure AD v mé organizaci (například jeden tenant)
- `AzureADMultipleOrgs`- Uživatelé s pracovním nebo školním účtem Microsoftu v tenantovi Azure AD libovolné organizace (například s více klienty).
- `AzureADandPersonalMicrosoftAccount`- Uživatelé s osobním účtem Microsoft nebo pracovním nebo školním účtem v tenantovi Azure AD jakékoli organizace
- `PersonalMicrosoftAccount`- Osobní účty, které se používají k přihlášení ke službám, jako je Xbox a Skype.

Příklad:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>atribut tagy

| Klíč | Typ hodnoty |
| :--- | :--- |
| tags | Pole řetězců  |

Vlastní řetězce, které lze použít ke kategorizaci a identifikaci aplikace.

Příklad:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Běžné problémy

### <a name="manifest-limits"></a>Limity manifestu

Manifest aplikace má více atributů, které jsou označovány jako kolekce; například appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess a oauth2Permissions. V rámci úplného manifestu žádosti pro každou přihlášku byl celkový počet položek ve všech kombinovaných sbírkách omezen na 1200. Pokud jste dříve zadat 100 přesměrování IDENTIFIKÁTORŮ URI v manifestu aplikace, pak jste jen odešel s 1100 zbývajícípoložky pro použití ve všech ostatních kolekcí v kombinaci, které tvoří manifest.

> [!NOTE]
> V případě, že se pokusíte přidat více než 1200 položek v manifestu aplikace, může se zobrazit chyba **"Nepodařilo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: Velikost manifestu překročila jeho limit. Snižte počet hodnot a opakujte požadavek."**

### <a name="unsupported-attributes"></a>Nepodporované atributy

Manifest aplikace představuje schéma základního aplikačního modelu ve službě Azure AD. Jak se vyvíjí základní schéma, editor manifestu bude čas od času aktualizován tak, aby odrážel nové schéma. V důsledku toho můžete zaznamenat nové atributy, které se zobrazují v manifestu aplikace. Ve výjimečných případech můžete zaznamenat syntaktické nebo sémantické změny v existující atributy nebo můžete najít atribut, který existoval dříve nejsou podporovány již. Například se zobrazí nové atributy v [registracích aplikací](https://go.microsoft.com/fwlink/?linkid=2083908), které jsou známé s jiným názvem v prostředí registrace aplikací (Starší verze).

| Registrace aplikací (starší verze)| Registrace aplikací           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Popisy těchto atributů naleznete v [části s odkazem](#manifest-reference) na manifest.

Při pokusu o nahrání dříve staženého manifestu se může zobrazit jedna z následujících chyb. Tato chyba je pravděpodobně, protože editor manifestu nyní podporuje novější verzi schématu, která se neshoduje s verzí, kterou se pokoušíte nahrát.

* "Aktualizace aplikace xxxxxx se nezdařila. Podrobnosti o chybě: Neplatný identifikátor objektu "undefined". []."
* "Aktualizace aplikace xxxxxx se nezdařila. Podrobnosti o chybě: Jedna nebo více zadaných hodnot vlastností je neplatných. []."
* "Aktualizace aplikace xxxxxx se nezdařila. Podrobnosti o chybě: Není povoleno nastavit k dispoziciOtherTenants v této verzi rozhraní API pro aktualizaci. []."
* "Aktualizace aplikace xxxxxx se nezdařila. Podrobnosti o chybě: Aktualizace vlastnosti 'replyUrls' není pro tuto aplikaci povolena. Místo toho použijte vlastnost 'replyUrlsWithType'. []."
* "Aktualizace aplikace xxxxxx se nezdařila. Podrobnosti o chybě: Byla nalezena hodnota bez názvu typu a není k dispozici žádný očekávaný typ. Pokud je zadán model, každá hodnota v datové části musí mít typ, který může být zadán v datové části, explicitně volajícím nebo implicitně odvozen z nadřazené hodnoty. []"

Pokud se zobrazí jedna z těchto chyb, doporučujeme následující akce:

1. Upravte atributy jednotlivě v editoru manifestu namísto nahrání dříve staženého manifestu. Pomocí tabulky [odkazů manifestu](#manifest-reference) můžete porozumět syntaxi a sémantice starých a nových atributů, abyste mohli úspěšně upravit atributy, které vás zajímají. 
1. Pokud váš pracovní postup vyžaduje, abyste uložili manifesty do zdrojového úložiště pro pozdější použití, doporučujeme přepnutí uložených manifestů ve vašem úložišti s tím, které se zobrazí v prostředí **registrace aplikací.**

## <a name="next-steps"></a>Další kroky

* Další informace o vztahu mezi objekty aplikačního a instančního objektu aplikace a objekty instančního objektu najdete [v tématu Objekty hlavního nastavení aplikací a služeb ve službě Azure AD](app-objects-and-service-principals.md).
* Definice některých základních konceptů vývojáře platformy microsoftových identit najdete v [glosáři vývojářů platformy Microsoft pro](developer-glossary.md) vývojáře identit.

V následující části s komentáři můžete poskytnout zpětnou vazbu, která pomůže upřesnit a utvářet náš obsah.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
