---
title: Rozhraní API pro fakturaci Azure podle smlouvy Enterprise
description: Přečtěte si o rozhraních API pro generování sestav, která umožňuje zákazníkům se smlouvou Azure Enterprise programově získávat data týkající se spotřeby.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: a1c420eed89b7b45ea6c50345737b8615f39ad8c
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602077"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Přehled rozhraní API pro vytváření sestav pro podnikové zákazníky

> [!Note]
> Microsoft už neaktualizuje rozhraní API služby Enterprise Reporting pro fakturaci Azure. Místo toho byste měli využívat rozhraní API služby [Azure Consumption](/rest/api/consumption).

Rozhraní API pro generování sestav umožňují podnikovým zákazníkům Azure programově předávat data o spotřebě a fakturaci do upřednostňovaných nástrojů pro analýzu dat. Podnikoví zákazníci uzavřeli s Azure [smlouvu Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), ve které si vyjednali Azure Prepayment (dřív označovaný jako peněžní závazek) a která jim poskytuje přístup k vlastním cenám prostředků Azure.

Všechny parametry data a času požadované pro rozhraní API musí být reprezentované jako kombinované hodnoty koordinovaného světového času (UTC). Hodnoty vrácené rozhraními API se zobrazují ve formátu UTC.

## <a name="enabling-data-access-to-the-api"></a>Povolení přístupu k datům v rozhraní API
* **Vygenerování nebo načtení klíče rozhraní API** – přihlaste se na web Enterprise Portal, přejděte na položky Sestavy > Stažení dat o využití > Přístupový klíč rozhraní API a vygenerujte nebo stáhněte klíč rozhraní API.
* **Předání klíčů do rozhraní API** – klíč rozhraní API je potřeba předat při každém volání kvůli ověřování a autorizaci. V hlavičkách protokolu HTTP se musí nacházet následující vlastnost.

|Klíč v hlavičce požadavku | Hodnota|
|-|-|
|Autorizace| Zadejte hodnotu v tomto formátu: **bearer {KLÍČ_API}** . <br/> Příklad: bearer eyr....09|

## <a name="consumption-based-apis"></a>Rozhraní API na základě spotřeby
Pro níže popsaná rozhraní API je [tady](https://consumption.azure.com/swagger/ui/index) dostupný koncový bod Swagger, který by měl umožňovat snadnou introspekci rozhraní API a měl by poskytovat možnost generovat klientské sady SDK pomocí nástroje [AutoRest](https://github.com/Azure/AutoRest) nebo [Swagger CodeGen](https://swagger.io/swagger-codegen/). Prostřednictvím tohoto rozhraní API jsou dostupná data od 1. května 2014.

* **Zůstatek a souhrn:** [Rozhraní API Zůstatek a souhrn](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) nabízí měsíční přehled informací o zůstatcích, nových nákupech, poplatcích ve službě Azure Marketplace, úpravách a poplatcích za nadlimitní využití.

* **Podrobnosti využití:** [Rozhraní API Podrobnosti využití](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) poskytuje denní rozpis spotřebovaných prostředků a odhadované poplatky za určitou smlouvu. Výsledek obsahuje také informace o instancích, měřičích a odděleních. Na rozhraní API je možné zadávat dotazy podle fakturačního období nebo podle zadaného počátečního a koncového data.

* **Poplatky za obchod Marketplace:** [Rozhraní API Poplatky za obchod Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) vrací rozpis poplatků vzniklých v obchodě na základě využití po jednotlivých dnech, a to pro zadané fakturační období nebo konkrétní počáteční a koncové datum (nezahrnuje jednorázové poplatky).

* **Ceník:** [Rozhraní API Ceník](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) poskytuje platnou sazbu každého měřiče pro danou smlouvu a fakturační období.

* **Podrobnosti o rezervované instanci:** [Rozhraní API Podrobnosti o rezervované instanci](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) vrací informace o využití nákupů rezervovaných instancí. [Rozhraní API Podrobnosti o rezervované instanci](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) uvádí provedené fakturační transakce.

## <a name="data-freshness"></a>Aktuálnost dat
Na dotazy na všechna výše uvedená rozhraní API se vrací značky ETag. Změna značky ETag znamená, že došlo k aktualizaci dat.  V následných voláních stejného rozhraní API s využitím stejných parametrů předejte v hlavičce požadavku HTTP získanou značku ETag s klíčem „If-None-Match“. Pokud už se data dál neaktualizovala, bude mít odpověď stavový kód „NotModified“ a nevrátí se žádná data. Pokud došlo ke změně značky ETag, vrátí rozhraní API celou datovou sadu za požadované období.

## <a name="helper-apis"></a>Pomocná rozhraní API
 **Seznam fakturačních období:** [Rozhraní API Fakturační období](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) vrací seznam fakturačních období, která obsahují data o spotřebě pro danou smlouvu, a to v obráceném chronologickém pořadí. Každé období obsahuje vlastnost ukazující na trasu rozhraní API pro čtyři sady dat: BalanceSummary, UsageDetails, MarketplaceCharges a PriceSheet.


## <a name="api-response-codes"></a>Kódy odpovědí rozhraní API   
|Stavový kód odpovědi|Zpráva|Popis|
|-|-|-|
|200| OK|Bez chyby|
|400| Chybný požadavek| Neplatné parametry – rozsahy dat, čísla smluv EA atd.|
|401| Neautorizováno| Klíč rozhraní API se nedá najít, je neplatný, vypršela jeho platnost atd.|
|404| Neaktivní| Nenašel se koncový bod sestavy.|
|429 | TooManyRequests | Požadavek se omezil. Po uplynutí časového limitu zadaného v hlavičce <code>x-ms-ratelimit-microsoft.consumption-retry-after</code> zkuste operaci zopakovat.|
|500| Chyba serveru| Neočekávaná chyba při zpracování žádosti|
| 503 | ServiceUnavailable | Služba je dočasně nedostupná. Po uplynutí časového limitu zadaného v hlavičce <code>Retry-After</code> zkuste operaci zopakovat.|
