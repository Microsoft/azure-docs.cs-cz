---
title: Princip Azure Active Directory manifestu aplikace
titleSuffix: Microsoft identity platform
description: Podrobné pokrytí manifestu Azure Active Directory aplikace, který představuje konfiguraci identity aplikace v tenantovi Azure AD a používá se k usnadnění autorizace OAuth, používání souhlasu a dalších.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 04/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: d855e124c84dee8554073d05fa04fe078b92ddaa
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208885"
---
# <a name="azure-active-directory-app-manifest"></a>Manifest aplikace Azure Active Directory

Manifest aplikace obsahuje definici všech atributů objektu aplikace na platformě Microsoft identity. Slouží také jako mechanismus pro aktualizaci objektu aplikace. Další informace o entitě aplikace a jejím schématu naleznete v dokumentaci k [entitě aplikace Graph API](/graph/api/resources/application).

Atributy aplikace můžete nakonfigurovat pomocí Azure Portal nebo programově pomocí [REST API](/graph/api/resources/application) nebo [PowerShellu](/powershell/module/azuread#applications). Existují však situace, kdy budete muset upravit manifest aplikace a nakonfigurovat atribut aplikace. Mezi tyto scénáře patří:

* Pokud jste aplikaci zaregistrovali jako víceklientské a osobní účty Microsoft Azure AD, nemůžete v uživatelském rozhraní měnit podporované účty Microsoft. Místo toho je nutné použít editor manifestu aplikace ke změně podporovaného typu účtu.
* Pokud potřebujete definovat oprávnění a role, které vaše aplikace podporuje, je nutné změnit manifest aplikace.

## <a name="configure-the-app-manifest"></a>Konfigurace manifestu aplikace

Konfigurace manifestu aplikace:

1. Přejít na <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Vyhledejte a vyberte službu **Azure Active Directory** .
1. Vyberte **Registrace aplikací**.
1. Vyberte aplikaci, kterou chcete nakonfigurovat.
1. Na stránce **Přehled** aplikace vyberte část **Manifest**. Otevře se webový editor manifestu, který umožňuje upravovat manifest v rámci portálu. Volitelně můžete vybrat **Stáhnout** a upravit manifest místně a potom použít **nahrávání** pro jeho opakované použití do aplikace.

## <a name="manifest-reference"></a>Odkaz na manifest

Tato část popisuje atributy nalezené v manifestu aplikace.

### <a name="id-attribute"></a>atribut ID

| Klíč | Typ hodnoty |
| :--- | :--- |
| id | Řetězec |

Jedinečný identifikátor aplikace v adresáři Toto ID není identifikátor používaný k identifikaci aplikace v jakékoli transakci protokolu. Používá se pro odkazování na objekt v dotazech adresářů.

Příklad:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| accessTokenAcceptedVersion | Hodnota Int32 s možnou hodnotou null |

Určuje verzi přístupového tokenu, kterou prostředek očekával. Tento parametr změní verzi a formát tokenu JWT vyprodukovaného nezávisle na koncovém bodu nebo klientovi, který se použil k vyžádání přístupového tokenu.

Koncový bod použitý v 1.0 nebo v 2.0 je vybraný klientem a má vliv jenom na verzi id_tokens. Prostředky musí být explicitně nakonfigurovány `accesstokenAcceptedVersion` tak, aby označovaly formát podporovaného přístupového tokenu.

Možné hodnoty pro `accesstokenAcceptedVersion` jsou 1, 2 nebo null. Pokud je hodnota null, tento parametr se nastaví na hodnotu 1, která odpovídá koncovému bodu v 1.0.

Pokud `signInAudience` má `AzureADandPersonalMicrosoftAccount` hodnotu, musí být hodnota `2` .

Příklad:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| addIns | Kolekce |

Definuje vlastní chování, které může přijímající služba použít k volání aplikace v konkrétních kontextech. Například aplikace, které mohou vykreslovat datové proudy souborů, mohou nastavit `addIns` vlastnost pro její funkci "handlerer". Tento parametr umožní službám, jako Microsoft 365 volat aplikaci v kontextu dokumentu, na kterém uživatel pracuje.

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

### <a name="allowpublicclient-attribute"></a>allowPublicClient – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| allowPublicClient | Logická hodnota |

Určuje typ záložní aplikace. Služba Azure AD ve výchozím nastavení odvodí typ aplikace z replyUrlsWithType. Existují některé scénáře, kdy služba Azure AD nemůže určit typ klientské aplikace. Například jedním z takových scénářů je [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) tok, ve kterém se požadavek HTTP stane bez přesměrování adresy URL). V těchto případech služba Azure AD bude interpretovat typ aplikace na základě hodnoty této vlastnosti. Pokud je tato hodnota nastavená na true, typ záložní aplikace se nastaví jako veřejný klient, jako je například nainstalovaná aplikace spuštěná v mobilním zařízení. Výchozí hodnota je false, což znamená, že typ záložní aplikace je důvěrný klient, jako je například webová aplikace.

Příklad:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>atribut appId

| Klíč | Typ hodnoty |
| :--- | :--- |
| appId | Řetězec |

Určuje jedinečný identifikátor pro aplikaci, která je přiřazená aplikaci pomocí Azure AD.

Příklad:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| appRoles | Kolekce |

Určuje kolekci rolí, které může aplikace deklarovat. Tyto role se dají přiřadit uživatelům, skupinám nebo objektům služby. Další příklady a informace najdete v tématu [Přidání rolí aplikace v aplikaci a jejich přijetí v tokenu](howto-add-app-roles-in-azure-ad-apps.md).

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

### <a name="errorurl-attribute"></a>errorUrl – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| errorUrl | Řetězec |

Neplatné.

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
|groupMembershipClaims | Řetězec |

Nakonfiguruje `groups` deklaraci identity vydanou v uživatelském nebo přístupovém tokenu OAuth 2,0, který očekává aplikace. Chcete-li nastavit tento atribut, použijte jednu z následujících platných řetězcových hodnot:

- `"None"`
- `"SecurityGroup"` (pro skupiny zabezpečení a role Azure AD)
- `"ApplicationGroup"` (Tato možnost zahrnuje jenom skupiny, které jsou přiřazené aplikaci.)
- `"All"` (zobrazí se všechny skupiny zabezpečení, distribuční skupiny a role adresáře Azure AD, kterých je přihlášený uživatel členem.

Příklad:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>optionalClaims – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| optionalClaims | Řetězec |

Volitelné deklarace identity vrácené v tokenu službou tokenu zabezpečení pro tuto konkrétní aplikaci.

V tuto chvíli aplikace, které podporují osobní účty i službu Azure AD (zaregistrované prostřednictvím portálu pro registraci aplikací), nemůžou používat volitelné deklarace identity. Aplikace zaregistrované pro jenom Azure AD pomocí koncového bodu v 2.0 ale můžou získat volitelné deklarace identity, které požadoval v manifestu. Další informace najdete v tématu [volitelné deklarace identity](active-directory-optional-claims.md).

Příklad:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>identifierUris – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| identifierUris | Pole řetězců |

Uživatelsky definované identifikátory URI, které jedinečně identifikují webovou aplikaci v rámci jejího tenanta Azure AD, nebo v ověřené vlastní doméně, pokud je aplikace více tenantů.

Příklad:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| informationalUrls | Řetězec |

Určuje odkazy na podmínku služby a prohlášení o zásadách ochrany osobních údajů aplikace. Podmínky služby a prohlášení o zásadách ochrany osobních údajů jsou v souladu s uživatelským prostředím týkajícím se souhlasu uživatele. Další informace najdete v tématu [Postup: Přidání podmínek služby a prohlášení o zásadách ochrany osobních údajů pro registrované aplikace služby Azure AD](howto-add-terms-of-service-privacy-statement.md).

Příklad:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>Přihlašovací údaje atributu

| Klíč | Typ hodnoty |
| :--- | :--- |
| keyCredentials | Kolekce |

Obsahuje odkazy na přihlašovací údaje přiřazené aplikacím, sdílené tajné klíče založené na řetězci a certifikáty X. 509. Tyto přihlašovací údaje se používají při požadování přístupových tokenů (když aplikace funguje jako klient, ale jako prostředek).

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

### <a name="knownclientapplications-attribute"></a>knownClientApplications – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| knownClientApplications | Pole řetězců |

Používá se ke sdružování souhlasu, pokud máte řešení, které obsahuje dvě části: klientská aplikace a vlastní aplikace webového rozhraní API. Pokud zadáte appID klientské aplikace do této hodnoty, bude uživatel muset pro klientskou aplikaci pouze odsouhlasit. Služba Azure AD bude mít za to, že se souhlasem s klientem znamená, že implicitně souhlasí s webovým rozhraním API. Budou automaticky zřizovat instanční objekty pro klientské i webové rozhraní API současně. Klient i aplikace webového rozhraní API musí být zaregistrované ve stejném tenantovi.

Příklad:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| logoUrl | Řetězec |

Hodnota jen pro čtení, která odkazuje na adresu URL CDN na logo, které se nahrálo na portálu.

Příklad:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| logoutUrl | Řetězec |

Adresa URL pro odhlášení z aplikace

Příklad:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atribut Name

| Klíč | Typ hodnoty |
| :--- | :--- |
| name | Řetězec |

Zobrazovaný název aplikace

Příklad:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2AllowImplicitFlow | Logická hodnota |

Určuje, jestli tato webová aplikace může vyžádat tokeny přístupu implicitního toku OAuth 2.0. Výchozí hodnotou je hodnota false. Tento příznak se používá pro aplikace založené na prohlížeči, jako jsou například jednostránkové aplikace JavaScriptu. Pokud se chcete dozvědět víc, zadejte `OAuth 2.0 implicit grant flow` do obsahu obsah a podívejte se na témata popisující implicitní tok.

Příklad:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Logická hodnota |

Určuje, jestli tato webová aplikace může vyžádat tokeny pro implicitní ID toku OAuth 2.0. Výchozí hodnotou je hodnota false. Tento příznak se používá pro aplikace založené na prohlížeči, jako jsou například jednostránkové aplikace JavaScriptu.

Příklad:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2Permissions | Kolekce |

Určuje kolekci oborů oprávnění OAuth 2,0, které aplikace webového rozhraní API (Resource) zpřístupňuje klientským aplikacím. Tyto obory oprávnění se můžou klientským aplikacím udělit během souhlasu.

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

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| oauth2RequiredPostResponse | Logická hodnota |

Určuje, jestli v rámci požadavků na token OAuth 2,0 Azure AD umožní žádosti POST, a to na rozdíl od získání požadavků. Výchozí hodnota je false (NEPRAVDA), která určuje, že budou povoleny pouze požadavky GET.

Příklad:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| parentalControlSettings | Řetězec |

- `countriesBlockedForMinors` Určuje země nebo oblasti, ve kterých je aplikace blokovaná pro nezletilé.
- `legalAgeGroupRule` Určuje pravidlo pro věkovou skupinu platné pro uživatele aplikace. Lze nastavit na `Allow` , `RequireConsentForPrivacyServices` ,, `RequireConsentForMinors` `RequireConsentForKids` nebo `BlockMinors` .

Příklad:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| passwordCredentials | Kolekce |

Podívejte se na popis `keyCredentials` Vlastnosti.

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

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| preAuthorizedApplications | Kolekce |

Vypíše aplikace a požadovaná oprávnění pro implicitní souhlas. Vyžaduje, aby správce poskytl souhlas s aplikací. preAuthorizedApplications nevyžaduje, aby uživatel mohl udělit souhlas s požadovanými oprávněními. Oprávnění uvedená v preAuthorizedApplications nevyžadují souhlas uživatele. Nicméně jakákoli další požadovaná oprávnění, která nejsou uvedená v preAuthorizedApplications, vyžadují souhlas uživatele.

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

### <a name="publisherdomain-attribute"></a>publisherDomain – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| publisherDomain | Řetězec |

Ověřená doména vydavatele pro aplikaci. Jen pro čtení.

Příklad:

```json
    "publisherDomain": "https://www.contoso.com",
```

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| replyUrlsWithType | Kolekce |

Tato vlastnost s více hodnotami obsahuje seznam registrovaných redirect_uri hodnot, které služba Azure AD přijme jako cílová umístění při vracení tokenů. Každá hodnota identifikátoru URI by měla obsahovat přidruženou hodnotu typu aplikace. Podporované hodnoty typu jsou:

- `Web`
- `InstalledClient`
- `Spa`

Další informace najdete v tématu [omezení a omezení replyUrl](./reply-url.md).

Příklad:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| requiredResourceAccess | Kolekce |

S dynamickým souhlasem se `requiredResourceAccess` pro uživatele, kteří používají statický souhlas, řídí prostředí pro vyjádření souhlasu správce a uživatelské prostředí pro vyjádření souhlasu uživatele. Tento parametr ale neřídí uživatelské prostředí pro vyjádření souhlasu uživatele pro obecný případ.

- `resourceAppId` je jedinečný identifikátor pro prostředek, ke kterému aplikace vyžaduje přístup. Tato hodnota by měla být stejná jako appId deklarované v cílové aplikaci prostředků.
- `resourceAccess` je pole, ve kterém jsou uvedené obory oprávnění OAuth 2.0 a aplikační role, které aplikace vyžaduje ze zadaného prostředku. Obsahuje `id` hodnoty a pro `type` zadané prostředky.

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

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| samlMetadataUrl | Řetězec |

Adresa URL metadat SAML pro aplikaci

Příklad:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| signInUrl | Řetězec |

Určuje adresu URL domovské stránky aplikace.

Příklad:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience – atribut

| Klíč | Typ hodnoty |
| :--- | :--- |
| signInAudience | Řetězec |

Určuje, jaké účty Microsoft se pro aktuální aplikaci podporují. Podporované hodnoty jsou:
- `AzureADMyOrg` – Uživatelé s pracovním nebo školním účtem Microsoft v tenantovi Azure AD ve vaší organizaci (například jeden tenant)
- `AzureADMultipleOrgs` – Uživatelé s pracovním nebo školním účtem Microsoft v tenantovi Azure AD ve vaší organizaci (například víceklientská tenant)
- `AzureADandPersonalMicrosoftAccount` – Uživatelé s osobní účet Microsoft nebo pracovní nebo školní účet v tenantovi Azure AD ve vaší organizaci
- `PersonalMicrosoftAccount` – Osobní účty používané pro přihlášení ke službám, jako jsou Xbox a Skype.

Příklad:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>atribut Tags

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

### <a name="manifest-limits"></a>Omezení manifestu

Manifest aplikace má více atributů, které jsou označovány jako kolekce; například appRoles, přihlašovací údaje, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess a oauth2Permissions. V rámci kompletního manifestu aplikace pro libovolnou aplikaci byl celkový počet položek ve všech kolekcích kombinovaný v kombinaci s omezené v 1200. Pokud jste v manifestu aplikace dříve zadali 100 identifikátorů URI pro přesměrování, budete mít k pouze 1100 zbývajících záznamů pro použití ve všech ostatních kolekcích, které dohromady tvoří manifest.

> [!NOTE]
> V případě, že se pokusíte přidat více než 1200 záznamů v manifestu aplikace, může se zobrazit chyba **"Nepodařilo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: Velikost manifestu překročila svůj limit. Snižte počet hodnot a opakujte požadavek. "**

### <a name="unsupported-attributes"></a>Nepodporované atributy

Manifest aplikace představuje schéma základního aplikačního modelu ve službě Azure AD. V rámci vývoje základního schématu se editor manifestu aktualizuje tak, aby odrážel nové schéma od času až po čas. V důsledku toho můžete všimnout, že se nové atributy zobrazí v manifestu aplikace. Ve výjimečných případech si můžete všimnout syntaktické nebo sémantické změny v existujících atributech. můžete ale najít již dříve nepodporovaný atribut. V [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908)se například zobrazí nové atributy, které se v prostředí registrace aplikací (starší verze) označují jiným názvem.

| Registrace aplikací (starší verze)| Registrace aplikací           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Popisy těchto atributů naleznete v části Reference k [manifestu](#manifest-reference) .

Při pokusu o nahrání dříve staženého manifestu se může zobrazit jedna z následujících chyb. Tato chyba je pravděpodobně způsobena tím, že editor manifestu teď podporuje novější verzi schématu, která neodpovídá tomu, který se pokoušíte nahrát.

* Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: neplatný identifikátor objektu undefined. []."
* Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: jedna nebo více zadaných hodnot vlastností je neplatných. []."
* Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: nepovoluje se nastavit availableToOtherTenants v této verzi rozhraní API pro aktualizaci. []."
* Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: aktualizace vlastnosti ' replyUrls ' není pro tuto aplikaci povolena. Místo toho použijte vlastnost ' replyUrlsWithType '. []."
* Nepovedlo se aktualizovat aplikaci xxxxxx. Podrobnosti o chybě: byla nalezena hodnota bez názvu typu a není k dispozici žádný očekávaný typ. Při zadání modelu musí mít každá hodnota v datové části typ, který může být buď zadán v datové části, explicitně volajícím nebo implicitně odvozený z nadřazené hodnoty. []"

Když se zobrazí jedna z těchto chyb, doporučujeme, abyste provedli následující akce:

1. Upravte atributy jednotlivě v editoru manifestu namísto nahrávání dříve staženého manifestu. Použijte [referenční tabulku manifestu](#manifest-reference) k pochopení syntaxe a sémantiky starých a nových atributů, abyste mohli úspěšně upravit atributy, které vás zajímají.
1. Pokud váš pracovní postup vyžaduje, abyste uložili manifesty ve zdrojovém úložišti pro pozdější použití, doporučujeme, abyste uložili manifesty v úložišti, které jste viděli v prostředí **Registrace aplikací** .

## <a name="next-steps"></a>Další kroky

* Další informace o vztahu mezi aplikací aplikace a objekty instančních objektů najdete v tématu [aplikace a instanční objekty služby v Azure AD](app-objects-and-service-principals.md).
* V tématu [Microsoft Identity Platform Developer Glosář](developer-glossary.md) najdete definice některých základních konceptů vývojářů platformy Microsoft Identity Platform.

Následující komentáře vám poskytnou zpětnou vazbu, která pomáhá Upřesnit a natvarovat obsah.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
