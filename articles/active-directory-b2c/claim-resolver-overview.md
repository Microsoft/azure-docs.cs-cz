---
title: Nárokovat překladače ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Zjistěte, jak používat překladače deklarací identity ve vlastních zásadách ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0bdede482b79c82e6e05b1429cb7c17399bc2277
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756605"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Překladače deklarací ve vlastních zásadách služby Azure Active Directory B2C

Překladače deklarací ve [vlastních zásadách](custom-policy-overview.md) Azure Active Directory B2C (Azure AD B2C) poskytují kontextové informace o žádosti o autorizaci, jako je název zásady, ID korelace požadavku, jazyk uživatelského rozhraní a další.

Chcete-li použít překládání deklarace identity ve vstupní nebo výstupní deklaraci, definujte řetězec **ClaimType**, pod [ClaimSchema](claimsschema.md) element a potom nastavíte **DefaultValue** na překládání deklarace v elementu vstupní nebo výstupní deklarace identity. Azure AD B2C přečte hodnotu překládání deklarací a používá hodnotu v technickém profilu.

V následujícím příkladu je `correlationId` typ deklarace s `string`názvem **datatype** .

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

V technickém profilu namapujte překladač deklarace na typ deklarace. Azure AD B2C vyplní hodnotu `{Context:CorrelationId}` překládání deklarací do deklarace a `correlationId` odešle deklaraci do technického profilu.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typy překladače deklarací

V následujících částech jsou k dispozici překladače deklarací.

### <a name="culture"></a>Culture

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Jazyková verze:LanguageName} | Dvoupísmenný kód ISO pro jazyk. | en |
| {Jazyková verze:LCID}   | LCID kódu jazyka. | 1033 |
| {Jazyková verze:Název_oblasti} | Dvoupísmenný kód ISO pro oblast. | USA |
| {Jazyková verze:RFC5646} | Kód jazyka RFC5646. | cs-CZ |

### <a name="policy"></a>Zásada

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Zásady:Id zásad} | Název zásady předávající strany. | B2C_1A_signup_signin |
| {Zásada:RelyingPartyTenantId} | ID klienta zásady předávající strany. | your-tenant.onmicrosoft.com |
| {Policy:TenantObjectId} | ID objektu klienta zásady předávající strany. | 00000000-0000-0000-0000-000000000000 |
| {Zásada:Rozhraní TrustFrameworkTenantId} | ID klienta rámce důvěryhodnosti. | your-tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Parametr `acr_values` řetězce dotazu. | – |
| {OIDC:Id klienta} |Parametr `client_id` řetězce dotazu. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Parametr `domain_hint` řetězce dotazu. | facebook.com |
| {OIDC:LoginHint} |  Parametr `login_hint` řetězce dotazu. | someone@contoso.com |
| {OIDC:MaxAge} | Hodnota `max_age` | – |
| {OIDC:Nonce} |Parametr `Nonce` řetězce dotazu. | výchozí Nonce |
| {OIDC:Heslo}| Pověření [vlastníka prostředku hesla toku](ropc-custom.md) uživatele heslo.| heslo1| 
| {OIDC:Výzva} | Parametr `prompt` řetězce dotazu. | přihlášení |
| {OIDC:PřesměrovatUri} |Parametr `redirect_uri` řetězce dotazu. | https://jwt.ms |
| {OIDC:Zdroj} |Parametr `resource` řetězce dotazu. | – |
| {OIDC:Obor} |Parametr `scope` řetězce dotazu. | Openid |
| {OIDC:Uživatelské jméno}| Přihlašovací [údaje vlastníka prostředku tok](ropc-custom.md) uživatelské jméno uživatele.| emily@contoso.com| 

### <a name="context"></a>Kontext

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Kontext:BuildNumber} | Verze rozhraní Identity Experience Framework (číslo sestavení).  | 1.0.507.0 |
| {Kontext:Identifikátor CorrelationId} | ID korelace.  | 00000000-0000-0000-0000-000000000000 |
| {Kontext:DateTimeInUtc} |Čas data v utc.  | 10/10/2018 12:00:00 |
| {Kontext:Režim nasazení} |Režim nasazení zásad.  | Výroba |
| {Kontext:ADRESA IPAddress} | IP adresa uživatele. | 11.111.111.11 |
| {Kontext:KMSI} | Označuje, zda je zaškrtnuto políčko [Ponechat přihlášenou.](custom-policy-keep-me-signed-in.md) |  true |

### <a name="claims"></a>Deklarace identity 

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {Deklarace:typ deklarace identity} | Identifikátor typu deklarace identity, který je již definován v části ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad.  Například: `{Claim:displayName}`, `{Claim:objectId}`nebo . | Hodnota typu deklarace.|


### <a name="oauth2-key-value-parameters"></a>Parametry hodnoty klíče OAuth2

Libovolný název parametru zahrnutý jako součást požadavku OIDC nebo OAuth2 lze namapovat na deklaraci v cestě uživatele. Požadavek z aplikace může například obsahovat parametr řetězce `app_session` `loyalty_number`dotazu s názvem , nebo libovolný vlastní řetězec dotazu.

| Deklarovat | Popis | Příklad |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Parametr řetězce dotazu. | Hawaii |
| {OAUTH-KV:app_session} | Parametr řetězce dotazu. | A3C5R |
| {OAUTH-KV:loyalty_number} | Parametr řetězce dotazu. | 1 234 |
| {OAUTH-KV:libovolný vlastní řetězec dotazu} | Parametr řetězce dotazu. | – |

### <a name="oauth2"></a>OAuth2

| Deklarovat | Popis | Příklad |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Přístupový token. | – |


### <a name="saml"></a>SAML

| Deklarovat | Popis | Příklad |
| ----- | ----------- | --------|
| {SAML:Hodnota AuthnContextClassReferences} | Hodnota `AuthnContextClassRef` prvku z požadavku SAML. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Atribut `Format` z `NameIDPolicy` prvku požadavku SAML. | urn:oasis:jména:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Vydavatel} |  Hodnota prvku `Issuer` SAML požadavku SAML.| `https://contoso.com` |
| {SAML:Povolitvytvoření} | Hodnota `AllowCreate` atributu `NameIDPolicy` z prvku požadavku SAML. | True |
| {SAML:ForceAuthn} | Hodnota `ForceAuthN` atributu `AuthnRequest` z prvku požadavku SAML. | True |
| {SAML:Název zprostředkovatele} | Hodnota `ProviderName` atributu `AuthnRequest` z prvku požadavku SAML.| Contoso.com |
| {SAML:RelayState} | Parametr `RelayState` řetězce dotazu.| 

## <a name="using-claim-resolvers"></a>Použití překladačů deklarací

Překladače deklarací identity můžete použít s následujícími prvky:

| Položka | Prvek | Nastavení |
| ----- | ----------------------- | --------|
|Technický profil Application Insights |`InputClaim` | |
|Technický profil [služby Azure Active Directory](active-directory-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technický profil [OAuth2](oauth2-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technický profil [OpenID Connect](openid-connect-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technický profil [transformace nároků](claims-transformation-technical-profile.md)| `InputClaim`, `OutputClaim`| 1, 2|
|Technický profil [poskytovatele RESTful](restful-technical-profile.md)| `InputClaim`| 1, 2|
|[SAML2](saml-technical-profile.md) technický profil| `OutputClaim`| 1, 2|
|[Vlastní uplatněný](self-asserted-technical-profile.md) technický profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Definice obsahu](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|Technický profil [RelyingParty](relyingparty.md#technicalprofile)| `OutputClaim`| 2 |

Nastavení:
1. Metadata `IncludeClaimResolvingInClaimsHandling` musí být `true`nastavena na .
1. Atribut `AlwaysUseDefaultValue` vstupních nebo výstupních `true`deklarací musí být nastaven na .

## <a name="claim-resolvers-samples"></a>Ukázky překladačů deklarací

### <a name="restful-technical-profile"></a>Technický profil RESTful

V technickém profilu [RESTful](restful-technical-profile.md) můžete chtít odeslat uživatelský jazyk, název zásady, obor a ID klienta. Na základě deklarací identity rozhraní REST API můžete spustit vlastní obchodní logiku a v případě potřeby vyvolat lokalizované chybové zprávy.

Následující příklad ukazuje technický profil RESTful s tímto scénářem:

```XML
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

Pomocí překládání deklarací identity můžete předem vyplnit přihlašovací jméno nebo přímé přihlášení ke konkrétnímu poskytovateli sociální identity, jako je Facebook, LinkedIn nebo účet Microsoft. Další informace najdete [v tématu Nastavení přímého přihlášení pomocí služby Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamické přizpůsobení uživatelského rozhraní

Azure AD B2C umožňuje předat parametry řetězce dotazu do koncových bodů definice obsahu HTML dynamicky vykreslovat obsah stránky. Tato funkce například umožňuje upravit image pozadí na stránce registrace nebo přihlášení Azure AD B2C na základě vlastního parametru, který předáte z webové nebo mobilní aplikace. Další informace najdete [v tématu Dynamicky konfigurace ui pomocí vlastních zásad v Azure Active Directory B2C](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri). Stránku HTML můžete také lokalizovat na základě parametru jazyka nebo můžete změnit obsah na základě ID klienta.

Následující příklad předá v parametru řetězce dotazu `Hawaii`s názvem **campaignId** s hodnotou , kód **jazyka** `en-US`a **aplikace** představující ID klienta:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

V důsledku toho Azure AD B2C odešle výše uvedené parametry na stránku obsahu HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Definice obsahu

V [ContentDefinition](contentdefinitions.md) `LoadUri`, můžete odeslat překládání deklarací pro vyžádat obsah z různých míst, na základě použitých parametrů.

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Technický profil Application Insights

Pomocí Azure Application Insights a překladačů deklarací můžete získat přehled o chování uživatelů. V technickém profilu Application Insights odesíláte vstupní deklarace identity, které jsou trvalé, do Azure Application Insights. Další informace najdete [v tématu Sledování chování uživatelů v cestách Azure AD B2C pomocí Application Insights](analytics-with-application-insights.md). Následující příklad odešle ID zásady, ID korelace, jazyk a ID klienta do Azure Application Insights.

```XML
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

V technickém profilu zásad [předávající strany](relyingparty.md) můžete chtít odeslat ID klienta nebo ID korelace do aplikace předávající strany v rámci JWT.

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
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
