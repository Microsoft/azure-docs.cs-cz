---
title: Definování Application Insights technického profilu ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte Application Insights technický profil ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201408"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování Application Insights technického profilu ve vlastních zásadách Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) podporuje odesílání dat události přímo do [Application Insights](../azure-monitor/app/app-insights-overview.md) pomocí klíče instrumentace poskytnutého Azure AD B2C.  S Application Insights Technical profilem můžete získat podrobné a přizpůsobené protokoly událostí pro vaše uživatelské cesty:

* Získejte přehled o chování uživatelů.
* Řešení potíží s vlastními zásadami ve vývoji nebo v produkčním prostředí.
* Změřte výkon.
* Vytvoří oznámení z Application Insights.


## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které používá Azure AD B2C pro Application Insights: `Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje běžný Application Insights technický profil. Mezi další Application Insights technické profily patří AzureInsights-Common, která využívají jeho konfiguraci.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do Application Insights. Můžete také namapovat název vaší deklarace na název, který se zobrazí v Application Insights. Následující příklad ukazuje, jak odeslat telemetrií do Application Insights. Vlastnosti události jsou přidány pomocí syntaxe `{property:NAME}` , kde název je přidán do události. DefaultValue může být buď statická hodnota, nebo hodnota, která je vyřešená jedním z podporovaných [překladačů deklarací identity](claim-resolver-overview.md).

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před odesláním do Application Insights.

## <a name="persist-claims"></a>Zachovat deklarace identity

Element PersistedClaims se nepoužívá.

## <a name="output-claims"></a>Deklarace výstupů

Prvky OutputClaims a OutputClaimsTransformations nejsou použity.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element CryptographicKeys se nepoužívá.


## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| InstrumentationKey| Ano | Application Insights [klíč instrumentace](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key), který se použije pro protokolování událostí. | 
| DeveloperMode| No | Logická hodnota, která označuje, zda je povolen režim pro vývojáře. Možné hodnoty: `true` nebo `false` (výchozí). Tato metadata řídí, jak jsou události ukládány do vyrovnávací paměti. Když ve vývojovém prostředí s minimálním objemem událostí povolíte vývojářský režim, budou se události odesílat okamžitě Application Insights.|  
|DisableTelemetry |No |Logická hodnota, která označuje, zda má být povolena telemetrie. Možné hodnoty: `true` nebo `false` (výchozí).| 


## <a name="next-steps"></a>Další kroky

- [Vytvoření prostředku Application Insights](../azure-monitor/app/create-new-resource.md)
- Naučte se [sledovat chování uživatele v Azure Active Directory B2C pomocí Application Insights](analytics-with-application-insights.md)
