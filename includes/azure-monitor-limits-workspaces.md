---
title: zahrnout soubor
description: zahrnout soubor
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 21e2d3f75028d239175effa7a3608cc18ccfc95c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305266"
---
**Kolekce objem a uchování dat** 

| Úroveň | Omezit za den | Uchovávání dat | Poznámka |
|:---|:---|:---|:---|
| Cenová úroveň aktuální za GB<br>(zavedena. dubna 2018) | Bez omezení | 30 - 730 dnů | Uchovávání dat nad rámec 31 dní je k dispozici pro další poplatky. Další informace o cenách služby Azure Monitor. |
| Starší verze bezplatných vrstev<br>(zavedena. dubna 2016) | 500 MB | 7 dní | 500 MB za den, limit dosáhne pracovního prostoru příjem dat se zastaví a bude pokračovat na začátku dalšího dne. Den vychází ze standardu UTC. Všimněte si, že data shromážděná službou Azure Security Center není součástí tohoto 500 MB za den, limit a bude pokračovat, které se mají shromažďovat nad tento limit.  |
| Starší verze úroveň samostatné za GB<br>(zavedena. dubna 2016) | Bez omezení | 30 do 730 dnů | Uchovávání dat nad rámec 31 dní je k dispozici pro další poplatky. Další informace o cenách služby Azure Monitor. |
| Starší verze na jeden uzel (OMS)<br>(zavedena. dubna 2016) | Bez omezení | 30 do 730 dnů | Uchovávání dat nad rámec 31 dní je k dispozici pro další poplatky. Další informace o cenách služby Azure Monitor. |
| Starší verze úrovně Standard | Bez omezení | 30 dní  | Uchovávání dat se nedá upravit. |
| Starší verze na úrovni Premium | Bez omezení | 365 dnů  | Uchovávání dat se nedá upravit. |

**Počet pracovních prostorů na předplatné.**

| Cenová úroveň    | Limit úložiště | Komentáře
|:---|:---|:---|
| Úroveň Free  | 10 | Toto omezení nejde zvýšit. |
| Všechny další úrovně | Bez omezení | Co vás omezuje počet prostředků v rámci skupiny prostředků a počtem skupin prostředků na předplatné. |

**Azure Portal**

| Category | Limits | Komentáře |
|:---|:---|:---|
| Maximální počet záznamů vrácených dotazem protokolu | 10 000 | Omezte výsledky pomocí oboru dotazu, časový rozsah a filtrů v dotazu. |


**Rozhraní API kolekce dat**

| Category | Limits | Komentáře |
|:---|:---|:---|
| Maximální velikost pro jeden příspěvek | 30 MB | Rozdělte větší svazky na více příspěvků. |
| Maximální velikost pro hodnoty pole  | 32 KB. | Pole delší než 32 kB se oříznou. |

**Rozhraní API pro vyhledávání**

| Category | Limits | Komentáře |
|:---|:---|:---|
| Maximální záznamů vrácených pro neagregovaná data | 5 000 | |
| Maximální počet záznamů pro agregovaná data | 500,000 | Agregovaná data znamenají vyhledávání, která zahrnuje `summarize` příkazu. |
| Maximální velikost dat vrácených | 64,000,000 bajtů (~ 61 MiB)| |
| Maximální dobu spuštění dotazu | 10 minut | Zobrazit [vypršení časových limitů](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) podrobnosti.  |
| Maximální frekvence požadavků | 200 požadavků za 30 sekund na IP adresu klienta nebo uživatel AAD | Zobrazit [omezení přenosové rychlosti](https://dev.loganalytics.io/documentation/Using-the-API/Limits) podrobnosti. |

**Omezení obecného pracovního prostoru**

| Category | Limits | Komentáře |
|:---|:---|:---|
| Maximální počet sloupců v tabulce         | 500 | |
| Maximální počet znaků pro název sloupce | 500 | |
| Oblasti plně využívá kapacitu | Středozápadní USA | Nelze vytvořit aktuálně nový pracovní prostor v této oblasti protože se nachází v dočasné kapacity. Toto omezení se plánuje ji adresovat podle konce září. května 2019. |
| Export dat | není aktuálně k dispozici | Agregovat a export dat pomocí funkce Azure Functions nebo Logic App. | 

>[!NOTE]
>V závislosti na tom, jak dlouho používáte Log Analytics můžete mít přístup k cenové úrovně starší verze. Další informace o [starší verze Log Analytics cenové úrovně](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 