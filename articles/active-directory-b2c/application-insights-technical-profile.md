---
title: Definování technického profilu Application Insights ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ve vlastní chodníčkové službě Azure Active Directory B2C definujte technický profil přehledů aplikací ve vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108572"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definování technického profilu Application Insights ve vlastních zásadách Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) podporuje odesílání dat událostí přímo do [Application Insights](../azure-monitor/app/app-insights-overview.md) pomocí instrumentační klíč poskytované Azure AD B2C.  Pomocí technického profilu Application Insights můžete získat podrobné a přizpůsobené protokoly událostí pro vaše cesty uživatelů:

* Získejte přehled o chování uživatelů.
* Poradce při potížích s vlastními zásadami ve vývoji nebo v produkčním prostředí.
* Měření výkonu.
* Vytvořte oznámení z Application Insights.


## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C pro application insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje společný technický profil Application Insights. Mezi další technické profily Application Insights patří AzureInsights-Common, které využívají jeho konfiguraci.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Vstupní deklarace

**InputClaims** Element obsahuje seznam deklarací, které mají být odeslány do Application Insights. Název deklarace můžete také namapovat na název, který chcete zobrazit v Application Insights. Následující příklad ukazuje, jak odeslat telemetry do Application Insights. Vlastnosti události jsou přidány `{property:NAME}`prostřednictvím syntaxe , kde NAME je vlastnost, která je přidána do události. DefaultValue může být buď statická hodnota nebo hodnota, která je vyřešena jedním z podporovaných [překládání deklarací](claim-resolver-overview.md).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations** Element může obsahovat kolekci **InputClaimsTransformation** prvky, které se používají k úpravě vstupní deklarace identity nebo generovat nové před odesláním do Application Insights.

## <a name="persist-claims"></a>Trvalé nároky

Prvek PersistedClaims se nepoužívá.

## <a name="output-claims"></a>Výstupní pohledávky

OutputClaims a OutputClaimsTransformations prvky nejsou použity.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek CryptographicKeys se nepoužívá.


## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| InstrumentaceKlíč| Ano | [Klíč instrumentace](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)Application Insights , který bude použit pro protokolování událostí. | 
| Režim vývojáře| Ne | Logická hodnota, která označuje, zda je povolen režim vývojáře. Možné `true` hodnoty: `false` nebo (výchozí). Tato metadata řídí, jak jsou události ukládání do vyrovnávací paměti. Ve vývojovém prostředí s minimálním objemem událostí, což umožňuje vývojářský režim, má za následek okamžité odeslání událostí do Application Insights.|  
|Zakázat telemetrii |Ne |Logická hodnota, která označuje, zda by telemetrie měla být povolena nebo ne. Možné `true` hodnoty: `false` nebo (výchozí).| 


## <a name="next-steps"></a>Další kroky

- [Vytvoření prostředku Přehledy aplikací](../azure-monitor/app/create-new-resource.md)
- Zjistěte, jak [sledovat chování uživatelů ve službě Azure Active Directory B2C pomocí přehledů aplikací](analytics-with-application-insights.md)
