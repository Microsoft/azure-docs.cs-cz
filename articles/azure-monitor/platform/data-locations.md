---
title: Monitorování umístění dat v Azure Monitor | Microsoft Docs
description: V této části najdete popis různých umístění, ve kterých jsou data monitorování uložená v Azure, včetně datové platformy Azure Monitor.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: bwren
ms.openlocfilehash: 6f6071bc1d3d7514d22658a07810690ec8a8056b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972758"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>Monitorování umístění dat v Azure Monitor

Azure Monitor je založena na [datové platformě](data-platform.md) [protokolů](data-platform-logs.md) a [metrik](data-platform-metrics.md) , jak je popsáno v [Azure monitor datovou platformu](data-platform.md). Data monitorování z prostředků Azure se můžou zapisovat do jiných míst i po jejich zkopírování do Azure Monitor nebo k podpoře dalších scénářů. 

## <a name="monitoring-data-locations"></a>Umístění dat monitorování

Následující tabulka uvádí různá umístění, kde jsou odesílána data monitorování v Azure, a různé metody pro přístup k nim.

| Umístění | Popis | Metody přístupu |
|:---|:---|:---|:--|
| Azure Monitor metriky | Databáze časových řad, která je optimalizována pro analýzu časových údajů s časovým razítkem. | [Průzkumník metrik](metrics-getting-started.md)<br>[Rozhraní API pro Azure Monitor metriky](/rest/api/monitor/metrics) |
| Protokoly služby Azure Monitor    | Log Analytics pracovní prostor založený na Azure Průzkumník dat, který poskytuje výkonný analytický modul a bohatý dotazovací jazyk. | [Log Analytics](../log-query/portals.md)<br>[Rozhraní API pro Log Analytics](https://dev.loganalytics.io/)<br>[Rozhraní API pro Application Insights](https://dev.applicationinsights.io/reference/get-query) |
| Protokol aktivit | Data z protokolu aktivit jsou nejužitečnější, když jsou odesílána do Azure Monitor protokolů, aby je bylo možné analyzovat s ostatními daty, ale jsou shromažďována i samostatně, takže je lze přímo zobrazit v Azure Portal. | [Azure Portal](activity-log-view.md#azure-portal)<br>[Rozhraní API pro Azure Monitor události](/rest/api/monitor/eventcategories) |
| Azure Storage | Některé zdroje dat budou zapisovat přímo do služby Azure Storage a vyžadují, aby konfigurace přesunula data do protokolů. Můžete také odesílat data do služby Azure Storage pro archivaci a pro integraci s externími systémy.  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Průzkumník serveru](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[Průzkumník služby Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| Event Hubs | Odešlete data do Azure Event Hubs, abyste je mohli streamovat do jiných umístění. | [Zachytit do úložiště](../../event-hubs/event-hubs-capture-overview.md)  |
| Azure Monitor pro virtuální počítače | Azure Monitor pro virtuální počítače ukládá data o stavu úloh ve vlastním umístění, které používá jeho prostředí pro monitorování v Azure Portal. | [Azure Portal](../insights/vminsights-overview.md)<br>[REST API monitorování úloh](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[REST API stavu prostředků Azure](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| Výstrahy | Výstrahy vytvořené pomocí Azure Monitor. | [Azure Portal](alerts-managing-alert-instances.md)<br>[REST API správy výstrah](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>Další kroky

- Podívejte se na různé zdroje [dat monitorování shromažďovaných pomocí Azure monitor](data-sources.md).
- Přečtěte si o [typech dat monitorování shromažďovaných nástrojem Azure monitor](data-platform.md) a o tom, jak tato data zobrazit a analyzovat.
