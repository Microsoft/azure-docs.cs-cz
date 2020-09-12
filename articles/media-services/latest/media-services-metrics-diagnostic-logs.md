---
title: Media Services metriky a diagnostické protokoly s Azure Monitor
titleSuffix: Azure Media Services
description: Naučte se monitorovat metriky Azure Media Services a diagnostické protokoly přes Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c03950d64c9ead17dfa5c07ef70ab2b7ee0e90bb
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89296647"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitorovat metriky Media Services a diagnostické protokoly přes Azure Monitor

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) vám umožní monitorovat metriky a diagnostické protokoly, které vám pomůžou pochopit, jak vaše aplikace provádí. Všechna data shromažďovaná Azure Monitor se vejdou do jednoho ze dvou základních typů: metriky a protokoly. Můžete monitorovat protokoly diagnostiky Media Services a vytvářet výstrahy a oznámení pro shromážděné metriky a protokoly. Data metriky můžete vizualizovat a analyzovat pomocí [Průzkumníka metrik](../../azure-monitor/platform/metrics-getting-started.md). Můžete odeslat protokoly do [Azure Storage](https://azure.microsoft.com/services/storage/), streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), exportovat je do [Log Analytics](https://azure.microsoft.com/services/log-analytics/)nebo použít služby třetích stran.

Podrobný přehled najdete v tématu [Azure monitor metriky](../../azure-monitor/platform/data-platform.md) a [Azure monitor diagnostických protokolů](../../azure-monitor/platform/platform-logs-overview.md).

Toto téma popisuje podporované [Media Services metriky](#media-services-metrics) a [protokoly Media Services diagnostiky](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Media Services metriky

Metriky se shromažďují v pravidelných intervalech bez ohledu na to, jestli se hodnota mění. Jsou užitečné pro upozorňování, protože je možné je vzorkovat často a výstraha se dá rychle aktivovat s relativně jednoduchou logikou. Informace o tom, jak vytvořit výstrahy metriky, najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../../azure-monitor/platform/alerts-metric.md).

Media Services podporuje monitorování metrik pro následující prostředky:

* Účet
* Koncový bod streamování

### <a name="account"></a>Účet

Můžete monitorovat následující metriky účtu.

|Název metriky|Zobrazované jméno|Popis|
|---|---|---|
|AssetCount|Počet assetů|Prostředky ve vašem účtu.|
|AssetQuota|Kvóta prostředků|Kvóta prostředků ve vašem účtu.|
|AssetQuotaUsedPercentage|Procento využité kvóty prostředků|Procento kvóty Assetu se už používá.|
|ContentKeyPolicyCount|Počet zásad klíče obsahu|Zásady klíčů obsahu ve vašem účtu.|
|ContentKeyPolicyQuota|Kvóta zásad pro klíč obsahu|Kvóta zásad pro klíče obsahu ve vašem účtu.|
|ContentKeyPolicyQuotaUsedPercentage|Procento využité kvóty zásad klíčů obsahu|Procento již používané kvóty zásad klíčů obsahu.|
|StreamingPolicyCount|Počet zásad streamování|Zásady streamování ve vašem účtu.|
|StreamingPolicyQuota|Kvóta zásad streamování|Kvóta zásad streamování ve vašem účtu.|
|StreamingPolicyQuotaUsedPercentage|Procento využité kvóty zásad streamování|Procento použité kvóty zásad streamování se už používá.|

Měli byste taky zkontrolovat [kvóty a omezení účtu](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování

Jsou podporovány následující Media Services metriky [koncových bodů streamování](/rest/api/media/streamingendpoints) :

|Název metriky|Zobrazované jméno|Popis|
|---|---|---|
|Žádosti|Žádosti|Poskytuje celkový počet požadavků HTTP poskytovaných koncovým bodem streamování.|
|Výchozí přenos dat|Výchozí přenos dat|Celkový počet odchozích bajtů za minutu na koncový bod streamování.|
|SuccessE2ELatency|Koncová latence úspěch|Doba trvání od okamžiku, kdy koncový bod streamování přijal požadavek na odeslání posledního bajtu odpovědi.|

### <a name="why-would-i-want-to-use-metrics"></a>Proč bych chtěl použít metriky?

Tady jsou příklady, jak vám monitorování Media Services metriky můžou porozumět tomu, jak vaše aplikace provádí. Některé otázky, které je možné řešit pomocí Media Services metriky:

* Návody monitorování koncového bodu standardního streamování, abyste věděli, že překročili jste limity?
* Návody vědět, jestli mám dostatek jednotek škálování koncového bodu Premium pro streamování?
* Jak můžu nastavit výstrahu, která bude vědět, kdy se má změnit velikost svých koncových bodů streamování?
* Návody nastavit výstrahu, která ví, kdy bylo dosaženo maximálního počtu výstupů nakonfigurovaných v účtu?
* Jak zjistím, že se nedaří rozčlenění požadavků a co způsobuje selhání?
* Jak můžu zjistit, kolik požadavků na HLS nebo POMLČKu se z tohoto balíčku vyřazuje?
* Návody nastavit výstrahu, která ví, že se dosáhlo prahové hodnoty # neúspěšných požadavků?

### <a name="example"></a>Příklad

Podívejte [se, jak monitorovat metriky Media Services](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Protokoly diagnostiky Media Services

Diagnostické protokoly poskytují bohatou a častou data o provozu prostředku Azure. Další informace najdete v tématu [Jak shromažďovat a využívat data protokolu z vašich prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md).

Media Services podporuje následující diagnostické protokoly:

* Doručení klíče

### <a name="key-delivery"></a>Doručení klíče

|Název|Popis|
|---|---|
|Požadavek služby doručení klíčů|Protokoly, které zobrazují informace o požadavku služby doručování klíčů Další informace najdete v tématu [schémata](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Proč bych chtěl použít diagnostické protokoly?

Mezi další věci, které můžete prozkoumávat pomocí protokolů pro diagnostiku doručení klíčů, patří:

* Podívejte se na počet licencí dodaných podle typu DRM.
* Podívejte se na počet licencí dodaných podle zásad.
* Podívejte se na chyby podle DRM nebo typu zásad.
* Podívejte se na počet neautorizovaných žádostí o licenci od klientů.

### <a name="example"></a>Příklad

Viz [jak monitorovat diagnostické protokoly služby Media Service](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Další kroky

* [Jak shromažďovat a využívat data protokolu z vašich prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Vytváření, zobrazení a správa upozornění na metriky pomocí služby Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Jak monitorovat metriky Media Services](media-services-metrics-howto.md)
* [Monitorování diagnostických protokolů služby Media Service](media-services-diagnostic-logs-howto.md)
