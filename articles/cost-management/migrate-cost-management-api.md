---
title: Migrace z smlouvy Enterprise do rozhraní API smlouvy zákazníků společnosti Microsoft – Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže pochopit důsledky migrace na Microsoft zákaznické smlouvy Microsoft Enterprise Agreement (EA).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 9f5ea30c458954a3e87b9575b5632fbfb0df97b8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318596"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migraci na smlouvu Enterprise pro smlouvy zákazníků společnosti Microsoft

Tento článek vám pomůže pochopit strukturu dat, rozhraní API a další systémové integrace rozdíly mezi účty Enterprise Agreement (EA) a smlouvy zákazníka Microsoftu (MCA). Azure Cost Management podporuje rozhraní API pro oba typy účtů. Zkontrolujte [nastavení fakturační účet pro](../billing/billing-mca-setup-account.md) článku smlouvy zákazníka se společností Microsoft před pokračováním.

Organizace s existujícím účtem EA by měl k tomuto článku ve spojení s nastavíte účet MCA. Prodlužuje se platnost účtu EA dříve, vyžaduje minimální úkony přesunout z původní registrace do nové. Migrace na účet MCA ale vyžaduje další úsilí. Další úsilí je z důvodu změn v podkladové fakturační subsystému to mít vliv na všechny související náklady na rozhraní API a nabídky služeb.

## <a name="mca-apis-and-integration"></a>Rozhraní API MCA a integrace

Rozhraní API MCA a nově zavedené integraci vám umožní:

- Máte kompletní dostupnost rozhraní API pomocí nativních rozhraní API Azure.
- V jedné fakturační účet nakonfigurujte více faktur.
- Přístup k kombinované rozhraní API se využití služeb Azure, třetích stran využití webu Marketplace a nákupy na Marketplace.
- Zobrazení nákladů napříč fakturace profily (stejné jako registrací) pomocí Azure Cost Management.
- Přístup k nové rozhraní API pro zobrazení nákladů, nechte se informovat náklady překročit předdefinovaným prahovým hodnotám a export nezpracovaných dat automaticky.

## <a name="migration-checklist"></a>Kontrolní seznam pro migraci

V následujících položek nápovědy přechod do MCA rozhraní API.

- Seznamte se s novými [Microsoft na základě smlouvy fakturační účet](../billing/billing-mca-overview.md).
- Určete, které rozhraní API můžete použít a zobrazit ty, které nahrazují v následující části.
- Seznamte se s [rozhraní REST API Azure Resource Manageru](/rest/api/azure).
- Pokud ještě není používáte rozhraní API Azure Resource Manageru, [registrace vaší klientské aplikace v Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aktualizace kódu na [ověřování pomocí služby Azure AD](/rest/api/azure/#create-the-request).
- Aktualizace kódu nahraďte volání rozhraní API MCA volání rozhraní API EA.
- Aktualizace pro zpracování použít nové chybové kódy chyb.
- Zkontrolujte doplňkové integraci nabídky, jako Cloudyn a Power BI pro jiné potřebné akce.

## <a name="ea-apis-replaced-with-mca-apis"></a>Rozhraní EA API nahradí MCA rozhraní API

Rozhraní EA API pomocí klíče rozhraní API pro ověřování a autorizaci. MCA rozhraní API pomocí ověřování Azure AD.

| Účel | EA API | MCA API |
| --- | --- | --- |
| Zůstatek a kreditech | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Využití (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Využití (CSV) | [/ usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) [ /usagedetails/odeslání](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Využití webu Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Fakturační období | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Ceník | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Formát Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download = json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... Formát /pricesheet/default/download = json|CSV Microsoft.Billing/billingAccounts/... / billingProfiles /.. /providers/Microsoft.consumption/pricesheets/download  |
| Nákup rezervace | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Doporučení pro rezervaci | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Využití rezervace | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> služby azure a využití webu Marketplace třetích stran jsou k dispozici [podrobnosti o použití rozhraní API](/rest/api/consumption/usagedetails).

Následující rozhraní API jsou k dispozici pro fakturační účty MCA:

| Účel | Rozhraní API smlouvy (MCA) zákazníků společnosti Microsoft |
| --- | --- |
| Fakturace účty<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Profily fakturace<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Části faktury<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Faktury | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Fakturace předplatného | {scope}/billingSubscriptions |

<sup>2</sup> rozhraní API vrátí seznam objektů, které jsou obory, kde prostředí pro správu nákladů na webu Azure Portal a rozhraní API pro provoz. Další informace o oborech Cost Management najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

Pokud použijete všechny existující rozhraní API EA, musíte je pro fakturační účty MCA aktualizovat. Následující tabulka uvádí další změny integrace:

| Účel | Staré nabídky | Nová nabídka |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) obsah balíčku a konektoru | [Aplikace Microsoft Azure Consumption Insights Power BI](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) a [ konektoru Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Rozhraní API pro získání zůstatek a kredity

[Získání souhrnu Zůstatek](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) rozhraní API vám dává měsíční přehled:

- Zůstatky
- Nové nákupy
- Poplatky za služby Azure Marketplace
- Úpravy
- Poplatky za nadlimitní využití služby

Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request).

Rozhraní API získat souhrn zůstatek nahrazuje rozhraní API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Pokud chcete získat dostupné zůstatky dostupná rozhraní API pro vyrovnávat:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Rozhraní API pro získání nákladů a využití

Získejte denní rozpis nákladů od využití služeb Azure, využití webu Marketplace třetích stran a další nákupy na Marketplace s následující rozhraní API. Následující rozhraní API samostatné byly sloučeny pro využití webu Marketplace třetích stran a služeb Azure. Staré rozhraní API jsou nahrazené [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) rozhraní API. Přidá nákupy na Marketplace, které byly dříve zobrazí pouze v zůstatek souhrn k datu.

- [Získat podrobnosti o využití a stahování](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Získání podrobností/odeslat využití](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Získat podrobnosti o využití/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Získat podrobnosti o využití/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Získat úložiště marketplace poplatek/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Získat úložiště marketplace poplatek/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request).

Předchozí rozhraní API se nahrazuje rozhraní API využití nebo použití podrobností.

Pokud chcete získat podrobnosti o použití rozhraní API pro využití podrobnosti:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Podrobnosti o použití rozhraní API, stejně jako u rozhraní API pro správu všech náklady, je k dispozici v několika oborech. Invoiced náklady jako by se tradičně dostat na úrovni registrace, použijte fakturační oboru profilu.  Další informace o oborech Cost Management najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

| Type | Formát ID |
| --- | --- |
| Fakturační účet | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Fakturační profil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Předplatné | `/subscriptions/{subscriptionId}` |
| Skupina prostředků | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Použijte následující parametry řetězce dotazu k aktualizaci kódu.

| Staré parametry | Nové parametry |
| --- | --- |
| `billingPeriod={billingPeriod}` | Nepodporuje se |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Text odpovědi změní také.

Původní text odpovědi:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nový text odpovědi:

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

Název vlastnosti obsahující pole záznamů využití změněn od dat k _hodnoty_. Každý záznam pro plochý seznam podrobné vlastnosti. Ale každý záznam teď najdete všechny podrobnosti jsou nyní ve vnořené vlastnosti s názvem _vlastnosti_, kromě značek. Nová struktura je konzistentní s ostatními rozhraními API Azure. Změnily se některé názvy vlastností. V následující tabulce jsou uvedeny odpovídající vlastnosti.

| Původní vlastnosti | Nové vlastnosti | Poznámky |
| --- | --- | --- |
| ID účtu | neuvedeno | Vytvoření odběru se nesleduje. Použijte invoiceSectionId (stejné jako departmentId). |
| AccountNameAccountOwnerId a AccountOwnerEmail | neuvedeno | Vytvoření odběru se nesleduje. Použijte invoiceSectionName (stejné jako departmentName). |
| Další informace | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Všimněte si, že tyto vlastnosti jsou opaky. Pokud isAzureCreditEnabled hodnotu true, bude ChargesBilledSeparately vracet hodnotu false. |
| Využité množství | množství | &nbsp; |
| Využívaná služba | consumedService | Přesné řetězcové hodnoty se mohou lišit. |
| ID využívané služby | Žádný | &nbsp; |
| Nákladové středisko | costCenter | &nbsp; |
| Datum a usageStartDate | date | &nbsp;  |
| Den | Žádný | Analyzuje den od data. |
| ID oddělení | invoiceSectionId | Přesné hodnoty se liší. |
| Název oddělení | invoiceSectionName | Přesné řetězcové hodnoty se mohou lišit. Části faktury tak, aby odpovídaly oddělení, nakonfigurujte v případě potřeby. |
| ExtendedCost a nákladů | costInBillingCurrency | &nbsp;  |
| ID instance | resourceId | &nbsp;  |
| Je pravidelný poplatek | Žádný | &nbsp;  |
| Umístění | location | &nbsp;  |
| Kategorie měřiče | meterCategory | Přesné řetězcové hodnoty se mohou lišit. |
| ID měřiče | meterId | Přesné řetězcové hodnoty se liší. |
| Název měřiče | meterName | Přesné řetězcové hodnoty se mohou lišit. |
| Oblast měřiče | meterRegion | Přesné řetězcové hodnoty se mohou lišit. |
| Podkategorie měřiče | meterSubCategory | Přesné řetězcové hodnoty se mohou lišit. |
| Měsíc | Žádný | Analyzuje měsíců od data. |
| Název nabídky | Žádný | Použití publisherName a productOrderName. |
| OfferId | Žádný | &nbsp;  |
| Číslo objednávky | Žádný | &nbsp;  |
| PartNumber | Žádný | Použijte meterId a productOrderName k jednoznačné identifikaci ceny. |
| Název plánu | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ID produktu | productId | Přesné řetězcové hodnoty se liší. |
| Název vydavatele | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Přesné řetězcové hodnoty se liší. |
| Umístění prostředku | resourceLocation | &nbsp;  |
| ID umístění prostředku | Žádný | &nbsp;  |
| Sazba zdroje | effectivePrice | &nbsp;  |
| ID správce služby | neuvedeno | &nbsp;  |
| Informace o službách 1 | serviceInfo1 | &nbsp;  |
| Informace o službách 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Přesné řetězcové hodnoty se mohou lišit. |
| ServiceTier | meterSubCategory | Přesné řetězcové hodnoty se mohou lišit. |
| Identifikátor služby úložiště | neuvedeno | &nbsp;  |
| GUID odběru | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Název odběru | subscriptionName | &nbsp;  |
| Značky | tags | Vlastnosti značky se vztahují na kořenový objekt, nikoli na vnořené vlastnosti. |
| Měrná jednotka | unitOfMeasure | Přesné řetězcové hodnoty se liší. |
| usageEndDate | date | &nbsp;  |
| Rok | Žádný | Analyzuje rok od data. |
| (nové)  | billingCurrency | Měna použitá pro příslušný poplatek. |
| (nové)  | billingProfileId | Jedinečné ID pro fakturační profil (stejně jako s registrací). |
| (nové)  | billingProfileName | Název fakturačního profilu (stejně jako s registrací). |
| (nové)  | chargeType | Slouží k rozlišení využití služeb Azure, využití webu Marketplace a nákupy. |
| (nové)  | invoiceId | Jedinečné ID pro fakturu. Prázdná pro aktuální, otevřete měsíce. |
| (nové)  | publisherType | Typ vydavatele pro nákupy od jiných. Prázdná pro využití. |
| (nové)  | serviceFamily | Typ nákupu. Prázdná pro využití. |
| (nové)  | servicePeriodEndDate | Datum ukončení zakoupené služby. |
| (nové)  | servicePeriodStartDate | Počáteční datum zakoupené služby. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Rozhraní API období nahrazuje faktury rozhraní API pro fakturaci

Fakturační účty MCA nepoužívejte fakturační období. Místo toho používají faktury oboru náklady na konkrétní fakturační období. [Rozhraní API pro fakturaci období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) nahrazuje rozhraní API faktury. Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request).

Pokud chcete získat faktury s rozhraním API faktury:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Rozhraní API pro ceny list

Tato část popisuje stávajících rozhraní API tabulky ceny a poskytuje doporučení pro přesun do rozhraní API tabulky ceny pro smlouvy Microsoft zákazníka. Také popisuje rozhraní API tabulky ceny pro smlouvy Microsoft zákazníka a vysvětluje polí v nepublikovaných ceníků. [Organizace získat ceník](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) a [Enterprise získáte fakturačních obdobích, která](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) jsou rozhraní API pro zákazníka smlouvu se společností Microsoft (Microsoft.Billing/billingAccounts/billingProfiles nahrazuje rozhraní API tabulky cena / pricesheet). Nové rozhraní API podporuje JSON a sdíleného svazku clusteru formáty ve formátech, asynchronní REST. Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Rozhraní API pro fakturaci Enterprise

API pro fakturaci Enterprise s podnikové registrace umožňuje získat ceny a informace o fakturačním období. Ověřování a autorizaci používat Azure Active Directory webových tokenů.

Pokud chcete získat příslušné ceny pro zadaný podnikového zápisu ceníku a fakturační období rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Ceny rozhraní API list pro smlouvy Microsoft zákazníka

Pomocí rozhraní API tabulky ceny pro smlouvy Microsoft zákazníka chcete-li zobrazit ceny pro všechny služby poskytované v konektoru Azure Consumption and Marketplace. Pro fakturační profil uvedené ceny platí pro všechna předplatná, která patří do fakturační profil.

Pomocí rozhraní API tabulky cena zobrazíte všechna data ceník služby Azure Consumption ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Zobrazíte všechna data ceník služby Azure Consumption ve formátu JSON pomocí rozhraní API tabulky Cena:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Pomocí rozhraní API vrátí ceníku pro celý účet. Ale můžete také získat zkrácenou verzi ceníku ve formátu PDF. Souhrn obsahuje konektoru Azure Consumption and Marketplace využití služby, které se účtují pro konkrétní fakturu. Faktura je identifikován InvoiceID {nebyl}, což je stejná jako **číslo faktury** uvedené v souboru PDF souhrn faktury. Tady je příklad.

![Zobrazuje číslo faktury, která odpovídá InvoiceID nebyl obrázku](./media/migrate-cost-management-api/invoicesummary.png)

Zobrazení informací faktury s rozhraním API tabulky cena ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Zobrazení informací faktury s rozhraním API tabulky cena ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Najdete v článku odhad ceny platné pro libovolnou službu Azure Consumption nebo Marketplace spotřebu v aktuálního fakturačního cyklu otevřít nebo limitu služby.

Chcete-li zobrazit odhad ceny za spotřebu služeb pomocí rozhraní API tabulky cena ve formátu CSV:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Chcete-li zobrazit odhad ceny za spotřebu služeb pomocí rozhraní API tabulky cena ve formátu JSON:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Rozhraní API Microsoft zákazníka smlouvy cena listu jsou *asynchronního rozhraní API REST*. Odpovědi na rozhraní API se změnil z starší synchronního rozhraní API. Změní také těle odpovědi rozhraní API.

#### <a name="old-response-body"></a>Původní text odpovědi

Tady je příklad synchronní odpověď rozhraní REST API:

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

#### <a name="new-response-body"></a>Nový text odpovědi

Podpora rozhraní API [REST Azure asynchronní](../azure-resource-manager/resource-manager-async-operations.md) formátu. Volání rozhraní API pomocí GET a zobrazí se následující odpověď:

```
No Response Body

HTTP Status 202 Accepted
```

Umístění výstupu se odesílají následující hlavičky:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Ujistěte se, volání do umístění jiného GET. Odpověď na volání GET je stejný, dokud operace dosaženo stavu dokončení nebo selhání. Po dokončení vrátí odpověď na místo volání GET adresu URL pro stažení. Tak, jako kdyby operace se provedl ve stejnou dobu. Tady je příklad:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

Klienta můžete také provést volání GET `Azure-AsyncOperation`. Koncový bod vrátí stav operace.

Pole v starší API Enterprise získáte seznam ceny v následující tabulce. Obsahuje odpovídající pole nového ceníku pro smlouvy Microsoft zákazníka:

| Původní vlastnosti | Nové vlastnosti | Poznámky |
| --- | --- | --- |
| billingPeriodId  | _Není k dispozici_ | Není k dispozici. Pro smlouvy Microsoft zákazníka nahradí koncept billingPeriodId faktury a přidružené ceníku. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Přesné řetězcové hodnoty se mohou lišit. |
| includedQuantity  | includedQuantity | Není k dispozici pro služby v Microsoft zákaznických smluv. |
| PartNumber  | _Není k dispozici_ | Místo toho použijte kombinaci productOrderName (stejné jako offerId) a meterid. |
| UnitPrice  | UnitPrice | Cena za jednotku platí za služby využité v Microsoft zákaznických smluv. |
| Kód měny  | pricingCurrency | Microsoft zákaznických smluv mají cenu reprezentace v cenách měn a fakturační Měna. Kód měny odpovídá pricingCurrency ve smlouvách Microsoft zákazníka. |
| offerId | productOrderName | Místo OfferId můžete použít productOrderName ale není stejná jako OfferId. Ale productOrderName a měření určit, že ceny ve smlouvách Microsoft zákazníků týkající se meterId a Offerid ve starší verzi registrace. |

## <a name="consumption-price-sheet-api-operations"></a>Operace rozhraní API tabulky cena spotřeby

Pro smlouvy Enterprise, můžete použít rozhraní API tabulky cena spotřeby [získat](/rest/api/consumption/pricesheet/get) a [získat podle fakturační období](/rest/api/consumption/pricesheet/getbybillingperiod) operací pro obor ID předplatného nebo fakturačního období. Rozhraní API pomocí ověřování Azure Resource Manageru.

Získat informace o cenách z ceníku pro obor s rozhraním API tabulky Cena:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Chcete-li získat informace o cenách z ceníku fakturační období s rozhraním API tabulky Cena:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Místo výše uvedené koncové body rozhraní API použijte následující dotazy pro smlouvy Microsoft zákazníka:

**Ceny rozhraní API list pro zákazníka smlouvu se společností Microsoft (asynchronní rozhraní REST API)**

Toto rozhraní API je pro smlouvy Microsoft zákazníků a poskytuje další atributy.

**Ceníku pro fakturační profil obor v Vytvoření fakturačního účtu**

Toto rozhraní API se stávajícím rozhraním API. Zajištění nového ceníku pro fakturační profil v fakturační účet byl aktualizován.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Ceník pro obor podle fakturační účet

Ověřování pomocí Azure Resource Manager se používá při získat ceník v oboru registrace s fakturačním účtu.

Pokud chcete získat ceník na účet pro zápis ve fakturační účet:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Zákaznické smlouvy Microsoft použijte informace v následující části. Poskytuje vlastnosti pole použité pro smlouvy Microsoft Customer.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Ceníku pro fakturační profil obor v fakturační účet

Aktualizovaný ceník fakturační účet rozhraní API získá ceníku ve formátu CSV. Pokud chcete získat ceník v oboru fakturační profil pro MCA:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

V oboru registrace EA odpověď rozhraní API a vlastnosti jsou identické. Vlastnosti odpovídají vlastnostem MCA stejné.

Starší vlastnosti [rozhraní API Azure Resource Manageru cena list](/rest/api/consumption/pricesheet) a stejné nové vlastnosti jsou v následující tabulce.

| Staré Azure Resource Manageru cena list rozhraní API vlastnost  | Vlastnosti nového rozhraní API služeb Microsoft zákazníka smlouvy cena list   | Popis |
| --- | --- | --- |
| ID měření | _MeterId_ | Jedinečný identifikátor pro měřidla. Stejné jako meterId. |
| Název měřiče | meterName | Název měřiče. Představuje nasaditelný prostředky služeb Azure. |
| Kategorie měřiče  | service | Název kategorie klasifikace pro měřidla. Stejné jako u služby Microsoft zákazníka smlouvy ceníku. Přesné řetězcové hodnoty se liší. |
| Podkategorie měřiče | meterSubCategory | Název dílčí klasifikace v rámci kategorie měřiče. Podle klasifikace rozdílů mezi sadu základních funkcí ve službě. Například základní SQL DB vs. standardní databázi SQL. |
| Oblast měřiče | meterRegion | &nbsp;  |
| Jednotka | _Není k dispozici_ | Může být analyzován z unitOfMeasure. |
| Jednotka měření | unitOfMeasure | &nbsp;  |
| Číslo části | _Není k dispozici_ | Místo partNumber použijte k jednoznačné identifikaci cena za fakturační profil productOrderName a MeterId. Na faktuře MCA místo partNumber v MCA faktury jsou uvedena pole. |
| Jednotková cena | UnitPrice | Cena za jednotku smlouvy zákazníka se společností Microsoft. |
| Kód měny | pricingCurrency | Microsoft zákaznických smluv představují ceny v měně ceny a fakturace měny. Kód měny je stejný jako pricingCurrency ve smlouvách Microsoft zákazníka. |
| Zahrnuté množství | includedQuantity | Není k dispozici ke službám ve smlouvách Microsoft zákazníka. Zobrazit s hodnotou nula. |
|  ID nabídky  | productOrderName | Namísto OfferId použijte productOrderName. Není stejný jako OfferId, productOrderName a měření zjistit ceny ve smlouvách Microsoft zákazníka. Ve starší verzi registrací souvisejících s meterId a Offerid. |

Ceny pro smlouvy Microsoft zákazníků je definován jiným způsobem než smlouvy Enterprise. Cena služby v podnikového zápisu je jedinečný pro produkt, PartNumber, měřidla a nabídky. PartNumber není používanými ve smlouvách Microsoft zákazníka.

Ceny služby Azure Consumption, který je součástí smlouvy zákazníka Microsoftu je jedinečný pro productOrderName a meterId. Představují měřiče služby a plán produktu.

K synchronizaci mezi ceníku a jeho použití v rozhraní API podrobnosti o použití, můžete použít productOrderName a meterId.

Uživatelé, kteří mají fakturace profilu vlastník, Přispěvatel, Čtenář a fakturace práva správce, můžete si stáhnout ceníku.

Ceník zahrnuje ceny za služby, jejichž cena je založená na využití. Služby zahrnují využití Azure a využití webu Marketplace. Nejnovějšími cenami na konci každé období služby je uzamčen a použít pro použití v jedné službě období. Pro služby konektoru Azure consumption limitu služby je obvykle kalendářní měsíc.

### <a name="retired-price-sheet-api-fields"></a>Vyřazeno polí listu ceny rozhraní API

Následující pole jsou buď není k dispozici v rozhraní API od Microsoftu zákazníka smlouvy cena list nebo mají stejná pole.

|Vyřazeno pole| Popis|
|---|---|
| billingPeriodId | Nejsou k dispozici. Odpovídá InvoiceID nebyl pro MCA. |
| offerId | Není k dispozici. Odpovídá productOrderName v MCA. |
| meterCategory  | Není k dispozici. Služba v MCA odpovídá. |
| jednotka | Není k dispozici. Může být analyzován z unitOfMeasure. |
| Kód měny | Stejné jako pricingCurrency v MCA. |
| meterLocation | Stejné jako meterRegion v MCA. |
| partNumber partnumber | Nedá se použít kvůli výrobní číslo není uveden ve MCA faktury. Místo partnumber použijte kombinaci meterId a productOrderName k jednoznačné identifikaci ceny. |
| totalIncludedQuantity | Není k dispozici. |
| pretaxStandardRate  | Není k dispozici. |

## <a name="reservation-instance-charge-api-replaced"></a>Rezervované Instance poplatek za rozhraní API nahradit

Můžete získat fakturační transakce pro nákup rezervace se [API poplatku za rezervované Instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Nové rozhraní API zahrnuje všechny nákupy, včetně nabídky na webu Marketplace třetích stran. Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request). Rozhraní API pro poplatek za rezervované Instance se nahrazuje rozhraní API pro transakce.

Pokud chcete získat rezervace nákupní transakce s transakcí rozhraní API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Doporučení nahrazuje rozhraní API

Rozhraní API doporučení nákupu rezervované Instance poskytují využití virtuálního počítače za posledních 7, 30 a 60 dní. Rozhraní API také poskytnout doporučení nákupu rezervace. Mezi ně patří:

- [Sdílené rezervované Instance doporučení API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Rozhraní API pro doporučování jednu rezervovanou instanci](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request). Nahrazují doporučení rezervace rozhraní API výše uvedenými [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) rozhraní API.

Pokud chcete získat doporučení rezervace rezervace rozhraní Recommendations API:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Rozhraní API využití rezervace nahradit

Můžete získat rezervace využití registrace s rozhraním API pro využití rezervovaných instancí. Pokud registrace existuje více než jednu rezervovanou instanci, můžete také získat využití všech rezervovaných instancí nákupy použitím tohoto rozhraní API.

Mezi ně patří:

- [Podrobnosti o použití rezervované Instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Souhrn využití rezervovaných instancí](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary)

Všechna rozhraní API využití jsou nahrazené nativních rozhraní API Azure, které používají Azure AD pro ověřování a autorizaci. Další informace o volání rozhraní Azure REST API najdete v tématu [Začínáme se službou REST](/rest/api/azure/#create-the-request). Nahrazují doporučení rezervace rozhraní API výše uvedenými [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) a [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) rozhraní API.

Pokud chcete získat podrobnosti o rezervaci s rozhraním API podrobnosti o rezervaci:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Pokud chcete získat rezervace souhrny přes rozhraní API souhrny rezervaci:

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Přesunout do služby Cost Management od Cloudyn

Organizace, které používají [Cloudyn](https://cloudyn.com) by měla začít používat [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) pro všechny potřeby správy nákladů. Služba Cost Management je k dispozici na webu Azure Portal s žádné zařazení do systému a latenci osmi hodin. Další informace najdete v tématu [dokumentace ke službě Cost Management](index.yml).

Ve službě Azure Cost Management můžete:

- Zobrazení nákladů v čase ve vztahu k rozpočtu předdefinované. Analýza denní náklady na modely k identifikaci a zastavit útraty anomálie. Rozdělte náklady podle značek, skupinu prostředků, služby a umístění.
- Vytvoření rozpočty si nastavit limity na využití a nákladů a nechte se informovat důležité prahové hodnoty jsou dosaženy. Nastavení služby automation s skupin akcí na vlastní události a vynucování pevných limitů podle vašich představ.
- Optimalizace nákladů a využití pomocí doporučení Azure advisoru. Zjistit optimalizace nákupu se rezervace, downsize využívané virtuální počítače a odstraňte nepoužívané prostředky držet se v mezích rozpočtu.
- Plánování nákladů a využití export dat denně publikování souboru CSV do vašeho účtu úložiště. Automatizujte integraci s externími systémy fakturačních dat udržovat synchronizované a aktuální.

## <a name="power-bi-integration"></a>Integrace Power BI

Pokud používáte Power BI pro vytváření sestav nákladů, musíte přejít na následující:

- Microsoft Azure Consumption Insights aplikace Power BI
- Klasické pracovní plochy konektoru Azure Consumption Insights


Konektor se doporučuje pro organizace, kteří požadují největší flexibilitu. Aplikace Power BI je ale také k dispozici pro rychlé nastavení.

- Nainstalujte [Power BI aplikace Microsoft Azure Consumption Insights](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Připojit pomocí konektoru Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

Starší Consumption Insights balíček obsahu a konektor pracoval na úrovni registrace. Ta vyžaduje alespoň přístup čtení. Nová aplikace Consumption Insights Power BI a nový konektor Azure Consumption Insights jsou k dispozici pro fakturační profil uživatele. Týmy, které potřebují další možnosti pro kontrolu nákladů nebo pokud chcete zobrazit náklady napříč fakturace profily používejte v [analýza nákladů](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) na webu Azure portal.

## <a name="next-steps"></a>Další postup

- Přečtěte si [dokumentace ke službě Cost Management](index.yml) se naučíte monitorovat a kontrolovat útraty Azure. Nebo, pokud chcete optimalizovat využití prostředků ve službě Cost Management.
