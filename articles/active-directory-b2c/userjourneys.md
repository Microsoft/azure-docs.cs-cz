---
title: Userjourney | Microsoft Docs
description: Zadejte element Userjourney vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aac75e7876ce59b90e27f9e87c96240755d26235
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120738"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cesty uživatelů určují explicitní cesty, pomocí kterých zásada umožňuje aplikaci předávající strany získat požadované deklarace identity pro uživatele. Uživatel se převezme prostřednictvím těchto cest, aby načetl deklarace identity, které se mají předložit předávající straně. Jinými slovy, cesty uživatelů definují obchodní logiku toho, co koncový uživatel projde, jako Azure AD B2C architektura pro prostředí identity zpracuje požadavek.

Tyto cesty uživatelů je možné považovat za šablony, které jsou k dispozici pro splnění základních potřeb různých předávající strany komunity zájmu. Cesty uživatelů usnadňují definici části zásady předávající strany. Zásada může definovat několik cest uživatelů. Každá cesta uživatele je posloupnost kroků orchestrace.

Pro definování cest uživatelů podporovaných touto zásadou se do prvku nejvyšší úrovně v souboru zásad přidá element **userjourney** .

Element **userjourney** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | Cesta uživatele definující všechny konstrukce, které jsou nezbytné pro kompletní tok uživatele. |

Element **UserJourney** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor cesty uživatele, který lze použít k odkazování na jiný prvek v zásadě. Element **DefaultUserJourney** [zásady předávající strany](relyingparty.md) odkazuje na tento atribut. |

Element **UserJourney** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | Seznam technických profilů autorizace | 
| OrchestrationSteps | 1: n | Sekvence orchestrace, která musí následovat po úspěšné transakci. Každá cesta uživatele se skládá z uspořádaného seznamu kroků orchestrace, které se spustí v posloupnosti. Pokud nějaký krok selhává, transakce se nezdařila. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

Předpokládejme, že uživatel dokončil UserJourney a získal přístup nebo token ID. Aby bylo možné spravovat další prostředky, musí být uživatel identifikován jako [koncový bod UserInfo](userinfo-endpoint.md). Chcete-li spustit tento proces, musí uživatel předložit přístupový token, který byl vydán dříve jako důkaz, že byly původně ověřeny platnou zásadou Azure AD B2C. Platný token pro uživatele musí být vždy přítomen během tohoto procesu, aby bylo zajištěno, že uživatel bude moci tuto žádost vytvořit. Technické profily autorizace ověřují příchozí tokeny a extrahují deklarace identity z tokenu.

Element **AuthorizationTechnicalProfiles** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | Seznam technických profilů autorizace | 

Element **AuthorizationTechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Ano | Identifikátor technického profilu, který má být spuštěn. |

Následující příklad ukazuje prvek cesty uživatele s technickými profily autorizace:

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

Cesta uživatele je reprezentována jako sekvence orchestrace, která musí následovat po úspěšné transakci. Pokud nějaký krok selhává, transakce se nezdařila. Tyto kroky orchestrace odkazují na stavební bloky i zprostředkovatele deklarací identity povolené v souboru zásad. Libovolný krok orchestrace zodpovědný za zobrazení nebo vykreslení uživatelského prostředí má také odkaz na odpovídající identifikátor definice obsahu.

Kroky orchestrace můžou být podmíněně spouštěny na základě předběžných podmínek definovaných v prvku kroku Orchestration. Například můžete provést krok orchestrace pouze v případě, že existuje konkrétní deklarace identity nebo pokud je deklarace identity shodná nebo není zadanou hodnotou.

K určení seřazeného seznamu kroků orchestrace se jako součást zásady Přidá element **OrchestrationSteps** . Tento prvek je povinný.

Element **OrchestrationSteps** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Seřazený krok orchestrace. |

Element **OrchestrationStep** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| `Order` | Ano | Pořadí kroků orchestrace. |
| `Type` | Ano | Typ kroku orchestrace Možné hodnoty: <ul><li>**Claimsproviderselection.** – určuje, že krok orchestrace prezentuje různým zprostředkovatelům deklarací identity uživateli možnost výběru jednoho.</li><li>**CombinedSignInAndSignUp** – určuje, že krok orchestrace prezentuje kombinované přihlášení ke zprostředkovateli sociálních sítí a přihlašovací stránku místního účtu.</li><li>**ClaimsExchange** – určuje, že krok orchestrace vyměňuje deklarace identity se zprostředkovatelem deklarací identity.</li><li>**Getclaims** – určuje, že krok orchestrace by měl zpracovat data deklarace identity odesílaná do Azure AD B2C od předávající strany prostřednictvím její `InputClaims` konfigurace.</li><li>**InvokeSubJourney** – určuje, že krok orchestrace vyměňuje deklarace identity s [podřízenou cestou](subjourneys.md) (ve verzi Public Preview).</li><li>**SendClaims** – určuje, že krok orchestrace odesílá deklarace identity předávající straně s tokenem vystaveným vystavitelem deklarací identity.</li></ul> |
| ContentDefinitionReferenceId | Ne | Identifikátor [definice obsahu](contentdefinitions.md) přidruženého k tomuto kroku orchestrace. Identifikátor odkazu definice obsahu je obvykle definován v technickém profilu s vlastním uplatněním. Existují však případy, kdy Azure AD B2C musí zobrazit něco bez technického profilu. Existují dva příklady – Pokud je typ kroku orchestrace jedna z následujících: `ClaimsProviderSelection` nebo  `CombinedSignInAndSignUp` Azure AD B2C nutné zobrazit výběr poskytovatele identity bez technického profilu. |
| CpimIssuerTechnicalProfileReferenceId | Ne | Typ kroku orchestrace je `SendClaims` . Tato vlastnost definuje identifikátor technického profilu zprostředkovatele deklarací, který vydává token pro předávající stranu.  Pokud chybí, není vytvořen token předávající strany. |

Element **OrchestrationStep** může obsahovat následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Předběžné podmínky | 0: n | Seznam předpokladů, které musí být splněny, aby bylo možné provést krok orchestrace. |
| ClaimsProviderSelections | 0: n | Seznam výběrů zprostředkovatele deklarací pro krok orchestrace |
| ClaimsExchanges | 0: n | Seznam výměn deklarací identity pro krok Orchestration |
| JourneyList | 0:1 | Seznam kandidátů na dílčí cestu pro krok orchestrace. |

### <a name="preconditions"></a>Předběžné podmínky

Element **Conditions** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Předběžná podmínka | 1: n | V závislosti na použitém technickém profilu přesměruje klienta na základě výběru zprostředkovatele deklarací identity nebo vyvolá volání serveru k výměně deklarací identity. |


#### <a name="precondition"></a>Předběžná podmínka

Prvek **předběžné podmínky** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ kontroly nebo dotazu, který má být proveden pro tuto podmínku. Hodnota může být **ClaimsExist**, která určuje, jestli se mají akce provádět, pokud zadané deklarace identity existují v aktuální sadě deklarací identity nebo **ClaimEquals**, která určuje, jestli se mají akce provádět, pokud existuje zadaná deklarace identity a její hodnota se rovná zadané hodnotě. |
| `ExecuteActionsIf` | Ano | Použijte test true nebo false a rozhodněte se, zda by měly být provedeny akce v předběžné podmínce. |

Prvky **předběžné podmínky** obsahují následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Hodnota | 1: n | ClaimTypeReferenceId, pro který se má dotazovat. Další element Value obsahuje hodnotu, která se má zkontrolovat.</li></ul>|
| Akce | 1:1 | Akce, která má být provedena, pokud je splněna kontrolní podmínka v kroku orchestrace. Pokud `Action` je hodnota nastavená na `SkipThisOrchestrationStep` , přidružený `OrchestrationStep` by neměl být proveden. |

#### <a name="preconditions-examples"></a>Příklady předběžných podmínek

Následující předpoklady kontrolují, zda existuje identifikátor objectId uživatele. Uživatel v cestě uživatele zvolil možnost přihlásit se pomocí místního účtu. Pokud identifikátor objectId existuje, přeskočte tento krok Orchestration.

```xml
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

Následující předběžné podmínky kontrolují, jestli se uživatel přihlásil pomocí účtu sociální sítě. Byl proveden pokus o vyhledání uživatelského účtu v adresáři. Pokud se uživatel přihlásí nebo zaregistruje pomocí místního účtu, přeskočte tento krok Orchestration.

```xml
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

Předběžné podmínky mohou kontrolovat více předběžných podmínek. Následující příklad ověří, zda existuje objectId nebo e-mail. Pokud je první podmínka pravdivá, přeskočí cesta k dalšímu kroku orchestrace.

```xml
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

## <a name="identity-provider-selection"></a>Výběr zprostředkovatele identity

Výběr zprostředkovatele identity umožňuje uživatelům vybrat akci ze seznamu možností. Výběr zprostředkovatele identity se skládá z páru dvou kroků orchestrace: 

1. **Tlačítka** – spustí se typu `ClaimsProviderSelection` , nebo `CombinedSignInAndSignUp` obsahuje seznam možností, ze kterých si uživatel může vybrat. Pořadí možností uvnitř `ClaimsProviderSelections` elementu řídí pořadí tlačítek prezentovaných uživateli.
2. **Akce** – následuje typ `ClaimsExchange` . ClaimsExchange obsahuje seznam akcí. Akce je odkaz na technický profil, jako je například [OAuth2](oauth2-technical-profile.md), [OpenID Connect](openid-connect-technical-profile.md), [transformovat deklarace](claims-transformation-technical-profile.md)nebo s [vlastním uplatněním](self-asserted-technical-profile.md). V případě, že uživatel klikne na jedno z tlačítek, je provedena odpovídající akce.

Element **ClaimsProviderSelections** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Claimsproviderselection. | 1: n | Poskytuje seznam zprostředkovatelů deklarací identity, které se dají vybrat.|

Element **ClaimsProviderSelections** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| DisplayOption| Ne | Řídí chování případu, kde je k dispozici jeden výběr zprostředkovatele deklarací identity. Možné hodnoty: `DoNotShowSingleProvider` (výchozí) – uživatel je okamžitě přesměrován na federovaného zprostředkovatele identity. Nebo `ShowSingleProvider` Azure AD B2C prezentují přihlašovací stránku s jedním vybraným zprostředkovatelem identity. Chcete-li použít tento atribut, musí být [verze definice obsahu](page-layout.md) `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` a vyšší.|

Element **claimsproviderselection.** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Ne | Identifikátor výměny deklarací identity, který se spustí v dalším kroku orchestrace výběru zprostředkovatele deklarací. Tento atribut nebo atribut ValidationClaimsExchangeId musí být zadán, ale ne oba. |
| ValidationClaimsExchangeId | Ne | Identifikátor výměny deklarací identity, který se spustí v aktuálním kroku Orchestration pro ověření výběru zprostředkovatele deklarací identity. Tento atribut nebo atribut TargetClaimsExchangeId musí být zadán, ale ne oba. |

### <a name="claimsproviderselection-example"></a>Příklad Claimsproviderselection.

V následujícím kroku orchestrace se uživatel může přihlásit přes Facebook, LinkedIn, Twitter, Google nebo místní účet. Pokud uživatel vybere jednoho ze zprostředkovatelů sociálních identit, spustí se druhý krok orchestrace s vybraným výměnou deklarací identity zadaným v `TargetClaimsExchangeId` atributu. Druhý krok orchestrace přesměruje uživatele na zprostředkovatele sociální identity, aby dokončil proces přihlášení. Pokud se uživatel rozhodne přihlásit pomocí místního účtu, Azure AD B2C zůstane na stejném kroku orchestrace (stejná přihlašovací stránka nebo přihlašovací stránka) a přeskočí druhý krok Orchestration.

```xml
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

Element **ClaimsExchanges** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1: n | V závislosti na použitém technickém profilu buď přesměrujte klienta podle Claimsproviderselection., který jste vybrali, nebo zavolá serverové volání na výměnu deklarací identity. |

Element **ClaimsExchange** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor kroku výměny deklarací identity. Identifikátor se používá k odkazování na výměnu deklarací z kroku výběru zprostředkovatele deklarací v zásadě. |
| TechnicalProfileReferenceId | Ano | Identifikátor technického profilu, který má být spuštěn. |

## <a name="journeylist"></a>JourneyList

Element **JourneyList** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Zvolen | 1:1 | Odkaz na podřízenou cestu, která má být volána. |

### <a name="candidate"></a>Zvolen

**Kandidátný** prvek obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Ano | Identifikátor [dílčí cesty](subjourneys.md) , která má být provedena. |
