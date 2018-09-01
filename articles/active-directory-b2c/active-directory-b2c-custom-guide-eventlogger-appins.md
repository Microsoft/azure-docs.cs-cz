---
title: Sledování chování uživatele pomocí událostí ve službě Application Insights z Azure Active Directory B2C | Dokumentace Microsoftu
description: Podrobný průvodce pro povolení protokolů událostí ve službě Application Insights z cesty uživatele Azure AD B2C pomocí vlastních zásady (preview)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c77feed3b86358c74f741b53aa03ecb454dc9a62
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337098"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Sledování chování uživatelů v centrech Azure AD B2C s využitím Application Insights

Azure Active Directory B2C (Azure AD B2C) pracuje s Azure Application Insights. Pro vaše uživatele vytvořit vlastní cesty poskytují podrobné a vlastní protokoly událostí. Tento článek ukazuje, jak začít pracovat, což vám umožní:

* Získejte přehled o chování uživatele.
* Řešení potíží s vlastním zásad při vývoji nebo v produkčním prostředí.
* Měření výkonu.
* Vytvoření oznámení ze služby Application Insights.

> [!NOTE]
> Tato funkce je ve verzi Preview.

## <a name="how-it-works"></a>Jak to funguje

Architekturu rozhraní identit v Azure AD B2C teď obsahuje zprostředkovatele `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Odesílání dat události přímo do služby Application Insights s použitím Instrumentační klíč poskytuje Azure AD B2C.

Technický profil používá k definování události z B2C tohoto zprostředkovatele.  Profil, který určuje název události, deklarace identity, které se budou zaznamenávat a Instrumentační klíč.  K odeslání události, technický profil se pak přidá jako `orchestration step` nebo stejně jako `validation technical profile` v cestě vlastní uživatele.

Application Insights můžete sjednotit událostí s použitím ID korelace pro vytvoření záznamu relace uživatele. Application Insights zpřístupňuje události a relace za několik sekund a nabízí mnoho vizualizací, export a analytických nástrojů.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md). Tento článek předpokládá, že používáte starter pack vlastní zásady. Ale starter pack není povinné.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Krok 1. Vytvořte prostředek Application Insights a získejte klíč instrumentace

Při použití služby Application Insights s Azure AD B2C, jediným požadavkem je k vytvoření prostředku a získání Instrumentační klíč. Vytvořit prostředek v [webu Azure portal.](https://portal.azure.com)

1. Na webu Azure Portal, v rámci vašeho předplatného tenanta, vyberte **+ vytvořit prostředek**. Tento tenant není vašeho tenanta Azure AD B2C.  
2. Vyhledejte a vyberte **Application Insights**.  
3. Vytvořit prostředek, který používá **webová aplikace ASP.NET** jako **typ aplikace**, v rámci předplatného dáváte přednost.
4. Po vytvoření prostředku Application Insights, otevřete ho a Všimněte si, že Instrumentační klíč.

![Přehled služby Application Insights a instrumentačním klíčem](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Krok 2. Přidejte nové definice typu deklarace identity do souboru rozšíření framework vztahu důvěryhodnosti

Otevřete soubor rozšíření z této sady starter a přidejte následující prvky, které mají `<BuildingBlocks>` uzlu. Obvykle je název souboru `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Krok 3. Přidat nové technické profily, které používají poskytovatele služby Application Insights

Technické profily lze považovat za funkce v Identity prostředí Framework z Azure AD B2C. Tento příklad definuje pět technické profily otevřete relaci a odesílat události:

| Technický profil | Úkol |
| ----------------- | -----|
| AzureInsights-Common | Vytvoří společnou sadu parametrů, které mají být zahrnuty ve všech profilech technické AzureInsights | 
| JourneyContextForInsights | Otevře se relace ve službě Application Insights a odesílá ID korelace |
| AzureInsights-SignInRequest | Vytvoří `SignIn` událost s sadu deklarací identity, pokud byla přijata žádost o přihlášení | 
| AzureInsights-UserSignup | Vytvoří UserSignup událost, když uživatel spustí možnost zápisu v cestě přihlášení-registrace/přihlášení | 
| AzureInsights-SignInComplete | Zaznamenává úspěšné dokončení ověření, pokud token byl odeslán do aplikace předávající strany | 

Soubor rozšíření z této sady starter přidáním těchto prvků můžete přidat profily `<ClaimsProviders>` uzlu.  Obvykle je název souboru `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Změnit klíč instrumentace do `ApplicationInsights-Common` technický profil na identifikátor GUID, který poskytuje prostředek Application Insights.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Krok 4. Přidejte technické profily pro službu Application Insights Orchestrace kroky v existující cesty uživatele

Volání `JournyeContextForInsights` jako Orchestrace krok 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Volání `Azure-Insights-SignInRequest` jako Orchestrace krok 2 pro sledování, že znak v nebo odhlášením žádost byla přijata:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Okamžitě *před* `SendClaims` Orchestrace krokem je přidání nového kroku, který volá `Azure-Insights-UserSignup`. To se aktivuje, když uživatel vybere tlačítko registrace v cestě přihlášení-registrace/přihlášení.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

Ihned po `SendClaims` krok Orchestrace, volání `Azure-Insights-SignInComplete`. Tento krok zahrnuje úspěšně dokončila. cestu.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po přidání nové kroky Orchestrace přečíslování kroky postupně bez přeskočí jakékoli celých čísel od 1 do N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Krok 5. Nahrajte upravenou přípony souborů, spuštění zásady a zobrazení událostí ve službě Application Insights

Uložit a nahrajte nový soubor rozšíření framework vztah důvěryhodnosti. Potom zavolejte předávající strana zásad z aplikace nebo použití `Run Now` v rozhraní Azure AD B2C. Během několika sekund události jsou k dispozici ve službě Application Insights.

1. Otevřít **Application Insights** prostředků ve vašem tenantovi Azure Active Directory.
2. Vyberte **využití** > **události**.
3. Nastavte **během** k **za poslední hodinu** a **podle** k **3 minuty**.  Musíte vybrat **aktualizovat** zobrazíte výsledky.

![Události Application Insights využití-Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Další postup

Přidáte typy deklarací identity a události na vaší cestě uživatele podle vašich potřeb. Tady je seznam možných deklarací identity, použití překladačů další deklarace identity

### <a name="culture-specific-claims"></a>Deklarace identity specifické pro jazykovou verzi

```xml
Referenced using: {Culture:One of the property names below}
```

| Deklarovat | Definice | Příklad: |
| ----- | -----------| --------|
| LanguageName | Dvě písmena kód ISO pro jazyk | en |
| RegionName | Dvě písmena kód ISO pro oblast | USA |
| RFC5646 | Kód jazyka RFC5646 | cs-CZ |
| LCID   | LCID kód jazyka | 5 |

### <a name="policy-specific-claims"></a>Deklarace identity specifické pro zásady

```xml
Referenced using {Policy:One of the property names below}
```

| Deklarovat | Definice | Příklad: |
| ----- | -----------| --------|
| TrustFrameworkTenantId | Id tenanta trustframework | neuvedeno |
| RelyingPartyTenantId | Id tenanta předávající strany | neuvedeno |
| PolicyId | Id zásad zásad | neuvedeno |
| TenantObjectId | Id objektu tenanta zásad | neuvedeno |

### <a name="openid-connect-specific-claims"></a>Deklarace identity specifické pro OpenID Connect

```xml
Referenced using {OIDC:One of the property names below}
```

| Deklarovat | Parametr OpenIdConnect | Příklad: |
| ----- | ----------------------- | --------|
| řádek | řádek | neuvedeno |
| LoginHint |  login_hint | neuvedeno |
| DomainHint | domain_hint | neuvedeno |
|  MaxAge | max_age | neuvedeno |
| ID klienta | client_id | neuvedeno |
| Uživatelské jméno | login_hint | neuvedeno |
|  Prostředek | prostředek| neuvedeno |
| AuthenticationContextReferences | acr_values | neuvedeno |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parametry bez protokolu zahrnout OIDC & OAuth2 s požadavky

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Libovolný název parametru jako součást požadavku OIDC nebo OAuth2 lze mapovat na deklaraci identity v cestě uživatele. Potom ji můžete zaznamenat události. Žádost z aplikace může například obsahovat parametr řetězce dotazu s názvem `app_session`, `loyalty_number` nebo `any_string`.

Tady je ukázková žádost z aplikace:

```
https://sampletenant.b2clogin.com/tfp/sampletenant.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Poté můžete přidat deklarace identity tak, že přidáte `Input Claim` element události Application Insights. Vlastnosti události jsou přidány pomocí syntaxe {název: vlastnosti}, jehož název je vlastnost přidávaný do události. Příklad:

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Další deklarace identity systému

Některé deklarace identity systému musí být přidaný do kontejneru deklarace identity jsou k dispozici pro záznam událostí. Technický profil `SimpleUJContext` musí být volána jako jeden krok Orchestrace nebo technickém profilu ověření před tyto deklarace jsou k dispozici.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


