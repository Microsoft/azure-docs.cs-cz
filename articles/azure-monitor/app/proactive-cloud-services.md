---
title: Upozornění na problémy ve Cloudových službách Azure pomocí integrace Diagnostika Azure s Azure Application Insights | Dokumenty společnosti Microsoft
description: Sledování problémů, jako jsou selhání spuštění, selhání a smyčky recyklace rolí ve Cloudových službách Azure pomocí Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 997c5e063c4181a597520e60e2a7669401b9677d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669739"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Upozornění na problémy ve Cloudových službách Azure pomocí integrace diagnostiky Azure s Azure Application Insights

V tomto článku popíšeme, jak nastavit pravidla výstrah, která monitorují problémy, jako jsou selhání spuštění, selhání a smyčky recyklace rolí ve cloudových službách Azure (webové a pracovní role).

Metoda popsaná v tomto článku je založena na [integraci Diagnostika Azure s Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)a nedávno vydané funkce [Výstrahy protokolů pro přehledy aplikací.](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/)

## <a name="define-a-base-query"></a>Definování základního dotazu

Chcete-li začít, definujeme základní dotaz, který načte události protokolu událostí systému Windows z kanálu Windows Azure, které jsou zachyceny do Application Insights jako záznamy trasování.
Tyto záznamy lze použít pro detekci různých problémů ve cloudových službách Azure, jako jsou selhání spuštění, selhání za běhu a cyklinky.

> [!NOTE]
> Níže uvedený základní dotaz kontroluje problémy v časovém okně 30 minut a předpokládá latenci 10 minut při ingestování záznamů telemetrie. Tyto výchozí hodnoty lze nakonfigurovat podle potřeby.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Kontrola konkrétních ID událostí

Po načtení událostí protokolu událostí systému Windows lze konkrétní problémy zjistit kontrolou jejich příslušných ID události a vlastností zprávy (viz příklady níže).
Jednoduše zkombinujte výše uvedený základní dotaz s jedním z níže uvedených dotazů a použitého kombinovaného dotazu při definování pravidla výstrahy protokolu.

> [!NOTE]
> V níže uvedených příkladech bude zjištěn problém, pokud jsou během analyzovaného časového okna nalezeny více než tři události. Toto výchozí nastavení lze nakonfigurovat tak, aby změnilo citlivost pravidla výstrahy.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Vytvoření upozornění

V navigační nabídce v rámci prostředku Application Insights přejděte na **Výstrahy**a vyberte **Nové pravidlo výstrah**.

![Snímek obrazovky s pravidlem vytvořit](./media/proactive-cloud-services/001.png)

V okně **Vytvořit pravidlo** klikněte v části **Definovat podmínku výstrahy** na **Přidat kritéria**a pak vyberte Vlastní **hledání protokolu**.

![Snímek obrazovky s definováním kritérií podmínky pro výstrahu](./media/proactive-cloud-services/002.png)

Do pole **Vyhledávací dotaz** vložte kombinovaný dotaz, který jste připravili v předchozím kroku.

Potom pokračujte do pole **Prahová hodnota** a nastavte jeho hodnotu na hodnotu 0. Volitelně můžete vyladit **pole** **Období** a Frekvence .
Klikněte na **Done** (Hotovo).

![Snímek obrazovky s konfigurátorem dotazu na logiku signálu](./media/proactive-cloud-services/003.png)

V části **Definovat podrobnosti výstrahy** zadejte pravidlo výstrahy **název** a **popis** a nastavte jeho **závažnost**.
Také se ujistěte, že tlačítko **Povolit pravidlo při vytváření** je nastaveno na **ano**.

![Podrobnosti upozornění na snímek obrazovky](./media/proactive-cloud-services/004.png)

V části **Definovat skupinu akcí** můžete vybrat existující **skupinu akcí** nebo vytvořit novou.
Můžete se rozhodnout, že skupina akcí bude obsahovat více akcí různých typů.

![Skupina akcí s obrazovky](./media/proactive-cloud-services/005.png)

Po definování skupiny Akcí potvrďte změny a klikněte na **Vytvořit pravidlo výstrahy**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o automatickém zjišťování:

[Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
[Anomálie výkonu nevracení](../../azure-monitor/app/proactive-potential-memory-leak.md)
[Performance anomalies](../../azure-monitor/app/proactive-performance-diagnostics.md) paměti

