---
title: RelyingParty-Azure Active Directory B2C | Microsoft Docs
description: Zadejte element RelyingParty vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5374ce59d3a599e243684c168a8d84a6434059ee
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103492009"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **RelyingParty** určuje cestu uživatele, která se má vynutit pro aktuální požadavek na Azure Active Directory B2C (Azure AD B2C). Určuje také seznam deklarací identity, které aplikace přijímající strany vyžaduje v rámci vystaveného tokenu. Aplikace RP, jako je například webová, mobilní nebo desktopová aplikace, zavolá soubor zásad RP. Soubor zásad RP spustí konkrétní úlohu, jako je například přihlašování, resetování hesla nebo úprava profilu. Víc aplikací může používat stejné zásady RP a jedna aplikace může používat víc zásad. Všechny aplikace pro RP získají stejný token s deklaracemi a uživatel projde stejnou cestou uživatele.

Následující příklad ukazuje element **RelyingParty** v souboru zásad *B2C_1A_signup_signin* :

```xml
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

Volitelný element **RelyingParty** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Výchozí cesta uživatele pro aplikaci RP. |
| Koncové body | 0:1 | Seznam koncových bodů. Další informace najdete v tématu [koncový bod UserInfo](userinfo-endpoint.md). |
| UserJourneyBehaviors | 0:1 | Rozsah chování při jízdě uživatelů. |
| TechnicalProfile | 1:1 | Technický profil podporovaný aplikací RP. Technický profil poskytuje kontrakt pro aplikaci RP, aby kontaktoval Azure AD B2C. |

## <a name="endpoints"></a>Koncové body

Element **Endpoints** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Koncový bod | 1:1 | Odkaz na koncový bod.|

Element **Endpoint** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Jedinečný identifikátor koncového bodu.|
| UserJourneyReferenceId | Ano | Identifikátor cesty uživatele v zásadě. Další informace najdete v tématu [cesty uživatelů](userjourneys.md) .  | 

Následující příklad ukazuje předávající stranu s [koncovým bodem UserInfo](userinfo-endpoint.md):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney`Element určuje odkaz na identifikátor cesty uživatele, která je definována v zásadách základní nebo rozšíření. Následující příklady znázorňují cestu k registraci nebo přihlašování uživatele určenou v elementu **RelyingParty** :

Zásada *B2C_1A_signup_signin* :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* nebo *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Element **DefaultUserJourney** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor cesty uživatele v zásadě. Další informace najdete v tématu [cesty uživatelů](userjourneys.md) . |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Element **UserJourneyBehaviors** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Rozsah chování relace jednotného přihlašování (SSO) pro cestu uživatele. |
| SessionExpiryType |0:1 | Chování při ověřování relace. Možné hodnoty: `Rolling` nebo `Absolute` . `Rolling`Hodnota (výchozí) znamená, že uživatel zůstane přihlášený, dokud bude uživatel v aplikaci neustále aktivní. `Absolute`Hodnota označuje, že se uživatel bude nucen znovu ověřit po uplynutí časového období určeného v době platnosti relace aplikace. |
| SessionExpiryInSeconds | 0:1 | Doba života souboru cookie relace Azure AD B2C's zadaná jako celé číslo uložené v prohlížeči uživatele po úspěšném ověření. |
| JourneyInsights | 0:1 | Klíč instrumentace Azure Application Insights, který se má použít. |
| ContentDefinitionParameters | 0:1 | Seznam párů klíč-hodnota, které se mají připojit k identifikátoru URI načtení definice obsahu. |
|ScriptExecution| 0:1| Podporované režimy spuštění [JavaScriptu](javascript-and-page-layout.md) . Možné hodnoty: `Allow` nebo `Disallow` (výchozí).
| JourneyFraming | 0:1| Umožňuje načtení uživatelského rozhraní této zásady do prvku IFRAME. |

### <a name="singlesignon"></a>SingleSignOn

Element **SingleSignon** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Obor | Ano | Rozsah chování jednotného přihlašování. Možné hodnoty: `Suppressed` , `Tenant` , `Application` , nebo `Policy` . `Suppressed`Hodnota znamená, že se chování potlačí a uživatel se vždy zobrazí výzva k výběru poskytovatele identity.  `Tenant`Hodnota označuje, že se chování použije u všech zásad v tenantovi. Například uživatel, který přecházení ze dvou cest zásad pro tenanta, nevyzve k výběru poskytovatele identity. `Application`Hodnota označuje, že chování bude použito pro všechny zásady aplikace, které vytváří požadavek. Například uživatel, který přecházení ze dvou cest zásad pro aplikaci, nezobrazuje výzvu k výběru poskytovatele identity. `Policy`Hodnota znamená, že chování se vztahuje pouze na zásadu. Například uživatel, který přechází ze dvou cest zásad pro rozhraní vztahu důvěryhodnosti, se při přepínání mezi zásadami zobrazí dotaz na výběr poskytovatele identity. |
| KeepAliveInDays | Ne | Určuje, jak dlouho zůstane uživatel přihlášený. Nastavením hodnoty 0 dojde k vypnutí funkcí políčko zůstat přihlášeni. Další informace najdete v tématu [zůstat přihlášeni](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi). |
|EnforceIdTokenHintOnLogout| Ne|  Vynutí předání dříve vydaného tokenu ID koncovému bodu pro odhlášení jako pomocný parametr pro aktuální ověřenou relaci koncového uživatele s klientem. Možné hodnoty: `false` (výchozí), nebo `true` . Další informace najdete v tématu věnovaném [webovému přihlášení pomocí OpenID Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

Element **JourneyInsights** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| TelemetryEngine | Ano | Hodnota musí být `ApplicationInsights` . |
| InstrumentationKey | Ano | Řetězec, který obsahuje klíč instrumentace pro element Application Insights. |
| DeveloperMode | Ano | Možné hodnoty: `true` nebo `false` . Pokud `true` Application Insights zrychlí telemetrii prostřednictvím kanálu zpracování. Toto nastavení je vhodné pro vývoj, ale je omezené na vysoké objemy. Podrobné protokoly aktivit jsou navržené jenom k podpoře vývoje vlastních zásad. Nepoužívejte režim vývoje v produkčním prostředí. Protokoly shromažďují všechny deklarace, které během vývoje odesílají a od nich od poskytovatelů identity. Při použití v produkčním prostředí předpokládá vývojář zodpovědnost za osobní údaje shromažďované v protokolu App Insights, který vlastní. Tyto podrobné protokoly jsou shromažďovány, pouze pokud je tato hodnota nastavena na `true` .|
| ClientEnabled | Ano | Možné hodnoty: `true` nebo `false` . Pokud `true` aplikace odešle Application Insights skript na straně klienta pro sledování zobrazení stránky a chyby na straně klienta. |
| ServerEnabled | Ano | Možné hodnoty: `true` nebo `false` . Pokud `true` aplikace odešle existující USERJOURNEYRECORDER JSON jako vlastní událost pro Application Insights. |
| TelemetryVersion | Ano | Hodnota musí být `1.0.0` . |

Další informace najdete v tématu [shromažďování protokolů](troubleshoot-with-application-insights.md) .

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Pomocí vlastních zásad v Azure AD B2C můžete odeslat parametr v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Azure AD B2C předá parametry řetězce dotazu do dynamického souboru HTML, jako je třeba soubor ASPX.

Následující příklad předává parametr s názvem `campaignId` s hodnotou `hawaii` v řetězci dotazu:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Element **ContentDefinitionParameters** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Řetězec, který obsahuje dvojici klíč-hodnota, která je připojena k řetězci dotazu identifikátoru URI pro načtení definice obsahu. |

Element **ContentDefinitionParameter** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název páru klíč-hodnota. |

Další informace najdete v tématu [Konfigurace uživatelského rozhraní s dynamickým obsahem pomocí vlastních zásad](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) .

### <a name="journeyframing"></a>JourneyFraming

Element **JourneyFraming** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Povoleno | Ano | Povolí načtení této zásady v rámci prvku IFRAME. Možné hodnoty: `false` (výchozí), nebo `true` . |
| zdroje | Ano | Obsahuje domény, které budou načítat hosta elementu IFRAME. Další informace najdete v tématu [načítání Azure B2C v prvku IFRAME](embedded-login.md). |

## <a name="technicalprofile"></a>TechnicalProfile

Element **TechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Hodnota musí být `PolicyProfile` . |

**TechnicalProfile** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Řetězec, který obsahuje název technického profilu. |
| Popis | 0:1 | Řetězec, který obsahuje popis technického profilu. |
| Protokol | 1:1 | Protokol používaný pro federaci. |
| Metadata | 0:1 | Kolekce *položek* párů klíč/hodnota využívané protokolem pro komunikaci s koncovým bodem v průběhu transakce pro konfiguraci interakce mezi předávající stranou a ostatními účastníky komunity. |
| OutputClaims | 1:1 | Seznam typů deklarací, které jsou pořízeny jako výstup v technickém profilu. Každý z těchto prvků obsahuje odkaz na objekt **ClaimType** , který je již definován v části **ClaimsSchema** nebo v zásadě, ze které tento soubor zásad dědí. |
| SubjectNamingInfo | 1:1 | Název subjektu, který se používá v tokenech. |

Element **Protocol** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Název | Ano | Název platného protokolu podporovaného Azure AD B2C, který se používá jako součást technického profilu. Možné hodnoty: `OpenIdConnect` nebo `SAML2` . `OpenIdConnect`Hodnota představuje standard protokolu OpenID Connect 1,0 podle specifikace OpenID Foundation. `SAML2`Představuje standard protokolu SAML 2,0 podle specifikace pro Oasis. |

### <a name="metadata"></a>Metadata

Pokud je protokol `SAML` , element metadata obsahuje následující prvky. Další informace najdete v tématu [Možnosti registrace aplikace SAML v Azure AD B2C](saml-service-provider-options.md).

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| IdpInitiatedProfileEnabled | Ne | Označuje, zda je podporován tok iniciované IDP. Možné hodnoty: `true` nebo `false` (výchozí). | 
| XmlSignatureAlgorithm | Ne | Metoda, kterou Azure AD B2C používá k podepsání odpovědi SAML. Možné hodnoty: `Sha256` , `Sha384` , `Sha512` , nebo `Sha1` . Nezapomeňte nakonfigurovat algoritmus podpisu na obou stranách se stejnou hodnotou. Používejte jenom algoritmus, který podporuje váš certifikát. Pokud chcete nakonfigurovat kontrolní výraz SAML, přečtěte si [metadata Technical profil vystavitele SAML](saml-issuer-technical-profile.md#metadata). |
| DataEncryptionMethod | Ne | Určuje metodu, kterou Azure AD B2C používá k šifrování dat pomocí algoritmu standard AES (Advanced Encryption Standard) (AES). Metadata řídí hodnotu `<EncryptedData>` prvku v odpovědi SAML. Možné hodnoty: `Aes256` (výchozí), `Aes192` , `Sha512` nebo ` Aes128` . |
| KeyEncryptionMethod| Ne | Určuje metodu, kterou Azure AD B2C používá k zašifrování kopie klíče, který se použil k zašifrování dat. Metadata řídí hodnotu  `<EncryptedKey>` prvku v odpovědi SAML. Možné hodnoty: ` Rsa15` (výchozí) – algoritmus 1,5 PKCS (Public Key Cryptography Standard) standardu RSA ( ` RsaOaep` výplně OAEP) – optimální šifrování asymetrického šifrování (). |
| UseDetachedKeys | Ne |  Možné hodnoty: `true` , nebo `false` (výchozí). Pokud je hodnota nastavena na `true` , Azure AD B2C změní formát šifrovaných kontrolních výrazů. Použití odpojených klíčů přidá šifrovaný kontrolní výraz jako podřízený objekt EncrytedAssertion, a to na rozdíl od EncryptedData. |
| WantsSignedResponses| Ne | Určuje, zda Azure AD B2C podepíše `Response` oddíl odpovědi SAML. Možné hodnoty: `true` (výchozí) nebo `false` .  |
| RemoveMillisecondsFromDateTime| Ne | Určuje, zda budou milisekundy odebrány z hodnot DateTime v rámci odpovědi SAML (patří mezi ně IssueInstant, NotBefore, NotOnOrAfter a AuthnInstant). Možné hodnoty: `false` (výchozí) nebo `true` .  |


### <a name="outputclaims"></a>OutputClaims

Element **OutputClaims** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | Název očekávaného typu deklarace v seznamu podporovaných pro zásadu, které předávající strana přihlašuje k odběru. Tato deklarace slouží jako výstup pro technický profil. |

Element **OutputClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ano | Odkaz na objekt **ClaimType** již definovaný v oddílu **ClaimsSchema** v souboru zásad. |
| Hodnot | Ne | Výchozí hodnota, která se dá použít, pokud je hodnota deklarace prázdná. |
| PartnerClaimType | Ne | Odešle deklaraci identity v jiném názvu, jak je nakonfigurováno v definici ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Pomocí elementu **SubjectNameingInfo** řídíte hodnotu předmětu tokenu:

- **Token JWT** – `sub` deklarace identity Jedná se o objekt zabezpečení, o kterém token vyhodnotí informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Dá se použít k provádění bezpečných ověřovacích kontrol, například když se token používá pro přístup k prostředku. Ve výchozím nastavení se deklarace identity subjektu naplní s ID objektu uživatele v adresáři. Další informace najdete v tématu [Konfigurace tokenu, relace a jednotného přihlašování](session-behavior.md).
- **Token SAML** – `<Subject><NameID>` element, který identifikuje prvek předmětu. Formát NameId lze upravit.

Element **SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimType | Ano | Odkaz na **PartnerClaimTypeu** výstupní deklarace identity. Deklarace výstupů musí být definované v **OutputClaims** kolekci zásad předávající strany. |
| Formát | Ne | Používá se pro předávající strany SAML k nastavení **formátu NameId** vráceného v kontrolním výrazu SAML. |

Následující příklad ukazuje, jak definovat předávající stranu OpenID Connect. Informace o názvu subjektu jsou nakonfigurovány jako `objectId` :

```xml
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

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

Následující příklad ukazuje, jak definovat předávající stranu SAML. Informace o názvu subjektu jsou nakonfigurovány jako `objectId` a k `format` dispozici je NameId:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
