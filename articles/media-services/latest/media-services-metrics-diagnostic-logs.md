---
title: Monitorování služby Azure Media Services metriky a diagnostické protokoly prostřednictvím služby Azure Monitor | Dokumentace Microsoftu
description: Tento článek poskytuje přehled o tom, jak monitorovat Azure Media Services metriky a diagnostické protokoly prostřednictvím služby Azure Monitor.
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806000"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorování metrik služby Media Services a diagnostické protokoly

[Azure Monitor](../../azure-monitor/overview.md) umožňuje monitorování metrik a diagnostické protokoly, které vám pomohou pochopit, jaký výkon vašich aplikací. Všechna data shromážděná službou Azure Monitor se vejde do jednoho ze dvou základních typů, metriky a protokoly. Můžete sledovat diagnostické protokoly služby Media Services a vytvořte si upozornění a oznámení o shromážděných metrik a protokolů. Můžete vizualizovat a analyzovat data metrik s využitím [Průzkumníka metrik](../../azure-monitor/platform/metrics-getting-started.md). Můžete odeslat protokoly do [služby Azure Storage](https://azure.microsoft.com/services/storage/), Streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [Log Analytics](https://azure.microsoft.com/services/log-analytics/), nebo použít služby 3. stran.

Podrobnější přehled najdete v části [metrik Azure monitoru](../../azure-monitor/platform/data-platform.md) a [Azure Monitor diagnostické protokoly](../../azure-monitor/platform/diagnostic-logs-overview.md).

Toto téma popisuje podporované [Media Services metriky](#media-services-metrics) a [Media Services diagnostické protokoly](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metriky služby Media Services

Metriky se budou shromažďovat v pravidelných intervalech, zda hodnoty. Jsou užitečné pro výstrahy, protože můžou být často Vzorkovaná a upozornění můžete aktivuje rychle s relativně jednoduché logikou. Informace o tom, jak vytvářet upozornění metrik, najdete v tématu [vytvoření, zobrazení a Správa upozornění na metriku pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md).

Služba Media Services podporuje monitorování metrik pro následující prostředky:

* Účet
* Koncový bod streamování
 
### <a name="account"></a>Účet

Můžete monitorovat následující metriky účtu. 

|Název metriky|Display name|Popis|
|---|---|---|
|AssetCount|Počet prostředků|Prostředky ve vašem účtu.|
|AssetQuota|Prostředek kvóty|Prostředek kvóty ve vašem účtu.|
|AssetQuotaUsedPercentage|Procento využité místo pro prostředek|Procento kvóty prostředků už používá.|
|ContentKeyPolicyCount|Počet obsahu klíče zásad|Zásady obsahu klíčů ve vašem účtu.|
|ContentKeyPolicyQuota|Obsahu klíče zásad kvót|Kvóta obsahu zásady klíčů ve vašem účtu.|
|ContentKeyPolicyQuotaUsedPercentage|Použít procento obsahu klíče zásad kvót|Procento kvóty obsahu zásad klíče už používá.|
|StreamingPolicyCount|Počet datových proudů zásad|Streamování zásad ve vašem účtu.|
|StreamingPolicyQuota|Streamování zásad kvót|Streamování zásady kvót ve vašem účtu.|
|StreamingPolicyQuotaUsedPercentage|Použít procento streamování zásad kvót|Procento kvóty streamování zásady už používá.|
 
Také byste měli revidovat [účtu kvóty a omezení](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování

Tyto služby Media [koncové body streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) metriky jsou podporovány:

|Název metriky|Display name|Popis|
|---|---|---|
|Požadavky|Požadavky|Poskytuje celkový počet požadavků HTTP, které jsou obsluhovány koncový bod streamování.|
|Výchozí přenos|Výchozí přenos|Celkový počet bajtů výchozí přenos. Například bajtů streamují koncový bod streamování.|
|SuccessE2ELatency|Úspěchu celkovou latencí do|Doba trvání od přijetí požadavku k odeslání poslední bajt odpovědi koncového bodu streamování.|

### <a name="why-would-i-want-to-use-metrics"></a>Proč byste měli použít metriky? 

Tady jsou příklady, jak monitorování metrik služby Media Services vám pomůžou pochopit, jaký výkon vašich aplikací. Několik otázek, které lze řešit pomocí metrik služby Media Services jsou:

* Jak můžu monitorovat tento koncový bod streamování Standard při překročení limitů?
* Jak poznám, že mám dostatek jednotek škálování koncových bodů streamování Premium? 
* Jak můžete nastavit upozornění, abyste vědět, kdy chcete vertikálně navýšit kapacitu Moje koncové body streamování?
* Jak nastavím výstrahy vědět, kdy bylo dosaženo maximální nakonfigurovaný na účtu výchozí přenos dat?
* Jak lze zobrazit rozpis neúspěšných požadavků a co je příčinou selhání?
* Jak zjistím, kolik požadavků HLS nebo DASH se se berou z Balíčkovače?
* Jak nastavím výstrahy vědět, kdy bylo dosaženo prahová hodnota počet neúspěšných požadavků? 

### <a name="example"></a>Příklad

Zobrazit [monitorování metrik služby Media Services](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Diagnostické protokoly služby Media Services

Diagnostické protokoly poskytují bohatou, časté data o provozu prostředku Azure. Další informace najdete v tématu [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Služba Media Services podporuje následující protokoly diagnostiky:

* Doručení klíče

### <a name="key-delivery"></a>Doručení klíče

|Name|Popis|
|---|---|
|Žádost o doručení klíče služby|Protokoly, které zobrazují informace o žádosti o doručení klíče služby. Další podrobnosti najdete v tématu [schémata](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Proč byste měli používat protokoly diagnostiky? 

Některé věci, které můžete zkontrolovat pomocí klíče doručování diagnostické protokoly jsou:

* Zobrazit číslo licence poskytované typ DRM
* Zobrazit číslo licence poskytované zásad 
* Zobrazit chyby podle typu DRM nebo zásad
* Zobrazit počet neoprávněné licenční požadavky od klientů

### <a name="example"></a>Příklad

Zobrazit [monitorování mediálních služeb diagnostické protokoly](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Další postup 

* [Tom, jak shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Vytvořit, zobrazit a spravovat upozornění na metriku pomocí Azure monitoru](../../azure-monitor/platform/alerts-metric.md)
* [Jak monitorovat metriky služby Media Services](media-services-metrics-howto.md)
* [Jak monitorovat mediálních služeb diagnostické protokoly](media-services-diagnostic-logs-howto.md)
