---
title: "Úrovně výkonu služby Azure SQL Data Warehouse | Microsoft Docs"
description: "Úvod do pružnost a úrovně výkonu optimalizované výpočetní dostupné v Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 03881c12faed723999e97431e4a69fdeb6bfa10d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Úrovně výkonu Azure SQL Data Warehouse (Preview)
SQL Data Warehouse nabízí dvě úrovně výkonu, které jsou optimalizované pro analytické úlohy. Tento článek vysvětluje koncepty úrovně výkonu, které vám pomohou zvolit nejvhodnější úroveň výkonu pro úlohy. 

## <a name="what-is-a-performance-tier"></a>Co je úroveň výkonu?
Úroveň výkonu je možnost, která určuje konfiguraci datového skladu. Tato možnost je jedním z první možnosti výběru, které jste při vytváření datového skladu.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- **Úroveň výkonu optimalizovaná pro zajištění elasticity** odděluje výpočetní a úložnou vrstvu architektury. Tato možnost vyniká v úlohách, které můžou naplno využít oddělení výpočetního výkonu a úložiště, díky častému škálování podporujícímu krátká období nejvyšší aktivity. Tato výpočetní vrstva zahrnuje nízké vstupní náklady a škáluje se pro zajištění podpory většiny úloh zákazníků.

- **Úroveň výkonu optimalizovaná pro zajištění výpočetního výkonu** využívá nejnovější hardware Azure a zavádí novou mezipaměť disku NVMe SSD, která udržuje nejčastěji používaná data blízko procesorům, tedy přesně tam, kde je chcete mít. Díky automatickému vrstvení úložiště vyniká tato úroveň výkonu ve složitých dotazech, protože všechny vstupně-výstupní operace zůstávají místně na výpočetní vrstvě. Vylepšené sloupcové úložiště navíc umožňuje ve službě SQL Data Warehouse uchovávat neomezený objem dat. Úroveň výkonu optimalizovaná pro zajištění výpočetního výkonu poskytuje nejvyšší úroveň škálovatelnosti, která umožňuje škálování až na 30 000 výpočetních jednotek datového skladu (cDWU). Tuto úroveň zvolte pro úlohy vyžadující nepřetržitý a neuvěřitelně vysoký výkon.

## <a name="service-levels"></a>Úrovně služeb
Cíl na úrovni služby (SLO) je škálovatelnost nastavení, které určuje úroveň nákladů a výkonu datového skladu. Úrovně služeb pro optimalizovaný pro škálování vrstvy výpočetní výkon se měří v výpočetní jednotky datového skladu (cDWU), například DW2000c. Optimalizováno pro úrovně služeb pružnost se měří v Dwu, například DW2000. Další informace najdete v tématu [co je jednotek datových skladů?](what-is-a-data-warehouse-unit-dwu-cdwu.md)

V T-SQL SERVICE_OBJECTIVE nastavení určuje úroveň služby a úroveň výkonu pro datový sklad.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>Maximální hodnoty paměti
Úrovně výkonu mají různé paměti profily, které překládá do různých množství paměti na jeden dotaz. Optimalizováno pro výpočetní výkon vrstvě poskytuje 2,5 x více paměti na jeden dotaz než optimalizovaný pro úroveň výkonu pružnost. Tuto paměť navíc pomáhá optimalizovaný pro výpočetní výkon vrstvy poskytovat jeho mimořádně vysoký výkon. Další paměť na jeden dotaz můžete také spouštět další dotazy souběžně, protože dotazy můžete použít nižší [třídy prostředků](resource-classes-for-workload-management.md). 

### <a name="optimized-for-elasticity"></a>Optimalizováno pro elasticitu

Úrovně služeb pro optimalizovaný pro pružnost výkonu vrstvy v rozmezí od DW100 až DW6000. 

| Úroveň služby | Maximální počet souběžných dotazů | Výpočetní uzly | Distribuce na výpočetním uzlu | Maximální paměť na distribuce (MB) | Maximální paměť na datového skladu (GB) |
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

Úrovně služeb pro optimalizovaný pro výpočetní výkon vrstvy v rozsahu od DW1000c DW30000c. 

| Úroveň služby | Maximální počet souběžných dotazů | Výpočetní uzly | Distribuce na výpočetním uzlu | Maximální paměť na distribuce (GB) | Maximální paměť na datového skladu (GB) |
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
SQL Data Warehouse poskytuje špičkový souběžnosti na jednoho datového skladu. K zajištění, že každý dotaz nemá dostatek prostředků k provedení efektivně, systém sleduje výpočetní využití prostředků přiřazením souběžnosti sloty každý dotaz. Systém převádí dotazy do fronty kde budou čekat, až dostatek souběžnosti sloty jsou dostupné. 

Sloty souběžnosti taky určit stanovení priorit procesoru. Další informace najdete v tématu [analyzovat vaše úlohy](analyze-your-workload.md)

### <a name="concurrency-slots"></a>Sloty souběžnosti
Concurrency sloty jsou pohodlný způsob, jak sledovat prostředky k dispozici pro spuštění dotazu. Jsou to například lístků, které jste si koupili tak, aby vyhradil licencí na vzájemné součinnosti, protože zasedací je omezená. Podobně SQL Data Warehouse má omezený počet výpočetní prostředky. Dotazy rezervovat výpočetní prostředky tím, že získává sloty souběžnosti. Předtím, než dotaz můžete spustit provádění, musí být možné vyhradit dostatek sloty souběžnosti. Po dokončení dotazu uvolní jeho sloty souběžnosti. 

* Optimalizovaná pro úroveň výkonu pružnost škáluje 240 sloty souběžnosti.
* Optimalizovaná pro výpočetní výkon vrstvy měřítka na sloty 1200 souběžnosti.

Každý dotaz spotřebuje nula, jednu nebo více sloty souběžnosti. Dotazy na systém a některé trivial není využívat žádné sloty. Ve výchozím nastavení dotazy, které se řídí třídy prostředků vyžadují jeden slot souběžnosti. Složitější dotazy může vyžadovat další souběžnosti sloty.  

- Dotaz s 10 sloty souběžnosti můžete přístup 5krát víc zdrojích výpočtů než dotazu s 2 sloty souběžnosti.
- Pokud každý dotaz vyžaduje 10 souběžnosti sloty a nejsou 40 souběžnosti sloty, můžete současně spustit jenom 4 dotazy.
 
Pouze na dotazy prostředků, které využívají sloty souběžnosti. Přesné číslo souběžnosti sloty spotřebované je určen v dotazu [Třída prostředků](resource-classes-for-workload-management.md).

### <a name="optimized-for-compute"></a>Optimalizováno pro výpočetní výkon
Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [třída dynamické prostředků](resource-classes-for-workload-management.md).  Toto platí pro optimalizovaný pro výpočetní výkon vrstvě.

**Dynamické prostředků třídy**
| Úroveň služby | Maximální počet souběžných dotazů | Concurrency sloty, které jsou k dispozici | Sloty používané smallrc | Sloty používané mediumrc | Sloty používané largerc | Sloty používané xlargerc |
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

