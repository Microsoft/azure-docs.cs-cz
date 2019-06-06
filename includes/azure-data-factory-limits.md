---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427612"
---
Azure Data Factory je víceklientská služba, která má následující výchozí omezení na místě zajistit, aby chránily zákaznických předplatných z druhé strany úlohy. Chcete-li zvýšit limity až do maximální pro vaše předplatné, obraťte se na podporu.

### <a name="version-2"></a>Verze 2

| Resource | Výchozí omezení | Maximální omezení |
| -------- | ------------- | ------------- |
| Datových továren v předplatném Azure | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet entit, jako je například kanály, datových sad, aktivační události, propojené služby a moduly runtime integrace v rámci služby data factory | 5 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet jader procesoru pro prostředí Azure-SSIS Integration Runtime v rámci jednoho předplatného | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžný kanál spuštění za služby data factory, který je sdílen mezi všechny kanály v objektu pro vytváření | 10,000  | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Spuštění souběžných externích aktivit na předplatné a [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externí aktivity jsou spravovány v modulu runtime integrace, ale spustit na propojené služby, včetně Databricks, uložené procedury, HDInsights a dalších.</small> | 3000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Na předplatné a spuštění souběžných aktivit kanálu [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Aktivity v kanálu provést v prostředí integration runtime, včetně Lookup, GetMetadata a odstranit. </small>| 1000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžné operace na předplatné a vytváření [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Test připojení, procházet seznam složek a seznam tabulek, včetně náhledu data. | 200 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžné jednotky integrace dat<sup>1</sup> spotřeby na předplatné a [oblast Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Oblast skupina 1<sup>2</sup>: 6000<br>2. skupina oblasti<sup>2</sup>: 3000<br>Skupina oblast 3<sup>2</sup>: 1500 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální aktivity za kanál, který obsahuje vnitřní aktivity pro kontejnery | 40 | 40 |
| Maximální počet propojených integration runtime, který je možné vytvořit pro jednu místní prostředí integration runtime | 100 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální parametry za kanál | 50 | 50 |
| Položky ForEach | 100 000 | 100 000 |
| Paralelní zpracování ForEach | 20 | 50 |
| Znaků na výraz | 8 192 | 8 192 |
| Interval aktivační událost minimální aktivační událost pro přeskakující okno | 15 min | 15 min |
| Maximální časový limit pro kanál aktivita běží | 7 dní | 7 dní |
| Bajtů na objekty kanálu<sup>3</sup> | 200 KB | 200 KB |
| Bajtů na objekt pro datové sady a propojené služby objekty<sup>3</sup> | 100 KB | 2 000 KB |
| Jednotky integrace dat<sup>1</sup> za spuštění aktivity kopírování | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zápis volání rozhraní API | 2 500/h<br/><br/> Toto omezení se vynucují pomocí Azure Resource Manageru, nikoli Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Volání rozhraní API pro čtení | 12 500/h<br/><br/> Toto omezení se vynucují pomocí Azure Resource Manageru, nikoli Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorování dotazů za minutu | 1 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operace CRUD entity za minutu | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> jednotka integrace dat (DIÚ) se používá v operaci kopírování cloud-to-cloud, další informace z [jednotky integrace dat (verze 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Informace o fakturaci najdete v tématu [ceny služby Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [prostředí azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) je [globálně dostupné](https://azure.microsoft.com/global-infrastructure/services/) zajistit dodržování předpisů pro data, efektivita a snížená síťová výchozí přenos dat náklady. 

| Oblast skupiny | Oblasti | 
| -------- | ------ |
| Oblast skupiny 1 | Střed USA, východní USA, východní USA 2, Severní Evropa, západní Evropa, USA – Západ, USA – západ 2 |
| 2. skupina oblasti | Austrálie – východ, Austrálie – jihovýchod, Brazílie – Jih, střed Indie, Japonsko – východ, Northcentral USA, střed USA – Jih, jihovýchodní Asie, střed USA – západ |
| Skupina oblast 3 | Kanada – střed, Asie – východ, Francie – střed, Korea – střed, Velká Británie – jih |

<sup>3</sup> kanálu, datové sady a propojené služby objekty představují logické seskupení úloh. Omezení pro tyto objekty nejsou souvisí objem dat můžete přesouvat a zpracovávat pomocí Azure Data Factory. Data Factory je navržena tak, aby k manipulaci s petabajty dat.

### <a name="version-1"></a>Verze 1

| **Prostředek** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Datových továren v předplatném Azure |50 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Kanály v data factory |2,500 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datové sady v rámci služby data factory |5 000 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžné kolekce obsahuje nějaké řezy podle datové sady |10 |10 |
| Bajtů na objekty kanálu<sup>1</sup> |200 KB |200 KB |
| Nastavit počtu bajtů na objekt pro data a propojené služby objekty<sup>1</sup> |100 KB |2 000 KB |
| Azure HDInsight na vyžádání clusteru jader v rámci předplatného<sup>2</sup> |60 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Cloud jednotek přesunu dat za spuštění aktivity kopírování<sup>3</sup> |32 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pro spuštění aktivity kanálu počet opakování |1 000 |MaxInt (32 bitů) |

<sup>1</sup> kanálu, datové sady a propojené služby objekty představují logické seskupení úloh. Omezení pro tyto objekty nejsou souvisí objem dat můžete přesouvat a zpracovávat pomocí Azure Data Factory. Data Factory je navržena tak, aby k manipulaci s petabajty dat.

<sup>2</sup> mimo předplatné, které obsahuje objekt pro vytváření dat nejsou přidělená jádra HDInsight na vyžádání. V důsledku toho předchozí limit je limit jader vynucované služby Data Factory pro jádra HDInsight na vyžádání. To se liší od limit jader, který je spojen s vaším předplatným Azure.

<sup>3</sup> jednotka pohybu dat v cloudu (DMU) pro verzi 1 se používá v operaci kopírování cloudu do cloudu, přečtěte si další z [cloudu jednotek přesunu dat (verze 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Informace o fakturaci najdete v tématu [ceny služby Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Prostředek** | **Dolní mez výchozí** | **Minimální omezení** |
| --- | --- | --- |
| Plánování intervalu |15 minut |15 minut |
| Interval mezi opakovanými pokusy |1 sekunda |1 sekunda |
| Hodnota časového limitu opakování |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Omezení volání webové služby
Azure Resource Manager má omezení pro volání rozhraní API. Můžete provádět volání rozhraní API s rychlostí v rámci [omezení rozhraní API Azure Resource Manageru](../articles/azure-subscription-service-limits.md#resource-group-limits).
