---
title: Běžné dotazy k programovému přístupu k analytickým datům
description: Nejčastější dotazy týkající se programového přístupu k analytickým datům v partnerském centru pro vaše nabídky komerčního tržiště.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583733"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Běžné dotazy k programovému přístupu k analytickým datům

Tento článek obsahuje nejčastější dotazy týkající se programového přístupu k analytickým datům v partnerském centru pro vaše nabídky komerčního tržiště.

## <a name="api-responses"></a>Odezvy rozhraní API

Jaké jsou různé scénáře, v rámci kterých mohu přijmout odpověď rozhraní API jinou než 200 (úspěch)?

Tato tabulka popisuje odpovědi rozhraní API a to, co dělat, když je dostanete.

| Popis chyby | Kód chyby | Řešení potíží |
| ------------ | ------------- | ------------- |
| Neautorizováno | 401 | Toto je výjimka ověřování. Ověřte správnost tokenu Azure Active Directory (Azure AD). Token Azure AD je platný po 60 minutách, po uplynutí této doby byste mohli token Azure AD znovu vygenerovat. |
| Neplatný název tabulky | 400 | Název datové sady je chybný. Znovu ověřte název datové sady voláním rozhraní API "načíst všechny datové sady". |
| Nesprávný název sloupce | 400| Název sloupce v dotazu je nesprávný. Znovu ověřte název sloupce voláním rozhraní API načíst všechny datové sady nebo použijte názvy sloupců v následujících tabulkách:<br><ul><li>[Tabulka podrobností objednávky](orders-dashboard.md#orders-details-table)</li><li>[Tabulka s podrobnostmi o využití](usage-dashboard.md#usage-details-table)</li><li>[Tabulka s podrobnostmi o zákaznících](customer-dashboard.md#customer-details-table)</li><li>[Tabulka podrobností webu Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Hodnota null nebo chybějící hodnota | 400 | V rámci datové části požadavku rozhraní API možná chybí povinné parametry. |
| Neplatné parametry sestavy | 400 | Ujistěte se, že jsou parametry sestavy správné. Například může být pro parametr vydávat hodnotu menší než 4 `RecurrenceInterval` . |
| Interval opakování musí být mezi 4 a 90. | 400 | Ujistěte se, že hodnota `RecurrenceInterval` parametru Request je mezi 4 a 90. |
| Neplatný QueryId | 400 | Znovu zkontroluje vygenerované `QueryId` . |
| Neplatné parametry sestavy pro čas vytvoření a zahájení sestavy by měly být nejméně 4 hodiny od aktuálního času UTC- | 400 | Parametr času spuštění jako součást datové části požadavku by neměl být v minulosti. Čas zahájení sestavy by měl být nejméně 4 hodiny od aktuálního času UTC. |
| Požadovaná hodnota String se nenašla. | 400 | Ověřte, zda jste aktualizovali parametry žádosti `callbackurl` nebo `format` . |
| S danými filtry se nenašla žádná položka. | 404 | Ověřte `reportID` parametr použitý v rozhraní API pro spouštění sestav. |
| Pro dané podmínky filtru se nevyskytla žádná spuštění. Překontrolujte prosím reportId nebo executionId a zkuste rozhraní API znovu po plánovaném čase spuštění sestavy. | 404 | Ujistěte se, že `reportId` je správná. Po plánovaném spuštění sestavy znovu spusťte rozhraní API, jak je uvedeno v datové části požadavku. |
| Při vytváření sestavy došlo k vnitřní chybě. ID korelace <> | 500 | Ujistěte se, že formát data pro pole ' Čas_spuštění ', "QueryStartTime" a "QueryEndTime" je správný. |
| Nedostupná služba | 500 | Pokud trvale obdržíte nedostupnou službu (chyba 5xx), vytvořte prosím [lístek podpory](support.md). |
||||

## <a name="no-records"></a>Žádné záznamy

Při stažení sestavy z zabezpečeného umístění se mi zobrazí odpověď rozhraní API 200. Proč nezískávám žádné záznamy?

Ověřte, zda řetězec v dotazu má jednu z přípustných hodnot pro záhlaví sloupce. Tento dotaz například vrátí nulové výsledky:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

V tomto příkladu jsou povolené hodnoty pro SKUBillingType placené nebo bezplatné. Všechny možné hodnoty pro různé sloupce najdete v následujících tabulkách:

- [Tabulka podrobností objednávky](orders-dashboard.md#orders-details-table)
- [Tabulka s podrobnostmi o využití](usage-dashboard.md#usage-details-table)
- [Tabulka s podrobnostmi o zákaznících](customer-dashboard.md#customer-details-table)
- [Tabulka podrobností webu Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Další kroky

- [Rozhraní API pro přístup k datům analýzy komerčního webu Marketplace](analytics-available-apis.md)
