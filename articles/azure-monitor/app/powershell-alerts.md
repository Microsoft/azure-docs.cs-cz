---
title: Nastavení výstrah v application insights pomocí Powershellu | Dokumenty společnosti Microsoft
description: Automatizujte konfiguraci Application Insights a získejte e-maily o změnách metrik.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: c19cb43d08b44b55c786e750e64a83e6f0c67381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669841"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Použití prostředí PowerShell k nastavení výstrahy v nástroji Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Konfiguraci [výstrah](../../azure-monitor/app/alerts.md) můžete automatizovat v [application insights](../../azure-monitor/app/app-insights-overview.md).

Kromě toho můžete [nastavit webhooky pro automatizaci odpovědi na výstrahu](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Pokud chcete vytvářet prostředky a výstrahy současně, zvažte [použití šablony Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Jednorázové nastavení
Pokud jste powershell s předplatným Azure ještě nepoužívali:

Nainstalujte modul Azure Powershell do počítače, kde chcete spustit skripty.

* Nainstalujte [Instalační službu webové platformy Společnosti Microsoft (v5 nebo vyšší).](https://www.microsoft.com/web/downloads/platform.aspx)
* Použijte ho k instalaci Microsoft Azure Powershellu

## <a name="connect-to-azure"></a>Připojení k Azure
Spusťte Azure PowerShell a [připojte se k předplatnému:](/powershell/azure/overview)

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Dostávat upozornění
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Přidat výstrahu
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Příklad 1
Napište mi, jestli je odpověď serveru na požadavky HTTP, v průměru za 5 minut, pomalejší než 1 sekunda. Můj prostředek Application Insights se nazývá IceCreamWebApp a je ve skupině prostředků Fabrikam. Jsem vlastníkem předplatného Azure.

Identifikátor GUID je ID předplatného (nikoli instrumentační klíč aplikace).

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Příklad 2
Mám aplikaci, ve které používám [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) k nahlášení metriky s názvem "salesPerHour". Pošlete e-mail svým kolegům, pokud "salesPerHour" klesne pod 100, v průměru za 24 hodin.

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Stejné pravidlo lze použít pro metriku hlášenou pomocí [parametru měření](../../azure-monitor/app/api-custom-events-metrics.md#properties) jiného volání sledování, jako je TrackEvent nebo trackPageView.

## <a name="metric-names"></a>Názvy metrik
| Název metriky | Název obrazovky | Popis |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Výjimky prohlížečů |Počet nezachycených výjimek vyváděných v prohlížeči. |
| `basicExceptionServer.count` |Výjimky serveru |Počet neošetřených výjimek vyvržených aplikací |
| `clientPerformance.clientProcess.value` |Doba zpracování klienta |Doba mezi přijetím posledního bajtu dokumentu do načtení dom. Asynchronní požadavky mohou být stále zpracování. |
| `clientPerformance.networkConnection.value` |Čas připojení sítě načítání stránky |Čas, který prohlížeč potřebuje k připojení k síti. Může být 0, pokud je uložena v mezipaměti. |
| `clientPerformance.receiveRequest.value` |Doba odezvy příjmu |Doba mezi odesláním požadavku prohlížeče na zahájení příjmu odpovědi. |
| `clientPerformance.sendRequest.value` |Čas odeslání požadavku |Čas, který prohlížeč odebere k odeslání požadavku. |
| `clientPerformance.total.value` |Čas načítání stránky prohlížeče |Čas od požadavku uživatele až do DOM, styly, skripty a obrázky jsou načteny. |
| `performanceCounter.available_bytes.value` |Dostupná paměť |Fyzická paměť je okamžitě k dispozici pro proces nebo pro použití v systému. |
| `performanceCounter.io_data_bytes_per_sec.value` |Rychlost varychlost vi procesu |Celkový počet bajtů za sekundu čtení a zápis do souborů, sítě a zařízení. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |míra výjimek |Výjimky vyvolány za sekundu. |
| `performanceCounter.percentage_processor_time.value` |Procesní procesor |Procento uplynulého času všech vláken procesu používaných procesorem k provedení pokynů pro proces aplikace. |
| `performanceCounter.percentage_processor_total.value` |Čas procesoru |Procento času, který procesor stráví v vláknech bez nečinnosti. |
| `performanceCounter.process_private_bytes.value` |Zpracovat soukromé bajty |Paměť je přiřazena výhradně procesům monitorované aplikace. |
| `performanceCounter.request_execution_time.value` |ASP.NET čas spuštění požadavku |Doba spuštění poslední ho požadavku. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET požadavky ve frontě spuštění |Délka fronty žádostí o aplikaci. |
| `performanceCounter.requests_per_sec.value` |ASP.NET míra požadavků |Rychlost všech požadavků na aplikaci za sekundu od ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Selhání závislostí |Počet neúspěšných volání serverových aplikací do externích prostředků. |
| `request.duration` |Doba odezvy serveru |Doba mezi přijetím požadavku HTTP a dokončením odeslání odpovědi. |
| `request.rate` |Míra požadavků |Rychlost všech požadavků na aplikaci za sekundu. |
| `requestFailed.count` |Neúspěšné požadavky |Počet požadavků HTTP, které vedly k kódu odpovědi >= 400 |
| `view.count` |Zobrazení stránky |Počet požadavků uživatelů klienta na webovou stránku. Syntetický provoz je odfiltrován. |
| {název vlastní metriky} |{Název metriky} |Vaše metrická hodnota vykázaná [společností TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) nebo parametrem [měření sledovacího volání](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Metriky jsou odesílány různými moduly telemetrie:

| Skupina metrik | Modul kolektoru |
| --- | --- |
| basicExceptionBrowser,<br/>výkon klienta,<br/>Prohlédni |[JavaScript prohlížeče](../../azure-monitor/app/javascript.md) |
| Performancecounter |[Výkon](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Závislost](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Požadavek<br/>požadavek Se nezdařilo. |[Požadavek serveru](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooky
Odpověď [na výstrahu](../../azure-monitor/platform/alerts-webhooks.md)můžete automatizovat . Azure bude volat webovou adresu podle vašeho výběru, když je vyvolána výstraha.

## <a name="see-also"></a>Viz také
* [Skript pro konfiguraci přehledů aplikací](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Vytváření přehledů aplikací a webových testovacích prostředků ze šablon](powershell.md)
* [Automatizace propojení diagnostiky Microsoft Azure s přehledy aplikací](powershell-azure-diagnostics.md)
* [Automatizace odpovědi na výstrahu](../../azure-monitor/platform/alerts-webhooks.md)
