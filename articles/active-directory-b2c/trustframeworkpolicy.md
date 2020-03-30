---
title: TrustFrameworkPolicy – Služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zadejte element TrustFrameworkPolicy vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186382"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vlastní zásada je reprezentována jako jeden nebo více souborů ve formátu XML, které na sebe odkazují v hierarchickém řetězci. Elementy XML definují prvky zásady, jako je například schéma deklarací identity, transformace deklarací identity, definice obsahu, zprostředkovatelé deklarací identity, technické profily, cesta uživatele a kroky orchestrace. Každý soubor zásad je definován v rámci prvku **TrustFrameworkPolicy** nejvyšší úrovně souboru zásad.

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


Prvek **TrustFrameworkPolicy** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ano | Verze schématu, která má být použita ke spuštění zásady. Hodnota musí být`0.3.0.0` |
| TenantObjectId | Ne | Jedinečný identifikátor objektu klienta Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Ano | Jedinečný identifikátor klienta, ke kterému tato zásada patří. |
| PolicyId | Ano | Jedinečný identifikátor zásady. Tento identifikátor musí být předponou *B2C_1A_* |
| PublicPolicyUri | Ano | Identifikátor URI pro zásadu, která je kombinací ID klienta a ID zásady. |
| Režim nasazení | Ne | Možné hodnoty: `Production` `Development`, nebo . `Production` je výchozí možnost. Tuto vlastnost použijte k ladění zásad. Další informace naleznete v [tématu Shromažďování protokolů](troubleshoot-with-application-insights.md). |
| Koncový bod Uživatele JourneyRecorder | Ne | Koncový bod, který se používá při `Development` **DeploymentMode** je nastavena na . Hodnota musí `urn:journeyrecorder:applicationinsights`být . Další informace naleznete v [tématu Shromažďování protokolů](troubleshoot-with-application-insights.md). |


Následující příklad ukazuje, jak určit element **TrustFrameworkPolicy:**

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

- **Základní** soubor, který obsahuje většinu definic. Chcete-li pomoci s odstraňováním potíží a dlouhodobou údržbou zásad, doporučujeme provést v tomto souboru minimální počet změn.
- Soubor **rozšíření,** který obsahuje jedinečné změny konfigurace pro vašeho tenanta. Tento soubor zásad je odvozen ze základního souboru. Tento soubor slouží k přidání nových funkcí nebo přepsání existujících funkcí. Tento soubor můžete například použít k federate s novými poskytovateli identit.
- Soubor **předávající strany (RP),** který je souborem zaměřeným na úlohy, který je vyvolán přímo aplikací předávající strany, jako je například webová, mobilní nebo desktopová aplikace. Každý jedinečný úkol, jako je registrace nebo přihlášení, resetování hesla nebo úprava profilu, vyžaduje vlastní soubor zásad RP. Tento soubor zásad je odvozen ze souboru Přípony.

Aplikace předávající strany volá soubor zásad RP k provedení určité úlohy. Například zahájit tok přihlášení. Rozhraní identity experience framework v Azure AD B2C přidá všechny prvky nejprve ze souboru Base a potom ze souboru rozšíření a nakonec ze souboru zásad RP sestavit aktuální zásady v platnosti. Prvky stejného typu a názvu v souboru RP přepsat tyto prvky v rozšíření a rozšíření přepíše Base. Následující diagram znázorňuje vztah mezi soubory zásad a aplikacemi předávající strany.

![Diagram znázorňující model dědičnosti zásad architektury důvěryhodnosti](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Model dědičnosti je následující:

- Nadřazené zásady a podřízené zásady jsou stejné schéma.
- Podřízené zásady na libovolné úrovni můžete dědit z nadřazené zásady a rozšířit přidáním nové prvky.
- Počet úrovní není nijak omezen.

Další informace naleznete [v tématu Začínáme s vlastními zásadami](custom-policy-get-started.md).

## <a name="base-policy"></a>Základní zásady

Chcete-li zdědit zásadu z jiné zásady, musí být prvek **BasePolicy** deklarován v rámci prvku **TrustFrameworkPolicy** souboru zásad. Element **BasePolicy** je odkaz na základní zásady, ze kterých je tato zásada odvozena.

Element **BasePolicy** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identifikátor vašeho klienta Azure AD B2C. |
| PolicyId | 1:1 | Identifikátor nadřazené zásady. |


Následující příklad ukazuje, jak zadat základní zásady. Tato **B2C_1A_TrustFrameworkExtensions** politika je odvozena z **B2C_1A_TrustFrameworkBase** politiky.

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

## <a name="policy-execution"></a>Provádění zásad

Aplikace předávající strany, například webová, mobilní nebo desktopová aplikace, volá [zásady předávající strany (RP).](relyingparty.md) Soubor zásad RP provede určitou úlohu, například přihlášení, resetování hesla nebo úpravu profilu. Zásady RP konfiguruje seznam deklarací, které aplikace předávající strany obdrží jako součást vydaného tokenu. Více aplikací může používat stejné zásady. Všechny aplikace obdrží stejný token s deklaracemi identity a uživatel prochází stejnou cestou uživatele. Jedna aplikace může používat více zásad.

Uvnitř souboru zásad RP zadáte element **DefaultUserJourney,** který odkazuje na [UserJourney](userjourneys.md). Cesta uživatele je obvykle definována v zásadách Base nebo Extensions.

B2C_1A_signup_signin politika:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase nebo B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Cesta uživatele definuje obchodní logiku toho, čím uživatel prochází. Každá cesta uživatele je sada kroků orchestrace, která provádí řadu akcí, v pořadí z hlediska ověřování a shromažďování informací.

**SocialAndLocalAccounts** soubor zásad v [počáteční mašle](custom-policy-get-started.md#custom-policy-starter-pack) obsahuje SignUpOrSignIn, ProfileEdit, PasswordReset cesty uživatele. Můžete přidat další cesty uživatelů pro jiné scénáře, jako je změna e-mailové adresy nebo propojení a odpojení účtu na sociální síti.

Kroky orchestrace může volat [technický profil](technicalprofiles.md). Technický profil poskytuje rámec s vestavěným mechanismem pro komunikaci s různými typy stran. Technický profil může například provádět tyto akce mimo jiné:

- Vykreslete uživatelské prostředí.
- Umožněte uživatelům přihlásit se pomocí sociálního nebo podnikového účtu, jako je Facebook, účet Microsoft, Google, Salesforce nebo jakýkoli jiný poskytovatel identity.
- Nastavte ověření telefonu pro vícefaktorové ověřování.
- Čtení a zápis dat do a z úložiště identit Azure AD B2C.
- Zavolejte vlastní službu Restful API.

![Diagram znázorňující tok spuštění zásad](./media/trustframeworkpolicy/custom-policy-execution.png)

 Prvek **TrustFrameworkPolicy** obsahuje následující prvky:

- Zásady BasePolicy, jak je uvedeno výše
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
