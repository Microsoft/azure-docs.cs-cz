---
title: Inteligentní detekce ve službě Azure Application Insights | Microsoft Docs
description: Application Insights provede hloubkovou analýzu automatické vaší aplikace telemetrie a zobrazí upozornění na potenciální problémy.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: 38a7c5be98440a717eb54092bf7ce0d412619017
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="smart-detection-in-application-insights"></a>Inteligentní detekce ve službě Application Insights
 Inteligentní detekce automaticky zobrazí upozornění na potenciální problémy s výkonem ve webové aplikaci. Provede proaktivní analýzu telemetrii, kterou vaše aplikace odesílá do [Application Insights](app-insights-overview.md). Pokud je nečekané zvýšení sazby selhání nebo neobvyklé vzory výkon klienta nebo serveru, zobrazí se upozornění. Tato funkce vyžaduje žádná konfigurace. Pokud vaše aplikace odešle dost telemetrických dat funguje.

Inteligentní detekce výstrahy můžete přistupovat z e-mailů, které se zobrazí a z okna Inteligentní detekce.

## <a name="review-your-smart-detections"></a>Zkontrolujte vaše Inteligentní detekce
Může zjistit detekce dvěma způsoby:

* **Obdržíte e-mailu** z Application Insights. Tady je typický příklad:
  
    ![E-mailové výstrahy](./media/app-insights-proactive-diagnostics/03.png)
  
    Klikněte na tlačítko big otevřete podrobněji na portálu.
* **Na dlaždici Inteligentní detekce** na přehled vaší aplikace se zobrazí okno počet poslední výstrahy. Klikněte na dlaždici zobrazíte seznam poslední výstrahy.

![Zobrazení posledních detekce](./media/app-insights-proactive-diagnostics/04.png)

Vyberte výstrahu zobrazíte její podrobnosti.

## <a name="what-problems-are-detected"></a>Jaké problémy jsou rozpoznána?
Existují tři druhy zjišťování:

* [Inteligentní detekce - selhání anomálií](app-insights-proactive-failure-diagnostics.md). Používáme strojového učení nastavit očekávaný počet neúspěšných požadavků pro vaši aplikaci korelace s zatížení a dalších faktorů. Pokud je míra selhání proběhne mimo očekávaný obálky, můžeme zasílat výstrahu.
* [Inteligentní detekce - anomálie výkonu](app-insights-proactive-performance-diagnostics.md). Pokud doba odezvy trvání operace nebo závislostí je zpomalení nástroje porovnání se směrným plánem historických nebo pokud jsme identifikovat neobvyklé vzor doba odezvy nebo čas načítání stránky zobrazí oznámení.   
* [Inteligentní detekce - Azure Cloud Service problémy](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Zobrazí výstrahy, pokud vaše aplikace je hostovaná v Azure Cloud Services a instanci role má selhání spuštění, často recyklace nebo dojde k chybě modulu runtime.

(Nápovědu v každém upozornění odkazů můžete na odpovídající články.)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
Tyto diagnostické nástroje můžete zkontrolovat telemetrie z vaší aplikace:

* [Metriky explorer](app-insights-metrics-explorer.md)
* [Průzkumník služby Search](app-insights-diagnostic-search.md)
* [Analýza - účinný dotazovací jazyk](app-insights-analytics-tour.md)

Inteligentní detekce je zcela automatické. Ale možná chcete nastavit některé další výstrahy?

* [Ručně konfigurované metriky výstrahy](app-insights-alerts.md)
* [Testy dostupnosti webu](app-insights-monitor-web-app-availability.md) 

