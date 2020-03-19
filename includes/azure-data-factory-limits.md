---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086141"
---
Azure Data Factory je víceklientská služba, pro kterou kvůli zajištění ochrany jednotlivých úloh v zákaznických předplatných platí následující výchozí limity. Pokud pro své předplatné chcete zvýšit limity až na dostupné maximum, obraťte se na podporu.

### <a name="version-2"></a>Verze 2

| Prostředek | Výchozí omezení | Maximální omezení |
| -------- | ------------- | ------------- |
| Počet datových továren v předplatném Azure | 800 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet entit, jako jsou kanály, datové sady, triggery, propojené služby a moduly runtime integrace, v rámci datové továrny | 5 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet jader procesoru pro Azure-SSIS Integration Runtime v rámci jednoho předplatného | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná spuštění kanálů na datovou továrnu (tento limit sdílí všechny kanály v dané datové továrně) | 10 000  | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná spuštění externích aktivit na předplatné a [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externí aktivity se spravují v modulu runtime integrace, ale spouštějí se v propojených službách, mezi které patří Databricks, uložené procedury, HDInsight, web a další.</small> | 3 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná spuštění aktivit kanálů na předplatné a [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Aktivity kanálů se spouštějí v modulu runtime integrace, včetně aktivit Lookup, GetMetadata a Delete. </small>| 1000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžné operace vytváření na předplatné a [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Včetně testovacího připojení, procházení seznamu složek a tabulek a náhledu dat. | 200 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná spotřeba jednotek integrace dat<sup>1</sup> na předplatné a [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Skupina oblastí 1<sup>2</sup>: 6000<br>Skupina oblastí 2<sup>2</sup>: 3 000<br>Skupina oblastí 3<sup>2</sup>: 1 500 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální počet aktivit na kanál, včetně vnitřních aktivit kontejnerů | 40 | 40 |
| Maximální počet propojených modulů runtime integrace, které je možné vytvořit pro jedno místní prostředí Integration Runtime | 100 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální počet parametrů na kanál | 50 | 50 |
| Položky ForEach | 100 000 | 100 000 |
| Paralelismus ForEach | 20 | 50 |
| Maximální počet spuštění ve frontě na kanál | 100 | 100 |
| Počet znaků na výraz | 8 192 | 8 192 |
| Minimální interval aktivační události pro přeskakující okno | 15 min. | 15 min. |
| Maximální časový limit pro spuštění aktivit kanálů | 7 dní | 7 dní |
| Počet bajtů na objekt u objektů kanálů<sup>3</sup> | 200 kB | 200 kB |
| Počet bajtů na objekt u objektů datových sad a propojených služeb<sup>3</sup> | 100 kB | 2 000 kB |
| Počet jednotek integrace dat<sup>1</sup> na spuštění aktivity kopírování | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Volání rozhraní API pro zápis | 1 200/h.<br/><br/> Tento limit určuje Azure Resource Manager, a ne Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Volání rozhraní API pro čtení | 12 500/h.<br/><br/> Tento limit určuje Azure Resource Manager, a ne Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet dotazů monitorování za minutu | 1 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet operací CRUD s entitami za minutu | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální doba relace ladění toku dat | 8 hod. | 8 hod. |
| Počet souběžných toků dat na datovou továrnu | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet souběžných relací ladění toku dat na uživatele a datovou továrnu | 3 | 3 |
| Limit hodnoty TTL toku dat v Azure IR | 4 hod. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Jednotky integrace dat (DIU) se používají v operacích kopírování v rámci cloudu, další informace najdete v části [Jednotky integrace dat (verze 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Informace o fakturaci najdete v tématu [Ceny služby Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> Prostředí [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) je [globálně dostupné](https://azure.microsoft.com/global-infrastructure/services/), aby se zajistilo dodržování předpisů pro data, efektivita a snížení nákladů na celkový výstup sítě. 

| Skupina oblastí | Oblasti | 
| -------- | ------ |
| Skupina oblastí 1 | USA – střed, USA – východ, USA – východ 2, Evropa – sever, Evropa – západ, USA – západ, USA – západ 2 |
| Skupina oblastí 2 | Austrálie – východ, Austrálie – jihovýchod, Brazílie – jih, Indie – střed, Japonsko – východ, USA (střed) – sever, USA (střed) – jih, Jihovýchodní Asie, USA – středozápad |
| Skupina oblastí 3 | Kanada – střed, Východní Asie, Francie – střed, Korea – střed, Velká Británie – jih |

<sup>3</sup> Objekty kanálů, datových sad a propojených služeb představují logické seskupení úloh. Limity pro tyto objekty se nevztahují na množství dat, která můžete přesouvat a zpracovávat pomocí služby Azure Data Factory. Služba Data Factory je navržená tak, aby díky škálování dokázala zpracovávat petabajty dat.

### <a name="version-1"></a>Verze 1

| **Prostředek** | **Výchozí omezení** | **Maximální omezení** |
| --- | --- | --- |
| Počet kanálů v rámci datové továrny |2 500 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet datových sad v rámci datové továrny |5 000 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet souběžných řezů na datovou sadu |10 |10 |
| Počet bajtů na objekt u objektů kanálů<sup>1</sup> |200 kB |200 kB |
| Počet bajtů na objekt u objektů datových sad a propojených služeb<sup>1</sup> |100 kB |2 000 kB |
| Počet jader clusteru Azure HDInsight na vyžádání v rámci předplatného<sup>2</sup> |60 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet jednotek přesunu dat v cloudu na spuštění aktivity kopírování<sup>3</sup> |32 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet opakování pro spuštění aktivit kanálů |1 000 |MaxInt (32 bitů) |

<sup>1</sup> Objekty kanálů, datových sad a propojených služeb představují logické seskupení úloh. Limity pro tyto objekty se nevztahují na množství dat, která můžete přesouvat a zpracovávat pomocí služby Azure Data Factory. Služba Data Factory je navržená tak, aby díky škálování dokázala zpracovávat petabajty dat.

<sup>2</sup> Jádra HDInsight na vyžádání se přidělují z předplatného, které obsahuje datovou továrnu. Předchozí limit počtu jader proto vynucuje služba Data Factory u jader HDInsight na vyžádání. Liší se od limitu počtu jader, který platí pro vaše předplatné Azure.

<sup>3</sup> Jednotky přesunu dat v cloudu (DMU) ve verzi 1 se používají v operacích kopírování v rámci cloudu, další informace najdete v části [Jednotky přesunu dat v cloudu (verze 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Informace o fakturaci najdete v tématu [Ceny služby Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Prostředek** | **Výchozí dolní limit** | **Minimální limit** |
| --- | --- | --- |
| Interval plánování |15 minut |15 minut |
| Interval mezi opakovanými pokusy |1 sekunda |1 sekunda |
| Hodnota časového limitu opakování |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity volání webových služeb
Pro Azure Resource Manager platí limity volání rozhraní API. Rozhraní API můžete volat rychlostí, která je v mezích [limitů rozhraní API Azure Resource Manageru](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
