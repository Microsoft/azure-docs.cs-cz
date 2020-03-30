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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086141"
---
Azure Data Factory je víceklientská služba, která má následující výchozí limity na místě, aby bylo zajištěno, že odběry zákazníků jsou chráněny před úlohami ostatních. Chcete-li zvýšit limity až na maximum pro vaše předplatné, obraťte se na podporu.

### <a name="version-2"></a>Verze 2

| Prostředek | Výchozí omezení | Maximální omezení |
| -------- | ------------- | ------------- |
| Datové továrny v předplatném Azure | 800 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet entit, jako jsou kanály, sady dat, aktivační události, propojené služby a zaběhu integrace, v rámci datové továrny | 5 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Celkový počet procesorových jader pro běhy integrace Azure-SSIS v rámci jednoho předplatného | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžný kanál běží na datové továrny, která je sdílena mezi všechny kanály ve výrobě | 10 000  | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná externí aktivita se spouští podle předplatného na [oblast runtime integrace Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Externí aktivity jsou spravovány za běhu integrace, ale spouštějí se v propojených službách, včetně Databricks, uložené procedury, HDInsights, webu a dalších.</small> | 3000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná aktivita kanálu se spouští podle předplatného na [oblast runtime integrace Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Aktivity kanálu spustit v modulu runtime integrace, včetně vyhledávání, GetMetadata a Delete.</small>| 1000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžné operace vytváření podle předplatného na [oblast runtime integrace Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Včetně testovacího připojení, procházení seznamu složek a seznamu tabulek, náhledu dat. | 200 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžná spotřeba jednotek integrace dat<sup>1</sup> na jedno předplatné na [oblast runtime integrace Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Skupina regionů 1<sup>2</sup>: 6000<br>Skupina regionů 2<sup>2</sup>: 3000<br>Skupina regionů 3<sup>2</sup>: 1500 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální počet aktivit na kanál, který zahrnuje vnitřní aktivity pro kontejnery | 40 | 40 |
| Maximální počet propojených integračních runtime, které lze vytvořit proti jedinému prostředí runtime integrace s vlastním hostitelem | 100 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální počet parametrů na kanál | 50 | 50 |
| Položky ForEach | 100 000 | 100 000 |
| ForEach paralelismus | 20 | 50 |
| Maximální počet spuštění ve frontě na kanál | 100 | 100 |
| Znaky na výraz | 8 192 | 8 192 |
| Minimální interval aktivace omílání oken | 15 min | 15 min |
| Maximální časový limit pro spuštění aktivity kanálu | 7 dní | 7 dní |
| Bajty na objekt pro objekty potrubí<sup>3</sup> | 200 KB | 200 KB |
| Bajty na objekt pro objekty datové sady a propojené služby<sup>3</sup> | 100 KB | 2 000 KB |
| Jednotky integrace dat<sup>1</sup> na spuštění aktivity kopírování | 256 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zápis volání rozhraní API | 1 200/h<br/><br/> Toto omezení je vynuceno Správcem prostředků Azure, ne Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Čtení volání rozhraní API | 12 500/h<br/><br/> Toto omezení je vynuceno Správcem prostředků Azure, ne Azure Data Factory. | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Sledování dotazů za minutu | 1 000 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operace CRUD entity za minutu | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maximální doba relace ladění toku dat | 8 hod. | 8 hod. |
| Souběžný počet toků dat na výrobní závod | 50 | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžný počet ladicích relací toku dat na uživatele na továrnu | 3 | 3 |
| Limit TTL indičního diagramu datového toku Azure | 4 hodiny | [Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Jednotka integrace dat (DIU) se používá v operaci kopírování z cloudu na cloud, další informace o [jednotkách integrace dat (verze 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Informace o fakturaci najdete v [tématu Ceny Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) je globálně k [dispozici](https://azure.microsoft.com/global-infrastructure/services/) pro zajištění dodržování dat, efektivity a snížení nákladů na odchozí síťové odchozí přenos. 

| Skupina Region | Oblasti | 
| -------- | ------ |
| Skupina regionů 1 | Střední USA, Východní USA, Východní US2, Severní Evropa, Západní Evropa, Západní USA, Západní USA 2 |
| Skupina regionů 2 | Austrálie – východ, Austrálie – jihovýchod, Brazílie Jih, Střední Indie, Japonsko – východ, Northcentral USA, Jižní střed USA, Jihovýchodní Asie, – střed USA – západ |
| Skupina regionů 3 | Kanada – střed, Východní Asie, Francie – střed, Korea – střed, Spojené království – jih |

<sup>3</sup> Pipeline, sada dat a propojené objekty služby představují logické seskupení úlohy. Limity pro tyto objekty se nevztahují k množství dat, které můžete přesunout a zpracovat pomocí Azure Data Factory. Data Factory je navržen tak, aby škálování pro zpracování petabajtů dat.

### <a name="version-1"></a>Verze 1

| **Zdrojů** | **Výchozí omezení** | **Maximální limit** |
| --- | --- | --- |
| Kanály v rámci datové továrny |2,500 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Datové sady v rámci datové továrny |5 000 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Souběžné řezy na sadu dat |10 |10 |
| Počet bajtů na objekt pro objekty potrubí<sup>1</sup> |200 KB |200 KB |
| Bajty na objekt pro sadu dat a propojené objekty služby<sup>1</sup> |100 KB |2 000 KB |
| Jádra clusteru Azure HDInsight na vyžádání v rámci předplatného<sup>2</sup> |60 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednotky pohybu cloudových dat na aktivitu kopírování jsou spuštěny<sup>3</sup> |32 |[Obraťte se na podporu](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Počet opakování pro spuštění aktivity kanálu |1 000 |MaxInt (32 bitů) |

<sup>1</sup> Pipeline, sada dat a propojené objekty služby představují logické seskupení úlohy. Limity pro tyto objekty se nevztahují k množství dat, které můžete přesunout a zpracovat pomocí Azure Data Factory. Data Factory je navržen tak, aby škálování pro zpracování petabajtů dat.

<sup>2</sup> Jádra HDInsight na vyžádání jsou přidělena z předplatného, které obsahuje datovou továrnu. V důsledku toho předchozí limit je data factory vynucený základní limit pro jádra HDInsight na vyžádání. Liší se od základního limitu, který je spojený s vaším předplatným Azure.

<sup>3</sup> Jednotka pro pohyb dat v cloudu (DMU) pro verzi 1 se používá v operaci kopírování z cloudu na cloud, další informace se dozvíte více z [jednotek přesunu dat v cloudu (verze 1).](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) Informace o fakturaci najdete v [tématu Ceny Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Zdrojů** | **Výchozí dolní limit** | **Minimální limit** |
| --- | --- | --- |
| Interval plánování |15 minut |15 minut |
| Interval mezi pokusy o opakování |1 sekunda |1 sekunda |
| Hodnota časového času opakování |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Omezení volání webové služby
Azure Resource Manager má limity pro volání rozhraní API. Volání rozhraní API můžete provádět v rámci [omezení rozhraní API Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
