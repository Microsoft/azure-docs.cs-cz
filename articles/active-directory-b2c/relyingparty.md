---
title: RelyingParty-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element RelyingParty vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1bfa945843d185a46f1f1d79fd4dab0e991c769
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063812"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **RelyingParty** Určuje cestu uživatele k vykonání aktuální žádosti o Azure Active Directory B2C (Azure AD B2C). Určuje také seznam deklarací, které aplikace předávající strany (RP) potřebuje jako součást vydaného tokenu. Aplikace RP, jako je například webová, mobilní nebo desktopová aplikace, zavolá soubor zásad RP. Soubor zásad RP spustí konkrétní úlohu, jako je například přihlašování, resetování hesla nebo úprava profilu. Víc aplikací může používat stejné zásady RP a jedna aplikace může používat víc zásad. Všechny aplikace pro RP získají stejný token s deklaracemi a uživatel projde stejnou cestou uživatele.

Následující příklad ukazuje element **RelyingParty** v souboru zásad *B2C_1A_signup_signin* :

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Volitelný element **RelyingParty** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Výchozí cesta uživatele pro aplikaci RP. |
| UserJourneyBehaviors | 0:1 | Rozsah chování při jízdě uživatelů. |
| TechnicalProfile | 1:1 | Technický profil podporovaný aplikací RP. Technický profil poskytuje kontrakt pro aplikaci RP, aby kontaktoval Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` Prvek určuje odkaz na identifikátor cesty uživatele, který je obvykle definován v zásadách základní nebo rozšíření. Následující příklady znázorňují cestu k registraci nebo přihlašování uživatele určenou v elementu **RelyingParty** :

Zásady *B2C_1A_signup_signin* :

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* nebo *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Element **DefaultUserJourney** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor cesty uživatele v zásadě. Další informace najdete v tématu [cesty uživatelů](userjourneys.md) . |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Element **UserJourneyBehaviors** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Rozsah chování relace jednotného přihlašování (SSO) pro cestu uživatele. |
| SessionExpiryType |0:1 | Chování při ověřování relace. Možné hodnoty: `Rolling` nebo `Absolute`. `Rolling` Hodnota (výchozí) znamená, že uživatel zůstane přihlášený, dokud bude uživatel v aplikaci neustále aktivní. `Absolute` Hodnota označuje, že se uživatel bude nucen znovu ověřit po uplynutí časového období určeného v době platnosti relace aplikace. |
| SessionExpiryInSeconds | 0:1 | Doba života souboru cookie relace Azure AD B2C's zadaná jako celé číslo uložené v prohlížeči uživatele po úspěšném ověření. |
| JourneyInsights | 0:1 | Klíč instrumentace Azure Application Insights, který se má použít. |
| ContentDefinitionParameters | 0:1 | Seznam párů klíč-hodnota, které se mají připojit k identifikátoru URI načtení definice obsahu. |

### <a name="singlesignon"></a>SingleSignOn

Element **SingleSignon** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Scope | Ano | Rozsah chování jednotného přihlašování. Možné hodnoty: `Suppressed`, `Tenant`, `Application`, nebo `Policy`. `Suppressed` Hodnota označuje, že chování je potlačeno. Například v případě relace jednotného přihlašování není pro uživatele udržována žádná relace a uživatel se vždy zobrazí výzva k výběru poskytovatele identity. `TrustFramework` Hodnota označuje, že chování je použito pro všechny zásady v architektuře vztahů důvěryhodnosti. Například uživatel, který přecházení ze dvou cest zásad pro architekturu trustu, nevyzve k výběru poskytovatele identity. `Tenant` Hodnota označuje, že se chování použije u všech zásad v tenantovi. Například uživatel, který přecházení ze dvou cest zásad pro tenanta, nevyzve k výběru poskytovatele identity. `Application` Hodnota označuje, že chování bude použito pro všechny zásady aplikace, které vytváří požadavek. Například uživatel, který přecházení ze dvou cest zásad pro aplikaci, nezobrazuje výzvu k výběru poskytovatele identity. `Policy` Hodnota znamená, že chování se vztahuje pouze na zásadu. Například uživatel, který přechází ze dvou cest zásad pro rozhraní vztahu důvěryhodnosti, se při přepínání mezi zásadami zobrazí dotaz na výběr poskytovatele identity. |
| KeepAliveInDays | Ano | Určuje, jak dlouho zůstane uživatel přihlášený. Nastavením hodnoty 0 dojde k vypnutí funkcí políčko zůstat přihlášeni. Další informace najdete v tématu [zůstat](active-directory-b2c-reference-kmsi-custom.md)přihlášeni. |

## <a name="journeyinsights"></a>JourneyInsights

Element **JourneyInsights** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| TelemetryEngine | Ano | Hodnota musí být `ApplicationInsights`. |
| InstrumentationKey | Ano | Řetězec, který obsahuje klíč instrumentace pro element Application Insights. |
| DeveloperMode | Ano | Možné hodnoty: `true` nebo `false`. Pokud `true`Application Insights zrychlí telemetrii prostřednictvím kanálu zpracování. Toto nastavení je vhodné pro vývoj, ale je omezené na vysoké objemy. podrobné protokoly aktivit jsou navržené jenom na podporu vývoje vlastních zásad. Nepoužívejte režim vývoje v produkčním prostředí. Protokoly shromažďují všechny deklarace, které během vývoje odesílají a od nich od poskytovatelů identity. Pokud se v produkčním prostředí používá, vývojář předpokládá zodpovědnost za PII (soukromě identifikovatelné informace) shromážděné v protokolu App Insights, který vlastní. Tyto podrobné protokoly jsou shromažďovány, pouze pokud je tato hodnota `true`nastavena na.|
| ClientEnabled | Ano | Možné hodnoty: `true` nebo `false`. Pokud `true`aplikace odešle Application Insights skript na straně klienta pro sledování zobrazení stránky a chyby na straně klienta. |
| ServerEnabled | Ano | Možné hodnoty: `true` nebo `false`. Pokud `true`aplikace odešle existující UserJourneyRecorder JSON jako vlastní událost pro Application Insights. |
| TelemetryVersion | Ano | Hodnota musí být `1.0.0`. |

Další informace najdete v tématu [shromažďování protokolů](active-directory-b2c-troubleshoot-custom.md) .

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Pomocí vlastních zásad v Azure AD B2C můžete odeslat parametr v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Azure AD B2C předá parametry řetězce dotazu do dynamického souboru HTML, jako je třeba soubor ASPX.

Následující příklad předává parametr s názvem `campaignId` s `hawaii` hodnotou v řetězci dotazu:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Element **ContentDefinitionParameters** obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Řetězec, který obsahuje dvojici klíč-hodnota, která je připojena k řetězci dotazu identifikátoru URI pro načtení definice obsahu. |

Element **ContentDefinitionParameter** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Name | Ano | Název páru klíč-hodnota. |

Další informace najdete v tématu [Konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad](active-directory-b2c-ui-customization-custom-dynamic.md) .

## <a name="technicalprofile"></a>TechnicalProfile

Element **TechnicalProfile** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Hodnota musí být `PolicyProfile`. |

**TechnicalProfile** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Řetězec, který obsahuje název technického profilu, který se zobrazí uživatelům. |
| Popis | 0:1 | Řetězec, který obsahuje popis technického profilu, který se zobrazí uživatelům. |
| Protocol | 1:1 | Protokol používaný pro federaci. |
| Metadata | 0:1 | Kolekce *položek* párů klíč/hodnota využívané protokolem pro komunikaci s koncovým bodem v průběhu transakce pro konfiguraci interakce mezi předávající stranou a ostatními účastníky komunity. |
| OutputClaims | 0:1 | Seznam typů deklarací, které jsou pořízeny jako výstup v technickém profilu. Každý z těchto prvků obsahuje odkaz na objekt **ClaimType** , který je již definován v části **ClaimsSchema** nebo v zásadě, ze které tento soubor zásad dědí. |
| SubjectNamingInfo | 0:1 | Název subjektu, který se používá v tokenech. |

Element **Protocol** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Name | Ano | Název platného protokolu podporovaného Azure AD B2C, který se používá jako součást technického profilu. Možné hodnoty: `OpenIdConnect` nebo `SAML2`. `OpenIdConnect` Hodnota představuje standard protokolu OpenID Connect 1,0 podle specifikace OpenID Foundation. `SAML2` Představuje standard protokolu SAML 2,0 podle specifikace pro Oasis. Nepoužívejte token SAML v produkčním prostředí. |

## <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Název očekávaného typu deklarace v seznamu podporovaných pro zásadu, které předávající strana přihlašuje k odběru. Tato deklarace slouží jako výstup pro technický profil. |

Element **OutputClaim** obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Odkaz na objekt **ClaimType** již definovaný v oddílu **ClaimsSchema** v souboru zásad. |
| Hodnot | Ne | Výchozí hodnota, která se dá použít, pokud je hodnota deklarace prázdná. |
| PartnerClaimType | Ne | Odešle deklaraci identity v jiném názvu, jak je nakonfigurováno v definici ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Pomocí elementu **SubjectNameingInfo** řídíte hodnotu předmětu tokenu:
- **Token JWT** – `sub` deklarace identity Jedná se o objekt zabezpečení, o kterém token vyhodnotí informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Dá se použít k provádění bezpečných ověřovacích kontrol, například když se token používá pro přístup k prostředku. Ve výchozím nastavení se deklarace identity subjektu naplní s ID objektu uživatele v adresáři. Další informace najdete v tématu [Konfigurace tokenu, relace a jednotného přihlašování](active-directory-b2c-token-session-sso.md).
- **Token SAML** – `<Subject><NameID>` element, který identifikuje prvek předmětu.

Element **SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimType | Ano | Odkaz na PartnerClaimTypeu výstupní deklarace identity. Deklarace výstupů musí být definované v **OutputClaims** kolekci zásad předávající strany. |

Následující příklad ukazuje, jak definovat předávající stranu OpenID Connect. Informace o názvu subjektu jsou nakonfigurovány jako `objectId`:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Token JWT zahrnuje `sub` deklaraci identity s identifikátorem objectID uživatele:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


