---
title: Rozhraní API pro přístup k datům analýzy komerčního webu Marketplace
description: Pomocí těchto rozhraní API můžete programově přistupovat k analytickým datům v partnerském centru.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: ed6d658155267ab21fd4cdd28dd50fcbb258ee90
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583905"
---
# <a name="apis-for-accessing-commercial-marketplace-analytics-data"></a>Rozhraní API pro přístup k datům analýzy komerčního webu Marketplace

Níže najdete seznam rozhraní API pro přístup k obchodním datům z komerčního tržiště a jejich související funkce.

- [Rozhraní API pro vyžádání datové sady](#dataset-pull-apis)
- [Rozhraní API pro správu dotazů](#query-management-apis)
- [Rozhraní API pro správu sestav](#report-management-apis)
- [Rozhraní API pro vyžádání zpracování sestav](#report-execution-pull-apis)

## <a name="dataset-pull-apis"></a>Rozhraní API pro vyžádání datové sady

***Tabulka 1: rozhraní API pro vyžádání datové sady***

| **Rozhraní API** | **Funkce** |
| --- | --- |
| [Získat všechny datové sady](analytics-api-get-all-datasets.md) | Získá všechny dostupné datové sady. Datové sady vypíše tabulky, sloupce, metriky a časové rozsahy. |
|||

## <a name="query-management-apis"></a>Rozhraní API pro správu dotazů

***Tabulka 2: rozhraní API pro správu dotazů***

| **Rozhraní API** | **Funkce** |
| --- | --- |
| [Vytvořit dotaz sestavy](analytics-programmatic-access.md#create-report-query-api) | Vytvoří vlastní dotazy definující datovou sadu, ze které je nutné exportovat sloupce a metriky. |
| [ZÍSKAT dotazy sestavy](analytics-api-get-report-queries.md) | Načte všechny dotazy, které jsou k dispozici pro použití v sestavách. Získá všechny systémové a uživatelsky definované dotazy ve výchozím nastavení. |
| [Odstranit dotazy sestav](analytics-api-delete-report-queries.md) | Odstraní uživatelsky definované dotazy. |
|||

## <a name="report-management-apis"></a>Rozhraní API pro správu sestav

***Tabulka 3: rozhraní API pro správu sestav***

| **Rozhraní API** | **Funkce** |
| --- | --- |
| [Vytvořit sestavu](analytics-programmatic-access.md#create-report-api) | Naplánuje spuštění dotazu v pravidelných intervalech. |
| [Vyzkoušení dotazů sestav](analytics-api-try-report-queries.md) | Provede příkaz dotazu sestavy. Vrátí jenom 10 záznamů, které může partner použít k ověření, jestli jsou data podle očekávání. |
| [Get Report](analytics-api-get-report.md) | Získejte všechny plánované sestavy. |
| [Aktualizovat sestavu](analytics-api-update-report.md) | Úprava parametru sestavy. |
| [Odstranit sestavu](analytics-api-delete-report.md) | Odstraní všechny záznamy o spuštění sestavy a sestavy. |
| [Pozastavit provádění sestav](analytics-api-pause-report-executions.md) | Pozastaví naplánované provádění sestav. |
| [Pokračovat v provádění sestav](analytics-api-resume-report-executions.md) | Obnoví plánované spuštění pozastavené sestavy. |
|||

## <a name="report-execution-pull-apis"></a>Rozhraní API pro vyžádání zpracování sestav

***Tabulka 4: vyžádané rozhraní API pro provádění sestav***

| **Rozhraní API** | **Funkce** |
| --- | --- |
| [Získat spuštění sestavy](analytics-programmatic-access.md#get-report-executions-api) | Získá všechna spuštění, ke kterým došlo pro danou sestavu. |
|||

## <a name="next-steps"></a>Další kroky

- Rozhraní API můžete vyzkoušet prostřednictvím [adresy URL rozhraní Swagger](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html).
