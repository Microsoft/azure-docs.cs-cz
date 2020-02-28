---
title: Inteligentní zjišťování v Azure Application Insights | Microsoft Docs
description: Application Insights provádí automatickou hloubkovou analýzu telemetrie aplikací a upozorňuje na potenciální problémy.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: e232295f9da2a2ae8f3c6fafdd1dc33a42e92e69
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671796"
---
# <a name="smart-detection-in-application-insights"></a>Inteligentní zjišťování v Application Insights
 Inteligentní zjišťování vás automaticky upozorní na potenciální problémy s výkonem a anomálie při selhání ve vaší webové aplikaci. Provádí proaktivní analýzu telemetrie, kterou vaše aplikace odesílá [Application Insights](../../azure-monitor/app/app-insights-overview.md). Pokud dojde k náhlému nárůstu sazeb za selhání nebo abnormálních vzorů v výkonu klienta nebo serveru, zobrazí se upozornění. Tato funkce nepotřebuje žádnou konfiguraci. Funguje, pokud vaše aplikace posílá dostatek telemetrie.

K detekcím vydaným inteligentním zjišťováním můžete přistupovat z e-mailů, které obdržíte, a v okně inteligentní zjišťování.

## <a name="review-your-smart-detections"></a>Kontrola inteligentních zjišťování
Zjišťování můžete zjistit dvěma způsoby:

* **Dostanete e-mail** od Application Insights. Tady je typický příklad:
  
    ![E-mailové upozornění](./media/proactive-diagnostics/03.png)
  
    Kliknutím na tlačítko Velká otevřete další podrobnosti na portálu.
* Okno **inteligentní detekce** v Application Insights. V nabídce **prozkoumat** vyberte **inteligentní zjišťování** , aby se zobrazil seznam posledních zjištění.

![Zobrazit nedávné detekce](./media/proactive-diagnostics/04.png)

Výběrem detekce zobrazíte její podrobnosti.

## <a name="what-problems-are-detected"></a>Jaké problémy se zjišťují?
Inteligentní zjišťování detekuje a upozorňuje na nejrůznější problémy, například:

* [Inteligentní zjišťování – anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md). Machine Learning používáme k nastavení očekávané míry neúspěšných žádostí pro vaši aplikaci a koreluje se zatížením a dalšími faktory. Pokud je míra selhání mimo očekávanou obálku, pošleme vám upozornění.
* [Inteligentní zjišťování – anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md). Obdržíte oznámení v případě, že doba odezvy operace nebo trvání závislosti zpomaluje v porovnání s historickým směrným plánem nebo pokud identifikujeme vzor neobvyklé v době odezvy nebo v době načítání stránky.   
* Obecné degradace a problémy, jako jsou [snížení trasování](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [nevracení paměti](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [neobvyklé zvýšení objemu výjimek](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) a [antipatterny zabezpečení](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Odkazy na Help v jednotlivých oznámeních vás převezmou na příslušné články.)

## <a name="smart-detection-email-notifications"></a>E-mailová oznámení inteligentního zjišťování

Všechna pravidla inteligentního zjišťování s výjimkou pravidel označených jako _Preview_jsou ve výchozím nastavení nakonfigurována tak, aby odesílala e-mailová oznámení při nalezení detekce.

Konfigurace e-mailových oznámení pro konkrétní pravidlo inteligentního zjišťování se dá udělat tak, že otevřete okno **Nastavení** inteligentního zjišťování a vyberete pravidlo, ve kterém se otevře okno **Upravit pravidlo** .

Alternativně můžete změnit konfiguraci pomocí Azure Resource Manager šablon. Další podrobnosti [najdete v tématu Správa pravidel inteligentního vyhledávání Application Insights pomocí šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) .

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomůžou zkontrolovat telemetrii z vaší aplikace:

* [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md)
* [Průzkumník vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Analýza – výkonný dotazovací jazyk](../../azure-monitor/log-query/get-started-portal.md)

Inteligentní zjišťování je zcela automatické. Možná byste ale chtěli nastavit ještě nějaké další výstrahy?

* [Ručně nakonfigurované výstrahy metriky](../../azure-monitor/app/alerts.md)
* [Webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) 

