---
title: Inteligentní zjišťování ve službě Azure Application Insights | Dokumentace Microsoftu
description: Application Insights provádí automatické hloubkové analýzy telemetrie vaší aplikace a upozorní vás na potenciální problémy.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: ce259c2091fc2aec81cd85d4b1e3bd85ee92c806
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024344"
---
# <a name="smart-detection-in-application-insights"></a>Inteligentní detekce ve službě Application Insights
 Inteligentní zjišťování automaticky upozorní vás na potenciální problémy s výkonem ve webové aplikaci. Provádí proaktivní analýzy telemetrie, která vaše aplikace odesílá do [Application Insights](app-insights-overview.md). Pokud je nečekané zvýšení míry selhání nebo nestandardní vzorce výkonu klienta nebo serveru, zobrazí se upozornění. Tato funkce vyžaduje žádná konfigurace. To funguje, pokud vaše aplikace odesílá dostatek telemetrie.

Upozornění inteligentního zjišťování můžete přistupovat i z e-mailů, které se zobrazí a v okně inteligentní zjišťování.

## <a name="review-your-smart-detections"></a>Zkontrolujte Inteligentní detekce
Může zjišťovat detekce dvěma způsoby:

* **Dostávat e-mailu** ze služby Application Insights. Tady je typickým příkladem:
  
    ![E-mailové upozornění](./media/app-insights-proactive-diagnostics/03.png)
  
    Klikněte na tlačítko velké tlačítko pro otevření více podrobností na portálu.
* **Inteligentní zjišťování dlaždice** na vaše aplikace – přehled okno zobrazuje počet nedávných výstrah. Kliknutím na dlaždici zobrazíte seznam nedávných výstrah.

![Zobrazit poslední detekce](./media/app-insights-proactive-diagnostics/04.png)

Výběrem výstrahy zobrazíte její podrobnosti.

## <a name="what-problems-are-detected"></a>Zjištění jaké potíže?
Existují tři typy detekce:

* [Inteligentní zjišťování – anomálie selhání](../azure-monitor/app/proactive-failure-diagnostics.md). Jsme na základě machine learningu k nastavení očekávané míry neúspěšných žádostí pro vaši aplikaci korelace se zátěžovými testy a dalších faktorů. Pokud je míra selhání přejde mimo očekávané obálky, pošleme upozornění.
* [Inteligentní zjišťování – anomálie výkonu](../azure-monitor/app/proactive-performance-diagnostics.md). Budete dostávat oznámení, pokud doba odezvy trvání operace nebo závislost je zpomalení za porovnání se směrným plánem historických nebo identifikace neobvyklého vzoru v době odezvy nebo čas načítání stránky.   
* [Inteligentní zjišťování – potíže s cloudovými službami Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Získáváte upozornění, pokud je vaše aplikace hostovaná v Azure Cloud Services a role instance má selhání spuštění, častému restartování nebo selhání modulu runtime.

(Odkazy nápovědy v jednotlivých oznámení dostanete v příslušných článcích.)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
Tyto diagnostické nástroje umožňují kontrolovat telemetrie z vaší aplikace:

* [Průzkumník metrik](../azure-monitor/app/metrics-explorer.md)
* [Průzkumník služby Search](../azure-monitor/app/diagnostic-search.md)
* [Analýza – výkonný dotazovací jazyk](../azure-monitor/log-query/get-started-portal.md)

Inteligentní zjišťování je úplně automatický. Ale možná chcete nastavit některé další oznámení?

* [Ručně konfigurované metriky výstrahy](../azure-monitor/app/alerts.md)
* [Testy dostupnosti webu](../azure-monitor/app/monitor-web-app-availability.md) 

