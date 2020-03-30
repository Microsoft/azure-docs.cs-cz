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
ms.openlocfilehash: 97f265d76ac70891e9cefc0ef6651e439706ed23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334856"
---
**Objem a uchovávání dat** 

| Úroveň | Limit za den | Uchovávání dat | Poznámka |
|:---|:---|:---|:---|
| Aktuální cenová úroveň za GB<br>(zavedeno v dubnu 2018) | Bez omezení | 30 - 730 dní | Uchovávání dat po dobu 31 dnů je k dispozici za příplatek. Přečtěte si další informace o cenách Azure Monitoru. |
| Starší úrovně Zdarma<br>(zavedeno v dubnu 2016) | 500 MB | 7 dní | Když váš pracovní prostor dosáhne limitu 500 MB za den, ingestování dat se zastaví a obnoví se na začátku následujícího dne. Den vychází ze standardu UTC. Všimněte si, že data shromážděná službou Azure Security Center nejsou zahrnuta v tomto limitu 500 MB za den a budou se nadále shromažďovat nad tento limit.  |
| Starší verze samostatné úrovně na GB<br>(zavedeno v dubnu 2016) | Bez omezení | 30 až 730 dní | Uchovávání dat po dobu 31 dnů je k dispozici za příplatek. Přečtěte si další informace o cenách Azure Monitoru. |
| Starší verze na uzel (OMS)<br>(zavedeno v dubnu 2016) | Bez omezení | 30 až 730 dní | Uchovávání dat po dobu 31 dnů je k dispozici za příplatek. Přečtěte si další informace o cenách Azure Monitoru. |
| Starší úroveň Standard | Bez omezení | 30 dní  | Retence nelze upravit. |
| Starší úroveň Premium | Bez omezení | 365 dní  | Retence nelze upravit. |

**Počet pracovních prostorů na jedno předplatné.**

| Cenová úroveň    | Limit pracovního prostoru | Komentáře
|:---|:---|:---|
| Úroveň Free  | 10 | Tento limit nelze zvýšit. |
| Všechny ostatní úrovně | Bez omezení | Jste omezeni počtem prostředků v rámci skupiny prostředků a počtem skupin prostředků na předplatné. |

**Portál Azure**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet záznamů vrácených dotazem protokolu | 10 000 | Snižte výsledky pomocí oboru dotazu, časového rozsahu a filtrů v dotazu. |


**Rozhraní API pro sběr dat**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální velikost pro jeden příspěvek | 30 MB | Rozdělte větší svazky na více příspěvků. |
| Maximální velikost hodnot polí  | 32 KB | Pole delší než 32 kB se oříznou. |

**Search API**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet záznamů vrácených v jednom dotazu | 500 000 | |
| Maximální velikost vrácených dat | 64 000 000 bajtů (~61 MiB)| |
| Maximální doba spuštění dotazu | 10 minut | Podrobnosti najdete v [tématu Časové osy.](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)  |
| Maximální míra požadavků | 200 požadavků za 30 sekund na IP adresu uživatele nebo klienta AAD | Podrobnosti najdete v [tématu Omezení sazeb.](https://dev.loganalytics.io/documentation/Using-the-API/Limits) |

**Obecné limity pracovního prostoru**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet sloupců v tabulce         | 500 | |
| Maximální počet znaků pro název sloupce | 500 | |
| Export dat | Momentálně není k dispozici. | Pomocí funkce Azure nebo aplikace logiky agregujte a exportujte data. | 

**Rychlost objemu požití dat**


Azure Monitor je datová služba ve velkém měřítku, která poskytuje tisíce zákazníků odesílajících každý měsíc terabajty dat rostoucím tempem. Výchozí limit rychlosti rychlosti ingestování pro data odeslaná z prostředků Azure pomocí [nastavení diagnostiky](../articles/azure-monitor/platform/diagnostic-settings.md) je přibližně **6 GB/min** na pracovní plochu. Jedná se o přibližnou hodnotu, protože skutečná velikost se může lišit mezi datovými typy v závislosti na délce protokolu a jeho kompresním poměru. Toto omezení se nevztahuje na data odesílaná z agentů nebo [rozhraní API pro kolekcí dat](../articles/azure-monitor/platform/data-collector-api.md).

Pokud odesíláte data vyšší rychlostí do jednoho pracovního prostoru, některá data jsou vynechána a událost je odeslána do tabulky *Operace* v pracovním prostoru každých 6 hodin, zatímco prahová hodnota je stále překročena. Pokud svazek ingestování stále překračuje limit rychlosti nebo očekáváte, že k němu někdy brzy dosáhnete, můžete požádat o zvýšení pracovního prostoru otevřením žádosti o podporu.
 
Chcete-li být upozorněni na takovou událost v pracovním prostoru, vytvořte [pravidlo výstrahprotokolu](../articles/azure-monitor/platform/alerts-log.md) pomocí následujícího dotazu s logikou výstrahy, která je základem pro počet strmač výsledků než nula.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>V závislosti na tom, jak dlouho používáte Log Analytics, můžete mít přístup ke starším cenovým úrovním. Další informace o [starších cenových úrovních Služby Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 