---
title: Povolit protokolování diagnostiky pro Batch události - Azure | Microsoft Docs
description: Zaznamenávat a analyzovat události protokolů diagnostiky pro prostředkům účet Azure Batch, jako jsou fondy a úkoly.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41c8c9f8fd9302c610ce356b0485e33ea3c967d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Události protokolu diagnostiky hodnocení a sledování řešení Batch

Stejně jako u řadou služeb Azure, služba Batch vysílá události protokolu určitých prostředků během doby platnosti prostředku. Můžete povolení diagnostických protokolů Azure Batch zaznamenat události pro prostředkům, jako jsou fondy a úkoly a pak použít protokoly diagnostiky hodnocení a sledování. Vytvořit událostmi, jako je fondu, fond odstranit, spuštění úloh, úloha ukončena a jiné jsou součástí Batch diagnostické protokoly.

> [!NOTE]
> Tento článek popisuje protokolování událostí pro prostředky na účtu Batch, sami, není úloh a úloh výstupní data. Informace o ukládání výstupních dat úloh a úloh, najdete v tématu [zachovat Azure Batch výstup úlohy a úkolů](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Požadavky
* [Účet Azure Batch](batch-account-create-portal.md)
* [účet služby Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Udržení diagnostické protokoly Batch, musíte vytvořit účet úložiště Azure, kde Azure ukládat protokoly. Zadejte účet úložiště při jste [povolit protokolování diagnostiky](#enable-diagnostic-logging) vašeho účtu Batch. Účet úložiště, který zadáte, když povolíte protokol kolekce není stejný jako propojený účet úložiště uvedené v [balíčky aplikací](batch-application-packages.md) a [úloh výstup trvalost](batch-task-output.md) články.
  
  > [!WARNING]
  > Jste **účtovat** data uložená v účtu úložiště Azure. To zahrnuje diagnostických protokolů, které jsou popsané v tomto článku. Mějte na paměti při navrhování vaší [protokolu zásady uchovávání informací](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky
Ve výchozím nastavení vašeho účtu Batch není povoleno protokolování diagnostiky. Je potřeba explicitně povolit protokolování diagnostiky pro každého účtu Batch, kterou chcete sledovat:

[Postup povolení shromažďování diagnostických protokolů](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Doporučujeme, abyste si přečetli celý [přehled o Azure diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) článku získat představu o není pouze to, jak povolit protokolování, ale kategorií protokolu podporuje různé služby Azure. Například Azure Batch aktuálně podporuje jednu kategorii protokolu: **protokoly služby**.

## <a name="service-logs"></a>Protokoly služby
Protokoly služby Azure Batch obsahovat události vygenerované službou Azure Batch po dobu životnosti prostředků Batch jako fondu nebo úloh. Každá událost vysílaných Batch je uložený v zadaném účtu úložiště ve formátu JSON. To je třeba do těla ukázku **fondu vytvoření události**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Každé události textu se nachází v soubor .json v zadaném účtu úložiště Azure. Pokud chcete získat přímo přístup k protokoly, můžete zkontrolovat [schéma diagnostických protokolů v účtu úložiště](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Služba Protokol událostí
Služba Batch aktuálně vysílá následující události služby protokolu. Tento seznam nemusí být kompletní, protože další události mohly být přidány od poslední aktualizace v tomto článku.

| **Služba Protokol událostí** |
| --- |
| [Vytvoření fondu][pool_create] |
| [Spuštění odstranění fondu][pool_delete_start] |
| [Odstranění fondu dokončení][pool_delete_complete] |
| [Počáteční velikosti fondu][pool_resize_start] |
| [Dokončení změny velikosti fondu][pool_resize_complete] |
| [Spuštění úlohy][task_start] |
| [Dokončení úkolů][task_complete] |
| [Selhání úlohy][task_fail] |

## <a name="next-steps"></a>Další postup
Kromě ukládání diagnostických protokolů událostí v účtu Azure Storage, můžete také stream události protokolu služby Batch [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md)a pošlete je [Azure Log Analytics](../log-analytics/log-analytics-overview.md).

* [Stream Azure diagnostických protokolů do centra událostí](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Stream diagnostických událostí Batch ke službě příjem příchozích dat vysoce škálovatelné datového centra událostí. Centra událostí můžete přijímat miliony událostí za sekundu, které můžete transformovat a ukládat pomocí libovolného zprostředkovatele analýzu v reálném čase.
* [Analýza Azure diagnostických protokolů pomocí analýzy protokolů](../log-analytics/log-analytics-azure-storage.md)
  
  Odesílání diagnostických protokolů k analýze protokolů, kde můžete analyzovat na portálu Operations Management Suite (OMS), nebo exportovat pro analýzu v Power BI nebo Excelu.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
