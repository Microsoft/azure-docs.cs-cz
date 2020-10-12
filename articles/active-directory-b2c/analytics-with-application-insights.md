---
title: Sledovat chování uživatele pomocí Application Insights
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit protokoly událostí v Application Insights z Azure AD B2Cch cest uživatelů pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385973"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Sledovat chování uživatele v Azure Active Directory B2C pomocí Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) podporuje odesílání dat události přímo do [Application Insights](../azure-monitor/app/app-insights-overview.md) pomocí klíče instrumentace poskytnutého Azure AD B2C.  S Application Insights Technical profilem můžete získat podrobné a přizpůsobené protokoly událostí pro vaše uživatelské cesty:

* Získejte přehled o chování uživatelů.
* Řešení potíží s vlastními zásadami ve vývoji nebo v produkčním prostředí.
* Změřte výkon.
* Vytvoří oznámení z Application Insights.

## <a name="how-it-works"></a>Jak to funguje

[Application Insights](application-insights-technical-profile.md) Technical profil definuje událost z Azure AD B2C. Profil určuje název události, zaznamenané deklarace a klíč instrumentace. K odeslání události je technický profil přidán jako krok orchestrace v [cestě uživatele](userjourneys.md).

Application Insights může sjednotit události pomocí ID korelace pro záznam uživatelské relace. Application Insights zpřístupňuje událost a relaci během několika sekund a prezentuje mnoho nástrojů pro vizualizaci, export a analýzu.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlašování pomocí místních účtů.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Pokud používáte Application Insights s Azure AD B2C, stačí vytvořit prostředek a získat klíč instrumentace. Informace najdete v tématu [vytvoření prostředku Application Insights](../azure-monitor/app/create-new-resource.md) .

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a vyberete adresář, který obsahuje vaše předplatné. Tento tenant není vaším klientem Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** a pak vyhledejte a vyberte **Application Insights**.
4. Klikněte na **Vytvořit**.
5. Zadejte **název** prostředku.
6. Jako **Typ aplikace**vyberte **ASP.NET webová aplikace**.
7. V případě **skupiny prostředků**vyberte existující skupinu nebo zadejte název nové skupiny.
8. Klikněte na **Vytvořit**.
4. Po vytvoření prostředku Application Insights otevřete ho, rozbalte **základy**a zkopírujte klíč instrumentace.

![Přehled Application Insights a klíč instrumentace](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definovat deklarace identity

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. [Schéma deklarací identity](claimsschema.md) je místo, kde deklarujete deklarace identity.

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Do prvku **ClaimsSchema** přidejte následující deklarace identity. 

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

## <a name="add-new-technical-profiles"></a>Přidat nové technické profily

Technické profily lze považovat za funkce v architektuře prostředí identity Azure AD B2C. Tato tabulka definuje technické profily, které se používají k otevření relace a odeslání událostí.

| Technický profil | Úloha |
| ----------------- | -----|
| AppInsights-Common | Společná sada parametrů, které se mají zahrnout do všech technických profilů Azure Insights. |
| AppInsights-SignInRequest | Zaznamenává `SignInRequest` událost se sadou deklarací při přijetí žádosti o přihlášení. |
| AppInsights-UserSignUp | Zaznamenává `UserSignUp` událost, když uživatel aktivuje možnost registrace v cestě pro registraci a přihlašování. |
| AppInsights-SignInComplete | Zaznamenává `SignInComplete` událost po úspěšném dokončení ověřování, když byl token odeslán do aplikace předávající strany. |

Přidejte profily do souboru *TrustFrameworkExtensions.xml* z úvodní sady. Přidejte tyto prvky do prvku **ClaimsProviders** :

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
> Změňte klíč instrumentace v `AppInsights-Common` technickém profilu na identifikátor GUID, který poskytuje váš Application Insights prostředek.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Přidání technických profilů jako kroků orchestrace

Pokud `AppInsights-SignInRequest` chcete sledovat, že byla přijata žádost o přihlášení nebo přihlášení, zavolejte jako orchestrace krok 2:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bezprostředně *před* `SendClaims` krokem orchestrace přidejte nový krok, který bude volat `AppInsights-UserSignup` . Aktivuje se, když uživatel vybere tlačítko pro registraci v cestě k registraci nebo přihlašování.

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

Hned po `SendClaims` kroku orchestrace zavolejte `AppInsights-SignInComplete` . V tomto kroku se zobrazuje úspěšně dokončená cesta.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po přidání nových kroků orchestrace předávejte kroky postupně, aniž by bylo vynecháno celé číslo od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Nahrání souboru, spuštění zásad a zobrazení událostí

Uložte a odešlete soubor *TrustFrameworkExtensions.xml* . Pak zavolejte zásadu předávající strany z vaší aplikace nebo použijte **Spustit nyní** v Azure Portal. V sekundách jsou vaše události k dispozici v Application Insights.

1. Otevřete prostředek **Application Insights** ve vašem tenantovi Azure Active Directory.
2. Vyberte **Usage**  >  **události**využití.
3. Nastaví **During** se během **poslední hodiny** a **do** **3 minut**.  Pro zobrazení výsledků může být nutné vybrat možnost **aktualizovat** .

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Volitelné Shromažďování dalších dat

Přidejte do cesty uživatele typy deklarací identity a události, aby vyhovovaly vašim potřebám. Můžete použít [překladače deklarací identity](claim-resolver-overview.md) nebo jakýkoli typ deklarace identity, přidat deklarace identity přidáním prvku **vstupní deklarace identity** do události Application Insights nebo do technického profilu AppInsights-Common.

- **ClaimTypeReferenceId** je odkaz na typ deklarace identity.
- **PartnerClaimType** je název vlastnosti, která se zobrazuje v Azure Insights. Použijte syntaxi `{property:NAME}` , kde je do `NAME` události přidána vlastnost.
- **DefaultValue** používá libovolnou řetězcovou hodnotu nebo překladač deklarací identity.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Další kroky

- Další informace o [Application Insights](application-insights-technical-profile.md) Technical Profile najdete v referenčních informacích k IEF. 
