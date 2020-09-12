---
title: Omezení paměti a souběžnosti
description: Prohlédněte si limity paměti a souběžnosti přidělené různým úrovním výkonu a třídám prostředků ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5b72694f93ed5b712a0f684887df5b69a7b35c72
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441676"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Omezení paměti a souběžnosti pro Azure synapse Analytics

Prohlédněte si limity paměti a souběžnosti přidělené různým úrovním výkonu a třídám prostředků ve službě Azure synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Nastavení kapacity datového skladu

V následujících tabulkách jsou uvedeny maximální kapacity datového skladu na různých úrovních výkonu. Postup změny úrovně výkonu najdete v tématu [škálování COMPUTE COMPUTE – portál](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Úrovně služeb

Rozsah úrovní služeb od DW100c do DW30000c.

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

Maximální úroveň služby je DW30000c, která má 60 výpočetních uzlů a jednu distribuci na výpočetní uzel. Například datový sklad 600 TB v DW30000c zpracovává přibližně 10 TB na výpočetní uzel.

## <a name="concurrency-maximums-for-workload-groups"></a>Maximální počet souběžnosti pro skupiny úloh

V rámci zavedení [skupin úloh](sql-data-warehouse-workload-isolation.md)už koncept již neplatí.  Prostředky na žádost se přiřazují podle procentuální hodnoty a zadané v definici skupiny úloh.  Nicméně i s odebráním slotů pro souběžnost jsou pro každý dotaz na základě úrovně služby požadovány minimální množství prostředků.  Níže uvedená tabulka definuje minimální množství potřebných prostředků na dotaz napříč úrovněmi služeb a související souběžnosti, které je možné dosáhnout.

|Úroveň služeb|Maximální počet souběžných dotazů|Minimální počet podporovaných pro REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12,5%|
|DW300c|12|8 %|
|DW400c|16|6,25%|
|DW500c.|20|5 %|
|DW1000c|32|3 %|
|DW1500c|32|3 %|
|DW2000c|48|2 %|
|DW2500c|48|2 %|
|DW3000c|64|1,5%|
|DW5000c|64|1,5%|
|DW6000c|128|0,75 %|
|DW7500c|128|0,75 %|
|DW10000c|128|0,75 %|
|DW15000c|128|0,75 %|
|DW30000c|128|0,75 %|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Maximální počet souběžnosti pro třídy prostředků

Aby každý dotaz měl dostatek prostředků pro efektivní spuštění, synapse SQL sleduje využití prostředků přiřazením slotů souběžnosti k jednotlivým dotazům. Systém převede dotazy do fronty na základě důležitosti a slotů souběžnosti. Dotazy čekají ve frontě, dokud nebudou k dispozici dostatečné sloty souběžnosti. [Důležitost](sql-data-warehouse-workload-importance.md) a sloty souběžnosti určují stanovení priorit procesoru. Další informace najdete v tématu [Analýza úloh](analyze-your-workload.md) .

**Třídy statických prostředků**

V následující tabulce je uveden maximální počet souběžných dotazů a slotů souběžnosti pro každou [třídu statických prostředků](resource-classes-for-workload-management.md).  

| Úroveň služeb | Maximální počet souběžných dotazů | Dostupné sloty souběžného zpracování | Sloty používané v staticrc10 | Sloty používané v staticrc20 | Sloty používané v staticrc30 | Sloty používané v staticrc40 | Sloty používané v staticrc50 | Sloty používané v staticrc60 | Sloty používané v staticrc70 | Sloty používané v staticrc80 |
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

Následující tabulka ukazuje maximální počet souběžných dotazů a slotů souběžnosti pro každou [třídu dynamického prostředku](resource-classes-for-workload-management.md). Dynamické třídy prostředků používají Přidělení procentuálních hodnot paměti 3-10-22-70 pro třídy prostředků malé střední a velké XLarge napříč všemi úrovněmi služeb.

| Úroveň služeb | Maximální počet souběžných dotazů | Dostupné sloty souběžného zpracování | Sloty používané v smallrc | Sloty používané v mediumrc | Sloty používané v largerc | Sloty používané v xlargerc |
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

Pokud není k dispozici dostatek slotů souběžnosti ke spuštění provádění dotazů, dotazy jsou zařazeny do fronty a provedeny na základě důležitosti.  Pokud je stejná důležitost, dotazy se spouštějí v prvním, prvním.  Až se dokončí dotazy a počet dotazů a slotů klesne pod omezení, verze Azure synapse Analytics vyřadí dotazy ve frontě.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak využít třídy prostředků k optimalizaci úlohy, najdete v následujících článcích:

* [Třídy prostředků pro správu úloh](resource-classes-for-workload-management.md)
* [Analýza úloh](analyze-your-workload.md)
