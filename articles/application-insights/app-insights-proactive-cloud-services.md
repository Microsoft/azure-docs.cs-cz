---
title: Upozornění na potíže v cloudových službách Azure pomocí Azure Diagnostics integrace s Azure Application Insights | Dokumentace Microsoftu
description: Sledování problémů, jako je selhání spuštění, chyb a role recyklovat smyčky v cloudových službách Azure pomocí Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: cdb395a590fb200a24c68e56728a270b968e53b5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091419"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Upozornění na problémy v Azure Cloud Services pomocí integrace diagnostiky Azure pomocí Azure Application Insights

V tomto článku jsme popisuje, jak nastavit pravidel upozornění, která sledování problémů, jako je selhání spuštění, chyb a role recyklovat smyčky v cloudových službách Azure (webové a pracovní role).

Metoda popsaných v tomto článku je založena na [integrace Azure Diagnostics Application insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)a nedávno vydané [upozornění protokolů pro službu Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) funkce.

## <a name="define-a-base-query"></a>Vytvoření základního dotazu

Abyste mohli začít, nadefinujeme základní dotaz, který načte události protokolu událostí Windows z kanálu Windows Azure, které jsou zachyceny do Application Insights jako záznamy sledování.
Tyto záznamy lze použít pro zjišťování celou řadou potíží v Azure Cloud Services, jako je selhání spuštění, chyby za běhu a recyklovat smyčky.

> [!NOTE]
> Základní dotaz níže zjistí problémy v časovém intervalu 30 minut a předpokládá k ingestování telemetrických dat záznamů zpoždění 10 minut. Tato výchozí nastavení je možné nakonfigurovat podle svých potřeb.

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

## <a name="check-for-specific-event-ids"></a>Vyhledat konkrétní události ID

Po načtení událostí protokolu událostí Windows, lze zjistit určité problémy s kontrolou jejich příslušné ID a zpráva vlastnosti události (Další příklady naleznete níže).
Jednoduše kombinovat základní dotaz nad s jedním z dotazy níže a používané, které dotaz při definování pravidel upozornění protokolů.

> [!NOTE]
> V následujících příkladech se problém zjistil, nejsou-li více než tři události nalezeny analyzované časovém období. Toto výchozí nastavení můžete konfigurovat a měnit citlivosti pravidlo upozornění.

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

V nabídce navigace v rámci prostředku Application Insights, přejděte na **výstrahy**a pak vyberte **nové pravidlo upozornění**.

![Snímek obrazovky vytvořit pravidlo](./media/app-insights-proactive-cloud-services/001.png)

V **vytvořit pravidlo** okně v části **Definujte podmínku upozornění** části, klikněte na **přidat kritéria**a pak vyberte **prohledávání protokolů vlastní**.

![Snímek obrazovky definovat podmínky kritéria pro výstrahy](./media/app-insights-proactive-cloud-services/002.png)

V **vyhledávací dotaz** vložte kombinované dotazu, který jste připravili v předchozím kroku.

Potom pokračujte **prahová hodnota** pole a nastavte jej na hodnotu 0. Může volitelně můžete upravit **období** a frekvenci **pole**.
Klikněte na **Done** (Hotovo).

![Snímek obrazovky konfigurace signál logiku dotazu](./media/app-insights-proactive-cloud-services/003.png)

V části **definujte podrobnosti o upozornění** části, zadejte **název** a **popis** na pravidlo upozornění a nastavte jeho **závažnost**.
Také, ujistěte se, že **Povolit pravidlo po vytvoření** tlačítka nastaven **Ano**.

![Snímek obrazovky podrobností výstrah](./media/app-insights-proactive-cloud-services/004.png)

V části **definujte skupinu akcí** oddílu, můžete vybrat existující **skupiny akcí** nebo vytvořte novou.
Může zvolit, aby skupina akcí obsahovat více akcí z různých typů.

![Snímek obrazovky skupiny akcí](./media/app-insights-proactive-cloud-services/005.png)

Jakmile skupinu akcí, které jste definovali, potvrďte provedené změny a klikněte na tlačítko **vytvořit pravidlo upozornění**.

## <a name="next-steps"></a>Další kroky

Další informace o automatické zjišťování:

[Anomálie selhání](app-insights-proactive-failure-diagnostics.md)
[nevracení paměti](app-insights-proactive-potential-memory-leak.md)
[anomálie výkonu](app-insights-proactive-performance-diagnostics.md)

