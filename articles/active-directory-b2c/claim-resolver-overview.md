---
title: Informace o deklaraci identity překladače ve vlastních zásad Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o použití překladačů deklarace identity ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/08/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dccb597cda1f5aba30d18b0f71371caa6ceee9b4
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852374"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>O překladače deklarace identity v Azure Active Directory B2C vlastních zásad

Deklarace identity v Azure Active Directory (Azure AD) B2C překladače [vlastní zásady](active-directory-b2c-overview-custom.md) poskytují kontextové informace o žádost o autorizaci, jako název zásady, ID korelace žádosti, jazyk uživatelského rozhraní a další.

Použití překladače deklarace identity ve vstupních nebo výstupních deklarací, definujte řetězec **typu deklarace identity**v části [ClaimsSchema](claimsschema.md) prvek a potom nastavíte **DefaultValue** k deklaraci překladač ve vstupní nebo výstupní deklarace elementu. Azure AD B2C přečte hodnotu deklarace identity překladače a použije se hodnota v technickém profilu. 

V následujícím příkladu typ deklarace identity s názvem `correlationId` je definována s **datový typ** z `string`.  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

V technickém profilu mapování překladače deklarací na typ deklarace identity. Azure AD B2C, naplní hodnoty deklarace identity překladač `{Context:CorrelationId}` do deklarace identity `correlationId` a odešle žádost technický profil.

```XML
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Překladač typů deklarací identity

Překladače deklarace identity k dispozici v následujících částech.

### <a name="culture"></a>Jazyková verze

| Deklarovat | Popis | Příklad: |
| ----- | ----------- | --------|
| {Jazyková verze: LanguageName} | Dvě písmena kód ISO pro jazyk. | en |
| {Jazyková verze: LCID}   | LCID kód jazyka. | 5 |
| {Jazyková verze: RegionName} | Dvě písmena kód ISO pro danou oblast. | USA |
| {Jazyková verze: RFC5646} | Kód jazyka RFC5646. | cs-CZ |

### <a name="policy"></a>Zásada

| Deklarovat | Popis | Příklad: |
| ----- | ----------- | --------|
| {Zásad: PolicyId} | Název zásad přijímající strany. | B2C_1A_signup_signin |
| {Zásad: RelyingPartyTenantId} | ID tenanta předávající strana zásad. | vaše tenant.onmicrosoft.com |
| {Zásad: TenantObjectId} | ID objektu tenanta předávající strana zásad. | 00000000-0000-0000-0000-000000000000 |
| {Zásad: TrustFrameworkTenantId} | ID tenanta Framework vztah důvěryhodnosti. | vaše tenant.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Deklarovat | Popis | Příklad: |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |`acr_values` Parametr řetězce dotazu. | neuvedeno |
| {OIDC:ClientId} |`client_id` Parametr řetězce dotazu. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |`domain_hint` Parametr řetězce dotazu. | Facebook.com |
| {OIDC:LoginHint} |  `login_hint` Parametr řetězce dotazu. | someone@contoso.com |
| {OIDC:MaxAge} | `max_age`. | neuvedeno |
| {OIDC:Nonce} |`Nonce` Parametr řetězce dotazu. | defaultNonce |
| {OIDC:Prompt} | `prompt` Parametr řetězce dotazu. | přihlášení |
| {OIDC:Resource} |`resource` Parametr řetězce dotazu. | neuvedeno |
| {OIDC:scope} |`scope` Parametr řetězce dotazu. | openid |

### <a name="context"></a>Kontext

| Deklarovat | Popis | Příklad: |
| ----- | ----------- | --------|
| {Kontextu: ČísloSestavení} | Verze rozhraní prostředí pro Identity (číslo sestavení).  | 1.0.507.0 |
| {Kontextu: CorrelationId} | ID korelace.  | 00000000-0000-0000-0000-000000000000 |
| {Kontextu: DateTimeInUtc} |Datum čas ve standardu UTC.  | 10/10/2018 12:00:00 PM |
| {Kontextu: DeploymentMode} |Režim nasazení zásady.  | Výroba |
| {Kontextu: IPAddress} | IP adresy uživatele. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Parametry bez protokolu

Libovolný název parametru jako součást požadavku OIDC nebo OAuth2 lze mapovat na deklaraci identity v cestě uživatele. Žádost z aplikace může například obsahovat parametr řetězce dotazu s názvem `app_session`, `loyalty_number`, nebo všechny vlastní řetězec dotazu.

| Deklarovat | Popis | Příklad: |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Parametr řetězce dotazu. | Havajské ostrovy |
| {OAUTH-KV:app_session} | Parametr řetězce dotazu. | A3C5R |
| {OAUTH-KV:loyalty_number} | Parametr řetězce dotazu. | 1234 |
| {OAUTH KV: všechny vlastní řetězec dotazu} | Parametr řetězce dotazu. | neuvedeno |


## <a name="how-to-use-claim-resolvers"></a>Použití překladačů deklarace identity

### <a name="restful-technical-profile"></a>Technický profil rESTful

V [RESTful](restful-technical-profile.md) technický profil, můžete chtít odeslat jazyk uživatele, název zásady, oboru a ID klienta. Na základě těchto deklarací rozhraní REST API můžete spouštět vlastní obchodní logiky a v případě potřeby vyvolat lokalizované chybové zprávy. 

Následující příklad ukazuje RESTful technický profil:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Přímé přihlášení

Použití překladačů deklarace identity, můžete předem přihlašovacího jména nebo přímé přihlášení k poskytovateli konkrétní sociálních identit, jako je Facebook, LinkedIn nebo účtem Microsoft. Další informace najdete v tématu [nastavit přímé přihlášení pomocí Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamické přizpůsobení uživatelského rozhraní

Azure AD B2C umožňuje předat parametry řetězce dotazu do koncových bodů definici obsahu HTML tak, aby se může dynamicky vykreslit obsah stránky. Můžete například změnit obrázek pozadí na Azure AD B2C registrace nebo přihlášení stránku založenou na vlastní parametr předat z vašich webových nebo mobilních aplikací. Další informace najdete v tématu [dynamicky konfigurovat uživatelského rozhraní pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Také je možné lokalizovat stránce HTML na základě parametru jazyk, nebo můžete změnit obsah na základě ID klienta.

Následující příklad předá v řetězci dotazu parametr s názvem **campaignId** s hodnotou `hawaii`, **jazyk** kód `en-US`, a **aplikace** představující ID klienta:

```XML
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

V důsledku Azure AD B2C pošle výše uvedených parametrů na stránce obsahu HTML:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="application-insights-technical-profile"></a>Technický profil Application Insights

Azure Application Insights a deklarace identity překladače umožňují získat přehledy o chování uživatelů. V technickém profilu Application Insights odešlete do služby Azure Application Insights vstupních deklarací identity, které jsou trvalé. Další informace najdete v tématu [jízdy sledovat chování uživatelů v Azure AD B2C s využitím Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). Následující příklad odesílá ID zásad, ID korelace, jazyka a ID klienta do služby Azure Application Insights.

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
