---
title: Sledovat chování uživatele pomocí událostí ve službě Application Insights z Azure Active Directory B2C | Microsoft Docs
description: Podrobný průvodce pro povolení protokoly událostí ve službě Application Insights z cesty uživatele Azure AD B2C pomocí vlastních zásad (preview)
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 94d96af8db651a848ac092d1f8b85da4909427b7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110111"
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Sledovat chování uživatele v Azure AD B2C cesty pomocí Application Insights

Azure Active Directory B2C (Azure AD B2C) pracuje s Azure Application Insights. Obsahují podrobnější a vlastní protokoly událostí pro vaše uživatele vytvořit vlastní cesty. Tento článek ukazuje, jak začít pracovat, abyste mohli:

* Získáte přehled o chování uživatele.
* Řešení potíží se zásadami, vlastní vývoj nebo v produkčním prostředí.
* Měření výkonu.
* Vytvoření oznámení z Application Insights.

> [!NOTE]
> Tato funkce je ve verzi Preview.

## <a name="how-it-works"></a>Jak to funguje

Rozhraní Identity prostředí v Azure AD B2C nyní zahrnuje zprostředkovatele `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Odešle data události přímo do služby Application Insights pomocí klíč instrumentace poskytované Azure AD B2C.

Profil technické používá k definování událost na základě B2C tohoto zprostředkovatele.  Profil určuje název události, deklarace identity, které budou zaznamenány a klíč instrumentace.  Odeslat událost, se pak přidá technické profil jako `orchestration step` nebo jako `validation technical profile` vlastní uživatelské cesty.

Application Insights můžete sjednotit události pomocí ID korelace k zaznamenání relace uživatele. Application Insights zpřístupní události a relace během několika sekund a uvede mnoho vizualizace, export a analytické nástroje.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md). Tento článek předpokládá, že používáte sadu starter vlastní zásady. Ale starter pack není povinné.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Krok 1. Vytvořte prostředek Application Insights a získat klíč instrumentace

Když používáte Application Insights s Azure AD B2C, jediným požadavkem je vytvoření prostředku a získání kód instrumentace. Vytvořit prostředek v [portálu Azure.](https://portal.azure.com)

1. Na portálu Azure v rámci vašeho předplatného klienta, vyberte **+ vytvořit prostředek**. Tohoto klienta není vašeho klienta Azure AD B2C.  
2. Vyhledejte a vyberte **Application Insights**.  
3. Vytvořit prostředek, který používá **webové aplikace ASP.NET** jako **typ aplikace**, v rámci předplatného vaši volbu.
4. Po vytvoření prostředku Application Insights, otevřete ho a poznamenejte si klíč instrumentace.

![Přehled Application Insights a klíč instrumentace](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Krok 2. Přidat nové definice typ ClaimType do souboru rozšíření framework vztahu důvěryhodnosti

Otevřete soubor rozšíření z balíčku starter a přidejte následující prvky na `<BuildingBlocks>` uzlu. Obvykle je název souboru `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>Krok 3. Přidejte nové technické profily, které používají zprostředkovatele Application Insights

Technické profily lze považovat za funkcí v Identity rozhraní Framework z Azure AD B2C. Tento příklad definuje pět profilů technické otevřete relaci a odesílají události:

| Technické profilu | Úkol |
| ----------------- | -----|
| AzureInsights-Common | Vytvoří společnou sadu parametrů, které mají být zahrnuty ve všech profilech technické AzureInsights | 
| JourneyContextForInsights | Otevře relaci ve službě Application Insights a odešle ID korelace |
| AzureInsights-SignInRequest | Vytvoří `SignIn` událost s sadu deklarací identity, pokud byla přijata žádost o přihlášení | 
| AzureInsights-UserSignup | Vytvoří UserSignup událost, když uživatel spustí registrace možnost cesty registrace-množství nebo přihlášení | 
| AzureInsights-SignInComplete | Zaznamenává úspěšné dokončení ověření, pokud token byl odeslán do aplikace předávající strany | 

Přidejte profily do souboru rozšíření z balíčku starter přidáním tyto prvky, aby `<ClaimsProviders>` uzlu.  Obvykle je název souboru `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Změnit klíč instrumentace do `ApplicationInsights-Common` technické profilu na identifikátor GUID, který poskytuje prostředku Application Insights.

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

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Krok 4. Přidejte technické profily pro službu Application Insights jako orchestration kroky existující cesty uživatele

Volání `JournyeContextForInsights` jako orchestration krok 1:

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Volání `Azure-Insights-SignInRequest` jako orchestration krok 2 pro sledování, zda přihlašovací v nebo registrace-množství žádost byla přijata:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Okamžitě *před* `SendClaims` orchestration kroku, přidejte nový krok, který volá `Azure-Insights-UserSignup`. Ho se aktivuje, když uživatel vybere tlačítko registrace cesty registrace-množství nebo přihlášení.

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

Ihned po `SendClaims` krok orchestration, volání `Azure-Insights-SignInComplete`. Tento krok odráží úspěšně dokončila cesty.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po přidání nové kroky orchestration přečíslování kroky postupně bez přeskočení žádné celá čísla od 1 do N.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Krok 5. Nahrát soubor upravené rozšíření, spusťte zásady a zobrazení událostí ve službě Application Insights

Uložte a nahrát nový soubor rozšíření framework vztah důvěryhodnosti. Potom zavolejte předávající strany zásady z vaší aplikace nebo použití `Run Now` v rozhraní Azure AD B2C. V sekundách jsou události dostupné ve službě Application Insights.

1. Otevřete **Application Insights** prostředku v klientovi služby Azure Active Directory.
2. Vyberte **využití** > **události**.
3. Nastavit **během** k **poslední hodinu** a **podle** k **3 minut**.  Možná budete muset vybrat možnost **aktualizovat** Chcete-li zobrazit výsledky.

![Statistika využití události aplikace Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>Další postup

Přidáte typy deklarací identity a události na váš cesty uživatele podle vašich potřeb. Tady je seznam možných deklarace identity pomocí překladače další deklarace identity

### <a name="culture-specific-claims"></a>Deklarace identity specifické pro jazykovou verzi

```xml
Referenced using: {Culture:One of the property names below}
```

| Deklarovat | Definice | Příklad: |
| ----- | -----------| --------|
| LanguageName | Dva písmeno ISO kódu pro jazyk | en |
| RegionName | Dva písmeno kód ISO pro oblast | USA |
| RFC5646 | Kód jazyka RFC5646 | cs-CZ |
| LCID   | Identifikátor LCID kód jazyka | 5 |

### <a name="policy-specific-claims"></a>Deklarace identity specifické pro zásady

```xml
Referenced using {Policy:One of the property names below}
```

| Deklarovat | Definice | Příklad: |
| ----- | -----------| --------|
| TrustFrameworkTenantId | Id klienta trustframework | neuvedeno |
| RelyingPartyTenantId | Id klienta předávající strany | neuvedeno |
| PolicyId | Id zásady zásad | neuvedeno |
| TenantObjectId | Id objektu klienta zásad | neuvedeno |

### <a name="openid-connect-specific-claims"></a>Deklarace identity specifické pro OpenID Connect

```xml
Referenced using {OIDC:One of the property names below}
```

| Deklarovat | Parametr OpenIdConnect | Příklad: |
| ----- | ----------------------- | --------|
| Výzva | Výzva | neuvedeno |
| LoginHint |  login_hint | neuvedeno |
| DomainHint | domain_hint | neuvedeno |
|  MaxAge | max_age | neuvedeno |
| clientId | client_id | neuvedeno |
| Uživatelské jméno | login_hint | neuvedeno |
|  Prostředek | prostředek| neuvedeno |
| AuthenticationContextReferences | acr_values | neuvedeno |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parametry protokolu není součástí OIDC & OAuth2 požadavky

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Libovolný název parametru jsou součástí požadavek OIDC nebo OAuth2 lze mapovat na deklaraci identity v cesty uživatele. Potom ji můžete zaznamenat události. Žádosti z aplikace může například obsahovat parametr řetězce dotazu s názvem `app_session`, `loyalty_number` nebo `any_string`.

Zde je ukázka požadavku z aplikace:

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Poté můžete přidat deklarací identity tak, že přidáte `Input Claim` element události Application Insights. Vlastnosti události jsou přidány prostřednictvím syntaxe {vlastnost: NAME}, kde název vlastnosti se přidává do událost. Příklad:

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>Další deklarace identity systému

Některé systému deklarace identity musí být přidán do kontejneru deklarace identity, než jsou k dispozici pro záznam jako události. Technické profil `SimpleUJContext` před tyto deklarace identity jsou k dispozici, musí být volána jako na krok orchestration nebo technické profil ověření.

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


