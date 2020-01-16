---
title: Migrace EA na Microsoft Customer Agreement API – Azure
description: Tento článek vám pomůže pochopit důsledky migrace Microsoft smlouva Enterprise (EA) na zákaznickou smlouvu Microsoftu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0dfb7245135097780f027185e4037b6cafbae690
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989355"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrace z smlouva Enterprise na rozhraní API zákaznické smlouvy Microsoftu

Tento článek vám pomůže pochopit strukturu dat, rozhraní API a další rozdíly v integraci systému mezi smlouva Enterprise (EA) a účty Microsoft Customer Agreement (MCA). Azure Cost Management podporuje rozhraní API pro oba typy účtů. Než budete pokračovat, přečtěte si článek o [Nastavení fakturačního účtu pro](../manage/mca-setup-account.md) zákaznickou smlouvu od Microsoftu.

Organizace s existujícím účtem EA by si měli projít tento článek ve spojení s nastavením účtu MCA. Dříve se při obnovení účtu EA vyžadovalo, aby se přechod z původního zápisu do nového zavedl na nějakou minimální práci. Migrace na účet MCA ale vyžaduje další úsilí. Další úsilí je kvůli změnám v podkladovém subsystému, které se týkají všech cenově vydaných rozhraní API a nabídek služeb.

## <a name="mca-apis-and-integration"></a>Rozhraní API a integrace MCA

Rozhraní API pro MCA a novou integraci umožňují:

- Dokončete dostupnost rozhraní API prostřednictvím nativních rozhraní API Azure.
- Nakonfigurujte více faktur v jednom fakturačním účtu.
- Přístup k kombinovanému rozhraní API s využitím služeb Azure, využitím Marketplace a nákupy na webu Marketplace.
- Prohlédněte si náklady v rámci fakturačních profilů (stejné jako registrace) pomocí Azure Cost Management.
- Přístup k novým rozhraním API pro zobrazení nákladů vám pošle upozornění, když náklady překročí předdefinované prahové hodnoty, a automaticky se exportují nezpracované údaje

## <a name="migration-checklist"></a>Kontrolní seznam migrace

Následující položky vám pomůžou přejít na rozhraní API MCA.

- Seznamte se s novým [fakturačním účtem Microsoft Customer Agreement](../understand/mca-overview.md).
- Určete, která rozhraní API používáte, a zjistěte, které z nich jsou nahrazené v následující části.
- Seznamte se s [Azure Resource Manager rozhraní REST API](/rest/api/azure).
- Pokud rozhraní API Azure Resource Manager ještě nepoužíváte, [Zaregistrujte si klientskou aplikaci ve službě Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aktualizujte jakýkoliv kód programování a [použijte ověřování Azure AD](/rest/api/azure/#create-the-request).
- Aktualizujte jakýkoliv kód programování, aby nahradil volání rozhraní API EA pomocí volání rozhraní API MCA.
- Zpracování chyb aktualizace pro použití nových kódů chyb.
- Další akce, které je potřeba provést, najdete v dalších nabídkách integrace, jako je Cloudyn a Power BI.

## <a name="ea-apis-replaced-with-mca-apis"></a>Rozhraní API EA nahrazená rozhraními API MCA

Rozhraní API EA používají pro ověřování a autorizaci klíč rozhraní API. Rozhraní API pro MCA využívají ověřování Azure AD.

| Účel | EA API | ROZHRANÍ API MCA |
| --- | --- | --- |
| Zůstatek a kredity | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft. fakturace/billingAccounts/billingProfiles/availableBalanceussae |
| Použití (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Využití (CSV) | [/UsageDetails/Download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/UsageDetails/Submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. spotřebovat/usageDetails/stáhnout](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Využití Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. spotřebovat/usageDetails/stáhnout](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Fakturační období | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft. fakturace/billingAccounts/billingProfiles/faktur |
| Ceník | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. faktura/billingAccounts/billingProfiles/pricesheet/default/download Format = JSON|CSV Microsoft. fakturuje/billingAccounts/.../billingProfiles/............ /pricesheet/default/download Format = JSON|CSV Microsoft. fakturace/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Nákupy rezervací | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft. fakturace/billingAccounts/billingProfiles/transakcí |
| Doporučení pro rezervaci | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. spotřeb/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Využití rezervace | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. spotřeb/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft. spotřebovat/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> služba Azure a využití tržiště třetích stran jsou k dispozici v [rozhraní API s podrobnostmi o využití](/rest/api/consumption/usagedetails).

Pro fakturační účty MCA jsou k dispozici následující rozhraní API:

| Účel | Rozhraní API Microsoft Customer Agreement (MCA) |
| --- | --- |
| Fakturační účty<sup>2</sup> | Microsoft. fakturace/billingAccounts |
| Fakturační profily<sup>2</sup> | Microsoft. fakturace/billingAccounts/billingProfiles |
| Oddíly faktury<sup>2</sup> | Microsoft. fakturace/billingAccounts/invoiceSections |
| Faktury | Microsoft. fakturace/billingAccounts/billingProfiles/faktur |
| Fakturace předplatných | {Scope}/billingSubscriptions |

<sup>2</sup> rozhraní API vrací seznam objektů, které jsou obory, kde Cost Management prostředí Azure Portal a rozhraní API. Další informace o oborech Cost Management najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

Pokud používáte jakákoli existující rozhraní API EA, musíte je aktualizovat, aby podporovaly fakturační účty MCA. Následující tabulka uvádí další změny v integraci:

| Účel | Stará nabídka | Nová nabídka |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Balíček obsahu a konektor pro [Microsoft spotřeb Insights](/power-bi/desktop-connect-azure-consumption-insights) | [Microsoft Azure Consumption Insights Power BI aplikace](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) a [konektor Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Rozhraní API pro získání rovnováhy a kreditů

Souhrnné rozhraní API pro [získání zůstatku](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) vám poskytne Měsíční souhrn:

- Zůstatky
- Nové nákupy
- Poplatky za Azure Marketplace služby
- Opravy
- Poplatky za nadlimitní využití služby

Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request).

Souhrnné rozhraní API pro získání zůstatku je nahrazeno rozhraním API Microsoft. fakturace/billingAccounts/billingProfiles/availableBalance.

K získání dostupných zůstatků s dostupným rozhraním API pro vyrovnávání zatížení:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Rozhraní API k získání nákladů a využití

Pomocí následujících rozhraní API získáte denní rozpis nákladů na využití služeb Azure, využití Marketplace a další nákupy na webu Marketplace. Následující samostatná rozhraní API se sloučila pro služby Azure a použití na tržišti třetích stran. Stará rozhraní API se nahradí rozhraním API [Microsoft. spotřeb/usageDetails](/rest/api/consumption/usagedetails) . Přidá nákupy na webu Marketplace, které byly dříve zobrazeny pouze v souhrnu salda do data.

- [Získat podrobnosti o využití/stáhnout](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Získat podrobnosti o využití/odeslat](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Získat podrobnosti o využití/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Získat podrobnosti o využití/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Získat poplatek za marketplacecharges na webu Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Získat poplatek za marketplacechargesbycustomdate na webu Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request).

Všechna předchozí rozhraní API se nahradí rozhraním API pro využití a používání podrobností.

Získání podrobností o použití rozhraní API s podrobnostmi o využití:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Rozhraní API s podrobnostmi o využití, stejně jako u všech Cost Management rozhraní API, je k dispozici ve více oborech. U fakturovaných nákladů, jak byste tradičně získali na úrovni registrace, použijte rozsah fakturačního profilu.  Další informace o oborech Cost Management najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

| Typ | Formát ID |
| --- | --- |
| Fakturační účet | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Fakturační profil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Předplatné | `/subscriptions/{subscriptionId}` |
| Skupina prostředků | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Použijte následující parametry QueryString k aktualizaci kódu programování.

| Staré parametry | Nové parametry |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nepodporováno |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Změnil se také text odpovědi.

Původní tělo odpovědi:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nové tělo odpovědi:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Název vlastnosti obsahující pole záznamů o využití se změnil z dat na _hodnoty_. Každý záznam použitý k dispozici má nestrukturovaný seznam podrobných vlastností. Každý záznam teď ale všechny podrobnosti jsou teď ve vnořené vlastnosti s názvem _vlastnosti_, s výjimkou značek. Nová struktura je konzistentní s jinými rozhraními API Azure. Některé názvy vlastností se změnily. V následující tabulce jsou uvedeny odpovídající vlastnosti.

| Stará vlastnost | Nová vlastnost | Poznámky |
| --- | --- | --- |
| AccountId | Nevztahuje se | Autor předplatného není sledován. Použijte invoiceSectionId (totéž jako departmentId). |
| AccountNameAccountOwnerId a AccountOwnerEmail | Nevztahuje se | Autor předplatného není sledován. Použijte invoiceSectionName (stejné jako oddělení). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Všimněte si, že tyto vlastnosti jsou Opaky. Pokud má isAzureCreditEnabled hodnotu true, ChargesBilledSeparately by byl false. |
| ConsumedQuantity | množství | &nbsp; |
| ConsumedService | consumedService | Přesné řetězcové hodnoty se mohou lišit. |
| ConsumedServiceId | Žádné | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Datum a usageStartDate | date | &nbsp;  |
| Den | Žádné | Analyzuje den od data. |
| DepartmentId | invoiceSectionId | Přesné hodnoty se liší. |
| DepartmentName | invoiceSectionName | Přesné řetězcové hodnoty se mohou lišit. V případě potřeby nakonfigurujte v sekcích faktury odpovídající oddělení. |
| ExtendedCost a náklady | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Je opakovaný poplatek | Žádné | &nbsp;  |
| Umístění | location | &nbsp;  |
| MeterCategory | meterCategory | Přesné řetězcové hodnoty se mohou lišit. |
| ID měřiče | meterId | Přesné řetězcové hodnoty se liší. |
| MeterName | meterName | Přesné řetězcové hodnoty se mohou lišit. |
| MeterRegion | meterRegion | Přesné řetězcové hodnoty se mohou lišit. |
| MeterSubCategory | meterSubCategory | Přesné řetězcové hodnoty se mohou lišit. |
| Month | Žádné | Analyzuje měsíc od data. |
| Název nabídky | Žádné | Použijte Publisher a productOrderName. |
| Hodnotami OfferId | Žádné | &nbsp;  |
| Číslo objednávky | Žádné | &nbsp;  |
| PartNumber | Žádné | Pomocí meterId a productOrderName jednoznačně Identifikujte ceny. |
| Název plánu | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ProductId | productId | Přesné řetězcové hodnoty se liší. |
| Název vydavatele | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Přesné řetězcové hodnoty se liší. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Žádné | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Nevztahuje se | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Přesné řetězcové hodnoty se mohou lišit. |
| ServiceTier | meterSubCategory | Přesné řetězcové hodnoty se mohou lišit. |
| StoreServiceIdentifier | Nevztahuje se | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Značky | značek | Vlastnost tagss se vztahuje na kořenový objekt, nikoli na vlastnost vnořené vlastnosti. |
| UnitOfMeasure | unitOfMeasure | Přesné řetězcové hodnoty se liší. |
| usageEndDate | date | &nbsp;  |
| Year | Žádné | Analyzuje rok od data. |
| (nové) | billingCurrency | Měna použitá pro poplatek |
| (nové) | billingProfileId | Jedinečné ID pro fakturační profil (stejné jako registrace). |
| (nové) | billingProfileName | Název fakturačního profilu (stejný jako registrace). |
| (nové) | chargeType | Slouží k odlišení využití služeb Azure, využití Marketplace a nákupů. |
| (nové) | invoiceId | Jedinečné ID faktury Prázdné pro aktuální, otevřený měsíc. |
| (nové) | publisherType | Typ vydavatele pro nákupy Prázdné pro použití. |
| (nové) | serviceFamily | Typ nákupu. Prázdné pro použití. |
| (nové) | servicePeriodEndDate | Koncové datum zakoupené služby. |
| (nové) | servicePeriodStartDate | Počáteční datum zakoupené služby. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Rozhraní API pro fakturační období nahrazené rozhraním API faktury

Fakturační účty MCA nepoužívají fakturační období. Místo toho používají faktury k oboru nákladů na konkrétní fakturační období. [Rozhraní API pro fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) nahrazuje rozhraní API pro fakturaci. Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request).

K získání faktury pomocí rozhraní API pro fakturaci:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Rozhraní API ceníku

Tato část popisuje existující rozhraní API ceníku a poskytuje doporučení pro přechod na rozhraní API ceníku pro smlouvy o zákaznících Microsoftu. Popisuje také rozhraní API ceníku pro smlouvy o zákaznících Microsoftu a vysvětluje pole v ceníkech. Rozhraní API pro [podnikové získání ceníku](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) a [podnik získá fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) nahrazuje rozhraní API ceníku pro smlouvy o zákaznících Microsoftu (Microsoft. faktura/billingAccounts/billingProfiles/pricesheet). V případě asynchronních formátů REST podporuje nové rozhraní API formáty JSON i CSV. Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Fakturační podniková rozhraní API

Použili jste fakturovaná podniková rozhraní API s podnikovými registracemi k získání informací o cenách a fakturačních obdobích. Ověřování a autorizace používané Azure Active Directory webovými tokeny.

Pokud chcete získat platné ceny pro zadaný Podnikový zápis pomocí rozhraní API ceníku a fakturačního období:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Rozhraní API ceníku pro smlouvy o zákaznících Microsoftu

Pomocí rozhraní API ceníku pro smlouvy Microsoft Customer můžete zobrazit ceny za všechny služby Azure a spotřebu na webu Marketplace. Ceny zobrazené pro fakturační profil platí pro všechna předplatná, která patří do fakturačního profilu.

Pomocí rozhraní API ceníku si můžete zobrazit všechna data ceníku služeb Azure spotřebovaná ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Pomocí rozhraní API ceníku si můžete zobrazit všechny Ceníní data služby Azure spotřebovaná v tabulce ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Když použijete rozhraní API, vrátí se ceník pro celý účet. Můžete ale také získat zhuštěnou verzi ceníku ve formátu PDF. Shrnutí zahrnuje služby Azure pro využívání a spotřebu na webu Marketplace, které se účtují za konkrétní fakturu. Faktura je identifikována číslem {invoiceId}, který je stejný jako **číslo faktury** zobrazené v souborech PDF v souhrnném dokumentu faktury. Tady je příklad:

![Příklad obrázku znázorňujícího číslo faktury, které odpovídá InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Zobrazení informací o fakturaci pomocí rozhraní API ceníku ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Postup zobrazení informací o fakturaci pomocí rozhraní API ceníku ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

V aktuálním otevřeném fakturačním cyklu nebo období služby můžete také zobrazit odhadované ceny pro jakoukoli spotřebu Azure nebo službu Marketplace pro spotřebu na webu Marketplace.

Chcete-li zobrazit odhadované ceny pro služby spotřeby s rozhraním API ceníku ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Chcete-li zobrazit odhadované ceny pro služby spotřeby s rozhraním API ceníku ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Rozhraní API ceníku zákaznické smlouvy od Microsoftu jsou *asynchronní rozhraní REST API*. Odezvy pro rozhraní API se změnily ze starších synchronních rozhraní API. Změnil se také text odpovědi rozhraní API.

#### <a name="old-response-body"></a>Původní tělo odpovědi

Tady je příklad synchronní REST API odpovědi:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nové tělo odpovědi

Rozhraní API podporují [asynchronní formát REST Azure](../../azure-resource-manager/management/async-operations.md) . Volání rozhraní API pomocí GET a obdržíte následující odpověď:

```
No Response Body

HTTP Status 202 Accepted
```

Následující hlavičky jsou odesílány s umístění výstupu:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Zajistěte další volání GET do umístění. Odpověď na volání metody GET je stejná, dokud operace nedosáhne stavu dokončení nebo selhání. Po dokončení vrátí odpověď na umístění volání GET adresu URL pro stahování. Stejně, jako kdyby byla operace provedena ve stejnou dobu. Tady je příklad:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Klient může také provést volání metody GET pro `Azure-AsyncOperation`. Koncový bod vrátí stav operace.

V následující tabulce jsou uvedena pole v seznamu starší verze Enterprise získat ceník. Obsahuje odpovídající pole v novém ceníku pro smlouvy o zákaznících Microsoftu:

| Stará vlastnost | Nová vlastnost | Poznámky |
| --- | --- | --- |
| billingPeriodId  | _Nelze použít_ | Není k dispozici. U zákaznických smluv od Microsoftu se fakturovaná a přidružená cenová stránka nahradila konceptem billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Přesné řetězcové hodnoty se mohou lišit. |
| includedQuantity  | includedQuantity | Neplatí pro služby v zákaznických smlouvách Microsoftu. |
| partNumber  | _Nelze použít_ | Místo toho použijte kombinaci productOrderName (totéž jako hodnotami OfferId) a meterID. |
| unitPrice  | unitPrice | Jednotková cena se vztahuje na služby spotřebované v zákaznických smlouvách Microsoftu. |
| currencyCode  | pricingCurrency | Zákaznické smlouvy Microsoftu obsahují cenové reprezentace v cenové měně a v měně fakturace. CurrencyCode odpovídá pricingCurrencyům v rámci smluv o zákaznících Microsoftu. |
| Hodnotami OfferId | productOrderName | Místo hodnotami OfferId můžete použít productOrderName, ale není totéž jako hodnotami OfferId. ProductOrderName a měřič však určují ceny v rámci smluv o zákaznících Microsoftu, které se týkají meterId a hodnotami OfferId při starších registraci. |

## <a name="consumption-price-sheet-api-operations"></a>Provozní operace rozhraní API cenového listu spotřeby

V případě smluv Enterprise jste v rámci předplatného nebo fakturačního období použili operace [získat](/rest/api/consumption/pricesheet/get) a získat podle ceny pro účely [fakturačního období](/rest/api/consumption/pricesheet/getbybillingperiod) pro obor. Rozhraní API používá ověřování pomocí správy prostředků Azure.

Chcete-li získat informace ceníku pro obor s rozhraním API ceníku:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Chcete-li získat informace o ceníku podle fakturačního období pomocí rozhraní API ceníku:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Místo výše uvedených koncových bodů rozhraní API použijte pro smlouvy Microsoft Customer Agreement následující ty:

**Rozhraní API ceníku pro smlouvy o zákaznících Microsoftu (asynchronní REST API)**

Toto rozhraní API je pro zákaznické smlouvy Microsoftu a poskytuje další atributy.

**Ceník pro obor fakturačního profilu ve fakturačním účtu**

Toto rozhraní API je existující rozhraní API. Byla aktualizována, aby poskytovala ceník pro fakturační profil ve fakturačním účtu.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Ceník pro obor podle fakturačního účtu

Ověřování Azure Resource Manager se používá při získání ceníku v oboru registrace ve fakturačním účtu.

Postup získání ceníku na účtu pro registraci ve fakturačním účtu:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Pro smlouvu o zákaznících Microsoftu použijte informace v následující části. Poskytuje vlastnosti polí používané pro smlouvy o zákaznících Microsoftu.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Ceník pro obor fakturačního profilu ve fakturačním účtu

Aktualizovaný ceník pomocí rozhraní API fakturačního účtu získá ceník ve formátu CSV. Pro získání ceníku v oboru fakturačního profilu pro MCA:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

V oboru registrace EA je odpověď rozhraní API a vlastnosti identické. Vlastnosti odpovídají stejným vlastnostem MCA.

Starší vlastnosti pro [Azure Resource Manager rozhraní API ceníku](/rest/api/consumption/pricesheet) a stejné nové vlastnosti jsou v následující tabulce.

| Stará vlastnost rozhraní API ceníku Azure Resource Manager  | Nová vlastnost rozhraní API ceníku zákaznické smlouvy Microsoftu   | Popis |
| --- | --- | --- |
| ID měření | _meterId_ | Jedinečný identifikátor měřiče Stejné jako meterID. |
| Název měřiče | meterName | Název měřiče. Měřič představuje prostředek nasazení služby Azure. |
| Kategorie měřiče  | služba | Název klasifikační kategorie měřiče. Stejné jako služba v ceníku zákaznických smluv společnosti Microsoft. Přesné řetězcové hodnoty se liší. |
| Podkategorie měřiče | meterSubCategory | Název kategorie dílčí klasifikace měřiče. Na základě klasifikace rozlišení sady funkcí vysoké úrovně v rámci služby. Například základní databáze SQL databáze vs Standard SQL DB. |
| Oblast měřiče | meterRegion | &nbsp;  |
| Jednotka | _Nelze použít_ | Dá se analyzovat z unitOfMeasure. |
| Jednotka měření | unitOfMeasure | &nbsp;  |
| Číslo součásti | _Nelze použít_ | Místo části číslo použijte productOrderName a MeterID k jedinečné identifikaci ceny pro fakturační profil. Pole jsou uvedena na faktuře MCA místo číslo součásti v části faktury MCA. |
| Jednotková cena | unitPrice | Cena za jednotku zákaznických smluv Microsoftu |
| Kód měny | pricingCurrency | Smlouvy o zákaznících Microsoftu představuje ceny v cenové měně a v měně fakturace. Kód měny je stejný jako pricingCurrency v zákaznických smlouvách Microsoftu. |
| Zahrnuté množství | includedQuantity | Neplatí pro služby v zákaznických smlouvách Microsoftu. Zobrazit s hodnotami nula |
|  ID nabídky  | productOrderName | Místo hodnotami OfferId použijte productOrderName. To není stejné jako hodnotami OfferId, ale productOrderName a měřič určují ceny v zákaznických smlouvách Microsoftu. Související s meterId a hodnotami OfferId ve starších registraci. |

Cena za smlouvy Microsoft Customer Agreement je definována odlišně než u smluv Enterprise. Cena za služby v podnikovém zápisu je jedinečná pro produkt, číslo dílu, měřič a nabídku. Číslo součásti se nepoužívá v zákaznických smlouvách Microsoftu.

Cena služby spotřeba za Azure, která je součástí smlouvy o zákaznících Microsoftu, je jedinečná pro productOrderName a meterID. Představují měřič služby a plán produktu.

Pokud chcete sjednotit ceny mezi ceníkem a využitím v rozhraní API s podrobnostmi využití, můžete použít productOrderName a meterID.

Ceník můžou stáhnout uživatelé s právy vlastník fakturačního profilu, přispěvatel, čtenář a správce fakturace.

Ceník zahrnuje ceny za služby, jejichž ceny vycházejí z využití. Mezi tyto služby patří využití Azure a využití Marketplace. Poslední cena na konci každého období služby se uzamkne a použije se k využití v rámci jedné období služby. Pro služby Azure spotřeber je období služby obvykle kalendářní měsíc.

### <a name="retired-price-sheet-api-fields"></a>Vyřazená pole APLIKAČNÍho ceníku

Následující pole nejsou k dispozici v rozhraních API ceníku zákaznické smlouvy společnosti Microsoft ani mají stejná pole.

|Vyřazené pole| Popis|
|---|---|
| billingPeriodId | Nelze použít. Odpovídá InvoiceId pro MCA. |
| Hodnotami OfferId | Není k dispozici. Odpovídá productOrderName v MCA. |
| meterCategory  | Není k dispozici. Odpovídá službě v MCA. |
| jednotka | Není k dispozici. Dá se analyzovat z unitOfMeasure. |
| currencyCode | Stejné jako pricingCurrency v MCA. |
| meterLocation | Stejné jako meterRegion v MCA. |
| partNumber partnumber | Nelze použít, protože číslo součásti není uvedeno v části faktury MCA. Místo čísla součásti použijte kombinaci meterId a productOrderName k jednoznačné identifikaci cen. |
| totalIncludedQuantity | Není k dispozici. |
| pretaxStandardRate  | Není k dispozici. |

## <a name="reservation-instance-charge-api-replaced"></a>Nahradilo se rozhraní API pro účtování instance rezervace.

Fakturační transakce pro nákupy rezervací můžete získat pomocí [rozhraní API pro poplatky za rezervované instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Nové rozhraní API zahrnuje všechny nákupy, včetně nabídek na webu Marketplace třetích stran. Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request). Rozhraní API pro poplatky za rezervované instance nahrazuje rozhraní API transakcí.

Získání transakcí nákupu rezervací pomocí rozhraní API pro transakce:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Nahrazená rozhraní API doporučení

Rozhraní API doporučení pro nákup rezervovaných instancí poskytují využití virtuálních počítačů za posledních 7, 30 nebo 60 dnů. Rozhraní API také poskytují doporučení k nákupu rezervací. Patří mezi ně:

- [Rozhraní API pro doporučení sdílené rezervované instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Rozhraní API doporučení pro jednu rezervovanou instanci](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request). Rozhraní API doporučení pro rezervaci uvedená výše jsou nahrazena rozhraním API [Microsoft. spotřeb/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) .

Postup získání doporučení pro rezervaci pomocí rozhraní API pro rezervaci s doporučeními:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Rozhraní API využití rezervace nahrazeno

Můžete získat využití rezervace v registraci pomocí rozhraní Využití rezervovaných instancí API. Pokud je v registraci více než jedna rezervovaná instance, můžete pomocí tohoto rozhraní API získat také využití všech rezervovaných nákupů instancí.

Patří mezi ně:

- [Podrobnosti Využití rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Souhrn Využití rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Všechna rozhraní API pro vydanou spotřebu se nahrazují nativními rozhraními API Azure, která pro ověřování a autorizaci používají Azure AD. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme s REST](/rest/api/azure/#create-the-request). Rozhraní API doporučení pro rezervaci uvedená výše jsou nahrazena rozhraními API [Microsoft. spotřeb/reservationDetails](/rest/api/consumption/reservationsdetails) a [Microsoft. spotřeb/reservationSummaries](/rest/api/consumption/reservationssummaries) .

Získání podrobností o rezervacích pomocí rozhraní API s podrobnostmi rezervací:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Postup získání souhrnů rezervací pomocí rozhraní API souhrnů rezervací:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Přesunutí z Cloudyn na Cost Management

Organizace, které používají [Cloudyn](https://cloudyn.com) , by měly začít používat [Azure cost management](https://azure.microsoft.com/services/cost-management/) pro potřeby nákladů na správu. Cost Management je k dispozici v Azure Portal bez zaregistrování a latence při osmi hodinách. Další informace najdete v dokumentaci k [cost management](../index.yml).

Pomocí služby Azure Cost Management můžete:

- Zobrazení nákladů v průběhu času s předdefinovaným rozpočtem. Analyzujte denní vzorce nákladů, abyste identifikovali a zastavili nákladové anomálie. Snížit náklady podle značek, skupiny prostředků, služby a umístění.
- Vytvořte rozpočty pro nastavení omezení využití a nákladů a při přístupu k důležitým prahovým hodnotám Buďte upozorněni. Nastavte automatizaci pomocí skupin akcí, abyste aktivovali vlastní události a vynutili vámi nastavená pevná omezení.
- Optimalizujte náklady a využití pomocí doporučení od Azure Advisor. Objevte optimalizace nákupu s rezervacemi, klidnějších nepoužitými virtuálními počítači a odstraňujte nepoužívané prostředky, abyste zůstali v rámci rozpočtů.
- Naplánujte export nákladů a využití a exportujte soubor CSV do účtu úložiště denně. Automatizujte integraci s externími systémy a udržujte účtovací data v synchronizovaných a aktuálním stavu.

## <a name="power-bi-integration"></a>Integrace Power BI

Pro vytváření sestav nákladů můžete použít také Power BI. [Konektor Azure cost management](/power-bi/desktop-connect-azure-cost-management) pro Power BI Desktop můžete použít k vytváření výkonných, přizpůsobených sestav, které vám pomůžou lépe porozumět útratám Azure. Konektor Azure Cost Management aktuálně podporuje zákazníky se smlouvou o zákaznících Microsoftu nebo smlouva Enterprise (EA).

## <a name="next-steps"></a>Další kroky

- Přečtěte si [dokumentaci cost management](../index.yml) , kde se dozvíte, jak monitorovat a řídit výdaje na Azure. Nebo, pokud chcete optimalizovat využití prostředků pomocí Cost Management.
