---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte elementu TrustFrameworkPolicy vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 820e98b200071c95dc3d9506dd0cd0bbe86e2aae
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54849681"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady je vyjádřena jako jeden nebo více ve formátu XML soubory, které odkazují navzájem v hierarchické řetězci. Elementy XML definice prvků zásad, například schématu deklarace identity, transformace deklarací, definic obsahu, zprostředkovatelem deklarací identity, technické profily, cesty uživatele a kroků Orchestrace. Každý soubor zásad je definována v rámci na nejvyšší úrovni **TrustFrameworkPolicy** element soubor zásad. 

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ano | Verze schématu, která se má použít ke spuštění zásad. Hodnota musí být `0.3.0.0` |
| TenantObjectId | Ne | Identifikátor tenanta Azure Active Directory (Azure AD) B2C jedinečný objekt. |
| TenantId | Ano | Jedinečný identifikátor tenanta, ke kterému patří tato zásada. |
| PolicyId | Ano | Jedinečný identifikátor zásad. Tento identifikátor musí mít předponu *B2C_1A_* |
| PublicPolicyUri | Ano | Identifikátor URI pro zásady, které je kombinací ID tenanta a ID zásad. |
| DeploymentMode | Ne | Možné hodnoty: `Production`, `Debugging`, nebo `Development`. `Production` je výchozí možnost. Tuto vlastnost použijte, chcete-li ladit vaše zásady. Další informace najdete v tématu [shromažďování protokolů](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Ne | Koncový bod, který se nepoužívá, pokud **DeploymentMode** je nastavena na `Development`. Hodnota musí být `urn:journeyrecorder:applicationinsights`. Další informace najdete v tématu [shromažďování protokolů](active-directory-b2c-troubleshoot-custom.md). |


Následující příklad ukazuje, jak určit **TrustFrameworkPolicy** element:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Model dědičnosti

Tyto typy souborů, zásady se obvykle používají v cestě uživatele:

- A **Base** soubor, který obsahuje většinu definice. Usnadňující řešení problémů a dlouhodobé údržby zásad, doporučujeme vytvořit minimální počet změn do tohoto souboru.
- **Rozšíření** soubor, který obsahuje změny jedinečnou konfiguraci pro vašeho tenanta. Tento soubor zásad je odvozen od základního souboru. Pomocí tohoto souboru můžete přidat nové funkce nebo přepsat existující funkce. Tento soubor můžete například použijte pro vytvoření federace s noví zprostředkovatelé identity.
- A **předávající strany (RP)** souboru, který je jeden soubor zaměřený na úkol, který je vyvolán přímo z aplikace předávající strany, jako je například webových, mobilních a desktopových aplikací. Každý úkol jedinečné jako je například resetování hesla registrace nebo přihlašování, nebo úpravě profilu vyžaduje svůj vlastní soubor se zásadami předávající strany. Tento soubor zásad je odvozen ze souboru rozšíření. 

Aplikace předávající strany volá souboru zásad RP pro spuštění konkrétního úkolu. Chcete-li například spustit tok přihlášení. Architekturu rozhraní identit v Azure AD B2C přidá všechny prvky první v od základního souboru a pak ze souboru rozšíření a nakonec ze souboru zásad předávající strany k sestavení aktuální zásady platit. Prvky stejného typu a název v souboru RP přepsat tyto prvky v rozšířeních a rozšíření přepíše Base. Následující diagram znázorňuje vztah mezi soubory zásad a aplikace předávající strany.

![Model dědičnosti](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Model dědičnosti vypadá takto:

- Zásady nadřazená a podřízená zásada mají stejné schéma.
- Podřízená zásada na libovolné úrovni může zdědit z nadřazené zásady a rozšířit přidáním nových elementů.
- Neexistuje žádné omezení počtu zadaných úrovní.

Další informace najdete v tématu [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Základní zásady

Dědit z jiné zásady, zásady **BasePolicy** elementu musí být deklarována v rámci **TrustFrameworkPolicy** prvek souboru zásad. **BasePolicy** element je odkaz na základní zásady, ze kterého je tato zásada odvozen.  

**BasePolicy** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identifikátor tenanta Azure AD B2C. |
| PolicyId | 1:1 | Identifikátor nadřazené zásady. |


Následující příklad ukazuje, jak zadat základní zásady. To **B2C_1A_TrustFrameworkExtensions** zásad je odvozen z **B2C_1A_TrustFrameworkBase** zásad. 

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Zpracování zásad

Aplikaci předávající strany, jako je například webových, mobilních a desktopových aplikací, která volá [předávající stranu zásad](relyingparty.md). Soubor zásad předávající strany, který provede určité úlohy, jako je například přihlašování, resetuje se heslo nebo úpravy profilu. Zásady RP konfigurují seznamu deklarace identit, které aplikaci předávající strana přijímá jako součást tokenu mechanismu, který je vydán. Více aplikací můžete použít stejné zásady. Všechny aplikace zobrazí stejný token díky deklaracím identity a uživatel prochází stejnou cestu uživatele. Jednu aplikaci můžete použít několik zásad.

V souboru zásad RP zadáte **DefaultUserJourney** element, který odkazuje na [UserJourney](userjourneys.md). Cesty uživatele je obvykle definováno v zásadách Base nebo rozšíření.

B2C_1A_signup_signin zásad:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase nebo B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

Cesty uživatele definuje, co uživatel prochází obchodní logiky. Každé cesty uživatele je sada kroků Orchestrace, který provádí řadu akcí, v pořadí z hlediska informace o ověřování a kolekce. 

**SocialAndLocalAccounts** soubor zásad v [starter pack](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) obsahuje SignUpOrSignIn, ProfileEdit, jízdy PasswordReset uživatele. Můžete přidat další uživatele cesty pro jiné scénáře, jako je například změna e-mailovou adresu, propojení a zrušit propojení účtu na sociální síti nebo resetování hesla. 

Kroků Orchestrace může volat [technický profil](technicalprofiles.md). Technický profil poskytuje architekturu s předdefinovaný mechanismus pro komunikaci s různými typy stran. Technický profil můžete například provádět tyto akce mimo jiné:

- Vykreslení uživatelské prostředí.
- Povolit uživatelům přihlásit se přes sociální sítě nebo účet organizace, jako je Facebook, účet Microsoft, Google, Salesforce nebo jakýkoli jiný poskytovatel identity.
- Nastavení ověření telefonem pro vícefaktorové ověřování.
- Čtení a zápis dat do a z úložiště identit Azure AD B2C.
- Volání vlastního rozhraní Restful API služby.

![Zpracování zásad](./media/trustframeworkpolicy/custom-policy-execution.png)

 **TrustFrameworkPolicy** prvek obsahuje následující prvky:

- BasePolicy, jak je uvedeno výše
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [Userjourney](userjourneys.md)
- [RelyingParty](relyingparty.md)

