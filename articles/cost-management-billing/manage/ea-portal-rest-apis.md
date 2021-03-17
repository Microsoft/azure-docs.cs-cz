---
title: Rozhraní Azure Enterprise REST API
description: Tento článek popisuje rozhraní REST API, která se používají v souvislosti s registrací do Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 01/21/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 1fdf64053a55eb33d80ed461c231e8c6dd84d63b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677727"
---
# <a name="azure-enterprise-rest-apis"></a>Rozhraní Azure Enterprise REST API

Tento článek popisuje rozhraní REST API, která se používají v souvislosti s registrací do Azure Enterprise. Vysvětluje také, jak řešit běžné problémy s rozhraními REST API.

## <a name="consumption-and-usage-apis"></a>Rozhraní API pro spotřebu a využití

Zákazníci Microsoft Enterprise Azure mohou prostřednictvím rozhraní REST API získávat informace o využití a fakturaci. Vlastník role (podnikový správce, správce oddělení, vlastník účtu) musí přístup k rozhraní API umožnit vygenerováním klíče z portálu Azure EA. Pak může přístup k datům prostřednictvím rozhraní API získat kdokoli, komu bude poskytnuto číslo registrace a tento klíč.

### <a name="available-apis"></a>Dostupná rozhraní API

**Zůstatek a souhrn:** Toto [rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) poskytuje měsíční přehled informací o zůstatcích, nových nákupech, poplatcích ve službě Azure Marketplace, úpravách a poplatcích za nadlimitní využití. Další informace najdete v tématu [Rozhraní API pro vytváření sestav pro podnikové zákazníky – Zůstatek a souhrn](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary).

**Podrobnosti o využití:** Toto [rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) poskytuje denní rozpis spotřebovaných prostředků a odhadované poplatky podle registrace. Výsledek obsahuje také informace o instancích, měřičích a odděleních. Na rozhraní API můžete zadávat dotazy podle fakturačního období nebo podle konkrétního počátečního a koncového data. Další informace najdete v tématu [Rozhraní API pro vytváření sestav pro podnikové zákazníky – Podrobnosti o využití](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

**Poplatek obchodu Marketplace Store:** Toto [rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) vrací poplatky vzniklé v obchodě na základě využití, v rozpisu po dnech, a to pro zadané fakturační období nebo konkrétní počáteční a koncové datum. Další informace najdete v tématu [Rozhraní API pro vytváření sestav pro podnikové zákazníky – Poplatek obchodu Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge).

**Ceník:** Toto [rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) poskytuje platnou sazbu každého měřiče pro danou registraci a fakturační období. Další informace najdete v tématu [Rozhraní API pro vytváření sestav pro podnikové zákazníky – Ceník](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet).

**Fakturační období:** Toto [rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) vrací seznam fakturačních období, která obsahují data o spotřebě u dané registrace, a to v obráceném chronologickém pořadí. Každé období obsahuje vlastnost ukazující na trasu rozhraní API pro čtyři sady dat: BalanceSummary, UsageDetails, MarketplaceCharges a PriceSheet. Další informace najdete v tématu [Rozhraní API pro vytváření sestav pro podnikové zákazníky – Fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

### <a name="enable-api-data-access"></a>Povolení přístupu k datům rozhraní API

Vlastníci rolí mohou na portálu Azure EA provádět níže uvedené akce. Nejprve přejdou na **Reports** (Sestavy)  > **Download Usage** (Stažení dat o využití)  > **API Access Key** (Přístupový klíč rozhraní API). A pak mají tyto možnosti:

- Generování primárních a sekundárních přístupových klíčů
- Zakázání přístupových klíčů
- Zobrazení počátečního a koncového data přístupových klíčů

### <a name="generate-or-retrieve-the-api-key"></a>Vygenerování nebo načtení klíče rozhraní API

1. Přihlaste se jako podnikový správce.
2. V levém navigačním okně klikněte na **Reports** (Sestavy) a pak na kartu **Download Usage** (Stažení dat o využití).
3. Klikněte na **API Access Key** (Přístupový klíč rozhraní API).
4. V části **Enrollment Access Keys** (Přístupové klíče pro registraci) vyberte symbol pro vygenerování klíče a vygenerujte primární nebo sekundární klíč.
5. Pokud chcete zobrazit celý vygenerovaný přístupový klíč pro rozhraní API, vyberte **Expand Key** (Rozbalit klíč).
6. Vyberte **Copy** (Kopírovat) a budete moct přístupový klíč rozhraní API okamžitě použít.

![Příklad zobrazující stránku s přístupovým klíčem rozhraní API](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Pokud chcete přístupové klíče rozhraní API poskytnout uživatelům, kteří nejsou podnikovými správci v dané registraci, proveďte následující kroky:

1. V levém navigačním panelu klikněte na **Manage** (Spravovat).
2. Klikněte na symbol tužky vedle **DA view charges** (Správce oddělení může zobrazit náklady).
3. Vyberte **Enable** (Povolit) a potom klikněte na **Save** (Uložit).
4. Klikněte na symbol tužky vedle **AO view charges** (Vlastník účtu může zobrazit náklady).
5. Vyberte **Enable** (Povolit) a potom klikněte na **Save** (Uložit).

![Příklad znázorňující povolené možnosti DA view charges a AO view charges](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) Předchozí kroky poskytnou držitelům přístupového klíče rozhraní API přístup k informacím o nákladech a cenách v sestavách využití.

### <a name="pass-keys-in-the-api"></a>Předávání klíčů v rozhraní API

Klíč rozhraní API je potřeba předat při každém volání kvůli ověření a autorizaci. Do hlaviček protokolu HTTP je třeba vložit následující vlastnost:

| Klíč v hlavičce požadavku | Hodnota |
| --- | --- |
| Autorizace | Zadejte hodnotu v tomto formátu: **bearer {KLÍČ\_API}** .
Příklad: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Koncový bod Swagger je k dispozici v [následujících rozhraních API pro vytváření sestav v podnicích verze 3](https://consumption.azure.com/swagger/ui/index). Swagger usnadňuje kontrolu rozhraní API. Pomocí Swaggeru můžete vygenerovat klientské sady SDK [AutoRest](https://github.com/Azure/AutoRest) nebo [Swagger CodeGen](https://swagger.io/swagger-codegen/). Prostřednictvím rozhraní API jsou k dispozici data od 1. května 2014.

### <a name="api-response-codes"></a>Kódy odpovědí rozhraní API

Pokud používáte rozhraní API, budou se zobrazovat kódy stavu odpovědí. Jsou popsány v následující tabulce.

| Stavový kód odpovědi | Zpráva | Popis |
| --- | --- | --- |
| 200 | OK | Bez chyby |
| 401 | Neautorizováno | Klíč rozhraní API se nedá najít, je neplatný, vypršela jeho platnost atd. |
| 404 | Neaktivní | Nenašel se koncový bod sestavy. |
| 400 | Nesprávná žádost | Neplatné parametry – rozsahy dat, čísla smluv EA atd. |
| 500 | Chyba serveru | Neočekávaná chyba při zpracování žádosti |

### <a name="usage-and-billing-data-update-frequency"></a>Frekvence aktualizace dat o využití a fakturačních dat

Data o využití a fakturační data se pro aktuální fakturační měsíc aktualizují každých 24 hodin. Může ale docházet ke zpožďování dat v rozsahu až 3 dnů. Pokud například k využití dojde v pondělí, data se v datovém souboru nemusí zobrazit až do čtvrtka.

### <a name="azure-service-catalog"></a>Katalog služeb Azure

Všechny služby Azure se publikují do katalogu ve formátu CSV na blogu k úložišti Azure. Katalog je užitečný v případě, že potřebujete sestavit kurátorovaný seznam všech služeb Azure pro váš systém. Aktuální katalog je v [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv) .

### <a name="csv-data-file-details"></a>Podrobnosti o datovém souboru CSV

V následující části jsou popsány vlastnosti sestav rozhraní API.

#### <a name="usage-summary"></a>Souhrn využití

Ze sestavy CSV se vygenerují data ve formátu JSON. Výsledný formát proto stejný jako souhrnný formát CSV. Název sloupce je spojený, takže byste ho při spotřebě souhrnných dat JSON měli provést deserializovat do datové tabulky.

| Název sloupce CSV | Název sloupce JSON | Nový sloupec JSON | Komentář |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Název účtu | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Název předplatného | SubscriptionName | SubscriptionName |   |
| Datum | Datum | Datum | Zobrazuje datum, kdy byla spuštěna sestava katalogu služeb. Formátem je řetězec data bez časového razítka. |
| Month | Month | Month |   |
| Den | Den | Den |   |
| Year | Year | Year |   |
| Product | BillableItemName | Product |   |
| Meter ID | ResourceGUID | MeterId |   |
| Kategorie měření | Služba | MeterCategory | Užitečné při hledání služeb. Relevantní pro služby, které mají více položek ServiceType. Například Virtual Machines. |
| Meter Sub-Category | ServiceType | MeterSubCategory | Poskytuje druhou úroveň podrobností pro službu. Například Virtuální počítač A1 (ne Windows).  |
| Meter Region | ServiceRegion | MeterRegion | Třetí úroveň podrobností požadovaných pro službu. Užitečné při hledání kontextu oblasti pro ResourceGUID. |
| Meter Name | ServiceResource | MeterName | Název služby |
| Consumed Quantity | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Resource Location | ServiceSubRegion | ResourceLocation |   |
| Consumed Service | ServiceInfo | ConsumedService |   |
| Instance ID | Součást | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Značky | Značky | Značky |   |
| Store Service Identifier   | OrderNumber | StoreServiceIdentifier   |   |
| Department Name | DepartmentName | DepartmentName |   |
| Cost Center | CostCenter | CostCenter |   |
| Jednotka měření | UnitOfMeasure | UnitOfMeasure | Příklady hodnot: hodiny, GB, události, nabízení, jednotka, Hodiny za jednotku, MB, denní jednotky |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Sestava pro Azure Marketplace

| Název sloupce CSV | Název sloupce JSON | Nový sloupec JSON |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Název účtu | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Název předplatného | SubscriptionName |  SubscriptionName |
| Date (Datum) | BillingCycle |  Date (jen datový řetězec bez časového razítka)
| Month | Month |  Month |
| Den | Den |  Den |
| Year | Year |  Year |
| Meter ID | MeterResourceId |  MeterId |
| Název vydavatele | PublisherFriendlyName |  Název vydavatele |
| Název nabídky | OfferFriendlyName |  OfferName |
| Název plánu | PlanFriendlyName |  PlanName |
| Consumed Quantity | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Jednotka měření | UnitOfMeasure | UnitOfMeasure |
| Instance ID | InstanceId | InstanceId |
| Další informace | AdditionalInfo | AdditionalInfo |
| Značky | Značky | Značky |
| Číslo objednávky | OrderNumber | OrderNumber |
| Department Name | DepartmentNames | DepartmentName |
| Cost Center | CostCenters |  CostCenter |
| Skupina prostředků | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Ceník

| Název sloupce CSV | Název sloupce JSON | Komentář |
| --- | --- | --- |
| Služba | Služba |  Žádná změna ceny |
| Jednotka měření | UnitOfMeasure |   |
| Číslo nadlimitní položky | ConsumptionPartNumber |   |
| Cena nadlimitní položky | ConsumptionPrice |   |
| Kód měny | CurrencyCode |     |

### <a name="common-api-issues"></a>Běžné problémy s rozhraními API

Když používáte rozhraní Azure Enterprise REST API, můžete se setkat s následujícími běžnými problémy.

Možná jste se pokusili použít klíč rozhraní API, který nemá správný typ autorizace. Klíč rozhraní API může vygenerovat:

- Podnikový správce
- Správce oddělení (DA)
- Vlastník účtu (AO)

Klíč vygenerovaný podnikovým správcem (EA) poskytuje přístup ke všem informacím pro danou registraci. Klíč rozhraní API nemůže vygenerovat správce EA s oprávněním jen pro čtení.

Klíč vygenerovaný správcem DA nebo AO neposkytuje přístup k informacím o zůstatcích, poplatcích a ceníku.

Platnost klíče rozhraní API vyprší každých šest měsíců. Pokud platnost vypršela, budete ho muset vygenerovat znovu.

Pokud se zobrazí chyba kvůli časovému limitu, můžete ji vyřešit zvýšením tohoto limitu.

Může se zobrazit chyba 401 (Neautorizováno). K této chybě obvykle dochází, když vyprší platnost klíče. Pokud vypršela platnost klíče, můžete ho znovu vygenerovat.

Pokud pro vybraný rozsah dat nejsou k dispozici žádná aktuální data, může volání rozhraní API vrátit chyby 400 a 404 (Není k dispozici). K této chybě může dojít například proto, že byl v nedávné době zahájen přenos registrace. Data od určitého data se teď nacházejí v nové registraci. K chybě může dojít také v případě, že použijete nové číslo registrace k načtení informací, které se nacházejí ve staré registraci.

## <a name="next-steps"></a>Další kroky

- Správci na portálu Azure EA by si měli přečíst téma [Správa portálu Azure EA](ea-portal-administration.md), kde se seznámí s běžnými úlohami správy.
- Pokud potřebujete pomoc při řešení potíží s portálem Azure EA, přečtěte si téma [Řešení potíží s přístupem k portálu Azure EA](ea-portal-troubleshoot.md).