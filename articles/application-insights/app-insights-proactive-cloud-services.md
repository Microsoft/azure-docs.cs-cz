---
title: Výstraha ohledně problémů v Azure Cloud Services pomocí integrace Azure Diagnostics pomocí služby Azure Application Insights | Microsoft Docs
description: Monitorování pro problémy jako chyby spuštění, dojde k chybě a role recyklovat smyčky v Azure Cloud Services pomocí služby Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: harelbr; mbullwin
ms.openlocfilehash: 18817fd84a86a72d379f96973b71658f2cdf4afd
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851237"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Výstraha na problémy ve službě Azure Cloud Services pomocí integrace Azure diagnostics pomocí služby Azure Application Insights

V tomto článku jsme se popisují, jak nastavit pravidla výstrah, které sledování problémů jako selhání spuštění, dojde k chybě a role recyklovat smyčky v Azure Cloud Services (webové a pracovní role).

Metoda popsaná v tomto článku je na základě [Azure Diagnostics integrace s nástrojem Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)a nedávno vydané [výstrahy protokolu pro službu Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) schopností.

## <a name="define-a-base-query"></a>Vytvoření základního dotazu

Pokud chcete začít, bude definujeme základní dotaz, který načte události v protokolu událostí systému Windows z tohoto kanálu Windows Azure, které jsou zachycení do Application Insights jako záznamy trasování.
Tyto záznamy lze použít pro zjišťování celou řadu problémů v Azure Cloud Services, jako je selhání spuštění, selhání modulu runtime a recyklovat smyčky.

> [!NOTE]
> Základní dotaz níže zjistí problémy časové okno 30 minut a předpokládá latence 10 minut v příjem záznamy telemetrie. Tato výchozí nastavení lze nakonfigurovat podle svých potřeb.

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

## <a name="check-for-specific-event-ids"></a>Kontrola pro konkrétní ID událostí

Po načtení události v protokolu událostí systému Windows, můžete zjistit konkrétní problémy kontrolou jejich odpovídající ID a zpráva vlastnosti události (viz následující příklady).
Jednoduše kombinovat základní dotaz výše s jedním z dotazy níže a slouží, které při definování pravidlo výstrahy protokolu dotazu.

> [!NOTE]
> V následujících příkladech bude problém zjištěn, pokud se zjistí víc než třemi události během analyzovaných časový interval. Toto výchozí nastavení můžete konfigurovat a měnit citlivosti pravidlo výstrahy.

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

V navigační nabídce v prostředku Application Insights, přejděte na **výstrahy**a potom vyberte **nové pravidlo výstrahy**.

![Snímek obrazovky vytvořit pravidlo](./media/app-insights-proactive-cloud-services/001.png)

V **vytvořit pravidlo** okno, v části **definovat výstrahy podmínku** části, klikněte na **přidat kritéria**a potom vyberte **hledání protokolů vlastní**.

![Snímek obrazovky kritéria Definujte podmínky pro výstrahu](./media/app-insights-proactive-cloud-services/002.png)

V **vyhledávací dotaz** pole, vložte kombinované dotaz jste připravili v předchozím kroku.

Potom pokračujte **prahová hodnota** pole a jeho hodnotu nastavte na hodnotu 0. Může volitelně vylepšení **období** a četnost **pole**.
Klikněte na **Done** (Hotovo).

![Snímek obrazovky konfigurace signál logiku dotazu](./media/app-insights-proactive-cloud-services/003.png)

V části **definovat podrobnosti výstrahy** části, zadejte **název** a **popis** do pravidlo výstrahy a nastavte její **závažnost**.
Také zkontrolujte, zda **pravidlo povolit po vytvoření** tlačítko nastavena na **Ano**.

![Podrobnosti výstrahy – snímek obrazovky](./media/app-insights-proactive-cloud-services/004.png)

V části **definovat akce skupiny** části, můžete vybrat existující **akce skupiny** nebo vytvořte novou.
Můžete mít skupině Akce obsahovat více akcí různých typů.

![Snímek obrazovky akce skupiny](./media/app-insights-proactive-cloud-services/005.png)

Jakmile skupině Akce, které jste definovali, potvrďte změny a klikněte na **vytvořit pravidlo výstrahy**.

## <a name="next-steps"></a>Další kroky

Další informace o automatické zjišťování:

[Selhání anomálií](app-insights-proactive-failure-diagnostics.md)
[nevracení paměti](app-insights-proactive-potential-memory-leak.md)
[anomálie výkonu](app-insights-proactive-performance-diagnostics.md)

