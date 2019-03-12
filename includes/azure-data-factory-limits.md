---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 42c1856f30484532e1ace2e84187bcaaacdf4c72
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553358"
---
Azure Data Factory je víceklientská služba, která má následující výchozí omezení na místě zajistit, aby chránily zákaznických předplatných z druhé strany úlohy. Chcete-li zvýšit limity až do maximální pro vaše předplatné, obraťte se na podporu.

### <a name="version-2"></a>Verze 2

| Prostředek | Výchozí omezení | Maximální omezení |
| -------- | ------------- | ------------- |
| Datových továren v předplatném Azure | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet entit, jako je například kanály, datových sad, aktivační události, propojené služby a moduly runtime integrace v rámci služby data factory | 5 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet jader procesoru pro prostředí Azure-SSIS Integration Runtime v rámci jednoho předplatného | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžný kanál spuštění za služby data factory, který je sdílen mezi všechny kanály v objektu pro vytváření | 10 000  | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální aktivity za kanál, který obsahuje vnitřní aktivity pro kontejnery | 40 | 40 |
| Maximální počet propojených integration runtime, který je možné vytvořit pro jednu místní prostředí integration runtime | 20 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální parametry za kanál | 50 | 50 |
| Položky ForEach | 100 000 | 100 000 |
| Paralelní zpracování ForEach | 20 | 50 |
| Znaků na výraz | 8 192 | 8 192 |
| Interval aktivační událost minimální aktivační událost pro přeskakující okno | 15 min | 15 min |
| Maximální časový limit pro kanál aktivita běží | 7 dní | 7 dní |
| Bajtů na objekty kanálu<sup>1</sup> | 200 KB | 200 KB |
| Bajtů na objekt pro datové sady a propojené služby objekty<sup>1</sup> | 100 KB | 2 000 KB |
| Jednotky integrace dat za spuštění aktivity kopírování<sup>3</sup> | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zápis volání rozhraní API | 2 500/h<br/><br/> Toto omezení se vynucují pomocí Azure Resource Manageru, nikoli Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Volání rozhraní API pro čtení | 12 500/h<br/><br/> Toto omezení se vynucují pomocí Azure Resource Manageru, nikoli Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorování dotazů za minutu | 1 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operace CRUD entity za minutu | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


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

<sup>1</sup>kanálu, datové sady a propojené služby objekty představují logické seskupení úloh. Omezení pro tyto objekty nejsou souvisí objem dat můžete přesouvat a zpracovávat pomocí Azure Data Factory. Data Factory je navržena tak, aby k manipulaci s petabajty dat.

<sup>2</sup>mimo předplatné, které obsahuje objekt pro vytváření dat nejsou přidělená jádra HDInsight na vyžádání. V důsledku toho předchozí limit je limit jader vynucované služby Data Factory pro jádra HDInsight na vyžádání. To se liší od limit jader, který je spojen s vaším předplatným Azure.

<sup>3</sup>jednotka integrace dat (DIÚ) verze 2 nebo v cloudu jednotka pohybu dat (DMU) pro verzi 1 se používá v operaci kopírování cloud-to-cloud. Jedná se o míru, která představuje sílu jedné jednotky ve službě Data Factory. Kombinuje procesoru, paměti a přidělení prostředků sítě. Větší propustnost v kopírování lze dosáhnout pomocí další DMUs pro některé scénáře. Další informace najdete v tématu [jednotky integrace dat (verze 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) a [cloudu jednotek přesunu dat (verze 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Informace o fakturaci najdete v tématu [ceny služby Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>4</sup>prostředí integration runtime (IR) je výpočetní infrastruktura, službou Azure Data Factory používá k poskytování možnosti integrace dat napříč různými síťovými prostředími, jako je například přesun dat, odesílání aktivit výpočetních služeb, a spouštění balíčků služby SSIS. Další informace najdete v tématu [přehled modulu runtime integrace](../articles/data-factory/concepts-integration-runtime.md).

| **Prostředek** | **Dolní mez výchozí** | **Minimální omezení** |
| --- | --- | --- |
| Plánování intervalu |15 minut |15 minut |
| Interval mezi opakovanými pokusy |1 sekunda |1 sekunda |
| Hodnota časového limitu opakování |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Omezení volání webové služby
Azure Resource Manager má omezení pro volání rozhraní API. Můžete provádět volání rozhraní API s rychlostí v rámci [omezení rozhraní API Azure Resource Manageru](../articles/azure-subscription-service-limits.md#resource-group-limits).
