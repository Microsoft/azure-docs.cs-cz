---
title: Monitorování datových umístění ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje různá umístění, kde jsou data monitorování uložená v Azure, včetně datové platformy Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666611"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorování datových umístění ve službě Azure Monitor

Azure Monitor je založený na [datové platformě](data-platform.md) [protokolů](data-platform-logs.md) a [metrik,](data-platform-metrics.md) jak je popsáno v [datové platformě Azure Monitor](data-platform.md). Data monitorování z prostředků Azure se můžou zapisovat do jiných umístění, ale před jejich zkopírováním do Azure Monitoru nebo pro podporu dalších scénářů. 

## <a name="monitoring-data-locations"></a>Monitorování datových míst

V následující tabulce jsou uvedeny různá umístění, kam se data monitorování v Azure odesílají, a různé metody pro přístup k nim.

| Umístění | Popis | Metody přístupu |
|:---|:---|:---|:--|
| Metriky azure monitoru | Databáze časových řad, která je optimalizována pro analýzu dat s časovým razítkem. | [Průzkumník metrik](metrics-getting-started.md)<br>[Rozhraní API metrik azure monitoru](/rest/api/monitor/metrics) |
| Protokoly služby Azure Monitor    | Pracovní prostor Log Analytics, který je založený na Azure Data Explorer, který poskytuje výkonný modul analýzy a bohatý dotazovací jazyk. | [Log Analytics](../log-query/portals.md)<br>[Rozhraní API analýzy protokolů](https://dev.loganalytics.io/)<br>[Rozhraní API Přehledů aplikací](https://dev.applicationinsights.io/reference/get-query) |
| Protokol aktivit | Data z protokolu aktivit je nejužitečnější při odeslání do protokolů monitorování Azure k jejich analýze s jinými daty, ale také se shromažďují samostatně, aby je bylo možné přímo zobrazit na webu Azure Portal. | [Portál Azure](activity-log-view.md#azure-portal)<br>[Rozhraní API událostí monitorování Azure](/rest/api/monitor/eventcategories) |
| Azure Storage | Některé zdroje dat budou zapisovat přímo do úložiště Azure a vyžadují konfiguraci pro přesun dat do protokolů. Data můžete také odesílat do úložiště Azure pro archivaci a integraci s externími systémy.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Průzkumník serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Odesílejte data do Centra událostí Azure a streamujte je do jiných umístění. | [Digitalizace do úložiště](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor pro virtuální počítače | Azure Monitor pro virtuální počítače ukládá data o stavu úloh ve vlastním umístění, které se používá jeho prostředí pro monitorování na webu Azure Portal. | [Portál Azure](../insights/vminsights-overview.md)<br>[Rozhraní REST MONITOROVÁNÍ Úlohy](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Rozhraní REST stavu prostředků Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Výstrahy | Výstrahy vytvořené službou Azure Monitor. | [Portál Azure](alerts-managing-alert-instances.md)<br>[Rozhraní REST API pro správu výstrah](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Další kroky

- Podívejte se na různé zdroje [dat monitorování shromážděných službou Azure Monitor](data-sources.md).
- Přečtěte si [o typech dat monitorování shromážděných službou Azure Monitor](data-platform.md) a o tom, jak tato data zobrazit a analyzovat.
