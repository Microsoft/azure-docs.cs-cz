---
title: Inteligentní zjišťování v přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Application Insights provádí automatickou hloubkovou analýzu telemetrie aplikace a varuje vás před potenciálními problémy.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: ff9f88e1d2e643d04c4417283420217e7d496caf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536790"
---
# <a name="smart-detection-in-application-insights"></a>Inteligentní zjišťování v Application Insights
 Inteligentní detekce vás automaticky upozorní na potenciální problémy s výkonem a anomálie selhání ve vaší webové aplikaci. Provádí proaktivní analýzu telemetrie, kterou vaše aplikace odesílá do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Pokud dojde k náhlému nárůstu míry selhání nebo abnormální vzory výkonu klienta nebo serveru, zobrazí se výstraha. Tato funkce nepotřebuje žádnou konfiguraci. Funguje, pokud vaše aplikace odešle dostatek telemetrie.

K detekcím vydaná inteligentní detekcí můžete přistupovat jak z e-mailů, které obdržíte, tak z okna inteligentní detekce.

## <a name="review-your-smart-detections"></a>Zkontrolujte inteligentní detekce
Zjišťování můžete zjistit dvěma způsoby:

* **Obdržíte e-mail** od Application Insights. Tady je typický příklad:
  
    ![Upozornění na e-mail](./media/proactive-diagnostics/03.png)
  
    Kliknutím na velké tlačítko otevřete na portálu více podrobností.
* **Okno Inteligentní detekce** v Application Insights. V yberte **Inteligentní detekce** v nabídce **Investigate** zobrazíte seznam nejnovějších detekcí.

![Zobrazit nejnovější zjišťování](./media/proactive-diagnostics/04.png)

Vyberte detekci, chcete-li zobrazit jeho podrobnosti.

## <a name="what-problems-are-detected"></a>Jaké problémy jsou zjištěny?
Inteligentní detekce detekuje a upozorní na různé problémy, například:

* [Inteligentní detekce - anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md). Pomocí strojového učení nastavíme očekávanou rychlost neúspěšných požadavků na vaši aplikaci, což souvisí se zatížením a dalšími faktory. Pokud míra selhání neklesne mimo očekávanou obálku, odešleme výstrahu.
* [Inteligentní detekce - anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md). Dostanete oznámení, pokud doba odezvy operace nebo trvání závislosti se zpomaluje ve srovnání s historickým směrným plánem nebo pokud identifikujeme neobvyklý vzor v době odezvy nebo době načítání stránky.   
* Obecné degradace a problémy, jako [je degradace stopy](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [nevracení paměti](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [abnormální nárůst objemu výjimek](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) a [ochrana proti vzorům zabezpečení](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Odkazy nápovědy v každém oznámení vás přenese na příslušné články.)

## <a name="smart-detection-email-notifications"></a>E-mailová oznámení inteligentní detekce

Všechna pravidla inteligentního zjišťování, s výjimkou pravidel označených jako _náhled_, jsou ve výchozím nastavení konfigurována tak, aby při zjištění odesílala e-mailová oznámení.

Konfigurace e-mailových oznámení pro konkrétní pravidlo inteligentní detekce lze provést otevřením okna **Inteligentní nastavení** detekce a výběrem pravidla, které otevře okno **Upravit pravidlo.**

Případně můžete změnit konfiguraci pomocí šablon Azure Resource Manager. [Další podrobnosti najdete v tématu Správa pravidel inteligentního zjišťování application insights pomocí šablon Azure Resource Manager.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomohou zkontrolovat telemetrii z vaší aplikace:

* [Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md)
* [Průzkumník hledání](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – výkonný dotazovací jazyk](../../azure-monitor/log-query/get-started-portal.md)

Inteligentní detekce je zcela automatická. Ale možná byste chtěli nastavit nějaké další upozornění?

* [Ručně nakonfigurovaná upozornění na metriky](../../azure-monitor/app/alerts.md)
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md) 

