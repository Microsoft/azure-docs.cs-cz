---
title: Sledování chování uživatele pomocí událostí ve službě Application Insights z Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak povolit protokoly událostí ve službě Application Insights z cesty uživatele Azure AD B2C s použitím vlastní zásady (preview).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d7097886b746c225bb420f9a96e2b7ef5c95c913
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684729"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Sledování chování uživatelů v Azure Active Directory B2C pomocí Application Insights

Při použití Azure Active Directory (Azure AD) B2C společně s Azure Application Insights můžete získat podrobné a vlastní protokoly událostí pro vaše uživatele cesty. V tomto článku získáte informace o těchto tématech:

* Získejte přehled o chování uživatele.
* Řešení potíží s vlastním zásad při vývoji nebo v produkčním prostředí.
* Měření výkonu.
* Vytvoření oznámení ze služby Application Insights.

> [!NOTE]
> Tato funkce je ve verzi Preview.

## <a name="how-it-works"></a>Jak to funguje

Architekturu rozhraní identit v Azure AD B2C obsahuje zprostředkovatele `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Odesílání dat události přímo do služby Application Insights s použitím Instrumentační klíč poskytuje Azure AD B2C.

Technický profil pomocí tohoto zprostředkovatele definuje události z Azure AD B2C. Profil, který určuje název události, deklarace identity, které se zaznamenávají a Instrumentační klíč. K odeslání události, technický profil se pak přidá jako `orchestration step`, nebo jako `validation technical profile` v cestě vlastní uživatele.

Application Insights můžete sjednotit událostí s použitím ID korelace pro vytvoření záznamu relace uživatele. Application Insights zpřístupňuje události a relace za několik sekund a nabízí mnoho vizualizací, export a analytických nástrojů.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md). Tento článek předpokládá, že používáte starter pack vlastní zásady. Ale starter pack není povinné.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Při použití služby Application Insights s Azure AD B2C, vše, co je třeba provést je vytvořit prostředek a získejte klíč instrumentace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje předplatné Azure kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje vaše předplatné. Tento tenant není vašeho tenanta Azure AD B2C.
3. Zvolte **vytvořit prostředek** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Application Insights**.
4. Klikněte na možnost **Vytvořit**.
5. Zadejte **název** pro prostředek.
6. Pro **typ aplikace**vyberte **webová aplikace ASP.NET**.
7. Pro **skupiny prostředků**, vyberte existující skupinu, nebo zadejte název pro novou skupinu.
8. Klikněte na možnost **Vytvořit**.
4. Po vytvoření prostředku Application Insights, otevřete ji, rozbalte položku **Essentials**a zkopírujte klíč instrumentace.

![Přehled služby Application Insights a instrumentačním klíčem](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Přidání nových definic typu deklarace identity

Otevřít *TrustFrameworkExtensions.xml* z této sady starter a přidejte následující prvky, které mají [BuildingBlocks](buildingblocks.md) element:

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

## <a name="add-new-technical-profiles"></a>Přidat nové technické profily

Technické profily lze považovat za funkce v Identity prostředí Framework z Azure AD B2C. Tato tabulka definuje technické profily, které slouží k otevření relace a odesílání událostí.

| Technický profil | Úkol |
| ----------------- | -----|
| AzureInsights-Common | Vytvoří společnou sadu parametrů, které mají být zahrnuty všechny AzureInsights technické profily. | 
| AzureInsights-SignInRequest | Vytvoří událost SignIn sadu deklarací identity, pokud byla přijata žádost o přihlášení. | 
| AzureInsights-UserSignup | Vytvoří UserSignup událost, když uživatel spustí možnost zápisu v cestě přihlášení-registrace/přihlášení. | 
| AzureInsights-SignInComplete | Zaznamenává úspěšné dokončení ověření, pokud token byl odeslán do aplikace předávající strany. | 

Přidání profilů do *TrustFrameworkExtensions.xml* soubor z starter pack. Přidejte tyto prvky, aby **ClaimsProviders** element:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
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

> [!IMPORTANT]
> Změnit klíč instrumentace do `ApplicationInsights-Common` technický profil na identifikátor GUID, který poskytuje prostředek Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Podle kroků Orchestrace přidejte technické profily

Volání `Azure-Insights-SignInRequest` jako Orchestrace krok 2 pro sledování, že znak v nebo odhlášením žádost byla přijata:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Okamžitě *před* `SendClaims` Orchestrace krokem je přidání nového kroku, který volá `Azure-Insights-UserSignup`. To se aktivuje, když uživatel vybere tlačítko registrace v cestě přihlášení-registrace/přihlášení.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
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
</OrchestrationStep>
```

Ihned po `SendClaims` krok Orchestrace, volání `Azure-Insights-SignInComplete`. Tento krok popisuje úspěšně dokončila. cestu.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po přidání nové kroky Orchestrace přečíslování kroky postupně bez přeskočí jakékoli celých čísel od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Nahrát soubor, spustit zásady a zobrazení událostí

Uložte a odešlete *TrustFrameworkExtensions.xml* souboru. Potom zavolejte předávající strana zásad z aplikace nebo použití **Run Now** na webu Azure Portal. Během několika sekund události jsou k dispozici ve službě Application Insights.

1. Otevřít **Application Insights** prostředků ve vašem tenantovi Azure Active Directory.
2. Vyberte **využití** > **události**.
3. Nastavte **během** k **za poslední hodinu** a **podle** k **3 minuty**.  Musíte vybrat **aktualizovat** zobrazíte výsledky.

![Události Application Insights využití-Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Další postup

Přidáte typy deklarací identity a události na vaší cestě uživatele podle vašich potřeb. Můžete použít [deklarace identity překladače](claim-resolver-overview.md) nebo libovolný řetězec, typ deklarace identity, přidání deklarací identity tak, že přidáte **vstupní** element události Application Insights nebo technickém profilu AzureInsights běžné. 

- **ClaimTypeReferenceId** se odkaz na typ deklarace identity.
- **PartnerClaimType** je název vlastnosti, které se zobrazí ve službě Azure Insights. Použití syntaxe `{property:NAME}`, kde `NAME` je vlastnost přidávaný k této události. 
- **Výchozí hodnota** použít libovolnou hodnotu řetězce nebo překladač deklarace identity. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

