---
title: RelyingParty – Služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zadejte prvek RelyingParty vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 733a33881fe3acc962aeda4b05a1b01be4e148ca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680360"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Prvek **RelyingParty** určuje cestu uživatele k vynucení pro aktuální požadavek na Azure Active Directory B2C (Azure AD B2C). Také určuje seznam deklarací, které aplikace předávající strany (RP) potřebuje jako součást vydaného tokenu. Aplikace RP, například webová, mobilní nebo desktopová aplikace, volá soubor zásad RP. Soubor zásad RP provede určitou úlohu, například přihlášení, resetování hesla nebo úpravu profilu. Více aplikací může používat stejné zásady RP a jedna aplikace může používat více zásad. Všechny aplikace RP obdrží stejný token s deklaracemi identity a uživatel projde stejnou cestou uživatele.

Následující příklad ukazuje prvek **RelyingParty** v souboru zásad *B2C_1A_signup_signin:*

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
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
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

Volitelný prvek **RelyingParty** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výchozí cesta uživatele | 1:1 | Výchozí cesta uživatele pro aplikaci RP. |
| UserJourneyBehaviors | 0:1 | Rozsah chování cesty uživatele. |
| Technický profil | 1:1 | Technický profil podporovaný aplikací RP. Technický profil poskytuje smlouvu pro aplikaci RP kontaktovat Azure AD B2C. |

## <a name="defaultuserjourney"></a>Výchozí cesta uživatele

Prvek `DefaultUserJourney` určuje odkaz na identifikátor cesty uživatele, který je obvykle definován v zásadách Base nebo Extensions. Následující příklady ukazují cestu uživatele registrace nebo přihlášení zadanou v elementu **RelyingParty:**

*B2C_1A_signup_signin* politika:

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

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor cesty uživatele v zásadách. Další informace naleznete v tématu [cesty uživatelů](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Prvek **UserJourneyBehaviors** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Jednotné označení | 0:1 | Rozsah chování relace jednotného přihlašování (SSO) cesty uživatele. |
| Typ ukončení platnosti relace |0:1 | Chování ověřování relace. Možné `Rolling` hodnoty: `Absolute`nebo . Hodnota `Rolling` (výchozí) označuje, že uživatel zůstává přihlášen tak dlouho, dokud je uživatel neustále aktivní v aplikaci. Hodnota `Absolute` označuje, že uživatel je nucen znovu ověřit po uplynutí časového období určeného životností relace aplikace. |
| SessionExpiryInSeconds | 0:1 | Životnost souboru cookie relace Azure AD B2C určené jako celé číslo uložené v prohlížeči uživatele po úspěšném ověření. |
| Statistiky cesty | 0:1 | Klíč instrumentace Azure Application Insights, který se má použít. |
| ContentDefinitionParameters | 0:1 | Seznam párů hodnot klíčů, které mají být připojeny k identifikátoru URI načtení definice obsahu. |
|Spuštění skriptu| 0:1| Podporované režimy spuštění [javascriptu.](javascript-samples.md) Možné `Allow` hodnoty: `Disallow` nebo (výchozí).

### <a name="singlesignon"></a>Jednotné označení

Element **SingleSignOn** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Rozsah | Ano | Rozsah chování jednotného přihlášení. Možné `Suppressed`hodnoty: `Tenant` `Application`, `Policy`, , nebo . Hodnota `Suppressed` označuje, že chování je potlačeno a uživatel je vždy vyzván k výběru zprostředkovatele identity.  Hodnota `Tenant` označuje, že chování se použije pro všechny zásady v tenantovi. Například uživatel procházení přes dvě cesty zásad pro klienta není vyzván k výběru zprostředkovatele identity. Hodnota `Application` označuje, že chování se použije na všechny zásady pro aplikaci, která podává požadavek. Například uživatel procházení přes dvě cesty zásad pro aplikaci není vyzván k výběru zprostředkovatele identity. Hodnota `Policy` označuje, že chování platí pouze pro zásady. Například uživatel procházení přes dvě cesty zásad pro rámec důvěryhodnosti je vyzván k výběru zprostředkovatele identity při přepínání mezi zásadami. |
| KeepAliveInDays | Ano | Určuje, jak dlouho zůstane uživatel přihlášen. Nastavení hodnoty na hodnotu 0 vypne funkci KMSI. Další informace naleznete v tématu [Keep me signed in](custom-policy-keep-me-signed-in.md). |
|EnforceIdTokenHintOnLogout| Ne|  Vynutit předání dříve vydaného tokenu ID do koncového bodu odhlášení jako nápověda o aktuální ověřené relaci koncového uživatele s klientem. Možné hodnoty: `false` (výchozí) nebo `true`. Další informace naleznete [v tématu Webové přihlašování pomocí OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>Statistiky cesty

Prvek **JourneyInsights** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| TelemetrieMotor | Ano | Hodnota musí `ApplicationInsights`být . |
| InstrumentaceKlíč | Ano | Řetězec, který obsahuje klíč instrumentace pro prvek přehledy aplikace. |
| Režim vývojáře | Ano | Možné `true` hodnoty: `false`nebo . Pokud `true`application insights urychluje telemetrická data prostřednictvím kanálu zpracování. Toto nastavení je vhodné pro vývoj, ale omezena na velké objemy podrobné protokoly aktivit jsou určeny pouze pro podporu při vývoji vlastních zásad. Nepoužívejte vývojový režim ve výrobě. Protokoly shromažďovat všechny deklarace odeslané a od poskytovatelů identit během vývoje. Při použití v produkčním prostředí přebírá vývojář odpovědnost za osobní údaje (soukromě identifikovatelné informace) shromážděné v protokolu Přehledy aplikací, které vlastní. Tyto podrobné protokoly jsou shromažďovány pouze `true`v případě, že je tato hodnota nastavena na .|
| Klientpovoleno | Ano | Možné `true` hodnoty: `false`nebo . Pokud `true`odešle skript application insights na straně klienta pro sledování zobrazení stránky a chyby na straně klienta. |
| Serverpovoleno | Ano | Možné `true` hodnoty: `false`nebo . Pokud `true`odešle existující UserJourneyRecorder JSON jako vlastní událost application insights. |
| Telemetrická verze | Ano | Hodnota musí `1.0.0`být . |

Další informace naleznete v [tématu Shromažďování protokolů](troubleshoot-with-application-insights.md)

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Pomocí vlastních zásad v Azure AD B2C můžete odeslat parametr v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Azure AD B2C předá parametry řetězce dotazu do dynamického souboru HTML, jako je například soubor aspx.

Následující příklad předá `campaignId` parametr s `hawaii` názvem s hodnotou v řetězci dotazu:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Element **ContentDefinitionParameters** obsahuje následující prvek:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Řetězec, který obsahuje dvojici hodnot klíče, která je připojena k řetězci dotazu identifikátoru URI načtení definice obsahu. |

Element **ContentDefinitionParameter** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název dvojice hodnot klíče. |

Další informace najdete [v tématu Konfigurace rozhraní s dynamickým obsahem pomocí vlastních zásad](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)

## <a name="technicalprofile"></a>Technický profil

Prvek **TechnicalProfile** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Hodnota musí `PolicyProfile`být . |

**TechnicalProfile** obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Řetězec, který obsahuje název technického profilu. |
| Popis | 0:1 | Řetězec, který obsahuje popis technického profilu. |
| Protocol (Protokol) | 1:1 | Protokol používaný pro federaci. |
| Metadata | 0:1 | Kolekce *Položky* párů klíč/hodnota využité protokolem pro komunikaci s koncovým bodem v průběhu transakce ke konfiguraci interakce mezi předávající stranou a ostatními účastníky komunity. |
| OutputClaims | 1:1 | Seznam typů deklarací, které jsou brány jako výstup v technickém profilu. Každý z těchto prvků obsahuje odkaz na **ClaimType** již definována v **claimsSchema** části nebo v zásadě, ze kterého tento soubor zásad dědí. |
| Informace o pojmenování předmětu | 1:1 | Název subjektu použitý v tokenech. |

Element **Protocol** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název platného protokolu podporovaného Azure AD B2C, který se používá jako součást technického profilu. Možné `OpenIdConnect` hodnoty: `SAML2`nebo . Hodnota `OpenIdConnect` představuje standard protokolu OpenID Connect 1.0 podle specifikace nadace OpenID. Představuje `SAML2` standard protokolu SAML 2.0 podle specifikace OASIS. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** Element obsahuje následující prvek:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Výstupní nárok | 0:n | Název typu očekávané deklarace v podporovaném seznamu pro zásadu, ke které se předávající strana přihlásí. Toto tvrzení slouží jako výstup pro technický profil. |

Element **OutputClaim** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Odkaz na **ClaimType** již definována v **ClaimsSchema** části v souboru zásad. |
| Defaultvalue | Ne | Výchozí hodnota, kterou lze použít, pokud je hodnota deklarace pohledávky prázdná. |
| Typ deklarace programu Partner | Ne | Odešle deklaraci v jiném názvu, jak je nakonfigurováno v definici ClaimType. |

### <a name="subjectnaminginfo"></a>Informace o pojmenování předmětu

S **SubjectNameingInfo** prvek můžete řídit hodnotu předmětu tokenu:
- **JWT token** `sub` - deklarace. Toto je objekt zabezpečení, o kterém token uplatňuje informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani znovu použít. Lze jej použít k provádění bezpečných kontrol autorizace, například při použití tokenu pro přístup k prostředku. Ve výchozím nastavení je deklarace předmětu naplněna ID objektu uživatele v adresáři. Další informace naleznete v tématu [Token, session and single sign-on configuration](session-behavior.md).
- **SAML token** `<Subject><NameID>` - prvek, který identifikuje prvek předmětu.

Element **SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Typ deklarace pohledávky | Ano | Odkaz na výstupní deklaraci **PartnerClaimType**. Výstupní deklarace musí být definovány v kolekci zásad předávající **strany OutputClaims.** |

Následující příklad ukazuje, jak definovat předávající stranu OpenID Connect. Informace o názvu subjektu `objectId`jsou konfigurovány jako :

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
Token JWT zahrnuje `sub` deklaraci s objektem uživateleId:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
