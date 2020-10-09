---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element TrustFrameworkPolicy vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29eddbcfb7c0da98e5438f968dd3976b77a44680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203091"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásady jsou reprezentovány jako jeden nebo více souborů ve formátu XML, které na sebe navzájem odkazují v hierarchickém řetězu. Prvky XML definují prvky zásad, například schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatele deklarací, technické profily, cesty uživatelů a kroky orchestrace. Jednotlivé soubory zásad se definují v rámci souboru zásad na nejvyšší úrovni v **TrustFrameworkPolicy** elementu.

```xml
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


Element **TrustFrameworkPolicy** obsahuje následující atributy:

| Atribut | Povinné | Popis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ano | Verze schématu, která se má použít ke spuštění zásad. Hodnota musí být `0.3.0.0` |
| TenantObjectId | No | Jedinečný identifikátor objektu klienta Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Ano | Jedinečný identifikátor tenanta, ke kterému patří tato zásada |
| PolicyId | Ano | Jedinečný identifikátor pro zásady Tento identifikátor musí být předponou *B2C_1A_* |
| PublicPolicyUri | Ano | Identifikátor URI pro zásadu, která je kombinací ID tenanta a ID zásad. |
| DeploymentMode | No | Možné hodnoty: `Production` , nebo `Development` . `Production` je výchozí možnost. Tato vlastnost slouží k ladění zásad. Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | No | Koncový bod, který se používá, pokud je **DeploymentMode** nastaveno na `Development` . Hodnota musí být `urn:journeyrecorder:applicationinsights` . Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md). |


Následující příklad ukazuje, jak zadat element **TrustFrameworkPolicy** :

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

Tyto typy souborů zásad se obvykle používají v cestě uživatele:

- **Základní** soubor, který obsahuje většinu definic. Pro pomoc s řešením potíží a dlouhodobou údržbou zásad doporučujeme provést v tomto souboru minimální počet změn.
- **Příponový** soubor, který obsahuje jedinečné změny konfigurace vašeho tenanta. Tento soubor zásad je odvozen ze základního souboru. Pomocí tohoto souboru můžete přidat nové funkce nebo přepsat existující funkce. Pomocí tohoto souboru můžete například federovat s novými zprostředkovateli identity.
- Soubor **předávající strany (RP)** , který je jedním souborem zaměřeným na úlohy, který je vyvolán přímo pomocí aplikace předávající strany, jako jsou webové, mobilní nebo desktopové aplikace. Každý jedinečný úkol, jako je registrace nebo přihlášení, resetování hesla nebo úprava profilu, vyžaduje vlastní soubor zásad RP. Tento soubor zásad je odvozený od souboru rozšíření.

Aplikace předávající strany volá soubor zásad RP, aby spustil konkrétní úlohu. Chcete-li například spustit tok přihlášení. Architektura prostředí identity v Azure AD B2C přidá všechny prvky nejprve ze základního souboru a potom ze souboru rozšíření a nakonec ze souboru zásad RP, aby se shromáždily aktuální zásady. Prvky stejného typu a názvu v souboru RP přepíšou tyto prvky v rozšířeních a přepíší základ přípon. Následující diagram znázorňuje vztah mezi soubory zásad a aplikacemi předávající strany.

![Diagram znázorňující model dědičnosti zásad pro pravidlo důvěryhodnosti](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Model dědičnosti je následující:

- Zásada nadřazené zásady a podřízenosti je stejného schématu.
- Podřízená zásada na libovolné úrovni může dědit z nadřazené zásady a rozšiřuje ji přidáním nových elementů.
- Počet úrovní není nijak omezený.

Další informace najdete v tématu [Začínáme s vlastními zásadami](custom-policy-get-started.md).

## <a name="base-policy"></a>Základní zásady

Aby bylo možné dědit zásadu z jiné zásady, musí být deklarován element **BasePolicy** v rámci **TrustFrameworkPolicy** elementu souboru zásad. Element **BasePolicy** je odkaz na základní zásady, ze kterých je tato zásada odvozena.

Element **BasePolicy** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identifikátor vašeho tenanta Azure AD B2C. |
| PolicyId | 1:1 | Identifikátor nadřazené zásady |


Následující příklad ukazuje, jak zadat základní zásady. Tato zásada **B2C_1A_TrustFrameworkExtensions** se odvozuje od zásad **B2C_1A_TrustFrameworkBase** .

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

## <a name="policy-execution"></a>Spuštění zásad

Aplikace předávající strany, jako je například webová, mobilní nebo desktopová aplikace, zavolá [zásadu předávající strany (RP)](relyingparty.md). Soubor zásad RP spustí konkrétní úlohu, jako je například přihlašování, resetování hesla nebo úprava profilu. Zásady RP nakonfigurují seznam deklarací, které aplikace předávající strany obdrží jako součást tokenu, který je vystavený. Stejné zásady můžou používat víc aplikací. Všechny aplikace přijímají stejný token s deklaracemi a uživatel projde stejnou cestou uživatele. Jedna aplikace může používat více zásad.

V souboru zásad RP zadáte element **DefaultUserJourney** , který odkazuje na [UserJourney](userjourneys.md). Cesta uživatele je obvykle definovaná v zásadách základní nebo rozšíření.

Zásada B2C_1A_signup_signin:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase nebo B2C_1A_TrustFrameworkExtensionPolicy:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Cesta uživatele definuje obchodní logiku, kterou uživatel prochází. Každou cestu uživatele tvoří sadu kroků orchestrace, které provádějí řadu akcí, a to v pořadí podle ověřování a shromažďování informací.

Soubor zásad **SocialAndLocalAccounts** v [úvodní](custom-policy-get-started.md#custom-policy-starter-pack) sadě obsahuje cesty uživatelů v SignUpOrSignIn, ProfileEdit a PasswordReset. Můžete přidat další cesty uživatelů pro jiné scénáře, jako je například změna e-mailové adresy nebo propojení a odpojení účtu sociální sítě.

Kroky orchestrace můžou zavolat na [technický profil](technicalprofiles.md). Technický profil poskytuje rozhraní s integrovaným mechanismem pro komunikaci s různými typy stran. Technický profil může například provádět tyto akce mimo jiné:

- Vykreslete činnost koncového uživatele.
- Umožněte uživatelům, aby se přihlásili pomocí sociálních nebo podnikových účtů, jako je Facebook, účet Microsoft, Google, Salesforce nebo jakýkoli jiný zprostředkovatel identity.
- Nastavte ověřování na telefon pro MFA.
- Čtení a zápis dat do a z Azure AD B2C úložiště identity.
- Zavolejte vlastní službu RESTful API.

![Diagram znázorňující tok spuštění zásad](./media/trustframeworkpolicy/custom-policy-execution.png)

 Element **TrustFrameworkPolicy** obsahuje následující prvky:

- BasePolicy jak je uvedeno výše
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
