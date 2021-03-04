---
title: Překladače deklarací identity ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Naučte se používat překladače deklarací identity ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b69492dc41786a677043df5e77c9d12aa26893f6
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119769"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Překladače deklarací identity v Azure Active Directory B2C vlastní zásady

Překladače deklarací identity v Azure Active Directory B2C (Azure AD B2C) [vlastní zásady](custom-policy-overview.md) poskytují kontextové informace o žádosti o autorizaci, jako je název zásady, ID korelace požadavku, jazyk uživatelského rozhraní a další.

Chcete-li použít překladač deklarací identity ve vstupní nebo výstupní deklaraci identity, definujte v rámci elementu [ClaimsSchema](claimsschema.md) řetězec **ClaimType** a pak nastavte hodnotu **DefaultValue** na překladač deklarací identity v elementu Input nebo Output. Azure AD B2C přečte hodnotu překladače deklarací identity a použije hodnotu v technickém profilu.

V následujícím příkladu je typ deklarace s názvem `correlationId` definován s **datovým** typem `string` .

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

V technickém profilu namapujte překladač deklarací identity na typ deklarace identity. Azure AD B2C naplní hodnotu překladače deklarací identity na `{Context:CorrelationId}` deklaraci identity `correlationId` a pošle deklaraci do technického profilu.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typy překladače deklarací identity

Následující části uvádějí dostupné překladače deklarací identity.

### <a name="culture"></a>Kultura

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Culture: LanguageGroup} | Dva číslice kódu ISO pro jazyk | en |
| {Culture: LCID}   | Identifikátor LCID kódu jazyka. | 1033 |
| {Culture: RegionName} | Dvě písmena kódu ISO pro oblast. | USA |
| {Culture: RFC5646} | Kód jazyka RFC5646 | en-US |

### <a name="policy"></a>Zásady

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Policy: PolicyId} | Název zásady předávající strany | B2C_1A_signup_signin |
| {Policy: RelyingPartyTenantId} | ID tenanta zásady předávající strany. | your-tenant.onmicrosoft.com |
| {Policy: TenantObjectId} | ID objektu tenanta zásady předávající strany. | 00000000-0000-0000-0000-000000000000 |
| {Policy: TrustFrameworkTenantId} | ID tenanta architektury vztahu důvěryhodnosti. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {OIDC: AuthenticationContextReferences} |`acr_values`Parametr řetězce dotazu. | – |
| {OIDC: ClientId} |`client_id`Parametr řetězce dotazu. | 00000000-0000-0000-0000-000000000000 |
| {OIDC: DomainHint} |`domain_hint`Parametr řetězce dotazu. | facebook.com |
| {OIDC: LoginHint} |  `login_hint`Parametr řetězce dotazu. | someone@contoso.com |
| {OIDC: MaxAge} | Hodnota `max_age` | – |
| {OIDC: nonce} |`Nonce`Parametr řetězce dotazu. | defaultNonce |
| {OIDC: heslo}| Heslo uživatele [toku přihlašovacích údajů pro heslo vlastníka prostředku](ropc-custom.md) .| Heslo1| 
| {OIDC: prompt} | `prompt`Parametr řetězce dotazu. | přihlášení |
| {OIDC: RedirectUri} |`redirect_uri`Parametr řetězce dotazu. | https://jwt.ms |
| {OIDC: Resource} |`resource`Parametr řetězce dotazu. | – |
| {OIDC: Scope} |`scope`Parametr řetězce dotazu. | OpenID |
| {OIDC: username}| Uživatelské jméno uživatele [toku přihlašovacích údajů pro heslo vlastníka prostředku](ropc-custom.md) .| emily@contoso.com| 

### <a name="context"></a>Kontext

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Context: BuildNumber} | Verze architektury rozhraní identity Experience Framework (číslo buildu).  | 1.0.507.0 |
| {Context: ID korelace} | ID korelace.  | 00000000-0000-0000-0000-000000000000 |
| {Context: DateTimeInUtc} |Datum a čas ve standardu UTC.  | 10/10/2018 12:00:00 ODP. |
| {Context: DeploymentMode} |Režim nasazení zásad.  | Výroba |
| {Context: název_hostitele} | Název hostitele aktuálního požadavku.  | contoso.b2clogin.com |
| {Context: IPAddress} | IP adresa uživatele. | 11.111.111.11 |
| {Context: políčko zůstat přihlášeni} | Určuje, zda je zaškrtnuto políčko [zůstat přihlášeni](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) . |  true |

### <a name="claims"></a>Deklarace identit 

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Deklarace: typ deklarace identity} | Identifikátor typu deklarace identity, který už je definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad.  Například: `{Claim:displayName}` nebo `{Claim:objectId}` . | Hodnota typu deklarace.|


### <a name="oauth2-key-value-parameters"></a>Parametry klíč-hodnota OAuth2

Libovolný název parametru, který je součástí žádosti OIDC nebo OAuth2, se dá namapovat na deklaraci identity uživatele. Například požadavek z aplikace může obsahovat parametr řetězce dotazu s názvem `app_session` , `loyalty_number` nebo libovolným vlastním řetězcem dotazu.

| Deklarovat | Popis | Příklad |
| ----- | ----------------------- | --------|
| {OAUTH-KV: campaignId} | Parametr řetězce dotazu. | Hawaii |
| {OAUTH-KV: app_session} | Parametr řetězce dotazu. | A3C5R |
| {OAUTH-KV: loyalty_number} | Parametr řetězce dotazu. | 1 234 |
| {OAUTH-KV: jakýkoliv vlastní řetězec dotazu} | Parametr řetězce dotazu. | – |

### <a name="oauth2"></a>OAuth2

| Deklarovat | Popis | Příklad |
| ----- | ----------------------- | --------|
| {OAuth2: access_token} | Přístupový token | – |
| {OAuth2: refresh_token} | Obnovovací token | – |


### <a name="saml"></a>SAML

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {SAML: AuthnContextClassReferences} | `AuthnContextClassRef`Hodnota elementu, od požadavku SAML. | urn: Oasis: names: TC: SAML: 2.0: AC: třídy: PasswordProtectedTransport |
| {SAML: NameIdPolicyFormat} | `Format`Atribut z `NameIDPolicy` prvku požadavku SAML. | urn: Oasis: names: TC: SAML: 1.1: NameId-Format: emailAddress |
| {SAML: Issuer} |  `Issuer`Hodnota prvku SAML požadavku SAML.| `https://contoso.com` |
| {SAML: AllowCreate} | `AllowCreate`Hodnota atributu z `NameIDPolicy` prvku požadavku SAML. | Ano |
| {SAML: ForceAuthn} | `ForceAuthN`Hodnota atributu z `AuthnRequest` prvku požadavku SAML. | Ano |
| {SAML: ProviderName} | `ProviderName`Hodnota atributu z `AuthnRequest` prvku požadavku SAML.| Contoso.com |
| {SAML: RelayState} | `RelayState`Parametr řetězce dotazu.| 
| {SAML: Subject} | `Subject`Z prvku NameId žádosti AUTHN SAML.| 

## <a name="using-claim-resolvers"></a>Použití překladačů deklarací identity

Překladače deklarací identity můžete použít s následujícími prvky:

| Položka | Prvek | Nastavení |
| ----- | ----------------------- | --------|
|Application Insights technický profil |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md) technický profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md) Technical Profile| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md) Technical Profile| `InputClaim`, `OutputClaim`| 1, 2|
|Technický profil [transformace deklarací identity](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technický profil [poskytovatele RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|Technický profil [zprostředkovatele identity SAML](identity-provider-generic-saml.md)| `OutputClaim`| 1, 2|
|Technický profil [s vlastním uplatněním](self-asserted-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile) Technical Profile| `OutputClaim`| 2 |

Možnost
1. `IncludeClaimResolvingInClaimsHandling`Metadata musí být nastavena na `true` .
1. Vstupní nebo výstupní atribut deklarace identity `AlwaysUseDefaultValue` musí být nastaven na hodnotu `true` .

## <a name="claim-resolvers-samples"></a>Ukázky překladačů deklarací identity

### <a name="restful-technical-profile"></a>RESTful Technical Profile

V [RESTful](restful-technical-profile.md) Technical Profile můžete chtít odeslat uživatelský jazyk, název zásady, obor a ID klienta. Na základě deklarací identity může REST API spustit vlastní obchodní logiku a v případě potřeby vyvolat lokalizovanou chybovou zprávu.

Následující příklad ukazuje technický profil RESTful s tímto scénářem:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Přímé přihlášení

Pomocí překladačů deklarací identity můžete předem naplnit přihlašovací jméno nebo přímé přihlášení ke konkrétnímu poskytovateli sociálních identit, jako je Facebook, LinkedIn nebo účet Microsoft. Další informace najdete v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamické přizpůsobení uživatelského rozhraní

Azure AD B2C umožňuje předat parametry řetězce dotazu do koncových bodů definice obsahu HTML pro dynamické vykreslování obsahu stránky. Tato funkce například umožňuje upravit obrázek pozadí na stránce Azure AD B2C přihlašovací nebo přihlašovací stránku na základě vlastního parametru, který předáte z webové nebo mobilní aplikace. Další informace najdete v tématu [dynamická konfigurace uživatelského rozhraní pomocí vlastních zásad v Azure Active Directory B2C](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri). Můžete také lokalizovat stránku HTML na základě parametru jazyka nebo můžete změnit obsah na základě ID klienta.

Následující příklad předává parametr řetězce dotazu s názvem **campaignId** s hodnotou `Hawaii` , kód **jazyka** `en-US` a **aplikace** představující ID klienta:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

V důsledku toho Azure AD B2C odesílá výše uvedené parametry na stránku obsahu HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definice obsahu

V [ContentDefinition](contentdefinitions.md) `LoadUri` můžete posílat překladače deklarací identity na vyžádání obsahu z různých míst na základě použitých parametrů.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Application Insights technický profil

Díky Azure Application Insights a překladačům deklarací identity můžete získat přehled o chování uživatelů. V Application Insights Technical profil odešlete vstupní deklarace identity, které jsou trvalé ve službě Azure Application Insights. Další informace najdete v tématu [sledování chování uživatelů v Azure AD B2Cch cestách pomocí Application Insights](analytics-with-application-insights.md). Následující příklad odešle ID zásady, ID korelace, jazyk a ID klienta do Azure Application Insights.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Zásady předávající strany

V technickém profilu zásad [předávající strany](relyingparty.md) můžete chtít odeslat ID TENANTA nebo ID korelace do aplikace předávající strany v rámci tokenu JWT.

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
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
