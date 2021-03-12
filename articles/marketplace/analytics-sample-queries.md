---
title: Ukázkové dotazy pro programové analýzy
description: Pomocí těchto ukázkových dotazů můžete programově přistupovat ke analytickým datům Microsoftu pro komerční tržišti.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583747"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Ukázkové dotazy pro programové analýzy

Tento článek popisuje Ukázkové dotazy pro obchodní objednávky Microsoft Marketplace, jejich využití a sestavy zákazníků. Tyto dotazy můžete použít voláním koncového bodu rozhraní API pro [dotaz na vytvoření sestavy](analytics-programmatic-access.md#create-report-query-api) . V případě potřeby je možné změnit volání rozhraní API pro [dotazování na vytvoření sestavy](analytics-programmatic-access.md#create-report-query-api) , aby bylo možné přidat další sloupce, upravit období výpočtu a přidat podmínky filtrování. Podporovaná časová období jsou šest měsíců (6 min), 12 měsíců (12M) a vlastní časové období.

Podrobnosti o názvech, atributech a popisech sloupců najdete v následujících tabulkách:

- [Tabulka s podrobnostmi o zákaznících](customer-dashboard.md#customer-details-table)
- [Tabulka podrobností objednávky](orders-dashboard.md#orders-details-table)
- [Tabulka s podrobnostmi o využití](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Zákazníci hlásí dotazy

Tyto ukázkové dotazy se vztahují na sestavu zákazníci.

| **Popis dotazu** | **Vzorový dotaz** |
| --- | --- |
| Aktivní zákazníci z partnera do data, které zvolíte | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Zavedení zákazníci z partnera do data, které zvolíte | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Seznam nových zákazníků z konkrétní zeměpisné doby za posledních šest měsíců | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Dotazy k sestavám využití

Tyto ukázkové dotazy se vztahují na sestavu využití.

| **Popis dotazu** | **Vzorový dotaz** |
| --- | --- |
| Normalizované využití virtuálního počítače (VM), které se účtuje jako typ licence Marketplace pro Azure za poslední 6 min | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Nezpracované využití virtuálního počítače pro "Fakturovatelné přes Azure" typ licence Marketplace za poslední 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Normalizované využití virtuálních počítačů jako typ licence "Přineste si vlastní licenci" pro poslední 6 min | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Nezpracované využití virtuálního počítače pro "Přineste si vlastní licenci" – typ licence Marketplace pro poslední 6 min | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Podle data využití, denního celkového množství normalizovaného využití a "odhadované rozšířené poplatky (PC/CC)" pro placené plány za poslední měsíc | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Podle data využití, denního celkového nezpracovaného využití a "odhadované rozšířené poplatky (PC/CC)" pro placené plány za poslední měsíc | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| V případě konkrétního názvu nabídky se jedná o normalizované využití virtuálních počítačů, které se účtuje jako typ licence Marketplace na Azure pro poslední 6 min | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Pro konkrétní název nabídky měřené využití za poslední 6 min | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Dotazy sestav objednávky

Tyto ukázkové dotazy se vztahují na sestavu objednávky.

| **Popis dotazu** | **Vzorový dotaz** |
| --- | --- |
| Sestava objednávek pro typ licence Azure jako "Enterprise" pro poslední 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Sestava objednávek pro typ licence Azure jako "průběžné platby" za poslední 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Sestava objednávek pro konkrétní název nabídky za poslední 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Sestava objednávek pro aktivní objednávky za poslední 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Sestava objednávek pro zrušené objednávky za poslední 6 min | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Další kroky

- [Rozhraní API pro přístup k datům analýzy komerčního webu Marketplace](analytics-available-apis.md)
