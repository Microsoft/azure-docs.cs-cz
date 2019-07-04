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
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465899"
---
# <a name="smart-detection-in-application-insights"></a>Inteligentní detekce ve službě Application Insights
 Inteligentní zjišťování automaticky upozorní vás na potenciální problémy s výkonem a selhání anomálie ve webové aplikaci. Provádí proaktivní analýzy telemetrie, která vaše aplikace odesílá do [Application Insights](../../azure-monitor/app/app-insights-overview.md). Pokud je nečekané zvýšení míry selhání nebo nestandardní vzorce výkonu klienta nebo serveru, zobrazí se upozornění. Tato funkce vyžaduje žádná konfigurace. To funguje, pokud vaše aplikace odesílá dostatek telemetrie.

Můžete přistupovat detekcí vydané inteligentního zjišťování z e-mailů, které se zobrazí i v okně inteligentní zjišťování.

## <a name="review-your-smart-detections"></a>Zkontrolujte Inteligentní detekce
Může zjišťovat detekce dvěma způsoby:

* **Dostávat e-mailu** ze služby Application Insights. Tady je typickým příkladem:
  
    ![E-mailové upozornění](./media/proactive-diagnostics/03.png)
  
    Klikněte na tlačítko velké tlačítko pro otevření více podrobností na portálu.
* **Inteligentní zjišťování okno** ve službě Application Insights. Vyberte **inteligentního zjišťování** pod **prošetření** nabídku zobrazte seznam nedávných detekcí.

![Zobrazit poslední detekce](./media/proactive-diagnostics/04.png)

Vyberte detekce zobrazíte její podrobnosti.

## <a name="what-problems-are-detected"></a>Zjištění jaké potíže?
Inteligentní zjišťování zjistí a upozorní různé problémy, jako například:

* [Inteligentní zjišťování – anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md). Jsme na základě machine learningu k nastavení očekávané míry neúspěšných žádostí pro vaši aplikaci korelace se zátěžovými testy a dalších faktorů. Pokud je míra selhání přejde mimo očekávané obálky, pošleme upozornění.
* [Inteligentní zjišťování – anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md). Budete dostávat oznámení, pokud doba odezvy trvání operace nebo závislost je zpomalení za porovnání se směrným plánem historických nebo identifikace neobvyklého vzoru v době odezvy nebo čas načítání stránky.   
* Obecné přehledu a problémy, jako třeba [trasování snížení](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [nevracení paměti](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [Abnormal navýšení počtu výjimek](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) a [zabezpečení antimodely](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Odkazy nápovědy v jednotlivých oznámení dostanete v příslušných článcích.)

## <a name="smart-detection-email-notifications"></a>Inteligentní detekce e-mailových oznámení

Všechna pravidla inteligentního zjišťování, s výjimkou pravidla označen jako _ve verzi preview_, jsou nakonfigurované ve výchozím nastavení k odesílání e-mailová oznámení, pokud nenajdou detekcí.

Konfigurace e-mailová oznámení pro konkrétní pravidlo inteligentní zjišťování můžete udělat tak, že otevřete inteligentního zjišťování **nastavení** vyberte pravidlo, které se otevře se okno **upravit pravidlo** okno.

Alternativně můžete změnit konfiguraci pomocí šablon Azure Resource Manageru. [Pravidla inteligentního zjišťování spravovat Application Insights pomocí šablon Azure Resource Manageru najdete v článku](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) další podrobnosti.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Další postup
Tyto diagnostické nástroje umožňují kontrolovat telemetrie z vaší aplikace:

* [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md)
* [Průzkumník služby Search](../../azure-monitor/app/diagnostic-search.md)
* [Analýza – výkonný dotazovací jazyk](../../azure-monitor/log-query/get-started-portal.md)

Inteligentní zjišťování je úplně automatický. Ale možná chcete nastavit některé další oznámení?

* [Ručně konfigurované metriky výstrahy](../../azure-monitor/app/alerts.md)
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md) 

