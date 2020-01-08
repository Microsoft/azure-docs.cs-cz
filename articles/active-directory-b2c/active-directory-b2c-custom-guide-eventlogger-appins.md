---
title: Sledovat chování uživatele pomocí Application Insights
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit protokoly událostí v Application Insights z Azure AD B2Cch cest uživatelů pomocí vlastních zásad.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8376deecb5e184c01b41495b868b57bd8fd745d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367956"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Sledovat chování uživatele v Azure Active Directory B2C pomocí Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Pokud používáte Azure Active Directory B2C (Azure AD B2C) společně s Azure Application Insights, můžete získat podrobné a přizpůsobené protokoly událostí pro vaše cesty uživatelů. V tomto článku získáte informace o těchto tématech:

* Získejte přehled o chování uživatelů.
* Řešení potíží s vlastními zásadami ve vývoji nebo v produkčním prostředí.
* Změřte výkon.
* Vytvoří oznámení z Application Insights.

## <a name="how-it-works"></a>Jak to funguje

Architektura prostředí identit v Azure AD B2C zahrnuje `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`poskytovatele. Odesílá data události přímo do Application Insights pomocí klíče instrumentace, který je k dispozici Azure AD B2C.

Technický profil používá tohoto poskytovatele k definování události z Azure AD B2C. Profil určuje název události, zaznamenané deklarace a klíč instrumentace. K odeslání události se technický profil přidá jako `orchestration step` ve vlastní cestě uživatele.

Application Insights může sjednotit události pomocí ID korelace pro záznam uživatelské relace. Application Insights zpřístupňuje událost a relaci během několika sekund a prezentuje mnoho nástrojů pro vizualizaci, export a analýzu.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](active-directory-b2c-get-started-custom.md). V tomto článku se předpokládá, že používáte Startovní sadu Custom Policy. Úvodní sada ale není povinná.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Pokud používáte Application Insights s Azure AD B2C, stačí vytvořit prostředek a získat klíč instrumentace.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a vyberete adresář, který obsahuje vaše předplatné. Tento tenant není vaším klientem Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** a pak vyhledejte a vyberte **Application Insights**.
4. Klikněte na **Vytvořit**.
5. Zadejte **název** prostředku.
6. Jako **Typ aplikace**vyberte **ASP.NET webová aplikace**.
7. V případě **skupiny prostředků**vyberte existující skupinu nebo zadejte název nové skupiny.
8. Klikněte na **Vytvořit**.
4. Po vytvoření prostředku Application Insights otevřete ho, rozbalte **základy**a zkopírujte klíč instrumentace.

![Přehled Application Insights a klíč instrumentace](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Přidat nové definice ClaimType

Otevřete soubor *TrustFrameworkExtensions. XML* z úvodní sady a přidejte následující prvky do elementu [BuildingBlocks](buildingblocks.md) :

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

Technické profily lze považovat za funkce v architektuře prostředí identity Azure AD B2C. Tato tabulka definuje technické profily, které se používají k otevření relace a odeslání událostí.

| Technický profil | Úkol |
| ----------------- | -----|
| AzureInsights-Common | Vytvoří společnou sadu parametrů, které se mají zahrnout do všech technických profilů AzureInsights. |
| AzureInsights-SignInRequest | Vytvoří událost přihlášení se sadou deklarací při přijetí žádosti o přihlášení. |
| AzureInsights-UserSignup | Vytvoří událost UserSignup, když uživatel aktivuje možnost registrace v cestě k registraci nebo přihlašování. |
| AzureInsights-SignInComplete | Zaznamenává úspěšné dokončení ověření při odeslání tokenu do aplikace předávající strany. |

Přidejte profily do souboru *TrustFrameworkExtensions. XML* z úvodní sady. Přidejte tyto prvky do prvku **ClaimsProviders** :

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
> Změňte klíč instrumentace v `AzureInsights-Common` Technical profil na identifikátor GUID, který poskytuje váš prostředek Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Přidání technických profilů jako kroků orchestrace

Pokud chcete sledovat, že byla přijata žádost o přihlášení nebo přihlášení, zavolejte `Azure-Insights-SignInRequest` jako orchestrace 2. krok.

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bezprostředně *před* krokem `SendClaims` orchestrace přidejte nový krok, který volá `Azure-Insights-UserSignup`. Aktivuje se, když uživatel vybere tlačítko pro registraci v cestě k registraci nebo přihlašování.

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

Hned po kroku `SendClaims` orchestrace zavolejte `Azure-Insights-SignInComplete`. V tomto kroku se zobrazuje úspěšně dokončená cesta.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po přidání nových kroků orchestrace předávejte kroky postupně, aniž by bylo vynecháno celé číslo od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Nahrání souboru, spuštění zásad a zobrazení událostí

Uložte a nahrajte soubor *TrustFrameworkExtensions. XML* . Pak zavolejte zásadu předávající strany z vaší aplikace nebo použijte **Spustit nyní** v Azure Portal. V sekundách jsou vaše události k dispozici v Application Insights.

1. Otevřete prostředek **Application Insights** ve vašem tenantovi Azure Active Directory.
2. Vyberte > **události** **využití** .
3. Nastaví se během **poslední hodiny** a **do** **3 minut**.  Pro zobrazení výsledků může být nutné vybrat možnost **aktualizovat** .

![VYUŽITÍ Application Insights – události Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Další kroky

Přidejte do cesty uživatele typy deklarací identity a události, aby vyhovovaly vašim potřebám. Můžete použít [překladače deklarací identity](claim-resolver-overview.md) nebo jakýkoli typ deklarace identity, přidat deklarace identity přidáním prvku **vstupní deklarace identity** do události Application Insights nebo do AzureInsights-Common Technical Profile.

- **ClaimTypeReferenceId** je odkaz na typ deklarace identity.
- **PartnerClaimType** je název vlastnosti, která se zobrazuje v Azure Insights. Použijte syntaxi `{property:NAME}`, kde `NAME` je přidána vlastnost do události.
- **DefaultValue** používá libovolnou řetězcovou hodnotu nebo překladač deklarací identity.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

