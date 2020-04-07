---
title: Sledování chování uživatelů pomocí přehledů aplikací
titleSuffix: Azure AD B2C
description: Zjistěte, jak povolit protokoly událostí v Application Insights z cest uživatelů Azure AD B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25e62e7c6865f91daa242a33a0f491f8015be41a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672523"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Sledování chování uživatelů ve službě Azure Active Directory B2C pomocí přehledů aplikací

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) podporuje odesílání dat událostí přímo do [Application Insights](../azure-monitor/app/app-insights-overview.md) pomocí instrumentační klíč poskytované Azure AD B2C.  Pomocí technického profilu Application Insights můžete získat podrobné a přizpůsobené protokoly událostí pro vaše cesty uživatelů:

* Získejte přehled o chování uživatelů.
* Poradce při potížích s vlastními zásadami ve vývoji nebo v produkčním prostředí.
* Měření výkonu.
* Vytvořte oznámení z Application Insights.

## <a name="how-it-works"></a>Jak to funguje

Technický profil [Application Insights](application-insights-technical-profile.md) definuje událost z Azure AD B2C. Profil určuje název události, deklarace, které jsou zaznamenány a klíč instrumentace. Chcete-li událost zveřejnit, technický profil je pak přidán jako krok orchestrace v [cestě uživatele](userjourneys.md).

Application Insights můžete sjednotit události pomocí ID korelace pro záznam relace uživatele. Application Insights zpřístupní událost a relaci během několika sekund a představuje mnoho vizualizačních, exportních a analytických nástrojů.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí místních účtů.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Když používáte Application Insights s Azure AD B2C, stačí vytvořit prostředek a získat klíč instrumentace. Další informace naleznete [v tématu Vytvoření prostředku Application Insights.](../azure-monitor/app/create-new-resource.md)

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure výběrem **directory + předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vaše předplatné. Tento tenant není váš klient Azure AD B2C.
3. V levém horním rohu **portálu** Azure zvolte Vytvořit prostředek a pak vyhledejte a vyberte **Application Insights**.
4. Klikněte na **Vytvořit**.
5. Zadejte **název** zdroje.
6. V **případě Typ aplikace**vyberte ASP.NET **webovou aplikaci**.
7. V **části Skupina prostředků**vyberte existující skupinu nebo zadejte název nové skupiny.
8. Klikněte na **Vytvořit**.
4. Po vytvoření prostředku Application Insights jej otevřete, rozbalte **Essentials**a zkopírujte klíč instrumentace.

![Přehled aplikací a klíč instrumentace](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definovat deklarace identity

Deklarace poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. [Schéma nároků](claimsschema.md) je místo, kde deklarujete své nároky.

1. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Vyhledejte element [BuildingBlocks.](buildingblocks.md) Pokud prvek neexistuje, přidejte jej.
1. Vyhledejte [ClaimsSchema](claimsschema.md) element. Pokud prvek neexistuje, přidejte jej.
1. Přidejte následující deklarace identity do prvku **ClaimsSchema.** 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Přidání nových technických profilů

Technické profily lze považovat za funkce v rozhraní Identity Experience framework Azure AD B2C. Tato tabulka definuje technické profily, které se používají k otevření relace a zaúčtování událostí.

| Technický profil | Úkol |
| ----------------- | -----|
| AppInsights-Common | Společná sada parametrů, které mají být zahrnuty do všech technických profilů Azure Insights. |
| AppInsights-SignInRequest | Zaznamená `SignInRequest` událost se sadou deklarací identity po přijetí žádosti o přihlášení. |
| AppInsights-UserSignUp | Zaznamená `UserSignUp` událost, když uživatel spustí možnost registrace v cestě registrace nebo přihlášení. |
| AppInsights-SignInComplete | Zaznamená `SignInComplete` událost po úspěšném dokončení ověřování, když byl token odeslán do aplikace předávající strany. |

Přidejte profily do souboru *TrustFrameworkExtensions.xml* ze startovního balíčku. Přidejte tyto prvky do **claimsproviders** element:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Změňte klíč instrumentace v technickém `AppInsights-Common` profilu na identifikátor GUID, který poskytuje váš prostředek Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Přidání technických profilů jako kroků orchestrace

Volání `AppInsights-SignInRequest` jako krok orchestrace 2 ke sledování, že byla přijata žádost o přihlášení/registraci:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bezprostředně *before* před `SendClaims` krokem orchestrace přidejte nový `AppInsights-UserSignup`krok, který volá . Aktivuje se, když uživatel vybere tlačítko registrace v cestě registrace/přihlášení.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Ihned `SendClaims` po kroku orchestrace volejte `AppInsights-SignInComplete`. Tento krok ukazuje úspěšně dokončenou cestu.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po přidání nových kroků orchestrace přečíslovat kroky postupně bez přeskočení všech celá čísla od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Nahrání souboru, spuštění zásad a zobrazení událostí

Uložte a nahrajte soubor *TrustFrameworkExtensions.xml.* Potom zavolejte zásady předávající strany z vaší aplikace nebo použijte **Spustit nyní** na webu Azure Portal. Během několika sekund jsou vaše události k dispozici v Application Insights.

1. Otevřete prostředek **Application Insights** v tenantovi Služby Azure Active Directory.
2. Vyberte**možnost Události** **využití** > .
3. Nastavit **během** **na poslední hodinu** a **do** **3 minut**.  Chcete-li zobrazit výsledky, bude pravděpodobně nutné vybrat možnost **Aktualizovat.**

![Přehledy aplikací USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>[Nepovinné] Shromažďování dalších dat

Přidejte do cesty uživatele typy deklarací a události, aby vyhovovaly vašim potřebám. Můžete použít [překládání deklarací](claim-resolver-overview.md) nebo jakýkoli typ deklarace řetězce, přidat deklarace přidáním prvku **vstupní deklarace** do události Application Insights nebo do technického profilu AppInsights-Common.

- **ClaimTypeReferenceId** je odkaz na typ deklarace.
- **PartnerClaimType** je název vlastnosti, která se zobrazí v Azure Insights. Použijte syntaxi `{property:NAME}`, `NAME` kde je vlastnost přidaná k události.
- **DefaultValue** použít libovolnou hodnotu řetězce nebo překládání deklarací.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Další kroky

- Další informace o technickém profilu [Application Insights](application-insights-technical-profile.md) najdete v odkazu na IEF. 
