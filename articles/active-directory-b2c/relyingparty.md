---
title: RelyingParty – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte element RelyingParty vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a6e25bdbcec2a99e323ac7f426307dd49e50d76c
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352423"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty** prvek určuje cestu uživatele k vynucení pro aktuální žádost o Azure Active Directory (Azure AD) B2C. Také určuje seznam deklarací identity, které potřebuje předávající stranu aplikaci jako součást vydaný token. Aplikaci předávající strany, jako je třeba webových, mobilních a desktopových aplikací, která volá soubor zásad předávající strany. Soubor zásad předávající strany, který provede určité úlohy, jako je například přihlašování, resetuje se heslo nebo úpravy profilu. Více aplikací můžete použít stejné zásady RP a jednu aplikaci můžete použít několik zásad. Zobrazí všechny aplikace předávající strany stejný token díky deklaracím identity a uživatel prochází stejnou cestu uživatele.

Následující příklad ukazuje **RelyingParty** prvek *B2C_1A_signup_signin* soubor zásad:

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
      <SingleSignOn Scope="TrustFramework" />
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

Volitelný **RelyingParty** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Výchozí cestu uživatele pro aplikace předávající strany. |
| UserJourneyBehaviors | 0:1 | Rozsah cesty chování uživatelů. |
| Technický profil | 1:1 | Technický profil, který podporuje aplikace předávající strany. Technický profil obsahuje kontrakt pro aplikace předávající strany ke kontaktování Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` Prvek určuje odkaz na identifikátor, který je obvykle definováno v zásadách Base nebo rozšíření cesty uživatele. Následující příklady ukazují cestu registrace / přihlášení uživatele podle **RelyingParty** element:

*B2C_1A_signup_signin* zásad:

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

**DefaultUserJourney** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor cesty uživatele v zásadách. Další informace najdete v tématu [cesty uživatele](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

**UserJourneyBehaviors** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Rozsah jednotné přihlašování (SSO) chování relace cesty uživatele. |
| SessionExpiryType |0:1 | Chování ověřování relace. Možné hodnoty: `Rolling` nebo `Absolute`. `Rolling` Hodnotu (výchozí) určuje, že uživatel zůstane přihlášený za předpokladu, uživatel je neustále aktivní v aplikaci. `Absolute` Hodnota značí, že uživatel musí po době určené aplikace relace donutit k životnost. |
| SessionExpiryInSeconds | 0:1 | Doba života souboru cookie relace Azure AD B2C, která je zadán jako celé číslo uložené v prohlížeči uživatele po úspěšném ověření. |
| JourneyInsights | 0:1 | Azure Application Insights. Instrumentační klíč má být použit. |
| ContentDefinitionParameters | 0:1 | Seznam párů klíčových hodnot, které se mají připojit k načtení obsahu definice identifikátoru URI. |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn** obsahuje element v následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Rozsah | Ano | Rozsah chování jednotné přihlašování. Možné hodnoty: `Suppressed`, `Tenant`, `Application`, nebo `Policy`. `Suppressed` Hodnota označuje, zda je potlačeno chování. Například v případě jedné relace přihlášení, žádná relace se zachová pro uživatele a uživatel vždy zobrazí výzva výběru zprostředkovatele identity. `TrustFramework` Hodnota značí, že je chování aplikováno pro všechny zásady v rámci vztahu důvěryhodnosti. Například uživatel procházet dvě cesty zásady pro vztah důvěryhodnosti framework vyzván výběru zprostředkovatele identity. `Tenant` Hodnota značí, že je chování aplikováno na všechny zásady v tenantovi. Například uživatel procházení dvě cesty zásad pro klienta není vyzván výběru zprostředkovatele identity. `Application` Hodnota značí, že je chování aplikováno na všechny zásady pro aplikace, který zadal žádost. Například uživatel procházení dvě cesty zásad pro aplikaci není vyzván výběru zprostředkovatele identity. `Policy` Hodnota značí, že chování platí jenom pro zásadu. Například je procházení dvě cesty zásady pro vztah důvěryhodnosti framework uživatel vyzván výběru zprostředkovatele identity při přepínání mezi zásadami. |

## <a name="journeyinsights"></a>JourneyInsights

**JourneyInsights** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| TelemetryEngine | Ano | Hodnota musí být `ApplicationInsights`. | 
| InstrumentationKey | Ano | Řetězec, který obsahuje klíč instrumentace pro application insights element. |
| DeveloperMode | Ano | Možné hodnoty: `true` nebo `false`. Pokud `true`, Application Insights urychluje telemetrická data prostřednictvím kanálu zpracování. Toto nastavení je vhodný pro vývoj, ale omezené na velké objemy aktivity podrobné protokoly jsou určeny pouze pro vývoj vlastních zásad. Nepoužívejte režimu pro vývoj v produkčním prostředí. Protokoly shromažďovat všechny deklarace identity posílané do a z zprostředkovatelé identity během vývoje. Je-li použít v produkčním prostředí, vývojář zodpovědnost za identifikovatelné osobní údaje (soukromě údaje) shromážděné v protokolu App Insights, které vlastní. Tyto podrobné protokoly se shromažďují, pouze pokud je tato hodnota nastavena na `true`.|
| ClientEnabled | Ano | Možné hodnoty: `true` nebo `false`. Pokud `true`, odešle skript na straně klienta Application Insights pro sledování chyb stránky zobrazení a na straně klienta. | 
| ServerEnabled | Ano | Možné hodnoty: `true` nebo `false`. Pokud `true`, odešle existující UserJourneyRecorder JSON jako vlastní událost do Application Insights. | 
| TelemetryVersion | Ano | Hodnota musí být `1.0.0`. | 

Další informace najdete v tématu [shromažďování protokolů](active-directory-b2c-troubleshoot-custom.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Pomocí vlastních zásad v Azure AD B2C, můžete odeslat parametr v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Azure AD B2C předá dotaz parametry řetězce dynamické soubor HTML, jako je například souboru .aspx. 

Následující příklad předá parametr s názvem `campaignId` s hodnotou `hawaii` v řetězci dotazu:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Řetězec, který obsahuje pár klíč-hodnota, která se připojuje k řetězci dotazu identifikátoru definici obsahu zatížení identifikátor URI. |

**ContentDefinitionParameter** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název páru klíč-hodnota. |

Další informace najdete v tématu [konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad](active-directory-b2c-ui-customization-custom-dynamic.md)

## <a name="technicalprofile"></a>Technický profil

**Technický profil** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- | 
| ID | Ano | Hodnota musí být `PolicyProfile`. |

**Technický profil** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Zobrazovaný název | 0:1 | Řetězec, který obsahuje název technický profil, který se zobrazí uživatelům. |
| Popis | 0:1 | Řetězec, který obsahuje popis technický profil, který se zobrazí uživatelům. |
| Protocol (Protokol) | 1:1 | Protokol použitý pro federace. |
| Metadata | 0:1 | Kolekce *položky* párů klíč/hodnota využívaných protokol pro komunikaci s koncovým bodem v průběhu transakce nakonfigurujte interakci mezi předávající strany a ostatní účastníci komunity. |
| OutputClaims | 0:1 | Seznam typů deklarací identity, které jsou použity jako výstup v technickém profilu. Každý z těchto elementů obsahuje odkaz na **typu deklarace identity** již definována v **ClaimsSchema** části nebo v zásadách, ze kterého dědí tento soubor zásad. |
| SubjectNamingInfo | 0:1 | Název subjektu použít v tokenech. |

**Protokol** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název platný protokol podporovaný službou Azure AD B2C, který se používá jako součást technický profil. Možné hodnoty: `OpenIdConnect` nebo `SAML2`. `OpenIdConnect` Hodnota představuje standardní protokol OpenID Connect 1.0 podle specifikace foundation OpenID. `SAML2` Představuje standardní protokol SAML 2.0 podle specifikace OASIS. Nepoužívejte v produkčním prostředí tokenu SAML. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| outputClaim | 0: n | Název typu očekávané deklarace identity v seznamu podporovaných pro zásady, na který se přihlásí předávající straně. Tato deklarace identity slouží jako výstup pro technický profil. |

**OutputClaim** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Odkaz na **typu deklarace identity** již definována v **ClaimsSchema** oddílu v souboru zásad. |
| Výchozí hodnota | Ne | Výchozí hodnota, která lze použít, pokud hodnota deklarace identity je prázdný. |
| PartnerClaimType | Ne | Odešle deklaraci identity v jiný název, podle konfigurace v definici typu deklarace identity. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

S **SubjectNameingInfo** prvku, řídí hodnota tokenu subjektu:
- **JTW token** – `sub` deklarací identity. Toto je hlavní o tom, které token vyhodnocuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze přiřadit nebo znovu použít. Slouží ke kontrole autorizace bezpečně, třeba když se používá token pro přístup k prostředku. Ve výchozím nastavení deklarace identity subjektu se vyplní ID objektu uživatele v adresáři. Další informace najdete v tématu [Token, relace a konfigurace jednotného přihlašování](active-directory-b2c-token-session-sso.md).
- **SAML token** – `<Subject><NameID>` element, který identifikuje elementu předmět.

**SubjectNamingInfo** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| ClaimType | Ano | Odkaz na výstupní deklarací **PartnerClaimType**. Výstup deklarací identity, musí být definován v zásadách předávající strana **OutputClaims** kolekce. |

Následující příklad ukazuje, jak definovat OpenId Connect, předávající strany. Informace o názvu subjektu je nakonfigurovaný jako `objectId`:

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
Obsahuje JWT token `sub` deklarace identity s ID objektu uživatele:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


