---
title: Upozornění na problémy v Azure Cloud Services s využitím Integrace Azure Diagnostics s Azure Application Insights | Microsoft Docs
description: Monitorujte problémy, jako jsou chyby při spuštění, selhání a recyklace rolí v Azure Cloud Services s využitím Azure Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87309286"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Upozornění na problémy v Azure Cloud Services s využitím integrace diagnostiky Azure s Azure Application Insights

V tomto článku popíšeme, jak nastavit pravidla výstrah, která monitorují problémy, jako jsou chyby při spuštění, havárie a recyklace rolí v Azure Cloud Services (webové a pracovní role).

Metoda popsaná v tomto článku vychází z [Azure Diagnostics integrace s Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)a nedávno vydaných [výstrah protokolu pro Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) funkce.

## <a name="define-a-base-query"></a>Definovat základní dotaz

Pokud chcete začít, definujeme základní dotaz, který načte události protokolu událostí Windows z kanálu Windows Azure, které jsou zachyceny do Application Insights jako záznamy trasování.
Tyto záznamy se dají použít k detekci nejrůznějších problémů v Azure Cloud Services, jako jsou chyby při spuštění, selhání za běhu a recyklace smyček.

> [!NOTE]
> Základní dotaz níže vyhledává problémy v časovém intervalu 30 minut a při ingestování záznamů telemetrie předpokládá latenci 10 minut. Tato výchozí nastavení se dají nakonfigurovat podle potřeby.

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

## <a name="check-for-specific-event-ids"></a>Kontrolovat ID konkrétních událostí

Po načtení událostí protokolu událostí systému Windows můžete zjistit konkrétní problémy tak, že zkontrolujete jejich příslušné ID události a vlastnosti zprávy (viz příklady níže).
Jednoduše Zkombinujte základní dotaz výše s jedním z následujících dotazů a při definování pravidla upozornění protokolu se použije Kombinovaný dotaz.

> [!NOTE]
> V níže uvedených příkladech se zjistí problém, pokud se během analyzovaného časového okna najde víc než tři události. Tato výchozí hodnota se dá nakonfigurovat tak, aby se změnila citlivost pravidla výstrahy.

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

V navigační nabídce v prostředku Application Insights přejděte na **výstrahy** a pak vyberte **nové pravidlo výstrahy**.

![Snímek obrazovky s vytvořením pravidla](./media/proactive-cloud-services/001.png)

V okně **vytvořit pravidlo** v části **definovat podmínku upozornění** klikněte na **Přidat kritéria** a pak vyberte **vlastní prohledávání protokolu**.

![Snímek obrazovky s definicí kritérií podmínky pro výstrahu](./media/proactive-cloud-services/002.png)

Do pole **vyhledávací dotaz** vložte Kombinovaný dotaz, který jste připravili v předchozím kroku.

Pak přejděte do pole **prahová hodnota** a nastavte jeho hodnotu na 0. Volitelně můžete upravit **pole** **period** a frekvence.
Klikněte na **Hotovo**.

![Snímek obrazovky s konfigurací dotazu na logiku signálu](./media/proactive-cloud-services/003.png)

V části **definovat podrobnosti výstrahy** zadejte **název** a **Popis** pravidla výstrahy a nastavte jeho **závažnost**.
Také se ujistěte, že je tlačítko **Povolit pravidlo při vytváření** nastaveno na **Ano**.

![Podrobnosti o výstraze obrazovky](./media/proactive-cloud-services/004.png)

V části **definovat skupinu akcí** můžete vybrat existující **skupinu akcí** nebo vytvořit novou.
Můžete zvolit, aby skupina akcí obsahovala více akcí různých typů.

![Skupina akcí snímku obrazovky](./media/proactive-cloud-services/005.png)

Po definování skupiny akcí potvrďte provedené změny a klikněte na **vytvořit pravidlo upozornění**.

## <a name="next-steps"></a>Další kroky

Další informace o automatickém zjišťování:

[Anomálie selhání](./proactive-failure-diagnostics.md) 
 [Nevracení paměti](./proactive-potential-memory-leak.md) 
 [Anomálie výkonu](./proactive-performance-diagnostics.md)

