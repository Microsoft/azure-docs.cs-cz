---
title: Monitorování umístění dat ve službě Azure Monitor | Dokumentace Microsoftu
description: Popisuje různých umístěních, kde monitorování data uložená v Azure, včetně datová Platforma Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 61f34277a03deb312e93920e3bd76ce5297f020f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357540"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorování umístění dat ve službě Azure Monitor

Azure Monitor je založen na [datovou platformu](data-platform.md) z [protokoly](data-platform-logs.md) a [metriky](data-platform-metrics.md) jak je popsáno v [datová Platforma Azure Monitor](data-platform.md). Data monitorování z prostředků Azure může být zapsáno do jiných umístění, buď předtím, než jsou zkopírovány do Azure monitoru nebo pro podporu dalších scénářů. 

## <a name="monitoring-data-locations"></a>Umístění dat monitorování

V následující tabulce jsou uvedeny různých umístěních, kde je odeslána monitorování dat v Azure a různé metody pro přístup.

| Location | Popis | Metody přístupu |
|:---|:---|:---|:--|
| Metriky Azure Monitor | Databáze časových řad, který je optimalizovaný pro analýzu dat s časovým razítkem. | [Průzkumník metrik](metrics-getting-started.md)<br>[Azure Monitor metriky rozhraní API](/rest/api/monitor/metrics) |
| Azure Monitor Logs    | Pracovní prostor log Analytics, která je založena na Průzkumník dat Azure, které poskytuje výkonné analýzy modul a bohaté dotazovací jazyk. | [Log Analytics](../log-query/portals.md)<br>[Rozhraní API služby log Analytics](https://dev.loganalytics.io/)<br>[Rozhraní API služby Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Protokol aktivit | Data od protokolů aktivit jsou nejužitečnější tehdy, když odeslat protokoly Azure monitoru k analýze s ostatními daty, ale také shromáždění zpracovat sama o sobě, je možné přímo zobrazit na webu Azure Portal. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Azure Monitor události rozhraní API](/rest/api/monitor/eventcategories) |
| Azure Storage | Některé zdroje dat bude zapisovat přímo do služby Azure storage a bude vyžadovat konfigurace pro přesun dat do protokolů. Můžete také odeslat data do úložiště Azure pro archivaci a integraci s externími systémy.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Průzkumník serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/visualstudio/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Odesílání dat do služby Azure Event Hubs ke streamování do jiných umístění. | [Funkci Capture ve službě Storage](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor pro virtuální počítače | Azure Monitor pro virtuální počítače ukládá data o stavu úlohy do vlastního umístění, který používá jeho možnosti monitorování na webu Azure Portal. | [Azure Portal](../insights/vminsights-overview.md)<br>[Úloha monitorování rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure Resource health rozhraní REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Výstrahy | Výstrahy vytvořené službou Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[Rozhraní REST API pro správu výstrah](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Další postup

- Zobrazit různých zdrojích [monitorování dat shromážděných službou Azure Monitor](data-sources.md).
- Další informace o [typy monitorování dat shromážděných službou Azure Monitor](data-platform.md) a jak zobrazit a analyzovat tato data.
