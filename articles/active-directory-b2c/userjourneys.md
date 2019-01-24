---
title: Userjourney | Dokumentace Microsoftu
description: Zadejte element Userjourney vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6933a3d50807f38c0704f41dff7c9bcb3351949
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850635"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cesty uživatele zadejte explicitní cesty, pomocí kterých zásadu umožňuje aplikaci předávající strany k získání požadovaných deklarací identity pro uživatele. Uživatel je přesunete přes tyto cesty na načítají deklarace identity, které se budou zobrazovat předávající straně. Jinými slovy uživatel jízdy definovat obchodní logiku z jaké koncový uživatel prochází jako procesy architekturu rozhraní identit Azure AD B2C požadavku.

Tyto cesty uživatele lze považovat jako šablony, které jsou k dispozici splňovat základní potřebám různých odpovídajících stran, komunity, které vás zajímají. Uživatel jízdy usnadnění definice předávající strana část zásady. Zásady můžete definovat více cest uživatele. Každé cesty uživatele je posloupnost kroků Orchestrace.

K definování cest uživatele podporovaného zásadami, **Userjourney** prvek přidán pod prvek nejvyšší úrovně soubor zásad. 

**Userjourney** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | Cesta uživatele, který definuje všechny nezbytné pro dokončení uživatele tok konstruktorů. | 

**UserJourney** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor cesty uživatele, který slouží k odkazování z dalších prvků v zásadách. **DefaultUserJourney** elementu [předávající strana zásad](relyingparty.md) odkazuje na tento atribut. |

**UserJourney** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Orchestrace pořadí, který musí být následován úspěšná transakce. Každé cesty uživatele se skládá z uspořádaný seznam kroků Orchestrace, které jsou spouštěny v pořadí. Pokud kterýkoli krok selže, transakce se nezdaří. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Cesty uživatele představuje orchestraci pořadí, který musí být následován úspěšná transakce. Pokud kterýkoli krok selže, transakce se nezdaří. V souboru zásad povoleny poskytovatele deklarací identity a kroků Orchestrace odkazovat na stavební bloky. Libovolný krok Orchestrace, která odpovídá zobrazení nebo vykreslení uživatelské prostředí také obsahuje odkaz na odpovídající identifikátor definici obsahu.

Orchestrace postup může být conditionaly provedl a vytvořil podle předběžné podmínky definované v elementu krok Orchestrace. Pro examle můžete zkontrolovat provést krok Orchestrace pouze v případě, že konkrétní deklarace identity existuje, nebo pokud se deklarace identity rovná nebo není na zadanou hodnotu. 

Chcete-li určit seřazený seznam kroků Orchestrace, **OrchestrationSteps** prvek se přidá jako součást této zásady. Tento element je povinný.

**OrchestrationSteps** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Na seřazený Orchestrace krok. | 

**OrchestrationStep** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Objednání | Ano | Pořadí kroků Orchestrace. | 
| Typ | Ano | Typ kroku Orchestrace. Možné hodnoty: <ul><li>**ClaimsProviderSelection** – označuje, že krok Orchestrace představuje různých zprostředkovatelů deklarací identity k uživateli vybrat jednu.</li><li>**CombinedSignInAndSignUp** – označuje, že krok Orchestrace představuje kombinovanou poskytovatele sociálních sítí registrační stránku pro přihlášení a místní účet.</li><li>**ClaimsExchange** – označuje, že krok Orchestrace výměny deklarací identity se zprostředkovatelem deklarací identity.</li><li>**SendClaims** – označuje, že krok Orchestrace, odešle se u tokenu vydaného službou deklarace identity vystavitele deklarace identity na přijímající straně.</li></ul> | 
| ContentDefinitionReferenceId | Ne | Identifikátor [obsahu definice](contentdefinitions.md) přidružené k tento krok Orchestrace. Obvykle identifikátor obsahu definice odkazu je definován v s vlastním potvrzením technický profil. Ale existují případy, když Azure AD B2C potřebuje rychle zobrazit něco bez technického profilu. Existují dva příklady, pokud je typ kroku Orchestrace jednu z následujících akcí: `ClaimsProviderSelection` nebo `CombinedSignInAndSignUp`. Azure AD B2C musí zobrazit výběru zprostředkovatele identity bez nutnosti technický profil. | 
| CpimIssuerTechnicalProfileReferenceId | Ne | Typ kroku Orchestrace je `SendClaims`. Tato vlastnost definuje technický profil identifikátor zprostředkovatele deklarací identity, který vydá token pro předávající stranu.  Pokud chybí, je vytvořen žádný předávající strany token. |


**OrchestrationStep** element může obsahovat následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- | 
| Předběžné podmínky | 0: n | Seznam předběžné požadavky, které musí být splněny pro krok Orchestrace ke spuštění. | 
| ClaimsProviderSelections | 0: n | Seznam výběru zprostředkovatele deklarací identity pro krok Orchestrace. | 
| ClaimsExchanges | 0: n | Seznam výměny deklarací identity pro krok Orchestrace. | 

#### <a name="preconditions"></a>Předběžné podmínky

**Předběžné podmínky** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- | 
| Předběžné podmínky | 0: n | V závislosti na tom technický profil používá buď přesměruje klienta podle výběru zprostředkovatele deklarací identity nebo je deklarace volání serveru exchange. | 


##### <a name="precondition"></a>Předběžné podmínky

**Předběžné podmínky** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Typ | Ano | Typ kontroly nebo dotaz k provedení této předběžné podmínky. Hodnota může být **ClaimsExist**, která určuje, že by měl provést akce, pokud zadané deklarace existuje v aktuální sadě deklarací identity uživatele nebo **ClaimEquals**, která určuje, že akce je třeba provést, pokud existuje zadaný deklarace identity a jeho hodnota se rovná se zadanou hodnotou. |
| ExecuteActionsIf | Ano | Použijte hodnotu true nebo false test se rozhodnout, pokud by provádět akce v předpoklad. | 

**Předběžné podmínky** prvky obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Hodnota | 1: n | ClaimTypeReferenceId, aby se dalo dotazovat pro. Jiný element hodnota obsahuje hodnotu, která se má zkontrolovat.</li></ul>|
| Akce | 1:1 | Akce, která má být provedena, pokud v jednom z kroků Orchestrace předpoklad platí. Pokud hodnota `Action` je nastavena na `SkipThisOrchestrationStep`, přidružené `OrchestrationStep` by neměl být spouštěn. | 

### <a name="preconditions-examples"></a>Příklady předběžné podmínky

Tyto předběžné požadavky kontroluje, jestli ID objektu uživatele existuje. V cestě uživatele uživatel vybral se přihlásit pomocí místního účtu. Pokud existuje objectId, přeskočte tento krok Orchestrace.

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

Tyto předběžné požadavky kontroluje, zda uživatel přihlášený pomocí účtu na sociální síti. Je proveden pokus o vyhledání uživatelský účet v adresáři. Pokud se uživatel přihlásí nebo zaregistruje s místním účtem přeskočte, tento krok Orchestrace.

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

Předběžné podmínky můžete zkontrolovat několik podmínek. Následující příklad zkontroluje, zda existuje "ID objektu" nebo "e-mailu". Pokud je splněna první podmínka, cesty přeskočí na další krok Orchestrace.

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

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Na krok Orchestrace typu `ClaimsProviderSelection` nebo `CombinedSignInAndSignUp` může obsahovat seznam zprostředkovatelů deklarací identity, které uživatel může přihlásit pomocí. Pořadí prvků uvnitř `ClaimsProviderSelections` elementy určuje pořadí poskytovatelů identit budou zobrazovat uživateli.

**ClaimsProviderSelection** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0: n | Poskytuje seznam zprostředkovatelů deklarací identity, které je možné vybrat.|

**ClaimsProviderSelection** prvek obsahuje následující atributy: 

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Ne | Identifikátor výměna deklarací identit, který je proveden v dalším kroku Orchestrace výběru zprostředkovatele deklarací identity. Atribut ValidationClaimsExchangeId nebo tento atribut musí být zadaný, ale ne obojí. | 
| ValidationClaimsExchangeId | Ne | Identifikátor výměna deklarací identit, který je proveden v aktuální krok Orchestrace ověření výběru zprostředkovatele deklarací identity. Atribut TargetClaimsExchangeId nebo tento atribut musí být zadaný, ale ne obojí. |

### <a name="claimsproviderselection-example"></a>Příklad ClaimsProviderSelection.

V následujícím kroku Orchestrace můžete uživatele k přihlášení pomocí Facebooku, odka, Twitter, Google nebo místní účet. Pokud si uživatel vybere jeden zprostředkovatelů sociálních identit, druhý krok Orchestrace provede pomocí deklarací exchange podle `TargetClaimsExchangeId` atribut. Druhý krok Orchestrace přesměruje uživatele na zprostředkovatele sociální identity, dokončete proces přihlašování. Pokud uživatel zvolí možnost přihlásit se pomocí místního účtu, Azure AD B2C zůstává na stejné krok Orchestrace (stejnou stránku pro přihlášení nebo přihlašovací stránky) a druhý krok Orchestrace přeskočí.

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

**ClaimsExchanges** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0: n | V závislosti na tom technický profil používá buď přesměruje klienta podle ClaimsProviderSelection, který byl vybrán, nebo je deklarace volání serveru exchange. | 

**ClaimsExchange** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor kroku exchange deklarací identity. Tento identifikátor slouží k odkazu krok výměna deklarací identit z výběru zprostředkovatele deklarací identity v zásadách. | 
| TechnicalProfileReferenceId | Ano | Identifikátor technický profil, který má být provedena. |
 
















