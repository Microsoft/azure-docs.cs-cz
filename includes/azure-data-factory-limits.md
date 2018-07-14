---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 67144407b078a30c521201cbc31c6087c839fa26
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030013"
---
Data factory je víceklientská služba, která má následující výchozí omezení na místě zajistit, aby chránily zákaznických předplatných z druhé strany úlohy. Mnoho omezení můžete snadno navýšit pro vaše předplatné až do maximálního limitu tak, že kontaktujete podporu.

### <a name="version-2"></a>Verze 2

| Prostředek | Výchozí omezení | Maximální omezení |
| -------- | ------------- | ------------- |
| Datových továren v předplatném Azure | 50 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Celkový počet entit (kanál, datové sady, aktivační události, propojené služby, prostředí Integration Runtime) v rámci služby data factory | 5000 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Celkový počet jader procesoru pro prostředí Azure-SSIS Integration Runtime(s) v rámci jednoho předplatného | 128 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Souběžný kanál se spouští na kanálu | 100 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Souběžný kanál spuštění za služby data factory | 10 000  | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maximální počet aktivit za kanál (obsahuje vnitřní aktivity pro kontejnery) | 40 | 40 |
| Maximální počet parametrů na kanálu | 50 | 50 |
| Položky ForEach | 100,000 | 100,000 |
| Paralelní zpracování ForEach | 20 | 50 |
| Znaků na výraz | 8 192 | 8 192 |
| Minimální interval Přeskakující okno | 15 min | 15 min |
| Maximální časový limit pro spuštění aktivity kanálu | 7 dní | 7 dní |
| Bajtů na objekty kanálu <sup>1</sup> | 200 KB | 200 KB |
| Bajtů na objekt pro datové sady a propojené služby objekty <sup>1</sup> | 100 KB | 2 000 KB |
| Jednotky integrace dat za spuštění aktivity kopírování <sup>3</sup> | 256 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Zápis volání rozhraní API | 2 500/hod<br/><br/> Toto omezení se vynucují pomocí Azure Resource Manageru, nikoli Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Volání rozhraní API pro čtení | 12 500/hod<br/><br/> Toto omezení se vynucují pomocí Azure Resource Manageru, nikoli Azure Data Factory. | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Verze 1

| **Prostředek** | **Výchozí omezení** | **Maximální omezení** |
| --- | --- | --- |
| Datových továren v předplatném Azure |50 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Kanály v data factory |2500 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Datové sady v rámci služby data factory |5000 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Souběžné kolekce obsahuje nějaké řezy na datovou sadu |10 |10 |
| Bajtů na objekty kanálu <sup>1</sup> |200 KB |200 KB |
| Bajtů na objekt pro datové sady a propojené služby objekty <sup>1</sup> |100 KB |2 000 KB |
| Jádra HDInsight na vyžádání clusteru v rámci předplatného <sup>2</sup> |60 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Cloud jednotek přesunu dat za spuštění aktivity kopírování <sup>3</sup> |32 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pro spuštění aktivity kanálu počet opakování |1000 |MaxInt (32 bitů) |

<sup>1</sup> kanálu, datové sady a propojené služby objekty představují logické seskupení úloh. Omezení pro tyto objekty nesouvisí se množství dat můžete přesouvat a zpracovávat ve službě Azure Data Factory. Objekt pro vytváření dat je navržena tak, aby k manipulaci s petabajty dat.

<sup>2</sup> mimo předplatné, které obsahuje objekt pro vytváření dat nejsou přidělená jádra HDInsight na vyžádání. V důsledku toho výše uvedené omezení je objekt pro vytváření dat vynucovat core limit pro jádra HDInsight na vyžádání a se liší od základní omezení spojené s předplatným Azure.

<sup>3</sup> částí integrace dat (DIÚ) pro v2 nebo cloudových dat přesunu částí (DMU) pro v1 je používán v operaci kopírování cloud-to-cloud. Jedná se o míru, která představuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) jedné jednotky ve službě Data Factory. Větší propustnost v kopírování lze dosáhnout pomocí další DMUs pro některé scénáře. Odkazovat na [jednotky integrace dat (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) a [cloudu jednotek přesunu dat (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) části na podrobnosti a [stránce s cenami služby Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) fakturačních nepřímo.

<sup>4</sup> the Integration Runtime (IR) je výpočetní infrastruktura, službou Azure Data Factory používá k poskytování následujících funkcí integrace dat v různých síťových prostředích: Přesun dat, odesílání aktivit výpočetních služeb, spouštění balíčků služby SSIS. Další informace najdete v tématu [přehled modulu Runtime integrace](../articles/data-factory/concepts-integration-runtime.md).

| **Prostředek** | **Dolní mez výchozí** | **Minimální omezení** |
| --- | --- | --- |
| Plánování intervalu |15 minut |15 minut |
| Interval mezi opakovanými pokusy |1 sekunda |1 sekunda |
| Hodnota časového limitu opakování |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Omezení volání webové služby
Azure Resource Manager má omezení pro volání rozhraní API. Můžete provádět volání rozhraní API s rychlostí v rámci [omezení rozhraní API Azure Resource Manageru](../articles/azure-subscription-service-limits.md#resource-group-limits).
