---
title: Media Services monitorování
description: Začněte tady a Naučte se monitorovat Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 783d9e1b4ab86f6580cf3418a0676921aef2db6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598197"
---
# <a name="monitor-media-services"></a>Media Services monitorování

Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná nástrojem Media Services a způsob použití funkcí Azure Monitor k analýze a upozornění na tato data.

## <a name="metrics-are-useful"></a>Metriky jsou užitečné

Tady jsou příklady, jak vám monitorování Media Services metriky můžou porozumět tomu, jak vaše aplikace provádí. Některé otázky, které je možné řešit pomocí Media Services metriky:

- Návody monitorování koncového bodu standardního streamování, abyste věděli, že překročili jste limity?
- Návody vědět, jestli mám dostatek jednotek škálování koncového bodu Premium pro streamování?
- Jak můžu nastavit výstrahu, která bude vědět, kdy se má změnit velikost svých koncových bodů streamování?
- Návody nastavit výstrahu, která ví, kdy bylo dosaženo maximálního počtu výstupů nakonfigurovaných v účtu?
- Jak zjistím, že se nedaří rozčlenění požadavků a co způsobuje selhání?
- Jak můžu zjistit, kolik požadavků na HLS nebo POMLČKu se z tohoto balíčku vyřazuje?
- Návody nastavit výstrahu, která ví, kdy jsem dosáhl prahové hodnoty neúspěšných požadavků?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Media Services vytváří data monitorování pomocí [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview), což je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích.

Začněte s načtením článku [monitorování prostředků Azure pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), které popisuje následující koncepty:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

## <a name="monitoring-data"></a>Data monitorování

Media Services shromažďuje stejné typy dat monitorování jako další prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Všechna data shromážděná službou Azure Monitor patří do jednoho ze dvou základních typů: metriky a protokoly. Pomocí těchto dvou typů můžete:

- Vizualizujte a analyzujte data metrik pomocí Průzkumníka metrik.
- Monitorování diagnostických protokolů Media Services a vytváření výstrah a oznámení pro ně.
- Díky protokolům můžete:
  - Odeslat je do Azure Storage
  - Streamování do Azure Event Hubs
  - Exportujte je do Log Analytics
  - Použití služeb třetích stran

Podrobné informace o metrikách a protokolech, které vytváří Media Services, najdete v článku [monitorování Media Services dat](monitor-media-services-data-reference.md) .

## <a name="collection-and-routing"></a>Shromažďování a směrování

*Metriky platforem* a *Protokol aktivit* se shromažďují a ukládají automaticky, ale můžete je směrovat do jiných umístění pomocí diagnostického nastavení.  

*Protokoly prostředků* se **neshromažďují a** ukládají, dokud nevytvoříte nastavení diagnostiky a nebudete je směrovat do jednoho nebo více umístění.

Podrobný postup vytváření nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu najdete v článku [Vytvoření nastavení diagnostiky pro shromáždění protokolů a metrik platforem v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) .

Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit. Kategorie pro Media Services jsou uvedeny v referenčních informacích o [monitorování Media Services](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky pro Media Services můžete analyzovat pomocí metrik z jiných služeb Azure pomocí Průzkumníka metrik, a to tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) .

Seznam metrik shromažďovaných pro Media Services naleznete v tématu [Monitoring Media Services data reference](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Analýza protokolů

Data v Azure Monitor protokoly se ukládají v tabulkách, kde každá tabulka má svou vlastní sadu jedinečných vlastností.  

Všechny protokoly prostředků v Azure Monitor mají stejná pole následovaná poli pro konkrétní služby. Společné schéma je popsáno v [Azure monitor schéma protokolu prostředků](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema).

Schéma pro protokoly prostředků Media Services najdete v referenčních informacích k [monitorování Media Services dat](monitor-media-services-data-reference.md).

[Protokol aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log) je protokol platformy v Azure, který poskytuje přehled o událostech na úrovni předplatného. Můžete ji zobrazit nezávisle nebo ji směrovat do Azure Monitor protokolů, kde můžete provádět mnohem složitější dotazy pomocí Log Analytics.

Seznam typů protokolů prostředků shromážděných pro Media Services naleznete v tématu [Monitoring Media Services data reference](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Proč bych chtěl použít diagnostické protokoly?

V diagnostických protokolech můžete prozkoumávat tyto věci:

- Počet licencí dodaných typem DRM.
- Počet licencí dodaných podle zásad.
- Chyby podle typu DRM nebo zásad.
- Počet neautorizovaných žádostí o licenci od klientů.

## <a name="alerts"></a>Výstrahy

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy v systému před tím, než si je vaši zákazníci všimnete. Můžete nastavit výstrahy na [metrikách](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [protokolech](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)a [protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts).

Metriky Media Services se shromažďují v pravidelných intervalech bez ohledu na to, jestli se hodnota změní. Jsou užitečné pro upozorňování, protože je možné je často vzorkovat. Výstraha se dá rychle aktivovat pomocí relativně jednoduché logiky.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Další kroky

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
