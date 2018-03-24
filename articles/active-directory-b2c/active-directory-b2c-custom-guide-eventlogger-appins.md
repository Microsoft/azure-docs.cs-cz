---
title: Tom, jak sledovat chování uživatele pomocí událostí ve službě Application Insights z Azure AD B2C | Microsoft Docs
description: Podrobný návod, jak povolit protokoly událostí ve službě Application Insights z cesty uživatele Azure AD B2C, používat vlastní zásady - PREVIEW
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 3/15/2018
ms.author: davidmu
ms.openlocfilehash: 1e8c4a53266072db71952ee35b15e5de54fabb95
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="tracking-user-behavior-inside-azure-ad-b2c-journeys-using-application-insights"></a>Sledování chování uživatele v Azure AD B2C cesty pomocí Application Insights

Azure Active Directory B2C pracuje s Azure Application Insights.  Obsahují podrobnější a vlastní protokoly událostí pro vaše vlastní vytvořeného uživatele cesty.  Tato příručka ukazuje, jak začít pracovat, abyste mohli: 
* získáte přehled o chování uživatele
* řešení potíží se zásadami, vlastní vývoj nebo v produkčním prostředí
* míra výkonu
* Vytvoření oznámení z Application Insights

## <a name="how-it-works"></a>Jak to funguje
Nový zprostředkovatel byl přidán do Azure AD B2C Identity rozhraní Framework: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  Odešle data události přímo pomocí zadané do Azure AD B2C klíč instrumentace Application Insights.  Profil technické používá k definování událost na základě B2C tohoto zprostředkovatele.  Profil určuje název události, deklarace identity, které budou zaznamenány a klíč instrumentace.  Odeslat událost, technické profil pak přidat, protože se `orchestration step` nebo jako `validation technical profile` vlastní uživatelské cesty.  Události můžete unified pomocí Application Insights pomocí ID korelace k zaznamenání relace uživatele.  Application Insights zpřístupní události a relace během několika sekund a uvede mnoho vizualizace, export a analytické nástroje.



## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md).  Tato příručka předpokládá, že je použit úvodní sada vlastní zásady.  Ale není nutné.



## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>Krok 1. Vytvořte prostředek Application Insights a získat klíč instrumentace

Application Insights je výkonný nástroj. Při používání s Azure AD B2C, jediným požadavkem je vytvoření prostředku a získání kód instrumentace.  Application Insights musí být vytvořen v [portálu Azure.](https://portal.azure.com)

[Úplnou dokumentaci pro službu Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Klikněte na `+ Create a resource` na portálu Azure v rámci vašeho předplatného klienta.  Tohoto klienta není vašeho klienta Azure AD B2C.  
2. Vyhledejte a vyberte `Application Insights`  
3. Vytvořte prostředek z `Application Type` `ASP.NET web application` v rámci předplatného vaši volbu.
4. Po vytvoření otevřete prostředek Application Insights a Poznámka  `Instrumentation Key` 

![Přehled Application Insights a klíč instrumentace](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>Krok 2. Přidat nové definice typ ClaimType do souboru rozšíření důvěřovat Framework

### <a name="open-the-extension-file-from-the-starter-pack-and-add-the-following-elements-to-the-buildingblocks-node--the-extensions-filename-is-typically-yourtenantonmicrosoftcom-b2c1atrustframeworkextensionsxml"></a>Otevřete soubor rozšíření z balíčku starter a přidejte následující prvky na `<BuildingBlocks>` uzlu.  Název souboru rozšíření je obvykle `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

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

Technické profily lze považovat za funkcí v Azure AD B2C Identity rozhraní Framework.  Pět technické profily jsou definované v tomto příkladu otevřete relaci a odesílat události.

| Technické profilu       | Úkol |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | společné sady parametrů, které mají být zahrnuty ve všech profilech technické Statistika Azure     | 
| JourneyContextForInsights   | Otevře relaci v aplikaci přehled a odešle ID korelace |
| AzureInsights-SignInRequest     | vytvoří "Přihlášení" události s sadu deklarací identity, pokud byla přijata žádost o přihlášení      | 
| AzureInsights-UserSignup | vytváří událost volána "UserSignup", když se uživatel cesty registrace nebo přihlášení byla aktivována možnost registrace     | 
| AzureInsights-SignInComplete | zaznamenává úspěšné dokončení ověření, pokud token byl odeslán do aplikace předávající strany     | 

Přidejte profily do souboru rozšíření z balíčku starter přidáním tyto prvky, aby `<ClaimsProviders>` uzlu.  Název souboru rozšíření je obvykle `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Změna `Instrumentation Key` v `ApplicationInsights-Common` technické profilu na identifikátor GUID poskytované prostředku Application Insights.

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
            <!-- 
                            An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
                            to create an event with the specified value.
                        -->
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
            <!-- 
                            A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
                            with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
                        -->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
                            A boolean indicating whether telemetry should be enabled or not.
                        -->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
                            Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
                            to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
                        -->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>Krok 4. Přidejte technické profily pro službu Application Insights jako orchestration kroky existující cesty uživatele.

Volání `JournyeContextForInsights` jako orchestration krok 1

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Volání `Azure-Insights-SignInRequest` jako orchestration krok 2 pro sledování, zda přihlašovací v nebo registrace-množství žádost byla přijata.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Okamžitě **před** `SendClaims` orchestration kroku, přidejte nový krok, který volá `Azure-Insights-UserSignup`. Spuštění při kliknutí na tlačítko registrace cesty registrace nebo přihlášení.

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

Ihned po `SendClaims` krok orchestration, volání `Azure-Insights-SignInComplete`.   Tento krok bude odrážet úspěšně dokončila cesty.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> Po přidání nové kroků Orchestrace, přečíslování kroky postupně bez přeskočení žádné celá čísla od 1 do N


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>Krok 5. Nahrát soubor upravené rozšíření, spusťte zásady a zobrazení událostí ve službě Application Insights

Uložte a nahrát nový soubor rozšíření framework vztah důvěryhodnosti.  Potom zavolejte předávající strana zásady z vaší aplikace nebo použití `Run Now` v rozhraní Azure AD B2C.  Během několika sekund bude k dispozici ve službě Application Insights události.

1. Otevřete prostředek Application Insights v klientovi služby Azure Active Directory.
2. Klikněte na `Events` v `USAGE` podnabídky.
3. Nastavit `During` k `Last hour` a `By` k `3 minutes`.  Je třeba kliknout na `Refresh` zobrazení výsledků

![Statistika využití události aplikace Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  <a name="next-steps"></a>DALŠÍ KROKY

Přidáte další typy a události do vašeho cesty uživatele podle vašich potřeb.  Tady je seznam možných deklarací identity pomocí překladače další deklarace identity.

### <a name="culture-specific-claims"></a>Deklarace identity specifické pro jazykovou verzi

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### <a name="policy-specific-claims"></a>Deklarace identity specifické pro zásady

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### <a name="openidconnect-specific-claims"></a>Deklarace identity specifické pro OpenIDConnect

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>Parametry protokolu není součástí OIDC & OAuth2 požadavky

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Libovolný název parametru jsou součástí požadavek OIDC nebo OAuth2 lze mapovat na deklaraci identity v cesty uživatele.  Můžete se pak zaznamenávat v události. Žádosti z aplikace může obsahovat třeba parametr řetězce dotazu s názvem `app_session`, `loyalty_number` nebo `any_string`.

Ukázková žádost z aplikace:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Deklarace identity lze přidat pomocí události Application Insights přidáním element vstupní deklarace identity:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
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



