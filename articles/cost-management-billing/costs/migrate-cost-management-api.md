---
title: Migrace z rozhraní API podle smlouvy EA na rozhraní API podle smlouvy se zákazníkem Microsoftu – Azure
description: Tento článek vám pomůže porozumět důsledkům migrace smlouvy Microsoft Enterprise (EA) na smlouvu se zákazníkem Microsoftu (MCA).
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.openlocfilehash: 46ad81f6723d160bf1d675b68a8459dd8df32c80
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078345"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrace z rozhraní API podle smlouvy Enterprise (EA) na rozhraní API podle smlouvy se zákazníkem Microsoftu (MCA)

Tento článek vám pomůže pochopit rozdíly ve struktuře dat a rozhraních API a další odlišnosti v integraci systémů mezi účtem založeným na smlouvě Enterprise (EA) a účtem založeným na smlouvě se zákazníkem Microsoftu (MCA). Azure Cost Management podporuje rozhraní API pro oba typy účtů. Než budete pokračovat, přečtěte si článek [Vysvětlení fakturačních účtů pro smlouvu se zákazníkem Microsoftu](../manage/mca-setup-account.md).

Organizace, které už mají účet EA, by měly z tohoto článku vycházet při nastavování účtu MCA. Už dříve bylo při obnovování účtu EA třeba provést několik kroků pro zajištění přechodu z původní registrace do nové. Šlo o minimální objem práce. Migrace na účet MCA ale vyžaduje o něco větší úsilí. Je to kvůli změnám v základním fakturačním subsystému, které mají dopad na všechna rozhraní API související s náklady a na nabídky služeb.

## <a name="mca-apis-and-integration"></a>Rozhraní API a integrace pro MCA

Rozhraní API pro MCA a nová integrace vám umožní:

- Zajistit plnou dostupnost rozhraní API prostřednictvím nativních rozhraní API v Azure
- Nakonfigurovat více faktur v rámci jednoho fakturačního účtu
- Přistupovat ke kombinovanému rozhraní API pro využití služeb Azure, využití webu Marketplace ve prospěch třetích stran a nákupy na webu Marketplace
- Zobrazovat náklady napříč fakturačními profily (stejnými jako registrace) přes Azure Cost Management
- Přistupovat k novým rozhraním API pro zobrazování nákladů, pro varování, když náklady překročí předdefinované prahové hodnoty, a pro automatický export nezpracovaných údajů

## <a name="migration-checklist"></a>Kontrolní seznam pro migraci

Následující seznam vám usnadní přechod na rozhraní API podle MCA.

- Seznamte se s novým [fakturačním účtem podle smlouvy se zákazníkem Microsoftu](../understand/mca-overview.md).
- Zjistěte, která rozhraní API používáte, a v následující části si ověřte, která z nich budou nahrazena.
- Seznamte se s [rozhraními REST API pro Azure Resource Manager](/rest/api/azure).
- Pokud rozhraní API pro Azure Resource Manager ještě nepoužíváte, [zaregistrujte svou klientskou aplikaci v Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aktualizujte veškerý naprogramovaný kód tak, aby [používal ověřování Azure AD](/rest/api/azure/#create-the-request).
- Aktualizujte veškerý naprogramovaný kód tak, aby volání rozhraní API podle EA byla nahrazena voláními rozhraní API podle MCA.
- Aktualizujte zpracování chyb tak, aby se začaly používat nové kódy chyb.
- Projděte si další nabídky integrace, jako je Cloudyn a Power BI, a ověřte, jestli případně není nutné provést další akce.

## <a name="ea-apis-replaced-with-mca-apis"></a>Rozhraní API podle EA nahrazená rozhraními API podle MCA

Rozhraní API podle EA používají pro ověření a autorizaci klíč rozhraní API. Rozhraní API podle MCA používají ověření přes Azure AD.

| Účel | Rozhraní API podle EA | Rozhraní API podle MCA |
| --- | --- | --- |
| Zůstatek a kredity | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Využití (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Využití (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Využití Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Fakturační období | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Ceník | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. faktura/billingAccounts/billingProfiles/pricesheet/default/download Format = JSON \| CSV Microsoft. fakturace/billingAccounts/.../billingProfiles/.............. /pricesheet/default/download Format = JSON \| CSV Microsoft. fakturace/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Nákupy rezervací | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Doporučení pro rezervace | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Využití rezervací | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Využití služeb Azure a využití webu Marketplace ve prospěch třetích stran jsou k dispozici v [rozhraní API pro podrobnosti o využití](/rest/api/consumption/usagedetails).

Pro fakturační účty MCA jsou k dispozici následující rozhraní API:

| Účel | Rozhraní API podle smlouvy se zákazníkem Microsoftu (MCA) |
| --- | --- |
| Fakturační účty<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Fakturační profily<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Oddíly faktury<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faktury | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Fakturace předplatných | {scope}/billingSubscriptions |

<sup>2</sup> Rozhraní API vrací seznamy objektů, což jsou obory, v nichž operují funkce služby Cost Management na webu Azure Portal a v rozhraních API. Další informace o oborech služby Cost Management najdete v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).

Pokud už používáte nějaká rozhraní API podle EA, musíte je aktualizovat tak, aby podporovala fakturační účty MCA. Následující tabulka uvádí další změny integrace:

| Účel | Původní nabídka | Nová nabídka |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Balíček obsahu a konektor [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |  [Konektor Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Rozhraní API pro načtení zůstatku a kreditů

Rozhraní API pro [získání souhrnu zůstatků](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) poskytuje měsíční shrnutí následujících údajů:

- Zůstatky
- Nové nákupy
- Poplatky za služby Azure Marketplace
- Úpravy
- Poplatky za nadlimitní využití služby

Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request).

Rozhraní API pro získání souhrnu zůstatků je nahrazeno rozhraním API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Jak získat dostupné zůstatky pomocí příslušného rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Rozhraní API pro získání nákladů a využití

Pomocí následujících rozhraní API získáte denní rozpis nákladů na využití služeb Azure a údaje o využití Marketplace ve prospěch třetích stran a dalších nákupech na webu Marketplace. Následující samostatná rozhraní API pro služby Azure a využití webu Marketplace ve prospěch třetích stran byla sloučena. Původní rozhraní API jsou nahrazena rozhraním API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Přibyly v něm nákupy na webu Marketplace, které se dříve zobrazovaly jen v souhrnu zůstatků do určitého data.

- [Získat podrobnosti o využití/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Získat podrobnosti o využití/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Získat podrobnosti o využití/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Získat podrobnosti o využití/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Získat poplatky v obchodě Marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [ Marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request).

Všechna předchozí rozhraní API jsou nahrazena rozhraním API Consumption/Usage Details.

Jak získat podrobnosti o využití pomocí příslušného rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Rozhraní API pro podrobnosti o využití, stejně jako všechna rozhraní služby Cost Management, je k dispozici pro více oborů. U fakturovaných nákladů, které byste tradičně získávali na úrovni registrace, použijte obor Fakturační profil.  Další informace o oborech služby Cost Management najdete v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).

| Typ | Formát ID |
| --- | --- |
| Fakturační účet | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Fakturační profil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Předplatné | `/subscriptions/{subscriptionId}` |
| Skupina prostředků | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

K aktualizaci naprogramovaného kódu použijte následující parametry querystring.

| Původní parametry | Nové parametry |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nepodporováno |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Změnilo se také tělo odpovědi.

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

Název vlastnosti obsahující pole záznamů o využití se změnilo z hodnoty „data“ na _value_. Každý záznam dříve zahrnoval plochý seznam podrobných vlastností. Teď jsou u každého záznamu všechny podrobnosti, s výjimkou značek, ve vnořené vlastnosti s názvem _properties_. Nová struktura je konzistentní s jinými rozhraními API v Azure. Změnily se názvy některých vlastností. V následující tabulce jsou uvedeny odpovídající vlastnosti.

| Původní vlastnost | Nová vlastnost | Poznámky |
| --- | --- | --- |
| AccountId | – | Tvůrce předplatného se nesleduje. Použijte invoiceSectionId (totéž jako departmentId). |
| AccountNameAccountOwnerId a AccountOwnerEmail | – | Tvůrce předplatného se nesleduje. Použijte invoiceSectionName (totéž jako departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Všimněte si, že tyto vlastnosti představují opak. Pokud má isAzureCreditEnabled hodnotu true, bude mít ChargesBilledSeparately hodnotu false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | Přesné řetězcové hodnoty se můžou lišit. |
| ConsumedServiceId | Žádné | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date a usageStartDate | date | &nbsp;  |
| Den | Žádné | Parsuje den z data. |
| DepartmentId | invoiceSectionId | Přesné hodnoty se liší. |
| DepartmentName | invoiceSectionName | Přesné řetězcové hodnoty se můžou lišit. Oddíly faktur můžete v případě potřeby nakonfigurovat podle oddělení. |
| ExtendedCost a Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | Žádné | &nbsp;  |
| Umístění | location | &nbsp;  |
| MeterCategory | meterCategory | Přesné řetězcové hodnoty se můžou lišit. |
| MeterId | meterId | Přesné hodnoty se liší. |
| MeterName | meterName | Přesné řetězcové hodnoty se můžou lišit. |
| MeterRegion | meterRegion | Přesné řetězcové hodnoty se můžou lišit. |
| MeterSubCategory | meterSubCategory | Přesné řetězcové hodnoty se můžou lišit. |
| Month (Měsíc) | Žádné | Parsuje měsíc z data. |
| Název nabídky | Žádné | Použijte publisherName a productOrderName. |
| OfferID | Žádné | &nbsp;  |
| Číslo objednávky | Žádné | &nbsp;  |
| PartNumber | Žádné | Použijte meterId a productOrderName k jednoznačné identifikaci cen. |
| Název plánu | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ProductId | productId | Přesné hodnoty se liší. |
| Název vydavatele | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Přesné hodnoty se liší. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Žádné | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | – | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Přesné řetězcové hodnoty se můžou lišit. |
| ServiceTier | meterSubCategory | Přesné řetězcové hodnoty se můžou lišit. |
| StoreServiceIdentifier | – | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Značky | tags | Vlastnost tags se vztahuje na kořenový objekt, ne na vnořenou vlastnost properties. |
| UnitOfMeasure | unitOfMeasure | Přesné hodnoty se liší. |
| usageEndDate | date | &nbsp;  |
| Year (Rok) | Žádné | Parsuje rok z data. |
| (nové) | billingCurrency | Měna použitá pro poplatek |
| (nové) | billingProfileId | Jedinečný identifikátor fakturačního profilu (totéž jako registrace) |
| (nové) | billingProfileName | Název fakturačního profilu (totéž jako registrace) |
| (nové) | chargeType | Slouží k odlišení využití služeb Azure, využití webu Marketplace a nákupů. |
| (nové) | invoiceId | Jedinečné ID pro fakturu. Prázdné pro aktuální, otevřený měsíc. |
| (nové) | publisherType | Typ vydavatele u nákupů. Prázdné pro využití. |
| (nové) | serviceFamily | Typ nákupu. Prázdné pro využití. |
| (nové) | servicePeriodEndDate | Koncové datum nakupované služby. |
| (nové) | servicePeriodStartDate | Počáteční datum nakupované služby. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Rozhraní API pro fakturační období nahrazeno rozhraním API pro faktury

Fakturační účty podle MCA nevyužívají klasická fakturační období. Místo toho na základě faktur rozdělují náklady na specifická fakturační období. [Rozhraní API pro fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) je nahrazeno rozhraním API pro faktury. Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request).

Jak získat faktury pomocí příslušného rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Rozhraní API pro ceníky

Tato část se zabývá existujícími rozhraními API pro ceníky a přináší doporučení pro přechod na rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu. Popisuje také rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu a vysvětluje význam polí v těchto cenících. Rozhraní API [pro získání ceníku](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) a [pro získání fakturačních období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) podle smlouvy Enterprise jsou nahrazena rozhraním API pro ceníky podle smlouvy se zákazníkem Microsoftu (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). Pokud jde o asynchronní formáty REST, podporuje nové rozhraní API formáty JSON i CSV. Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Rozhraní API pro fakturaci podle smlouvy Enterprise

U registrací podle smlouvy Enterprise jste používali rozhraní API pro získávání informací o cenách a fakturačních obdobích. Pro ověřování a autorizaci se používaly webové tokeny Azure Active Directory.

Jak získat platné ceny pro zadanou registraci podle smlouvy Enterprise pomocí rozhraní API pro ceníky a fakturační období:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu

Pomocí rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu můžete zobrazit ceny pro všechny služby zahrnující spotřebu Azure a spotřebu Marketplace. Ceny zobrazené pro fakturační profil platí pro všechna předplatná, která spadají pod tento profil.

Pomocí rozhraní API pro ceníky si můžete všechny informace z ceníku týkající se spotřeby služeb Azure zobrazit ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Pomocí rozhraní API pro ceníky si můžete všechny informace z ceníku týkající se spotřeby služeb Azure zobrazit ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Při použití tohoto rozhraní API získáte ceník pro celý účet. Můžete ale získat i zhuštěnou verzi ceníku ve formátu PDF. Tento souhrn zahrnuje služby spotřebu služeb Azure a spotřebu na webu Marketplace, která je vyúčtována na konkrétní faktuře. Faktura je identifikována číslem {invoiceId}, které se shoduje s **číslem faktury** zobrazeným v souborech PDF se souhrnem faktury. Tady je příklad.

![Obrázek znázorňující příklad čísla faktury, které odpovídá položce InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Jak pomocí rozhraní API pro ceníky zobrazit informace o fakturaci ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Jak pomocí rozhraní API pro ceníky zobrazit informace o fakturaci ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

V aktuálním otevřeném fakturačním cyklu nebo období služby můžete také zobrazit odhadované ceny pro jakoukoli spotřebu služby Azure nebo spotřebu na webu Marketplace.

Jak pomocí rozhraní API pro ceníky zobrazit odhadované ceny pro spotřebu služeb ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Jak pomocí rozhraní API pro ceníky zobrazit odhadované ceny pro spotřebu služeb ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu představují *asynchronní rozhraní REST API*. Oproti starším synchronním rozhraním API se u těchto rozhraní API změnily odpovědi. Změnilo se také tělo odpovědi rozhraní API.

#### <a name="old-response-body"></a>Původní tělo odpovědi

Tady je příklad odpovědi synchronního rozhraní REST API:

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

Tato rozhraní API podporují [asynchronní formát Azure REST](../../azure-resource-manager/management/async-operations.md). Pokud zavoláte rozhraní API pomocí příkazu GET, obdržíte následující odpověď:

```
No Response Body

HTTP Status 202 Accepted
```

Odešlou se následující hlavičky s umístěním výstupu:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Nastavte jiný příkaz GET, aby volal do tohoto umístění. Odpověď na volání GET bude stejná, dokud operace nedosáhne stavu dokončení nebo selhání. Po dokončení se jako odpověď na volání GET do umístění vrátí adresa URL pro stahování – jako kdyby tato operace proběhla ve stejnou dobu. Tady je příklad:

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

Klient může také provést volání GET pro `Azure-AsyncOperation`. Koncový bod vrátí stav operace.

Následující tabulka uvádí pole ve starším rozhraní API pro získání ceníku podle smlouvy Enterprise. Zahrnuje odpovídající pole v novém rozhraní pro ceníky podle smlouvy se zákazníkem Microsoftu:

| Původní vlastnost | Nová vlastnost | Poznámky |
| --- | --- | --- |
| billingPeriodId  | _Nelze použít_ | Neužívá se. U smlouvy se zákazníkem Microsoftu je koncept billingPeriodId nahrazen fakturou a přidruženým ceníkem. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Přesné řetězcové hodnoty se můžou lišit. |
| includedQuantity  | includedQuantity | Neplatí pro služby na základě smlouvy se zákazníkem Microsoftu. |
| partNumber  | _Nelze použít_ | Místo toho použijte kombinaci productOrderName (totéž jako offerID) a meterID. |
| unitPrice  | unitPrice | Jednotková cena se vztahuje na služby spotřebované na základě smlouvy se zákazníkem Microsoftu. |
| currencyCode  | pricingCurrency | Smlouvy se zákazníkem Microsoftu zahrnují ceny uvedené v měně pro stanovení ceny i v měně fakturace. Položka currencyCode odpovídá položce pricingCurrency ve smlouvě se zákazníkem Microsoftu. |
| offerID | productOrderName | Místo hodnoty OfferId můžete použít productOrderName, ale není to totéž jako OfferId. U smluv se zákazníkem Microsoftu ale ceny určují položky productOrderName a meter, které odpovídají hodnotám meterId a OfferId ve starších registracích. |

## <a name="consumption-price-sheet-api-operations"></a>Operace rozhraní API pro ceníky spotřeby

U smluv Enterprise jste v rozhraní API pro ceníky spotřeby používali operaci [Get](/rest/api/consumption/pricesheet/get) a operaci [pro získání dat podle fakturačního období](/rest/api/consumption/pricesheet/getbybillingperiod), a to pro obor stanovený hodnotou subscriptionId nebo fakturačním obdobím. Toto rozhraní API používá ověřování přes Azure Resource Management.

Jak pomocí rozhraní API pro ceníky získat ceníkové údaje pro konkrétní obor:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Jak pomocí rozhraní API pro ceníky získat ceníkové údaje podle fakturačních období:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Místo výše uvedených koncových bodů rozhraní API použijte u smluv se zákazníkem Microsoftu následující položky:

**Rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu (asynchronní rozhraní REST API)**

Toto rozhraní API je určeno pro smlouvy se zákazníkem Microsoftu a poskytuje další atributy.

**Ceník pro obor fakturačního profilu ve fakturačním účtu**

Toto je existující rozhraní API. Bylo aktualizováno, aby poskytovalo ceník pro fakturační profil v rámci fakturačního účtu.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Ceník pro určitý obor podle fakturačního účtu

V případě získávání ceníku pro obor Registrace v rámci fakturačního účtu se používá ověřování přes Azure Resource Manager.

Jak získat ceník na úrovni registračního účtu v rámci fakturačního účtu:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

V případě smlouvy se zákazníkem Microsoftu uplatněte informace uvedené v následující části. Uvádí vlastnosti polí používané pro smlouvy se zákazníkem Microsoftu.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Ceník pro obor Fakturační profil v rámci fakturačního účtu

Aktualizované rozhraní API pro ceník podle fakturačních účtů načítá ceník ve formátu CSV. Jak získat ceník pro obor Fakturační profil pro MCA:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

V oboru registrace EA se odpověď rozhraní API a vlastnosti shodují. Vlastnosti odpovídají stejným vlastnostem MCA.

Starší vlastnosti z [rozhraní API pro ceníky v rámci Azure Resource Manageru](/rest/api/consumption/pricesheet) a odpovídající nové vlastnosti najdete v následující tabulce.

| Původní vlastnost rozhraní API pro ceníky v rámci Azure Resource Manageru  | Nová vlastnost rozhraní API pro ceníky podle smlouvy se zákazníkem Microsoftu   | Description |
| --- | --- | --- |
| Meter ID | _meterId_ | Jedinečný identifikátor měřiče Stejné jako meterID. |
| Název měřiče | meterName | Název měřiče. Měřič představuje nasaditelný prostředek služby Azure. |
| Kategorie měřiče  | service | Název klasifikační kategorie měřiče. Totéž jako služba v ceníku v rámci smlouvy se zákazníkem Microsoftu. Přesné hodnoty se liší. |
| Podkategorie měřiče | meterSubCategory | Název kategorie dílčí klasifikace měřiče. Na základě klasifikace pro rozlišení sad funkcí na nejvyšší úrovni v rámci služby. Například SQL Database úrovně Basic oproti SQL Database úrovně Standard. |
| Oblast měřiče | meterRegion | &nbsp;  |
| Jednotka | _Nelze použít_ | Lze parsovat z položky unitOfMeasure. |
| Jednotka měření | unitOfMeasure | &nbsp;  |
| Číslo části | _Nelze použít_ | Místo čísla části se k jedinečné identifikaci ceny pro fakturační profil používají položky productOrderName a MeterID. Tato pole jsou na faktuře MCA uvedena místo čísla části. |
| Jednotková cena | unitPrice | Jednotková cena podle smlouvy se zákazníkem Microsoftu |
| Kód měny | pricingCurrency | Smlouvy se zákazníkem Microsoftu uvádějí ceny v měně pro stanovení ceny i v měně fakturace. Kód měny se shoduje s položkou pricingCurrency ve smlouvě se zákazníkem Microsoftu. |
| Zahrnuté množství | includedQuantity | Neplatí pro služby v rámci smlouvy se zákazníkem Microsoftu. Zobrazí se i s nulovými hodnotami. |
|  ID nabídky  | productOrderName | Místo hodnoty OfferId se používá productOrderName. Neshoduje se s hodnotou OfferID, ale u smluv se zákazníkem Microsoftu ceny určují položky productOrderName a meter. Ty odpovídají hodnotám meterId a OfferId ve starších registracích. |

Cena u smluv se zákazníkem Microsoftu je definována jinak než u smluv Enterprise. Cena za služby v registraci podle smlouvy Enterprise jedinečně vyplývá z produktu, čísla části, měřiče a nabídky. Číslo části se u smluv se zákazníkem Microsoftu nepoužívá.

Cena za spotřebu služeb Azure, která spadá pod smlouvu se zákazníkem Microsoftu, jedinečně vyplývá z hodnot productOrderName a meterID. Ty představují měřič služby a plán produktu.

Pokud chcete porovnat ceník a využití v rozhraní API pro podrobnosti o využití, můžete použít hodnoty productOrderName a meterID.

Ceník si můžou stáhnout uživatelé s oprávněním vlastníka fakturačního profilu, přispěvatele, čtenáře a správce fakturace.

Ceník zahrnuje ceny služeb účtovaných podle využití. Patří sem spotřeba služeb Azure a spotřeba na webu Marketplace. Nejaktuálnější cena na konci každého období je uzamčená a vztahuje se na využití v rámci jednoho období. Pokud jde o spotřebu služeb Azure, účtuje se obvykle po kalendářních měsících.

### <a name="retired-price-sheet-api-fields"></a>Vyřazená pole v rozhraní API pro ceníky

Následující pole buď nejsou v rozhraních API pro ceníky podle smlouvy se zákazníkem Microsoftu k dispozici, nebo je nahradila obdobná pole.

|Vyřazené pole| Description|
|---|---|
| billingPeriodId | Neužívá se. Odpovídá položce InvoiceId u MCA. |
| offerID | Neužívá se. Odpovídá položce productOrderName u MCA. |
| meterCategory  | Neužívá se. Odpovídá položce Service u MCA. |
| unit | Neužívá se. Lze parsovat z položky unitOfMeasure. |
| currencyCode | Totéž jako položka pricingCurrency u MCA. |
| meterLocation | Totéž jako položka meterRegion u MCA. |
| partNumber partnumber | Neužívá se, protože číslo části se na fakturách podle MCA neuvádí. Místo čísla části se k jedinečné identifikaci cen používá kombinace položek meterID a productOrderName. |
| totalIncludedQuantity | Neužívá se. |
| pretaxStandardRate  | Neužívá se. |

## <a name="reservation-instance-charge-api-replaced"></a>Nahrazení rozhraní API pro poplatky za rezervované instance

Fakturované transakce spojené s nákupy rezervací můžete získat pomocí [rozhraní API pro poplatky za rezervované instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Nové rozhraní API zahrnuje všechny nákupy, včetně nabídek třetích stran na webu Marketplace. Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request). Rozhraní API pro poplatky za rezervované instance je nahrazeno rozhraním API pro transakce.

Jak získat transakce spojené s nákupem rezervací pomocí rozhraní API pro transakce:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Nahrazení rozhraní API pro doporučení

Rozhraní API pro doporučení k nákupu rezervovaných instancí poskytují údaje o využití virtuálních počítačů za posledních 7, 30 nebo 60 dnů. Rozhraní API také nabízejí doporučení k nákupu rezervací. Mezi ně patří:

- [Rozhraní API pro doporučení sdílených rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Rozhraní API pro doporučení jednotlivých rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request). Výše uvedená rozhraní API pro doporučení rezervací jsou nahrazena rozhraním API [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Jak získat doporučení rezervací pomocí příslušného rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Nahrazení rozhraní API pro využití rezervací

Údaje o využití rezervací v registraci můžete získat pomocí rozhraní API pro využití rezervovaných instancí. Pokud je v registraci více než jedna rezervovaná instance, můžete pomocí tohoto rozhraní API získat i údaje o využití všech nákupů rezervovaných instancí.

Mezi ně patří:

- [Podrobnosti Využití rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Souhrn využití rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Všechna rozhraní API pro spotřebu budou nahrazena nativními rozhraními API v Azure, která pro ověřování a autorizaci využívají Azure AD. Další informace o volání rozhraní Azure REST API najdete v článku [Začínáme s REST API](/rest/api/azure/#create-the-request). Výše uvedená rozhraní API pro doporučení rezervací jsou nahrazena rozhraním API [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) a [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Jak získat podrobnosti o rezervacích pomocí příslušného rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Jak získat souhrny rezervací pomocí příslušného rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Přechod ze systému Cloudyn na Cost Management

Organizace, které využívají [Cloudyn](https://cloudyn.com), by měly pro veškeré potřeby v oblasti správy nákladů začít používat [Azure Cost Management](https://azure.microsoft.com/services/cost-management/). Cost Management je na webu Azure Portal k dispozici bez onboardingu, a to po uplynutí 8 hodin od zadání požadavku. Další informace najdete v [dokumentaci k řešení Cost Management](../index.yml).

Azure Cost Management nabízí tyto možnosti:

- Průběžné sledování nákladů v porovnání s předdefinovaným rozpočtem. Analýzy denních schémat nabíhání nákladů s cílem identifikovat a eliminovat anomálie ve výdajích. Rozepsání nákladů podle značek, skupin prostředků, služeb a míst.
- Vytváření rozpočtů omezujících využití a náklady a nastavení výstrah při přiblížení k důležitým prahovým hodnotám. Nastavení automatizace pomocí skupin akcí, které budou aktivovat vlastní události a vynucovat pevně stanovené limity podle vámi stanovených podmínek.
- Optimalizace nákladů a využití na základě doporučení, které poskytuje Azure Advisor. Odhalování možností optimalizace nákupů prostřednictvím rezervací, vyřazování nepoužívaných virtuálních počítačů a odstraňování nepoužívaných prostředků, aby byly dodrženy rozpočty.
- Naplánování každodenního exportu dat o nákladech a využití a publikování tohoto souboru CSV na účtu úložiště. Automatizace integrace s externími systémy a udržování fakturačních dat v synchronizovaném a aktuálním stavu.

## <a name="power-bi-integration"></a>Integrace Power BI

Pro vykazování nákladů můžete použít také Power BI. Díky [konektoru řešení Azure Cost Management](/power-bi/desktop-connect-azure-cost-management) pro Power BI Desktop můžete vytvářet efektivní a přizpůsobené sestavy, které vám pomůžou lépe porozumět vašim výdajům v Azure. Tento konektor aktuálně podporuje držitele smlouvy se zákazníkem Microsoftu (MCA) i smlouvy Enterprise (EA).

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak monitorovat a řídit výdaje v Azure, najdete v [dokumentaci ke službě Cost Management](../index.yml). Najdete tam také informace o optimalizaci využití prostředků pomocí služby Cost Management.
