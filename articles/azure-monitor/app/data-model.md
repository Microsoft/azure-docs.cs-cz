---
title: Azure Application Insights Telemetrie datový Model | Dokumentace Microsoftu
description: Přehled služby Application Insights data modelu
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 749b4077b457eff836ec515f21d97e892e663156
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120661"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights telemetrie datového modelu

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) odesílá telemetrii z vaší webové aplikace na webu Azure portal, takže můžete analyzovat výkon a využití vaší aplikace. Telemetrická data modelu je standardizované. aby bylo možné vytvořit monitorování nezávislým na jazyku a platformě. 

Modely dat shromážděných službou Application Insights tento model provádění Typická aplikace:

![Aplikační Model Application Insights](./media/data-model/application-insights-data-model.png)

Následující typy telemetrických dat se používají ke sledování provádění vaší aplikace. Následující tři typy jsou obvykle automaticky shromážděná sadou Application Insights SDK z Architektura webových aplikací:

* [**Žádost o** ](data-model-request-telemetry.md) – generovaného protokolu žádosti přijaté vaší aplikace. Například webové služby Application Insights SDK automaticky generuje položky telemetrie požadavku pro každý požadavek HTTP, který vaše webová aplikace obdrží. 

    **Operace** je vlákna provádění, která zpracuje požadavek. Můžete také [napsat kód](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) k monitorování jiných typů operace, například "probuzení" ve webovém projektu nebo funkce, které pravidelně zpracovává data.  Každá operace nemá identifikátor. Toto ID, které je možné použít k [skupiny](../../azure-monitor/app/correlation.md) všechny telemetrie vygenerovaná během zpracování požadavku aplikace. Každá operace buď uspěje nebo selže a má dobu trvání času.
* [**Výjimka** ](data-model-exception-telemetry.md) – obvykle představuje výjimku, která způsobí, že se operace nezdaří.
* [**Závislost** ](data-model-dependency-telemetry.md) – představuje volání z vaší aplikace k externí službě nebo úložiště, například rozhraní REST API nebo SQL. V technologii ASP.NET, jsou definovány volání závislostí SQL `System.Data`. Volání koncových bodů HTTP, které jsou definovány pomocí `System.Net`. 

Application Insights poskytuje tři další datové typy pro vlastní telemetrii:

* [Trasování](data-model-trace-telemetry.md) – použít buď přímo nebo prostřednictvím adaptéru implementace protokolování diagnostiky pomocí rozhraní WMI, které je známé, jako `Log4Net` nebo `System.Diagnostics`.
* [Událost](data-model-event-telemetry.md) – obvykle používá k zachycení interakce uživatele s vaší službou moct analyzovat vzory využití.
* [Metrika](data-model-metric-telemetry.md) – používá se k sestavě pravidelná skalární měření.

Můžete definovat každé položce telemetrie [informace o kontextu](data-model-context.md) jako id aplikace verze nebo uživatelské relace. Kontext je sada polí pro silného typu, který se odblokuje určitých scénářů. Pokud verze aplikace je správně inicializována, Application Insights dokáže detekovat nové vzory v chování aplikace korelují s opětovné nasazení. Id relace lze použít k výpočtu výpadek nebo problém dopad na uživatele. Pro určité počítá počet jedinečných položek z hodnoty id relace se nezdařilo závislosti, trasování chyb nebo kritické výjimky poskytuje dobrý přehled o dopad.

Model telemetrie Application Insights definuje způsob, jak [korelovat](../../azure-monitor/app/correlation.md) telemetrii pro operace, které je součástí. Například požadavek mohl provádět volání SQL Database a zaznamenává diagnostické informace. Můžete nastavit kontext korelace telemetrie položek, které spojí zpět do telemetrických dat požadavek.

## <a name="schema-improvements"></a>Vylepšení schématu

Application Insights datový model je jednoduché a základní, ale efektivní způsob modelování telemetrie vaší aplikace. Snažíme se zachovat modelu jednoduché a tenký pro podporu základních scénářů a povolit rozšíření schématu pro pokročilé uživatele.

K hlášení datový model nebo schématu problémy i návrhy můžete využít GitHub [ApplicationInsights domovské](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) úložiště.

## <a name="next-steps"></a>Další postup

- [Psát vlastní telemetrii](../../azure-monitor/app/api-custom-events-metrics.md)
- Zjistěte, jak [rozšířit a filtrování telemetrie](../../azure-monitor/app/api-filtering-sampling.md).
- Použití [vzorkování](../../azure-monitor/app/sampling.md) Chcete-li minimalizovat množství telemetrie na základě dat modelu.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované službou Application Insights.
