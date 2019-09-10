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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70392854"
---
**Objem shromažďování dat a jejich uchovávání** 

| Vrstva | Limit za den | Uchovávání dat | Komentář |
|:---|:---|:---|:---|
| Cenová úroveň aktuální za GB<br>(představený 2018. dubna) | Bez omezení | 30-730 dní | Pro další poplatky je dostupné uchovávání dat přesahující 31 dní. Přečtěte si další informace o cenách Azure Monitor. |
| Starší úrovně Free<br>(představený 2016. dubna) | 500 MB | 7 dní | Když váš pracovní prostor dosáhne limitu 500 MB za den, příjem dat se zastaví a obnoví se na začátku dalšího dne. Den vychází ze standardu UTC. Všimněte si, že data shromážděná pomocí Azure Security Center nejsou zahrnutá do tohoto limitu 500 MB za den a budou se dál shromažďovat nad tímto limitem.  |
| Starší verze samostatné na GB úrovně<br>(představený 2016. dubna) | Bez omezení | 30 až 730 dnů | Pro další poplatky je dostupné uchovávání dat přesahující 31 dní. Přečtěte si další informace o cenách Azure Monitor. |
| Starší verze na uzel (OMS)<br>(představený 2016. dubna) | Bez omezení | 30 až 730 dnů | Pro další poplatky je dostupné uchovávání dat přesahující 31 dní. Přečtěte si další informace o cenách Azure Monitor. |
| Starší verze úrovně Standard | Bez omezení | 30 dní  | Uchování se nedá upravit. |
| Starší verze úrovně Premium | Bez omezení | 365 dní  | Uchování se nedá upravit. |

**Počet pracovních prostorů na předplatné**

| Cenová úroveň    | Limit pracovního prostoru | Komentáře
|:---|:---|:---|
| Úroveň Free  | 10 | Toto omezení nejde zvýšit. |
| Všechny ostatní úrovně | Bez omezení | Omezíte počet prostředků v rámci skupiny prostředků a počet skupin prostředků na předplatné. |

**Azure Portal**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet záznamů vrácených dotazem protokolu | 10,000 | Zmenšete výsledky pomocí rozsahu dotazu, časového rozsahu a filtrů v dotazu. |


**Rozhraní API kolekce dat**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální velikost pro jeden příspěvek | 30 MB | Rozdělte větší svazky na více příspěvků. |
| Maximální velikost pro hodnoty polí  | 32 KB | Pole delší než 32 kB se oříznou. |

**Rozhraní API pro hledání**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet záznamů vrácených v jednom dotazu | 500,000 | |
| Maximální velikost vrácených dat | 64 000 000 bajtů (~ 61 MiB)| |
| Maximální doba běhu dotazu | 10 minut | Podrobnosti najdete v podrobnostech o [časových limitech](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Maximální počet požadavků | 200 požadavků na uživatele nebo IP adresu klienta AAD za 30 sekund | Podrobnosti najdete v tématu [omezení přenosové rychlosti](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Obecné limity pracovního prostoru**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet sloupců v tabulce         | 500 | |
| Maximální počet znaků pro název sloupce | 500 | |
| Oblasti na kapacitě | Západní střed USA | V tuto chvíli nemůžete v této oblasti vytvořit nový pracovní prostor, protože se jedná o dočasný limit kapacity. Toto omezení se plánuje až do konce října 2019. |
| Export dat | Momentálně není k dispozici | Pomocí funkce Azure Functions nebo aplikace logiky můžete agregovat a exportovat data. | 

>[!NOTE]
>V závislosti na tom, jak dlouho jste používali Log Analytics, je možné, že budete mít přístup ke starším cenovým úrovním. Další informace o [cenových úrovních Log Analytics starších verzích](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 