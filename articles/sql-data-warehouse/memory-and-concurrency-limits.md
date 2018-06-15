---
title: Omezení paměti a souběžnost – Azure SQL Data Warehouse | Microsoft Docs
description: Zobrazení omezení paměti a souběžnost přidělit různé úrovně výkonu a třídy prostředků v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/07/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 46d41e3ee85deb20f189bc9c82a255178f3d7eee
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942249"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Paměť a souběžnost limity pro Azure SQL Data Warehouse
Zobrazení omezení paměti a souběžnost přidělit různé úrovně výkonu a třídy prostředků v Azure SQL Data Warehouse. Další informace a chcete použít tyto možnosti pro váš plán úloh správy, najdete v části [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md). 

Aktuálně nejsou k dispozici s SQL Data Warehouse – Gen1 a Gen2 dvou generací. Doporučujeme že využít Gen2 z SQL Data Warehouse získat nejlepšího výkonu pro vaše úlohy datového skladu. Gen2 zavádí nové mezipaměti NVMe plnou stav disku, který udržuje nejčastěji používaná data blízko procesorů. Touto akcí odeberete vzdálené vstupy/výstupy pro zatížení nejvíce náročné a náročná. Kromě výkonu Gen2 nabízí nejvyšší úroveň škálování umožňuje škálovat až 30 000 jednotky datového skladu a poskytnutím neomezená sloupcovém úložiště. Jsme bude stále podporují předchozí generace (Gen1) SQL Data Warehouse a zachovat stejné funkce; ale doporučujeme vám [upgradovat na Gen2](upgrade-to-latest-generation.md) na nejbližší usnadnění práce. 

## <a name="data-warehouse-capacity-settings"></a>Nastavení kapacity datového skladu
Následující tabulky uvádí maximální kapacity pro datový sklad na výkon různých úrovních. Chcete-li změnit úroveň výkonu, [škálování výpočetní - portálu](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 poskytuje 2,5 x více paměti na jeden dotaz, než Gen1. Tuto paměť navíc pomáhá Gen2, poskytovat jeho vysoký výkon.  Úrovně výkonu rozsah Gen2 z DW1000c DW30000c. 

| Úroveň výkonu | Výpočetní uzly | Distribuce na výpočetním uzlu | Paměť za datového skladu (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
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

Maximální Gen2 DWU je DW30000c, který má 60 výpočetních uzlů a jeden distribuční na výpočetním uzlu. Například datový sklad 600 TB na DW30000c zpracovává přibližně 10 TB na výpočetním uzlu.

### <a name="gen1"></a>Gen1

Úrovně služeb pro Gen1 rozsahu od od DW100 do DW6000. 

| Úroveň výkonu | Výpočetní uzly | Distribuce na výpočetním uzlu | Paměť za datového skladu (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| OD DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Maximální hodnoty souběžnosti
Zajistit, že každý dotaz nemá dostatek prostředků k úspěšnému provedení SQL Data Warehouse sleduje využití prostředků přiřazením souběžnosti sloty každý dotaz. Systém převádí dotazy do fronty kde budou čekat dostatek [souběžnosti sloty](resource-classes-for-workload-management.md#concurrency-slots) jsou k dispozici. Sloty souběžnosti taky určit stanovení priorit procesoru. Další informace najdete v tématu [analyzovat vaše úlohy](analyze-your-workload.md)

### <a name="gen2"></a>Gen2
 
**Statické prostředků třídy**

Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [Třída prostředků se statickou](resource-classes-for-workload-management.md).  

| Úroveň služby | Maximální počet souběžných dotazů | Concurrency sloty, které jsou k dispozici |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Dynamické prostředků třídy**

> [!NOTE]
> Třída prostředků smallrc na Gen2 dynamicky přidá paměti jako zvyšuje úrovně služby a podporuje pouze maximální počet souběžných dotazů 32.  Sloty souběžnosti a paměti, které zvýší smallrc jako zvýšení úrovně služby. 
>
>

Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [třída dynamické prostředků](resource-classes-for-workload-management.md). Na rozdíl od Gen1 je skutečně dynamická tříd dynamické prostředků na Gen2.  Gen2 využívá 3-10-22-70 procento přidělení paměti pro malé – střední velké xlarge prostředků třídy v všech úrovní služeb.

| Úroveň služby | Maximální počet souběžných dotazů | Concurrency sloty, které jsou k dispozici | Sloty používané smallrc | Sloty používané mediumrc | Sloty používané largerc | Sloty používané xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
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



#### <a name="gen1"></a>Gen1

Statické prostředků třídy

Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [Třída prostředků se statickou](resource-classes-for-workload-management.md) na **Gen1**.

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
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Dynamické prostředků třídy
> [!NOTE]
> Třída prostředků smallrc na Gen1 přiděluje pevné velikosti paměti na jeden dotaz, podobně jako způsobem staticrc10 třída statické prostředků.  Protože smallrc je statická, má schopnost škálovat 128 souběžných dotazů. 
>
>

Následující tabulka uvádí maximální počet souběžných dotazů a souběžnosti sloty pro každou [třída dynamické prostředků](resource-classes-for-workload-management.md) na **Gen1**.

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
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Když je splněna jedna z těchto prahových hodnot, nové dotazy jsou zařazeny do fronty a jsou prováděny na základě ven first-in.  Dokončení dotazy a počet dotazů a sloty klesnou pod omezení, uvolní datový sklad SQL ve frontě dotazů. 

## <a name="next-steps"></a>Další postup

Další informace o tom, jak využít prostředků třídy za účelem optimalizace zkontrolujte další úlohy v následujících článcích:
* [Třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md)
* [Analýza velikosti pracovní zátěže](analyze-your-workload.md)

