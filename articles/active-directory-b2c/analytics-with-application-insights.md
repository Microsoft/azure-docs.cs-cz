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
ms.openlocfilehash: 2cde44ddb49ede8002b8a25ab47ae92ccd602a9d
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226366"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Sledovat chování uživatele v Azure AD B2C pomocí Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

V Azure Active Directory B2C (Azure AD B2C) můžete odesílat data události přímo do [Application Insights](../azure-monitor/app/app-insights-overview.md) pomocí klíče instrumentace poskytnutého Azure AD B2C. S Application Insights Technical profilem můžete získat podrobné a přizpůsobené protokoly událostí pro vaše uživatelské cesty:

- Získejte přehled o chování uživatelů.
- Řešení potíží s vlastními zásadami ve vývoji nebo v produkčním prostředí.
- Změřte výkon.
- Vytvoří oznámení z Application Insights.

## <a name="overview"></a>Přehled

Pokud chcete povolit vlastní protokoly událostí, přidejte Application Insights technický profil. V technickém profilu definujete Application Insights klíč instrumentace, název události a deklarace identity, které se mají zaznamenat. K odeslání události přidejte technický profil jako krok orchestrace v [cestě uživatele](userjourneys.md).

Při použití Application Insights Vezměte v úvahu následující skutečnosti:

- Nové protokoly jsou v Application Insights k dispozici krátké zpoždění, obvykle méně než pět minut.
- Azure AD B2C vám umožní vybrat, které deklarace identity se mají zaznamenat. Nepoužívejte deklarace identity s osobními údaji.
- Pokud chcete zaznamenat relaci uživatele, můžete pro sjednocení událostí použít ID korelace.
- Zavolejte Application Insights Technical profil přímo z [cesty uživatele](userjourneys.md) nebo z [dílčí cesty](subjourneys.md). Nepoužívejte profil Application Insights Technical Profile jako [technický profil ověřování](validation-technical-profile.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights

Pokud používáte Application Insights s Azure AD B2C, stačí vytvořit prostředek a získat klíč instrumentace. Informace najdete v tématu [vytvoření prostředku Application Insights](../azure-monitor/app/create-new-resource.md).

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář s vaším předplatným Azure. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vaše předplatné Azure. Tento tenant není vaším klientem Azure AD B2C.
1. Zvolte **vytvořit prostředek** v levém horním rohu Azure Portal a pak vyhledejte a vyberte **Application Insights**.
1. Vyberte **Vytvořit**.
1. Do pole **název** zadejte název prostředku.
1. Jako **Typ aplikace** vyberte **ASP.NET webová aplikace**.
1. V případě **skupiny prostředků** vyberte existující skupinu nebo zadejte název nové skupiny.
1. Vyberte **Vytvořit**.
1. Otevřete nový prostředek Application Insights, rozbalte **základy** a zkopírujte klíč instrumentace.

![Snímek obrazovky zobrazující klíč instrumentace na kartě Přehled Application Insights](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definovat deklarace identity

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. Deklarace identity deklarujete v [elementu ClaimsSchema](claimsschema.md).

1. Otevřete soubor rozšíření vaší zásady. Soubor může vypadat podobně jako `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud prvek nevidíte, přidejte ho.
1. Vyhledejte element **ClaimsSchema** . Pokud prvek nevidíte, přidejte ho.
1. Do prvku **ClaimsSchema** přidejte následující deklarace identity:

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

Technické profily lze považovat za funkce ve vlastních zásadách. Tyto funkce používají přístup k [zahrnutí technického profilu](technicalprofiles.md#include-technical-profile) , kde technický profil obsahuje další technický profil a změny nastavení nebo přidává nové funkce. Následující tabulka definuje technické profily, které se používají k otevření relace a odeslání událostí.

| Technický profil | Úkol |
| ----------------- | -----|
| AppInsights-Common | Společný technický profil s typickou konfigurací. Zahrnuje Application Insights klíč instrumentace, kolekci deklarací pro záznam a vývojářský režim. Mezi další technické profily patří společný technický profil a přidání dalších deklarací identity, jako je třeba název události. |
| AppInsights-SignInRequest | Zaznamenává událost **SignInRequest** se sadou deklarací při přijetí žádosti o přihlášení. |
| AppInsights-UserSignUp | Zaznamenává událost **UserSignUp** , když uživatel aktivuje možnost registrace v cestě k registraci nebo přihlašování. |
| AppInsights-SignInComplete | Zaznamenává událost **SignInComplete** po úspěšném ověření při odeslání tokenu do aplikace předávající strany. |

Otevřete soubor *TrustFrameworkExtensions.xml* z úvodní sady. Přidejte technické profily do prvku **ClaimsProvider** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Přidejte nové kroky orchestrace, které odkazují na technické profily.

> [!IMPORTANT]
> Po přidání nových kroků orchestrace předávejte kroky postupně, aniž by bylo vynecháno celé číslo od 1 do N.

1. Zavolejte `AppInsights-SignInRequest` jako druhý krok orchestrace. Tento krok sleduje, zda byla přijata žádost o registraci nebo přihlášení.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Před `SendClaims` Krok orchestrace přidejte nový krok, který bude volat `AppInsights-UserSignup` . Aktivuje se, když uživatel vybere tlačítko pro registraci v cestě k registraci nebo přihlašování.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. Po `SendClaims` kroku orchestrace zavolejte `AppInsights-SignInComplete` . V tomto kroku se zobrazuje úspěšně dokončená cesta.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Nahrání souboru, spuštění zásad a zobrazení událostí

Uložte a odešlete soubor *TrustFrameworkExtensions.xml* . Pak zavolejte zásadu předávající strany z vaší aplikace nebo použijte **Spustit nyní** v Azure Portal. Počkejte, než budou události k dispozici v Application Insights.

1. Otevřete prostředek **Application Insights** ve vašem tenantovi Azure Active Directory.
1. Vyberte **využití** a pak vyberte **události**.
1. Nastaví  se během **poslední hodiny** a **do** **3 minut**. Možná budete muset aktualizovat okno, aby se zobrazily výsledky.

![Snímek obrazovky zobrazující statistiku událostí Application Insights](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Shromažďování dalších dat

Aby vyhovovaly vašim obchodním potřebám, možná budete chtít zaznamenat více deklarací identity. Pokud chcete přidat deklaraci identity, nejdřív [Definujte deklaraci](#define-claims)identity a pak přidejte deklaraci do vstupní kolekce deklarací. Deklarace identity, které přidáte do **AppInsights-Common** Technical Profile, se zobrazí ve všech událostech. Deklarace identity, které přidáte do konkrétního technického profilu, se zobrazí pouze v této události. Vstupní element deklarace identity obsahuje následující atributy:

- **ClaimTypeReferenceId** je odkaz na typ deklarace identity.
- **PartnerClaimType** je název vlastnosti, která se zobrazuje v Azure Insights. Použijte syntaxi `{property:NAME}` , kde `NAME` je vlastnost přidána do události.
- **DefaultValue** je předdefinovaná hodnota, která se má zaznamenat, jako je například název události. Pokud je deklarace identity, která se používá v cestě uživatele, prázdná, použije se výchozí hodnota. Například `identityProvider` deklaraci identity nastavuje federace Technical Profiles, jako je Facebook. Pokud je deklarace identity prázdná, indikuje to, že se uživatel přihlásil pomocí místního účtu. Proto je výchozí hodnota nastavená na **Local (místní**). [Překladač deklarací identity](claim-resolver-overview.md) můžete také zaznamenat pomocí kontextové hodnoty, jako je například ID aplikace nebo IP adresa uživatele.

### <a name="manipulate-claims"></a>Manipulace s deklaracemi

Pomocí [transformací vstupních deklarací identity](custom-policy-overview.md#manipulating-your-claims) můžete upravit vstupní deklarace identity nebo je před odesláním do Application Insights vytvořit nové. V následujícím příkladu obsahuje technický profil `CheckIsAdmin` transformaci vstupních deklarací identity.

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

Pokud chcete přidat událost, vytvořte nový technický profil, který bude obsahovat `AppInsights-Common` technický profil. Pak přidejte nový technický profil jako krok orchestrace na [cestu uživatele](custom-policy-overview.md#orchestration-steps). Jakmile budete připraveni, můžete událost aktivovat pomocí elementu [předběžné podmínky](userjourneys.md#preconditions) . Můžete například ohlásit událost pouze v případě, že uživatelé běží prostřednictvím vícefaktorového ověřování.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Když přidáte událost do cesty pro uživatele, nezapomeňte přečíslovat kroky orchestrace postupně.

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

Když použijete Application Insights k definování událostí, můžete určit, jestli je povolený vývojářský režim. Vývojářský režim řídí, jak se události ukládají do vyrovnávací paměti. Když ve vývojovém prostředí s minimálním objemem událostí povolíte vývojářský režim, budou se události odesílat okamžitě Application Insights. Výchozí hodnota je `false`. Nepovolujte vývojářský režim v produkčních prostředích.

Pokud chcete povolit režim pro vývojáře, změňte `DeveloperMode` metadata na `true` `AppInsights-Common` technický profil:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Zakázat telemetrii

Pokud chcete zakázat protokoly Application Insights, změňte `DisableTelemetry` metadata na `true` `AppInsights-Common` technický profil:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Další kroky

Naučte se [vytvářet vlastní řídicí panely klíčových ukazatelů výkonu pomocí Azure Application Insights](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end