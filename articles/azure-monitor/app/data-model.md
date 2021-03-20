---
title: Datový model Azure Telemetrie Application Insights | Microsoft Docs
description: Přehled datového modelu Application Insights
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: fd5848eb44ecd32612943662b5a4fd0a5091cd9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91461287"
---
# <a name="application-insights-telemetry-data-model"></a>Model dat Application Insights telemetrie

[Azure Application Insights](./app-insights-overview.md) odesílá telemetrie z vaší webové aplikace do Azure Portal, abyste mohli analyzovat výkon a využití vaší aplikace. Model telemetrie je standardizovaný tak, aby bylo možné vytvořit platformu a monitorování nezávislé na jazyce. 

Data shromažďovaná modelem Application Insights Tento typický vzor spuštění aplikace:

![Application Insights aplikační model](./media/data-model/application-insights-data-model.png)

Následující typy telemetrie slouží k monitorování provádění vaší aplikace. Následující tři typy jsou obvykle automaticky shromažďovány sadou Application Insights SDK z architektury webové aplikace:

* [**Požadavek**](data-model-request-telemetry.md) vygenerovaný k zaznamenání žádosti přijaté vaší aplikací Například Application Insights webová sada SDK automaticky vygeneruje položku telemetrie žádosti pro každý požadavek HTTP, který vaše webová aplikace obdrží. 

    **Operace** je podprocesy spuštění, které zpracovávají požadavek. Můžete také [napsat kód](./api-custom-events-metrics.md#trackrequest) pro monitorování jiných typů operací, jako je například "probuzení" ve webové úloze nebo funkci, která pravidelně zpracovává data.  Každá operace má ID. Toto ID, které se dá použít k [seskupení](./correlation.md) všech telemetrie generovaných v době, kdy vaše aplikace zpracovává požadavek. Každá operace buď proběhne úspěšně, nebo se nezdaří a má časový interval.
* [**Výjimka**](data-model-exception-telemetry.md) – obvykle představuje výjimku, která způsobí selhání operace.
* [**Dependency**](data-model-dependency-telemetry.md) – představuje volání z vaší aplikace do externí služby nebo úložiště, jako je například REST API nebo SQL. V ASP.NET jsou volání závislostí na SQL definována pomocí `System.Data` . Volání koncových bodů HTTP jsou definována pomocí `System.Net` . 

Application Insights poskytuje tři další datové typy pro vlastní telemetrii:

* [Trace](data-model-trace-telemetry.md) – používá se buď přímo, nebo prostřednictvím adaptéru k implementaci protokolování diagnostiky pomocí architektury instrumentace, která je pro vás známá, například `Log4Net` nebo `System.Diagnostics` .
* [Událost](data-model-event-telemetry.md) – obvykle se používá k zaznamenání interakce uživatele s vaší službou a k analýze vzorců používání.
* [Metrika](data-model-metric-telemetry.md) – používá se k hlášení periodických skalárních měření.

Každá položka telemetrie může definovat [kontextové informace](data-model-context.md) , jako je verze aplikace nebo ID uživatelské relace. Kontext je sada polí silného typu, která odblokuje určité scénáře. Pokud je verze aplikace správně inicializovaná, Application Insights může detekovat nové vzory v chování aplikace v souvislosti s novým nasazením. ID relace lze použít k výpočtu výpadku nebo dopadu problému na uživatele. Výpočet jedinečného počtu hodnot ID relace pro určitou neúspěšnou závislost, trasování chyb nebo kritickou výjimku poskytuje dobrý význam dopadu.

Model telemetrie Application Insights definuje způsob, jak [korelace](./correlation.md) telemetrie s provozem, který je součástí. Požadavek může například učinit SQL Database volání a zaznamenané diagnostické informace. Můžete nastavit kontext korelace pro tyto položky telemetrie, které ji spojí zpátky s telemetrie žádostí.

## <a name="schema-improvements"></a>Vylepšení schématu

Application Insights datový model je jednoduchý a základní, ale výkonný způsob modelování telemetrie aplikací. Snažíme se model zjednodušit a využít k podpoře základních scénářů a umožňuje rozšířit schéma pro pokročilé použití.

[K nahlášení problémů s datovým modelem nebo schématům a návrhům použijte naše úložiště GitHub](https://github.com/microsoft/ApplicationInsights-dotnet/issues/new/choose).

## <a name="next-steps"></a>Další kroky

- [Zápis vlastní telemetrie](./api-custom-events-metrics.md)
- Naučte se, jak můžete [zvětšit a filtrovat telemetrii](./api-filtering-sampling.md).
- Pomocí [vzorkování](./sampling.md) můžete minimalizovat množství telemetrie na základě datového modelu.
- Podívejte se na [platformy](./platforms.md) podporované nástrojem Application Insights.

