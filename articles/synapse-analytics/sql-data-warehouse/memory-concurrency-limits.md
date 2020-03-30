---
title: Limity paměti a souběžnosti
description: Zobrazení omezení paměti a souběžnosti přidělené různým úrovním výkonu a třídám prostředků v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c868d8c159bca0c8462acde48225dc45003cf84e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350993"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Limity paměti a souběžnosti pro Azure Synapse Analytics
Zobrazení omezení paměti a souběžnosti přidělené různým úrovním výkonu a třídám prostředků v Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Nastavení kapacity datového skladu
V následujících tabulkách je uveden maximální kapacita datového skladu na různých úrovních výkonu. Pokud chcete změnit úroveň výkonu, přečtěte si část [Škálování výpočetních prostředků – portál](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Úrovně služeb

Úrovně služeb se pohybují od DW100c do DW30000c.

| Úroveň výkonu | Výpočetní uzly | Distribuce na výpočetní uzel | Paměť na datový sklad (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c.            | 1             | 60                             |   300                          |
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1 500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Maximální úroveň služeb je DW30000c, který má 60 výpočetních uzlů a jednu distribuci na výpočetní uzel. Například datový sklad o velikosti 600 TB v dw30000c zpracovává přibližně 10 TB na výpočetní uzel.

## <a name="concurrency-maximums-for-workload-groups"></a>Maximální souběžnost pro skupiny úloh
Se zavedením [skupin úlohy](sql-data-warehouse-workload-isolation.md), koncept souběžnosti sloty již neplatí.  Zdroje na požadavek jsou přiděleny na základě procenta a zadaná v definici skupiny pracovního vytížení.  Však i při odebrání sloty souběžnosti, jsou minimální množství prostředků potřebných pro dotazy na základě úrovně služby.  Níže uvedená tabulka definovala minimální množství prostředků potřebných pro dotaz napříč úrovněmi služeb a přidruženou souběžnou, které lze dosáhnout. 

|Úroveň služeb|Maximální počet souběžných dotazů|Min % podporováno pro REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25 %|
|DW200c|8|12.5%|
|DW300c|12|8 %|
|DW400c|16|6.25%|
|DW500c.|20|5 %|
|DW1000c|32|3 %|
|DW1500c|32|3 %|
|DW2000c|48|2 %|
|DW2500c|48|2 %|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Maximální souběžnost pro třídy prostředků
Chcete-li zajistit, aby každý dotaz má dostatek prostředků pro efektivní spuštění, SQL Analytics v Azure Synapse sleduje využití prostředků přiřazením souběžnosti sloty pro každý dotaz. Systém umístí dotazy do fronty na základě důležitosti a souběžnosti sloty. Dotazy čekat ve frontě, dokud dostatek sloty souběžnosti jsou k dispozici. [Důležitost](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) a souběžnost sloty určit prioritu procesoru. Další informace najdete [v tématu Analýza pracovního vytížení](analyze-your-workload.md)

**Statické třídy prostředků**

V následující tabulce jsou uvedeny maximální souběžné dotazy a sloty souběžnosti pro každou [třídu statického prostředku](resource-classes-for-workload-management.md).  

| Úroveň služeb | Maximální počet souběžných dotazů | K dispozici sloty souběžnosti | Sloty používané staticrc10 | Sloty používané staticrc20 | Sloty používané staticrc30 | Sloty používané staticrc40 | Sloty používané staticrc50 | Sloty používané staticrc60 | Sloty používané staticrc70 | Sloty používané staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c.        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamické třídy prostředků**

V následující tabulce jsou uvedeny maximální souběžné dotazy a sloty souběžnosti pro každou [třídu dynamických prostředků](resource-classes-for-workload-management.md). Dynamické třídy prostředků používají přidělení procenta paměti 3-10-22-70 pro malé středně velké a velké třídy prostředků napříč všemi úrovněmi služeb.

| Úroveň služeb | Maximální počet souběžných dotazů | K dispozici sloty souběžnosti | Sloty používané smallrc | Sloty používané mediumrc | Sloty používané largerc | Sloty používané xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c.        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |


Pokud není dostatek slotů souběžnosti, které mohou spustit spuštění dotazu, dotazy jsou zařazeny do fronty a spouštěny na základě důležitosti.  Pokud je ekvivalentní význam, dotazy jsou prováděny na základě first-in, first-out.  Jako dotazy dokončí a počet dotazů a slotů spadají pod limity, SQL Data Warehouse uvolní dotazy ve frontě. 

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak využít třídy prostředků k optimalizaci úlohy, naleznete v následujících článcích:
* [Třídy prostředků pro správu pracovního vytížení](resource-classes-for-workload-management.md)
* [Analýza pracovního vytížení](analyze-your-workload.md)