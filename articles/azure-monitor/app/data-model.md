---
title: Telemetrický datový model Azure Application Insights | Dokumenty společnosti Microsoft
description: Přehled dat application insights – přehled
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 94013a12e1cf48a8007fce2547c200d82a657b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671830"
---
# <a name="application-insights-telemetry-data-model"></a>Telemetrický datový model Application Insights

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá telemetrická data z vaší webové aplikace na portál Azure, takže můžete analyzovat výkon a využití vaší aplikace. Model telemetrie je standardizován tak, aby bylo možné vytvořit monitorování nezávislé na platformě a jazyku. 

Data shromážděná modely Application Insights tento typický vzor spuštění aplikace:

![Aplikační model Přehledy aplikací](./media/data-model/application-insights-data-model.png)

Následující typy telemetrie se používají ke sledování provádění vaší aplikace. Následující tři typy jsou obvykle automaticky shromažďovány application insights SDK z rozhraní webové aplikace:

* [**Požadavek**](data-model-request-telemetry.md) – vygenerováno pro protokolování požadavku přijatého vaší aplikací. Například webová sada SDK Application Insights automaticky generuje telemetrickou položku požadavku pro každý požadavek HTTP, který vaše webová aplikace obdrží. 

    **Operace** je podprocesy provádění, které zpracovává požadavek. Můžete také [napsat kód](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) pro sledování jiných typů operací, jako je například "probuzení" ve webové úloze nebo funkci, která pravidelně zpracovává data.  Každá operace má ID. Toto ID, které lze použít k [seskupení](../../azure-monitor/app/correlation.md) všech telemetrie generovaných při zpracování žádosti vaší aplikací. Každá operace buď úspěšné nebo neúspěšné a má dobu trvání.
* [**Výjimka**](data-model-exception-telemetry.md) - Obvykle představuje výjimku, která způsobí selhání operace.
* [**Závislost**](data-model-dependency-telemetry.md) – představuje volání z vaší aplikace do externí služby nebo úložiště, jako je například rozhraní REST API nebo SQL. V ASP.NET jsou volání závislostí `System.Data`SQL definována . Volání koncových bodů HTTP `System.Net`jsou definována . 

Application Insights poskytuje tři další datové typy pro vlastní telemetrii:

* [Trace](data-model-trace-telemetry.md) - používá se buď přímo, nebo prostřednictvím adaptéru k implementaci protokolování `Log4Net` diagnostiky pomocí instrumentační ho rozhraní, které je vám známé, například nebo `System.Diagnostics`.
* [Událost](data-model-event-telemetry.md) – obvykle se používá k zachycení interakce uživatele s vaší službou, k analýze vzorců využití.
* [Metrika](data-model-metric-telemetry.md) - používá se k vykazování periodických skalárních měření.

Každá položka telemetrie můžete definovat [informace o kontextu,](data-model-context.md) jako je verze aplikace nebo id relace uživatele. Kontext je sada polí silného typu, která odblokuje určité scénáře. Když je verze aplikace správně inicializována, Application Insights může detekovat nové vzory v chování aplikace korelované s redeployment. ID relace lze použít k výpočtu výpadku nebo dopadu problému na uživatele. Výpočet odlišného počtu hodnot ID relace pro určitou neúspěšnou závislost, trasování chyb nebo kritickou výjimku poskytuje dobré pochopení dopadu.

Telemetrie modelu Application Insights definuje způsob, jak [korelovat](../../azure-monitor/app/correlation.md) telemetrie na provoz, jehož je součástí. Požadavek může například volat databázi SQL a informace o zaznamenané diagnostice. Můžete nastavit kontext korelace pro tyto položky telemetrie, které jej spojují zpět na telemetrii požadavku.

## <a name="schema-improvements"></a>Vylepšení schématu

Datový model Application Insights je jednoduchý a základní, ale výkonný způsob modelování telemetrie aplikace. Snažíme se, aby model jednoduchý a tenký pro podporu základních scénářů a umožnit rozšířit schéma pro pokročilé použití.

Chcete-li sestavovat problémy s datovým modelem nebo schématem a návrhy, použijte úložiště GitHub [ApplicationInsights-Home.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="next-steps"></a>Další kroky

- [Zápis vlastní telemetrie](../../azure-monitor/app/api-custom-events-metrics.md)
- Přečtěte si, jak [rozšířit a filtrovat telemetrii](../../azure-monitor/app/api-filtering-sampling.md).
- [Vzorkování](../../azure-monitor/app/sampling.md) slouží k minimalizaci množství telemetrie na základě datového modelu.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
