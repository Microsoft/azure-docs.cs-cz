---
title: Sledovat chování uživatele pomocí Application Insights
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit protokoly událostí v Application Insights z Azure AD B2Cch cest uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218549"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Sledovat chování uživatele v Azure Active Directory B2C pomocí Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) podporuje odesílání dat události přímo do [Application Insights](../azure-monitor/app/app-insights-overview.md) pomocí klíče instrumentace poskytnutého Azure AD B2C. S Application Insights Technical profilem můžete získat podrobné a přizpůsobené protokoly událostí pro vaše uživatelské cesty:

* Získejte přehled o chování uživatelů.
* Řešení potíží s vlastními zásadami ve vývoji nebo v produkčním prostředí.
* Změřte výkon.
* Vytvoří oznámení z Application Insights.

## <a name="overview"></a>Přehled

Pokud chcete povolit vlastní protokoly událostí, přidejte Application Insights technický profil. V technickém profilu definujete Application Insights klíč instrumentace, název události a deklarace identity, které se mají zaznamenat. K odeslání události je technický profil přidán jako krok orchestrace v [cestě uživatele](userjourneys.md).

Při použití Application Insights Vezměte v úvahu následující skutečnosti:

- Předtím, než budou nové protokoly k dispozici v Application Insights, je obvykle méně než pět minut krátké zpoždění.
- Azure AD B2C umožňuje vybrat deklarace identity, které mají být zaznamenávány. Nepoužívejte deklarace identity s osobními údaji.
- Chcete-li zaznamenat relaci uživatele, mohou být události sjednoceny pomocí ID korelace. 
- Zavolejte Application Insights Technical profil přímo z [cesty uživatele](userjourneys.md) nebo z [dílčích cest](subjourneys.md). Nepoužívejte profil Application Insights Technical Profile jako [technický profil ověřování](validation-technical-profile.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Pokud používáte Application Insights s Azure AD B2C, stačí vytvořit prostředek a získat klíč instrumentace. Informace najdete v tématu [vytvoření prostředku Application Insights](../azure-monitor/app/create-new-resource.md) .

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné Azure, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a vyberete adresář, který obsahuje vaše předplatné. Tento tenant není vaším klientem Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** a pak vyhledejte a vyberte **Application Insights**.
4. Klikněte na **Vytvořit**.
5. Zadejte **název** prostředku.
6. Jako **Typ aplikace** vyberte **ASP.NET webová aplikace**.
7. V případě **skupiny prostředků** vyberte existující skupinu nebo zadejte název nové skupiny.
8. Klikněte na **Vytvořit**.
4. Po vytvoření prostředku Application Insights otevřete ho, rozbalte **základy** a zkopírujte klíč instrumentace.

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

Technické profily lze považovat za funkce ve vlastních zásadách. Tato tabulka definuje technické profily, které se používají k otevření relace a odeslání událostí. Řešení používá přístup k [zahrnutí technického profilu](technicalprofiles.md#include-technical-profile) . Kde technický profil obsahuje jiný technický profil pro změnu nastavení nebo přidání nových funkcí.

| Technický profil | Úkol |
| ----------------- | -----|
| AppInsights-Common | Společný technický profil se společnou sadou konfigurací. Zahrnuje Application Insights klíč instrumentace, kolekce deklarací, které se mají zaznamenat, a vývojářský režim. Následující technické profily zahrnují běžný technický profil a přidávají další deklarace identity, jako je třeba název události. |
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
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
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
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
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

Uložte a odešlete soubor *TrustFrameworkExtensions.xml* . Pak zavolejte zásadu předávající strany z vaší aplikace nebo použijte **Spustit nyní** v Azure Portal. Počkejte minutu nebo to a vaše události budou k dispozici v Application Insights.

1. Otevřete prostředek **Application Insights** ve vašem tenantovi Azure Active Directory.
2. Vyberte **využití** a pak vyberte **události**.
3. Nastaví  se během **poslední hodiny** a **do** **3 minut**.  Pro zobrazení výsledků může být nutné vybrat možnost **aktualizovat** .

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Shromažďování dalších dat

Aby vyhovovaly vašim obchodním potřebám, možná budete chtít zaznamenat více deklarací identity. Pokud chcete přidat deklaraci identity, nejdřív [Definujte deklaraci](#define-claims)identity a pak přidejte deklaraci do vstupní kolekce deklarací. Deklarace identity, které přidáte do *AppInsights (Common* Technical Profile), se zobrazí ve všech událostech. Deklarace identity, které přidáte do konkrétního technického profilu, se zobrazí pouze v této události. Vstupní element deklarace identity obsahuje následující atributy:

- **ClaimTypeReferenceId** – odkaz na typ deklarace identity. 
- **PartnerClaimType** – je název vlastnosti, která se zobrazí v Azure Insights. Použijte syntaxi `{property:NAME}` , kde je do `NAME` události přidána vlastnost.
- **DefaultValue** – předdefinovaná hodnota, která má být zaznamenána, například název události. Deklarace identity, která se používá v cestě uživatele, například název zprostředkovatele identity. Pokud je deklarace identity prázdná, použije se výchozí hodnota. Například `identityProvider` deklaraci identity nastavuje federace Technical Profiles, jako je Facebook. Pokud je deklarace identity prázdná, indikuje to, že se uživatel přihlašuje pomocí místního účtu. Proto je výchozí hodnota nastavená na *Local (místní*). [Překladače deklarací identity](claim-resolver-overview.md) můžete také zaznamenat pomocí kontextové hodnoty, jako je například ID aplikace nebo IP adresa uživatele.

### <a name="manipulating-claims"></a>Manipulace s deklaracemi

Pomocí [transformací vstupních deklarací identity](custom-policy-trust-frameworks.md#manipulating-your-claims) můžete před odesláním do Application Insights upravit vstupní deklarace identity nebo vygenerovat nové. V následujícím příkladu obsahuje technický profil transformaci *CheckIsAdmin* vstupních deklarací identity. 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Přidat události

Pokud chcete přidat událost, vytvořte nový technický profil, který bude obsahovat *AppInsights-Common* Technical Profile. Pak přidejte technický profil jako krok orchestrace na [cestu uživatele](custom-policy-trust-frameworks.md#orchestration-steps). V případě potřeby můžete událost aktivovat pomocí [podmínky](userjourneys.md#preconditions) . Můžete například ohlásit událost pouze v případě, že uživatelé běží prostřednictvím vícefaktorového ověřování.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Teď, když máte technický profil, přidejte událost do cesty pro uživatele. Pak kroky přečíslujte sekvenčně bez přeskakování libovolných celých čísel od 1 do N.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Povolit vývojářský režim

Při použití Application Insights k definování událostí můžete určit, zda je povolen režim pro vývojáře. Vývojářský režim řídí, jak se události ukládají do vyrovnávací paměti. Když ve vývojovém prostředí s minimálním objemem událostí povolíte vývojářský režim, budou se události odesílat okamžitě Application Insights. Výchozí hodnota je `false`. Nepovolujte vývojářský režim v produkčních prostředích.

Pokud chcete povolit vývojářský režim, změňte v *AppInsights – Common* Technical Profile `DeveloperMode` metadata na `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Zakázat telemetrii

Chcete-li zakázat protokoly Insight Application Insights, v technickém profilu *AppInsights-Common* změňte `DisableTelemetry` metadata na `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvářet vlastní řídicí panely klíčových ukazatelů výkonu pomocí Azure Application Insights](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end