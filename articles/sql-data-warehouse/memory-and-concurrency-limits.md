---
title: Omezení paměti a souběžnost – Azure SQL Data Warehouse | Microsoft Docs
description: Zobrazení omezení paměti a souběžnost přidělit různé úrovně výkonu a třídy prostředků v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kevin
ms.reviewer: jrj
ms.openlocfilehash: 096dd5f1bac87e1442963b62067896b7abf20a8e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Paměť a souběžnost limity pro Azure SQL Data Warehouse
Zobrazení omezení paměti a souběžnost přidělit různé úrovně výkonu a třídy prostředků v Azure SQL Data Warehouse. Další informace a chcete použít tyto možnosti pro váš plán úloh správy, najdete v části [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md). 

## <a name="performance-tiers"></a>Úrovně výkonu

SQL Data Warehouse nabízí dvě úrovně výkonu, které jsou optimalizované pro analytické úlohy. Úroveň výkonu je možnost, která určuje konfiguraci datového skladu. Tato možnost je jedním z první možnosti výběru, které jste při vytváření datového skladu.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- **Úroveň výkonu optimalizovaná pro zajištění elasticity** odděluje výpočetní a úložnou vrstvu architektury. Tato možnost vyniká v úlohách, které můžou naplno využít oddělení výpočetního výkonu a úložiště, díky častému škálování podporujícímu krátká období nejvyšší aktivity. Tato výpočetní vrstva zahrnuje nízké vstupní náklady a škáluje se pro zajištění podpory většiny úloh zákazníků.

- **Úroveň výkonu optimalizovaná pro zajištění výpočetního výkonu** využívá nejnovější hardware Azure a zavádí novou mezipaměť disku NVMe SSD, která udržuje nejčastěji používaná data blízko procesorům, tedy přesně tam, kde je chcete mít. Díky automatickému vrstvení úložiště vyniká tato úroveň výkonu ve složitých dotazech, protože všechny vstupně-výstupní operace zůstávají místně na výpočetní vrstvě. Vylepšené sloupcové úložiště navíc umožňuje ve službě SQL Data Warehouse uchovávat neomezený objem dat. Úroveň výkonu optimalizovaná pro zajištění výpočetního výkonu poskytuje nejvyšší úroveň škálovatelnosti, která umožňuje škálování až na 30 000 výpočetních jednotek datového skladu (cDWU). Tuto úroveň zvolte pro úlohy vyžadující nepřetržitý a neuvěřitelně vysoký výkon.

## <a name="data-warehouse-limits"></a>Omezení datového skladu
Následující tabulky uvádí maximální kapacity pro datový sklad na výkon různých úrovních. Chcete-li změnit úroveň výkonu, [škálování výpočetní - portálu](quickstart-scale-compute-portal.md).

### <a name="optimized-for-elasticity"></a>Optimalizováno pro elasticitu

Úrovně služeb pro optimalizovaný pro pružnost výkonu vrstvy v rozmezí od DW100 až DW6000. 

| Úroveň výkonu | Maximální počet souběžných dotazů | Výpočetní uzly | Distribuce na výpočetním uzlu | Maximální paměť na distribuce (MB) | Maximální paměť na datového skladu (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| OD DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1,600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2,000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4,000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8 000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>Optimalizováno pro výpočetní výkon

Optimalizováno pro výpočetní výkon vrstvě poskytuje 2,5 x více paměti na jeden dotaz než optimalizovaný pro úroveň výkonu pružnost. Tuto paměť navíc pomáhá optimalizovaný pro výpočetní výkon vrstvy poskytovat jeho vysoký výkon.  Úrovně výkonu pro optimalizovaný pro výpočetní výkon vrstvy v rozsahu od DW1000c DW30000c. 

| Úroveň výkonu | Maximální počet souběžných dotazů | Výpočetní uzly | Distribuce na výpočetním uzlu | Maximální paměť na distribuce (GB) | Maximální paměť na datového skladu (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1 500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

Maximální cDWU je DW30000c, který má 60 výpočetních uzlů a jeden distribuční na výpočetním uzlu. Například datový sklad 600 TB na DW30000c zpracovává přibližně 10 TB na výpočetním uzlu.


## <a name="concurrency-maximums"></a>Maximální hodnoty souběžnosti
K zajištění, že každý dotaz nemá dostatek prostředků k provedení efektivně SQL Data Warehouse sleduje výpočetní využití prostředků přiřazením souběžnosti sloty každý dotaz. Systém převádí dotazy do fronty kde budou čekat dostatek [souběžnosti sloty](resource-classes-for-workload-management.md#concurrency-slots) jsou k dispozici. 

Sloty souběžnosti taky určit stanovení priorit procesoru. Další informace najdete v tématu [analyzovat vaše úlohy](analyze-your-workload.md)

### <a name="optimized-for-compute"></a>Optimalizováno pro výpočetní výkon
Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [třída dynamické prostředků](resource-classes-for-workload-management.md). Toto platí pro optimalizovaný pro výpočetní výkon vrstvě.

**Dynamické prostředků třídy**
| Úroveň výkonu | Maximální počet souběžných dotazů | Concurrency sloty, které jsou k dispozici | Sloty používané smallrc | Sloty používané mediumrc | Sloty používané largerc | Sloty používané xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Statické prostředků třídy**

Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [Třída prostředků se statickou](resource-classes-for-workload-management.md).  

| Úroveň služby | Maximální počet souběžných dotazů | Concurrency sloty, které jsou k dispozici |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Optimalizováno pro elasticitu
Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [třída dynamické prostředků](resource-classes-for-workload-management.md).  Toto platí pro optimalizovaný pro úroveň výkonu pružnost.

**Dynamické prostředků třídy**

| Úroveň služby | Maximální počet souběžných dotazů | Concurrency sloty, které jsou k dispozici | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| OD DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Statické prostředků třídy** následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [Třída prostředků se statickou](resource-classes-for-workload-management.md).  Toto platí pro optimalizovaný pro úroveň výkonu pružnost.

| Úroveň služby | Maximální počet souběžných dotazů | Sloty maximální souběžnosti |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| OD DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Když je splněna jedna z těchto prahových hodnot, nové dotazy jsou zařazeny do fronty a jsou prováděny na základě ven first-in.  Dokončení dotazy a počet dotazů a sloty klesnou pod omezení, uvolní datový sklad SQL ve frontě dotazů. 

## <a name="next-steps"></a>Další postup

Další informace o tom, jak využít prostředků třídy za účelem optimalizace zkontrolujte další úlohy v následujících článcích:
* [Třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md)
* [Analýza velikosti pracovní zátěže](analyze-your-workload.md)

