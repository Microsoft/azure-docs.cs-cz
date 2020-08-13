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
ms.openlocfilehash: 6979ce65022f350a93f533951d634b8e436283bc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186300"
---
**Objem shromažďování dat a jejich uchovávání** 

| Úroveň | Limit za den | Uchovávání dat | Komentář |
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
| Maximální počet záznamů vrácených dotazem protokolu | 10 000 | Zmenšete výsledky pomocí rozsahu dotazu, časového rozsahu a filtrů v dotazu. |


**Rozhraní API kolekce dat**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální velikost pro jeden příspěvek | 30 MB | Rozdělte větší svazky na více příspěvků. |
| Maximální velikost pro hodnoty polí  | 32 KB | Pole delší než 32 kB se oříznou. |

**Rozhraní API pro vyhledávání**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet záznamů vrácených v jednom dotazu | 500 000 | |
| Maximální velikost vrácených dat | 64 000 000 bajtů (~ 61 MiB)| |
| Maximální doba běhu dotazu | 10 minut | Podrobnosti najdete v podrobnostech o [časových limitech](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Maximální počet požadavků | 200 požadavků za 30 sekund na uživatele nebo IP adresu klienta služby Azure AD | Podrobnosti najdete v tématu [omezení přenosové rychlosti](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Obecné limity pracovního prostoru**

| Kategorie | Omezení | Komentáře |
|:---|:---|:---|
| Maximální počet sloupců v tabulce         | 500 | |
| Maximální počet znaků pro název sloupce | 500 | |
| Export dat | Momentálně není k dispozici | Pomocí funkce Azure Functions nebo aplikace logiky můžete agregovat a exportovat data. | 

**Frekvence přijímání dat**

Azure Monitor je služba data ve velkém měřítku, která slouží tisícům zákazníků, kteří každý měsíc odesílají terabajty dat při rostoucím tempu. Limit přenosové rychlosti je v úmyslu chránit Azure Monitor zákazníky před náhlými špičkami příjmu ve víceklientském prostředí. Výchozí prahová hodnota frekvence pro ingestování 500 MB (komprimovaná) se vztahuje na pracovní prostory, které jsou přibližně **6 GB/min** nekomprimované – skutečná velikost se může mezi datovými typy lišit v závislosti na délce protokolu a jeho kompresním poměru. Tato prahová hodnota se vztahuje na všechna přijatá data, ať už jsou odesílána z prostředků Azure pomocí [nastavení diagnostiky](../articles/azure-monitor/platform/diagnostic-settings.md), [rozhraní API kolekce dat](../articles/azure-monitor/platform/data-collector-api.md) nebo agentů.

Když do pracovního prostoru odešlete data rychlostí vyšší než 80% prahové hodnoty nakonfigurované ve vašem pracovním prostoru, do tabulky *operace* v pracovním prostoru se pošle událost každých 6 hodin, zatímco prahová hodnota bude i nadále překročena. Když je rychlost příjmu dat vyšší než prahová hodnota, některá data se zahozena a do tabulky *operací* v pracovním prostoru se pošle událost každých 6 hodin, zatímco prahová hodnota bude i nadále překročena. Pokud vaše rychlost přijímání dat i nadále překračuje prahovou hodnotu nebo jste se k tomu již neočekávali, můžete požádat o jeho zvýšení v pracovním prostoru otevřením žádosti o podporu. 

Chcete-li být v pracovním prostoru upozorněni na událost, vytvořte [pravidlo výstrahy protokolu](../articles/azure-monitor/platform/alerts-log.md) pomocí následujícího dotazu se základem výstrahy upozornění na základě počtu výsledků od nuly, zkušebního období 5 minut a frekvence 5 minut.

Počet dosažených objemů příjmu 80% prahové hodnoty:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed 80% of the threshold"
```

Prahová hodnota dosažené míry objemu přijímání:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed the threshold"
```

>[!NOTE]
>V závislosti na tom, jak dlouho jste používali Log Analytics, je možné, že budete mít přístup ke starším cenovým úrovním. Další informace o [cenových úrovních Log Analytics starších verzích](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
