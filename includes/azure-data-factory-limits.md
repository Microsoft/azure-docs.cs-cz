---
title: zahrnout soubor
description: zahrnout soubor
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 04/27/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 03b7ce49f935c36b0e0652925c41deea987a2a26
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
Objekt pro vytváření dat je víceklientské služby, který má následující výchozí omezení na místě a ujistěte se, že předplatná zákazníka jsou chráněny z druhé strany úlohy. Mnoho omezení lze snadno zvýšit pro vaše předplatné až do maximálního limitu kontaktováním podpory.

### <a name="version-2"></a>Verze 2

| Prostředek | Výchozí omezení | Maximální omezení | 
| -------- | ------------- | ------------- | 
| datové továrny v předplatné Azure | 50 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| kanály v rámci objekt pro vytváření dat | 2500 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| datové sady v rámci objekt pro vytváření dat | 2500 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Aktivační události v rámci objekt pro vytváření dat | 2500 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Propojené služby v rámci objekt pro vytváření dat | 2500 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Integrační moduly Runtime v rámci služby data factory <sup>4</sup> | 2500 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Celkový počet jader procesoru pro Azure SSIS Runtime(s) integrace v rámci jednoho předplatného | 100 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Souběžné kanál spustí na každou kanálu | 20 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maximální počet aktivit na kanálu | 20 | 30 |
| Parametry maximální za kanálu | 20 | 30 |
| Počet bajtů za objektu pro objekty kanálu <sup>1</sup> | 200 KB | 200 KB |
| Počet bajtů za pro datovou sadu a propojené služby objekty <sup>1</sup> | 100 KB | 2000 KB |
| Cloud jednotek přesun dat na aktivity při spuštění <sup>3</sup> | 256 | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pro běh aktivit kanálu počet opakování | 1 day(timeout) | 1 den (časový limit) |
| Zápis volání rozhraní API | 2 500/hod.<br/><br/> Toto omezení je dáno pomocí Azure Resource Manager, není Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Volání rozhraní API pro čtení | 12 500/hod.<br/><br/> Toto omezení je dáno pomocí Azure Resource Manager, není Azure Data Factory. | [Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Verze 1

| **Prostředek** | **Výchozí omezení** | **Maximální omezení** |
| --- | --- | --- |
| datové továrny v předplatné Azure |50 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| kanály v rámci objekt pro vytváření dat |2500 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| datové sady v rámci objekt pro vytváření dat |5000 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| souběžné řezy za datové sady |10 |10 |
| Počet bajtů za objektu pro objekty kanálu <sup>1</sup> |200 KB |200 KB |
| Počet bajtů za pro datovou sadu a propojené služby objekty <sup>1</sup> |100 KB |2000 KB |
| HDInsight na vyžádání clusteru jader v rámci předplatného <sup>2</sup> |60 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Cloud jednotek přesun dat na aktivity při spuštění <sup>3</sup> |32 |[Kontaktování podpory](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pro běh aktivit kanálu počet opakování |1000 |MaxInt (32 bitů) |

<sup>1</sup> kanálu, datové sady a propojené služby objekty představují logické seskupení vašich úloh. Limity pro tyto objekty se nevztahují k množství dat, můžete přesunout a zpracovat služby Azure Data Factory. Objekt pro vytváření dat je navržena tak, aby pro manipulaci s petabajty dat.

<sup>2</sup> jader na vyžádání HDInsight jsou přiděleny mimo odběr, který obsahuje data factory. V důsledku toho tohoto limitu je objektu pro vytváření dat vynucené základní limit pro počet jader na vyžádání HDInsight a se liší od základní limit spojený s předplatným Azure.

<sup>3</sup> jednotky přesun dat cloudu (DMU) se používá v rámci cloudu cloudové kopírování operace. Se jedná o míru, která reprezentuje výkon (kombinaci procesoru, paměti a přidělení prostředků sítě) na jednu jednotku v datové továrně. Pomocí další DMUs v některých případech můžete dosáhnout vyšší propustnost kopírování. Odkazovat na [cloudu jednotky přesun dat (V2)](../articles/data-factory/copy-activity-performance.md#cloud-data-movement-units) a [cloudu jednotky přesun dat (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) části na podrobnosti.

<sup>4</sup> integrace modulu Runtime (IR) je na výpočetní infrastruktuře používá k zajištění následující funkce integrace dat různých prostředích sítě Azure Data Factory: Přesun dat, odeslání aktivity a výpočetní služby spouštění balíčků SSIS. Další informace najdete v tématu [Přehled integrace modulu Runtime](../articles/data-factory/concepts-integration-runtime.md).

| **Prostředek** | **Výchozí limit nižší** | **Minimální omezení** |
| --- | --- | --- |
| Plánování intervalu |15 minut |15 minut |
| Interval mezi pokusy o opakování |1 sekunda |1 sekunda |
| Opakujte hodnotu časového limitu |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Omezení volání webové služby
Azure Resource Manager má omezení pro volání rozhraní API. Můžete provádět volání rozhraní API s rychlostí v rámci [rozhraní API Správce prostředků Azure omezuje](../articles/azure-subscription-service-limits.md#resource-group-limits).
