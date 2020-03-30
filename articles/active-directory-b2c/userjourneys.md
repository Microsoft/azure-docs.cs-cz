---
title: UserJourneys | Dokumenty společnosti Microsoft
description: Zadejte prvek UserJourneys vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78227001"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cesty uživatele určují explicitní cesty, jejichž prostřednictvím zásada umožňuje aplikaci předávající strany získat požadované deklarace identity pro uživatele. Uživatel je přijata prostřednictvím těchto cest načíst deklarace identity, které mají být předloženy předávající strany. Jinými slovy, cesty uživatelů definují obchodní logiku toho, co koncový uživatel prochází jako rozhraní Azure AD B2C Identity Experience Framework zpracovává požadavek.

Tyto cesty uživatelů lze považovat za šablony, které jsou k dispozici pro uspokojení základní potřeby různých předávajících stran zájmových skupin. Cesty uživatele usnadňují definici části zásady předávající strany. Zásada může definovat více cest uživatelů. Každá cesta uživatele je posloupnost kroků orchestrace.

Chcete-li definovat cesty uživatele podporované zásadou, je prvek **UserJourneys** přidán pod prvek nejvyšší úrovně souboru zásad.

Prvek **UserJourneys** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Cesta uživatele | 1:n | Cesta uživatele, která definuje všechny konstrukce nezbytné pro úplný tok uživatele. |

Element **UserJourney** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor cesty uživatele, který lze použít k odkazování z jiných prvků v zásadě. Na tento atribut odkazuje prvek **DefaultUserJourney** [zásady předávající strany.](relyingparty.md) |

**UserJourney** Element obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Pořadí orchestrace, které musí být dodrženy prostřednictvím pro úspěšnou transakci. Každá cesta uživatele se skládá z uspořádaného seznamu kroků orchestrace, které jsou spouštěny postupně. Pokud některý krok selže, transakce se nezdaří. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Cesta uživatele je reprezentována jako sekvence orchestrace, která musí být dodržena pro úspěšnou transakci. Pokud některý krok selže, transakce se nezdaří. Tyto kroky orchestrace odkazují na stavební bloky a zprostředkovatele deklarací povolené v souboru zásad. Jakýkoli krok orchestrace, který je zodpovědný za zobrazení nebo vykreslení uživatelského prostředí, má také odkaz na odpovídající identifikátor definice obsahu.

Kroky orchestrace lze podmíněně provést na základě předpokladů definovaných v prvku kroku orchestrace. Můžete například zkontrolovat, zda chcete provést krok orchestrace pouze v případě, že existuje určitá deklarace identity nebo pokud je deklarace rovná nebo není zadanou hodnotou.

Chcete-li zadat seřazený seznam kroků orchestrace, **orchestrationsteps** prvek je přidán jako součást zásady. Tento prvek je povinný.

**OrchestrationSteps** Element obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Krok objednané orchestrace. |

**OrchestrationStep** Element obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| `Order` | Ano | Pořadí kroků orchestrace. |
| `Type` | Ano | Typ kroku orchestrace. Možné hodnoty: <ul><li>**ClaimsProviderSelection** - Označuje, že krok orchestrace představuje různé zprostředkovatele deklarací identity pro uživatele vybrat jeden.</li><li>**CombinedSignInAndSignUp** - Označuje, že krok orchestrace představuje kombinované sociální zprostředkovatele přihlášení a místní účet zaregistrovat stránku.</li><li>**ClaimsExchange** - Označuje, že krok orchestrace výměny deklarací s poskytovatelem deklarací.</li><li>**GetClaims** - Označuje, že krok orchestrace přečte vstupní deklarace identity.</li><li>**SendClaims** - Označuje, že krok orchestrace odešle deklarace předávající straně s tokenem vydaným vystavitelem deklarací.</li></ul> |
| ContentDefinitionReferenceId | Ne | Identifikátor definice [obsahu](contentdefinitions.md) přidružené k tomuto kroku orchestrace. Identifikátor odkazu na definici obsahu je obvykle definován v samoobslužném technickém profilu. Ale existují některé případy, kdy Azure AD B2C potřebuje zobrazit něco bez technického profilu. Existují dva příklady – pokud je typ kroku orchestrace `ClaimsProviderSelection` `CombinedSignInAndSignUp`jedním z následujících: nebo , Azure AD B2C potřebuje zobrazit výběr zprostředkovatele identity bez technického profilu. |
| CpimIssuerTechnicalProfileReferenceId | Ne | Typ kroku orchestrace je `SendClaims`. Tato vlastnost definuje identifikátor technického profilu poskytovatele deklarací identity, který vydává token pro předávající stranu.  Pokud chybí, je vytvořen token předávající strany. |


**OrchestrationStep** Element může obsahovat následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Předpoklady | 0:n | Seznam předpokladů, které musí být splněny pro krok orchestrace ke spuštění. |
| Výběry zprostředkovatelů deklarací identity | 0:n | Seznam výběrů zprostředkovatele deklarací pro krok orchestrace. |
| ClaimsExchanges | 0:n | Seznam výměn deklarací identity pro krok orchestrace. |

### <a name="preconditions"></a>Předpoklady

Prvek **Preconditions** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Předběžná podmínka | 1:n | V závislosti na použitém technickém profilu buď přesměruje klienta podle výběru zprostředkovatele deklarací nebo provede volání serveru k výměně deklarací. |


#### <a name="precondition"></a>Předběžná podmínka

Prvek **Condition obsahuje** následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ kontroly nebo dotazu, který má být pro tuto podmínku nastaven. Hodnota může být **ClaimsExist**, který určuje, že akce by měly být provedeny, pokud zadaný deklarace existují v aktuální sadě deklarací uživatele nebo **ClaimEquals**, který určuje, že akce by měly být provedeny, pokud zadaný deklarace existuje a jeho hodnota se rovná zadané hodnotě. |
| `ExecuteActionsIf` | Ano | Použijte test true nebo false k rozhodnutí, zda by měly být provedeny akce v rámci podmínky. |

Prvky **precondition** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Hodnota | 1:n | A ClaimTypeReferenceId, které mají být dotazován. Jiný prvek hodnoty obsahuje hodnotu, která má být zkontrolována.</li></ul>|
| Akce | 1:1 | Akce, která by měla být provedena, pokud je splněna kontrola předběžného stavu v rámci kroku orchestrace. Pokud je hodnota `Action` nastavena `SkipThisOrchestrationStep`na `OrchestrationStep` , přidružené by neměly být provedeny. |

#### <a name="preconditions-examples"></a>Příklady předběžných podmínek

Následující předpoklady zkontroluje, zda existuje objekt id uživatele. V cestě uživatele se uživatel přihlásil pomocí místního účtu. Pokud objektId existuje, přeskočte tento krok orchestrace.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Následující předpoklady zkontroluje, zda se uživatel přihlásil pomocí účtu na sociální síti. Došlo k pokusu o nalezení uživatelského účtu v adresáři. Pokud se uživatel přihlásí nebo zaregistruje pomocí místního účtu, přeskočte tento krok orchestrace.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Předběžné podmínky mohou zkontrolovat více předběžných podmínek. Následující příklad zkontroluje, zda 'objectId' nebo 'e-mail' existuje. Pokud je splněna první podmínka, cesta přeskočí na další krok orchestrace.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>Výběr zprostředkovatele pohledávek

Krok orchestrace typu `ClaimsProviderSelection` `CombinedSignInAndSignUp` nebo může obsahovat seznam zprostředkovatelů deklarací identity, pomocí kterých se uživatel může přihlásit. Pořadí prvků uvnitř `ClaimsProviderSelections` prvků řídí pořadí zprostředkovatelů identit y prezentovaných uživateli.

Prvek **ClaimsProviderSelections** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výběr zprostředkovatele pohledávek | 1:n | Obsahuje seznam zprostředkovatelů deklarací identity, které lze vybrat.|

Prvek **ClaimsProviderSelections** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Zobrazit možnost| Ne | Řídí chování případu, kdy je k dispozici výběr jednoho zprostředkovatele deklarací identity. Možné hodnoty: `DoNotShowSingleProvider` (výchozí) , uživatel je okamžitě přesměrován na poskytovatele federované identity. Nebo `ShowSingleProvider` Azure AD B2C představuje přihlašovací stránku s výběrem jednoho zprostředkovatele identity. Chcete-li použít [content definition version](page-layout.md) tento atribut, `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` musí být verze definice obsahu a vyšší.|

Prvek **ClaimsProviderSelection** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| CílClaimsExchangeId | Ne | Identifikátor výměny deklarací identity, který je proveden v dalším kroku orchestrace výběru zprostředkovatele deklarací. Tento atribut nebo Atribut ValidationClaimsExchangeId musí být zadán, ale ne obojí. |
| ValidaceClaimsExchangeId | Ne | Identifikátor výměny deklarací identity, který je proveden v aktuálním kroku orchestrace k ověření výběru zprostředkovatele deklarací. Tento atribut nebo TargetClaimsExchangeId atribut musí být zadán, ale ne obojí. |

### <a name="claimsproviderselection-example"></a>Příklad výběru claimsProvider

V následujícím kroku orchestrace se uživatel může přihlásit pomocí Facebooku, LinkedInu, Twitteru, Googlu nebo místního účtu. Pokud uživatel vybere jednoho z poskytovatelů sociální identity, druhý krok orchestrace se provede `TargetClaimsExchangeId` s vybranou výměnou deklarací zadanou v atributu. Druhý krok orchestrace přesměruje uživatele na poskytovatele sociální identity k dokončení procesu přihlášení. Pokud se uživatel rozhodne přihlásit pomocí místního účtu, Azure AD B2C zůstane na stejném kroku orchestrace (stejná přihlašovací stránka nebo přihlašovací stránka) a přeskočí druhý krok orchestrace.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

**ClaimsExchanges** Prvek obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1:n | V závislosti na technický profil, který se používá, buď přesměruje klienta podle ClaimsProviderSelection, který byl vybrán, nebo provede volání serveru pro výměnu deklarací. |

Prvek **ClaimsExchange** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor kroku výměny deklarací identity. Identifikátor se používá k odkazování na výměnu deklarací identity z kroku výběru zprostředkovatele deklarací v zásadách. |
| TechnicalProfileReferenceId | Ano | Identifikátor technického profilu, který má být proveden. |
