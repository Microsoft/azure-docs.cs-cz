---
title: Metriky mediálních služeb a diagnostické protokoly pomocí Azure Monitoru
titleSuffix: Azure Media Services
description: Zjistěte, jak sledovat metriky a diagnostické protokoly Azure Media Services prostřednictvím Azure Monitoru.
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
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585276"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitorování metrik mediálních služeb a diagnostických protokolů prostřednictvím služby Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) umožňuje sledovat metriky a diagnostické protokoly, které vám pomohou pochopit, jak si vaše aplikace vedou. Všechna data shromážděná službou Azure Monitor se vejdou do jednoho ze dvou základních typů: metriky a protokoly. Můžete sledovat diagnostické protokoly mediálních služeb a vytvářet výstrahy a oznámení pro shromážděné metriky a protokoly. Data metrik můžete vizualizovat a analyzovat pomocí [průzkumníka metrik](../../azure-monitor/platform/metrics-getting-started.md). Protokoly můžete odesílat do [Azure Storage](https://azure.microsoft.com/services/storage/), streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), exportovat je do [Log Analytics](https://azure.microsoft.com/services/log-analytics/)nebo používat služby třetích stran.

Podrobný přehled najdete v tématu [metriky azure monitoru](../../azure-monitor/platform/data-platform.md) a [protokoly diagnostiky azure monitoru](../../azure-monitor/platform/platform-logs-overview.md).

Toto téma popisuje podporované [protokoly metrik mediálních služeb](#media-services-metrics) a [diagnostiky mediálních služeb](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metriky mediálních služeb

Metriky jsou shromažďovány v pravidelných intervalech bez ohledu na to, zda se hodnota změní. Jsou užitečné pro výstrahy, protože mohou být vzorkovány často a výstraha může být aktivována rychle s relativně jednoduchou logikou. Informace o tom, jak vytvářet upozornění na metriky, najdete v [tématu Vytváření, zobrazení a správa upozornění na metriky pomocí Azure Monitoru](../../azure-monitor/platform/alerts-metric.md).

Služba Media Services podporuje metriky monitorování pro následující zdroje:

* Účet
* Koncový bod streamování

### <a name="account"></a>Účet

Můžete sledovat následující metriky účtu.

|Název metriky|Zobrazované jméno|Popis|
|---|---|---|
|Počet majetku|Počet majetku|Majetek na vašem účtu.|
|AssetQuota|Kvóta aktiv|Kvóta aktiv ve vašem účtu.|
|Procento použitého assetaused|Procento použité kvóty aktiv|Procento již použité kvóty majetku.|
|ContentKeyPolicyCount|Počet zásad klíče obsahu|Zásady obsahu ve vašem účtu.|
|ContentKeyPolicyQuota|Kvóta zásad klíče obsahu|Kvóta zásad obsahu ve vašem účtu.|
|Procento contentkeypolicyquotausedprocento|Procento použité kvóty zásad obsahu|Procento již použité kvóty zásad klíče obsahu.|
|StreamingPolicyCount|Počet zásad streamování|Zásady streamování ve vašem účtu.|
|StreamingPolicyQuota|Kvóta zásad streamování|Kvóta zásad streamování ve vašem účtu.|
|StreamingPolicyQuotaUsedPercentage|Procento použité kvóty zásad streamování|Procento kvóty zásad streamování, které již bylo použito.|

Měli byste také zkontrolovat [kvóty a limity účtu](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Koncový bod streamování

Podporovány jsou následující metriky [koncových bodů streamování](https://docs.microsoft.com/rest/api/media/streamingendpoints) mediálních služeb:

|Název metriky|Zobrazované jméno|Popis|
|---|---|---|
|Žádosti|Žádosti|Poskytuje celkový počet požadavků HTTP obsluhovaných koncovým bodem streamování.|
|Výchozí přenos dat|Výchozí přenos dat|Celkový počet odchozích bajtů. Například bajty vysílané koncovým bodem streamování.|
|SuccessE2ELatency|Úspěšnost latence od konce ke konci|Doba trvání od okamžiku, kdy koncový bod streamování obdržel požadavek na kdy byl odeslán poslední bajt odpovědi.|

### <a name="why-would-i-want-to-use-metrics"></a>Proč bych měl chtít používat metriky?

Tady jsou příklady toho, jak vám sledování metrik mediálních služeb může pomoci pochopit, jak si vaše aplikace vedou. Některé otázky, které lze řešit pomocí metrik mediálních služeb, jsou:

* Jak můžu sledovat svůj standardní koncový bod pro streamování, abych věděl, kdy jsem překročil limity?
* Jak poznám, že mám dostatek jednotek v měřítku Premium Streaming Endpoint?
* Jak můžu nastavit upozornění tak, aby vědělo, kdy mají být svoje koncové body streamování navýšené?
* Jak nastavím výstrahu tak, aby věděla, kdy bylo dosaženo maximálního odchozího přenosu nakonfigurovaného v účtu?
* Jak zjistím, že se lháseselhání žádostí a co je příčinou selhání?
* Jak zjistím, kolik požadavků HLS nebo DASH je vyžádáno z balíru?
* Jak nastavím výstrahu tak, aby věděla, kdy byla přístupována prahová hodnota # neúspěšných požadavků?

### <a name="example"></a>Příklad

Viz [Jak sledovat metriky mediálních služeb](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Diagnostické protokoly mediálních služeb

Diagnostické protokoly poskytují bohatá a častá data o provozu prostředku Azure. Další informace najdete v tématu [Jak shromažďovat a využívat data protokolu z prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md).

Služba Media Services podporuje následující diagnostické protokoly:

* Předání klíčů

### <a name="key-delivery"></a>Předání klíčů

|Name (Název)|Popis|
|---|---|
|Požadavek na službu doručování klíčů|Protokoly, které zobrazují informace o požadavku na službu doručování klíčů. Další informace naleznete [v tématu schémata](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Proč bych měl chtít používat diagnostické protokoly?

Některé věci, které můžete prozkoumat pomocí protokolů diagnostiky doručování klíčů, jsou:

* Podívejte se na počet licencí dodaných podle typu DRM.
* Podívejte se na počet licencí poskytovaných zásadami.
* Zobrazení chyb podle drm nebo typu zásady.
* Podívejte se na počet neoprávněných žádostí o licenci od klientů.

### <a name="example"></a>Příklad

Viz [Jak sledovat diagnostické protokoly služby Media Service](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Další kroky

* [Jak shromažďovat a využívat data protokolu z prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Vytváření, zobrazení a správa upozornění na metriky pomocí služby Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Jak sledovat metriky mediálních služeb](media-services-metrics-howto.md)
* [Jak sledovat diagnostické protokoly služby Media Service](media-services-diagnostic-logs-howto.md)
