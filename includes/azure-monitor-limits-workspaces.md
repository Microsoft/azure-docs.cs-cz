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
ms.openlocfilehash: e6b64b5a1a60ba3bbf93e607536eeb0379669c73
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91639395"
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

**<a name="data-ingestion-volume-rate">Frekvence přijímání dat</a>**

Azure Monitor je služba data ve velkém měřítku, která slouží tisícům zákazníků, kteří každý měsíc odesílají terabajty dat při rostoucím tempu. Limit přenosové rychlosti je v úmyslu izolovat Azure Monitor zákazníky od náhlých špičky příjmu v prostředí s více architekturami. Výchozí prahová hodnota frekvence pro ingestování 500 MB (komprimovaná) je definovaná v pracovních prostorech. Tato hodnota se přeloží na přibližně **6 GB/min** nekomprimovaná – skutečná velikost se může mezi datovými typy lišit v závislosti na délce protokolu a jeho kompresním poměru. Limit přenosové rychlosti se vztahuje na všechna přijatá data, ať už jsou odesílána z prostředků Azure pomocí [nastavení diagnostiky](../articles/azure-monitor/platform/diagnostic-settings.md), [rozhraní API kolekce dat](../articles/azure-monitor/platform/data-collector-api.md) nebo agentů.

Když do pracovního prostoru odešlete data rychlostí vyšší než 80% prahové hodnoty nakonfigurované ve vašem pracovním prostoru, do tabulky *operace* v pracovním prostoru se pošle událost každých 6 hodin, zatímco prahová hodnota bude i nadále překročena. Když je rychlost příjmu dat vyšší než prahová hodnota, některá data se zahozena a do tabulky *operací* v pracovním prostoru se pošle událost každých 6 hodin, zatímco prahová hodnota bude i nadále překročena. Pokud vaše rychlost ingestování stále překročí prahovou hodnotu nebo jste se k nim neočekávali, můžete požádat o jejich zvýšení otevřením žádosti o podporu. 

Pokud chcete být ve svém pracovním prostoru upozorněni na přístup nebo dosažení limitu přenosové rychlosti pro přijímání, vytvořte [pravidlo upozornění protokolu](../articles/azure-monitor/platform/alerts-log.md) pomocí následujícího dotazu se základní logikou výstrahy na základě počtu výsledků, který je větší než nula, zkušební období 5 minut a frekvence 5 minut.

Míra zpracování příjmu překročila prahovou hodnotu.
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Error"
```

Frekvence zpracování příjmu překročila 80% prahové hodnoty.
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Warning"
```

Frekvence zpracování příjmu překročila 70% prahové hodnoty.
```Kusto
Operation
| where OperationCategory == "Ingestion"
| where OperationKey == "Ingestion rate limit"
| where OperationStatus == "Info"
```

>[!NOTE]
>V závislosti na tom, jak dlouho jste používali Log Analytics, je možné, že budete mít přístup ke starším cenovým úrovním. Další informace o [cenových úrovních Log Analytics starších verzích](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
