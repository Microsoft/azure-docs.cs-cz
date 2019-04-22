---
title: Monitorování metrik služby Media Services a diagnostické protokoly prostřednictvím služby Azure Monitor | Dokumentace Microsoftu
description: Tento článek poskytuje přehled o tom, jak monitorovat metriky služby Media Services a diagnostické protokoly prostřednictvím služby Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 23c87ae92a0f22b4a1a31c054df730af2efc07d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785081"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorování metrik služby Media Services a diagnostické protokoly

[Azure Monitor](../../azure-monitor/overview.md) umožňuje monitorování metrik a diagnostické protokoly, které vám pomohou pochopit, jaký výkon vašich aplikací. Všechna data shromážděná službou Azure Monitor se vejde do jednoho ze dvou základních typů, metrik a protokolů. Můžete sledovat diagnostické protokoly služby Media Services a vytvořte si upozornění a oznámení o shromážděných metrik a protokolů. Můžete vizualizovat a analyzovat data metrik s využitím [Průzkumníka metrik](../../azure-monitor/platform/metrics-getting-started.md). Můžete odeslat protokoly do [služby Azure Storage](https://azure.microsoft.com/services/storage/), Streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [Log Analytics](https://azure.microsoft.com/services/log-analytics/), nebo použít služby 3. stran.

Podrobný přehled najdete v tématu [metrik Azure monitoru](../../azure-monitor/platform/data-platform.md) a [Azure Monitor diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md).

Toto téma popisuje aktuálně k dispozici [Media Services metriky](#media-services-metrics) a [Media Services diagnostické protokoly](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metriky služby Media Services

Metriky se budou shromažďovat v pravidelných intervalech, zda hodnoty. Jsou užitečné pro výstrahy, protože můžou být často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou.

V současné době následující Media Services [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) metriky jsou emitovány Azure:

|Název|Popis|
|---|---|
|Požadavky|Poskytuje podrobnosti o celkový počet požadavků, které jsou obsluhovány pomocí koncového bodu streamování.|
|Výchozí přenos|Celkový počet bajtů výchozí přenos. Například bajtů streamují koncový bod streamování.|
|Úspěchu celkovou latencí do| Poskytuje informace o komplexní latence úspěšných požadavků.|

Například získat metriky "Výchozí přenos dat" pomocí rozhraní příkazového řádku, spustili byste následující `az monitor metrics` příkazu rozhraní příkazového řádku:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Informace o tom, jak vytvářet upozornění metrik, najdete v tématu [vytvoření, zobrazení a Správa upozornění na metriku pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>Diagnostické protokoly služby Media Services

V současné době můžete získat následující diagnostických protokolů:

|Název|Popis|
|---|---|
|Žádost o doručení klíče služby|Protokoly, které zobrazují informace o žádosti o doručení klíče služby. Další podrobnosti najdete v tématu [schémata](media-services-diagnostic-logs-schema.md).|

Povolit ukládání diagnostických protokolů v účtu úložiště, spustili byste následující `az monitor diagnostic-settings` příkazu rozhraní příkazového řádku: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Příklad:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>Další postup 

[Tom, jak shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).
