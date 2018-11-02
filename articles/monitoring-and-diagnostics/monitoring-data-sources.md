---
title: Zdroje dat v Azure monitorování
description: Informace o všech monitorování zdroje dat dostupné v Azure ještě dnes.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: f5d1968503c7aa82ba58c80eec886b19fc44b4f5
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913589"
---
# <a name="consume-monitoring-data-from-azure"></a>Používat data monitorování z Azure

Celou platformu Azure Dáváme dohromady data monitorování na jednom místě díky Azure monitoru kanálu, ale prakticky vědomí, že se ještě dnes nejsou všechna data monitorování zatím k dispozici v tomto kanálu. V tomto článku se Shrneme informace z různých způsobů, jak můžete programově přístup k datům monitorování ze služeb Azure.

## <a name="options-for-data-consumption"></a>Možnosti využití dat

| Typ dat | Kategorie | Podporované služby | Metody přístupu |
| --- | --- | --- | --- |
| Metriky Azure Monitor úrovni platformy | Metriky | [Podívejte se sem seznam](monitoring-supported-metrics.md) | <ul><li>**Rozhraní REST API:** [metriky rozhraní API Azure monitoru](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Úložiště objektů blob nebo event hub:** [nastavení diagnostiky](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| Výpočet metrik OS hosta (např.) čítače výkonu) | Metriky | [Windows](../virtual-machines-dotnet-diagnostics.md) a virtuální počítače s Linuxem (v2), [cloudových služeb](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Storage table nebo blob:** [Windows nebo Linux Azure diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum událostí:** [Windows Azure diagnostics](azure-diagnostics-streaming-event-hubs.md)</li></ul> |
| Aplikace nebo vlastních metrik | Metriky | Všechny aplikace používaná s Application Insights | <ul><li>**Rozhraní REST API:** [rozhraní REST API služby Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Metriky úložiště | Metriky | Azure Storage | <ul><li>**Tabulka úložiště:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Fakturační údaje | Metriky | Všechny služby Azure | <ul><li>**Rozhraní REST API:** [RateCard API a využití prostředků Azure](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Protokol aktivit | Události | Všechny služby Azure | <ul><li>**Rozhraní REST API:** [události Azure Monitor, rozhraní API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Úložiště objektů blob nebo event hub:** [profilu protokolu](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Diagnostické protokoly Azure monitoru | Události | [Podívejte se sem seznam](monitoring-diagnostic-logs-schema.md) | <ul><li>**Úložiště objektů blob nebo event hub:** [nastavení diagnostiky](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| COMPUTE (např protokoly operačního systému hosta. Služba IIS, trasování událostí pro Windows, systémových protokolů) | Události | [Windows](../virtual-machines-dotnet-diagnostics.md) a virtuální počítače s Linuxem (v2), [cloudových služeb](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Storage table nebo blob:** [Windows nebo Linux Azure diagnostics](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Centrum událostí:** [Windows Azure diagnostics](azure-diagnostics-streaming-event-hubs.md)</li></ul> |
| Protokoly služby App Service | Události | Aplikační služby | <ul><li>**Soubor, tabulka nebo úložiště objektů blob:** [webové diagnostiky aplikací](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Protokoly úložiště | Události | Azure Storage | <ul><li>**Tabulka úložiště:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Výstrahy služby Security Center | Události | Azure Security Center | <ul><li>**Rozhraní REST API:** [výstrahy zabezpečení](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Generování sestav v Active Directory | Události | Azure Active Directory | <ul><li>**Rozhraní REST API:** [Azure Active Directory graph API](../active-directory/active-directory-reporting-api-getting-started-azure-portal.md)</li></ul> |
| Stav prostředku Security Center | Status | [Všechny podporované prostředky](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**Rozhraní REST API:** [stavy zabezpečení](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Resource Health | Status | Podporované služby | <ul><li>**Rozhraní REST API:** [Resource health rozhraní REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Upozornění metrik Azure monitoru | Oznámení | [Podívejte se sem seznam](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [Azure upozornění na metriku](insights-webhooks-alerts.md)</li></ul> |
| Upozornění protokolu aktivit monitorování Azure | Oznámení | Všechny služby Azure | <ul><li>**Webhook:** upozornění protokolů aktivit Azure</li></ul> |
| Oznámení o automatickém škálování | Oznámení | [Podívejte se sem seznam](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [schéma datová část webhooku oznámení automatického škálování](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Vyhledávací dotaz upozornění protokolů | Oznámení | Log Analytics | <ul><li>**Webhook:** [akce Webhooku pro pravidla upozornění protokolů](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)</li></ul> |
| Upozornění metrik Application Insights | Oznámení | Application Insights | <ul><li>**Webhook:** [oznámení Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Webové testy Application Insights | Oznámení | Application Insights | <ul><li>**Webhook:** [oznámení Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Další postup

- Další informace o [metrik Azure monitoru](../monitoring/monitoring-data-collection.md)
- Další informace o [protokolu aktivit Azure](monitoring-overview-activity-logs.md)
- Další informace o [diagnostické protokoly Azure](monitoring-overview-of-diagnostic-logs.md)
