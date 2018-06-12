---
title: Zdroje dat v Azure monitorování
description: Informace o všech monitorování zdroje dat k dispozici v Azure ještě dnes.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/27/2017
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 7b3cb869f3c220c20db53216c96449d01c16eaf6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263173"
---
# <a name="consume-monitoring-data-from-azure"></a>Využívat monitorování dat z Azure

Na platformě Azure jsme přináší společně dat monitorování na jednom místě s monitorováním Azure kanálu, ale prakticky vědomí, že se ještě dnes všechna data monitorování zatím k dispozici v tomto kanálu. V tomto článku jsme shrnuje různé způsoby, kterými data sledování můžete programově přistupovat ze služby Azure.

## <a name="options-for-data-consumption"></a>Možnosti spotřeby dat

| Typ dat | Kategorie | Podporované služby | Metody přístupu |
| --- | --- | --- | --- |
| Metriky úrovni platformy Azure monitorování | Metriky | [Najdete zde seznamu](monitoring-supported-metrics.md) | <ul><li>**Rozhraní API REST:** [metriky rozhraní API Azure monitorování](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Úložiště objektů blob nebo událost rozbočovače:** [nastavení diagnostiky](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Výpočetní hostovaného operačního systému (např metriky. čítače výkonu) | Metriky | [Windows](../virtual-machines-dotnet-diagnostics.md) a virtuální počítače s Linuxem (v2), [cloudových služeb](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Úložiště table nebo objekt blob:** [Windows nebo Linux Azure diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum událostí:** [Windows Azure diagnostics](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Metriky vlastní nebo aplikace | Metriky | Všechny aplikace instrumentovány s Application Insights | <ul><li>**Rozhraní API REST:** [Application Insights rozhraní REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Metriky úložiště | Metriky | Azure Storage | <ul><li>**Tabulka úložiště:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Fakturační údaje | Metriky | Všechny služby Azure | <ul><li>**Rozhraní API REST:** [využití prostředků Azure a rozhraní API RateCard](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Protokol aktivit | Události | Všechny služby Azure | <ul><li>**Rozhraní API REST:** [Azure sledování událostí rozhraní API](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Úložiště objektů blob nebo událost rozbočovače:** [profil protokolu](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Diagnostické protokoly Azure monitorování | Události | [Najdete zde seznamu](monitoring-diagnostic-logs-schema.md) | <ul><li>**Úložiště objektů blob nebo událost rozbočovače:** [nastavení diagnostiky](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Výpočetní hostovaného operačního systému (např protokoly. Systémové protokoly služby IIS, ETW) | Události | [Windows](../virtual-machines-dotnet-diagnostics.md) a virtuální počítače s Linuxem (v2), [cloudových služeb](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Úložiště table nebo objekt blob:** [Windows nebo Linux Azure diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum událostí:** [Windows Azure diagnostics](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Protokoly služby App Service | Události | Aplikační služby | <ul><li>**Soubor, tabulku nebo úložiště objektů blob:** [webové diagnostiky aplikací](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Protokoly úložiště | Události | Azure Storage | <ul><li>**Tabulka úložiště:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Výstrahy Security Center | Události | Azure Security Center | <ul><li>**Rozhraní API REST:** [výstrahy zabezpečení](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Generování sestav služby Active Directory | Události | Azure Active Directory | <ul><li>**Rozhraní API REST:** [Azure Active Directory graph API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Stav prostředku Security Center | Status | [Všechny podporované prostředky](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**Rozhraní API REST:** [stavy zabezpečení](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Resource Health | Status | Podporované služby | <ul><li>**Rozhraní API REST:** [stav prostředku REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure monitorování metriky výstrah | Oznámení | [Najdete zde seznamu](monitoring-supported-metrics.md) | <ul><li>**Webhooku:** [Azure metriky výstrahy](insights-webhooks-alerts.md)</li></ul> |
| Azure výstrahy protokolu aktivity monitorování. | Oznámení | Všechny služby Azure | <ul><li>**Webhooku:** Azure protokol aktivit výstrahy</li></ul> |
| Oznámení o automatickém škálování | Oznámení | [Najdete zde seznamu](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhooku:** [škálování oznámení webhooku datové části schématu](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Přihlaste se výstrahy vyhledávací dotaz. | Oznámení | Log Analytics | <ul><li>**Webhooku:** [výstrahy analýzy protokolů](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Metriky výstrah statistiky aplikace | Oznámení | Application Insights | <ul><li>**Webhooku:** [Application Insights výstrahy](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights webové testy | Oznámení | Application Insights | <ul><li>**Webhooku:** [Application Insights výstrahy](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Další postup

- Další informace o [metrik Azure monitorování](monitoring-overview-metrics.md)
- Další informace o [protokol činnosti Azure](monitoring-overview-activity-logs.md)
- Další informace o [diagnostických protokolů Azure.](monitoring-overview-of-diagnostic-logs.md)
