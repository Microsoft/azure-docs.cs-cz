---
title: Pomocí prostředí Powershell můžete nastavit výstrahy ve službě Application Insights | Dokumentace Microsoftu
description: Automatizace konfigurace Application Insights a získejte e-mailů o změnách metriky.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: 678a31b8c07b21e4bb2c43b8e8bc286d66ee4bab
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233741"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Použití prostředí PowerShell k nastavení výstrahy v nástroji Application Insights
Můžete automatizovat konfiguraci [výstrahy](app-insights-alerts.md) v [Application Insights](app-insights-overview.md).

Kromě toho můžete [nastavit webhooky automatizovat reakce na výstrahy](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

> [!NOTE]
> Pokud chcete vytvářet prostředky a výstrahy ve stejnou dobu, vezměte v úvahu [pomocí šablony Azure Resource Manageru](app-insights-powershell.md).
>
>

## <a name="one-time-setup"></a>Jednorázová nastavení
Pokud jste ještě nepoužívali prostředí PowerShell ve vašem předplatném Azure před:

Instalace modulu Azure Powershellu na počítači, ve kterém chcete spustit skripty.

* Nainstalujte [instalačního programu webové platformy (verze 5 nebo vyšší)](https://www.microsoft.com/web/downloads/platform.aspx).
* Použít k instalaci prostředí Azure Powershell

## <a name="connect-to-azure"></a>Připojení k Azure
Otevřete prostředí Azure PowerShell a [připojení k vašemu předplatnému](/powershell/azure/overview):

```PowerShell

    Add-AzureAccount
```


## <a name="get-alerts"></a>Získání výstrah
    Get-AzureAlertRmRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Přidat výstrahu
    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
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
Poslat mi e-mail, pokud odpověď serveru na požadavky HTTP, byla více než 5 minut, je nižší než 1 sekundu. Můj prostředek Application Insights se nazývá IceCreamWebApp a je ve skupině prostředků společnosti Fabrikam. Jmenuji se vlastník předplatného Azure.

Identifikátor GUID je ID předplatného (ne klíč instrumentace aplikace).

    Add-AlertRule -Name "slow responses" `
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
Mám aplikaci, ve které používám [metody TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) hlášení metriku s názvem "salesPerHour." Posílejte že e-mailu kolegové Pokud "salesPerHour" klesne pod 100, byla po dobu 24 hodin.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Stejné pravidlo lze použít pro metriku hlásí pomocí [měření parametr](app-insights-api-custom-events-metrics.md#properties) jiného sledování volání, jako je například TrackEvent nebo trackPageView.

## <a name="metric-names"></a>Názvy metrik
| Název metriky | Název obrazovky | Popis |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Výjimky prohlížečů |Počet nezachycených výjimek vyvolaných v prohlížeči |
| `basicExceptionServer.count` |Serverové výjimky |Počet nezpracovaných výjimek vyvolaných aplikací |
| `clientPerformance.clientProcess.value` |Čas klientského zpracování |Doba mezi přijetím posledního bajtu dokumentu, dokud je načten v modelu DOM. Asynchronní žádosti se možná ještě zpracovávají. |
| `clientPerformance.networkConnection.value` |Čas síťového připojení při stahování stránky |Čas, který prohlížeč pro připojení k síti. Může být 0, pokud uložené v mezipaměti. |
| `clientPerformance.receiveRequest.value` |Čas přijetí odezvy |Doba mezi prohlížeči odesílá požadavek do spuštění trvá příjem odpovědi. |
| `clientPerformance.sendRequest.value` |Čas odeslání žádosti |Doba, za kterou prohlížeč odešlete žádost. |
| `clientPerformance.total.value` |Čas načítání stránky prohlížeče |Doba od žádosti uživatele do načtení DOM, šablon stylů, skriptů a obrázků |
| `performanceCounter.available_bytes.value` |Dostupná paměť |Fyzická paměť k dispozici pro proces nebo pro použití systémem. |
| `performanceCounter.io_data_bytes_per_sec.value` |Frekvence V/V procesu |Celkem bajtů čtených ze souborů, sítě a zařízení nebo do nich zapisovaných za sekundu |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |frekvence výjimek |Výjimek vyvolaných za sekundu. |
| `performanceCounter.percentage_processor_time.value` |Proces – procesor |Procentuální hodnotu uplynulého času všechny podprocesy procesu používají procesor pro spouštění instrukcí pro proces aplikace. |
| `performanceCounter.percentage_processor_total.value` |Čas procesoru |Procento času, které procesor stráví na nečinných vláknech |
| `performanceCounter.process_private_bytes.value` |Privátní bajty procesu |Paměť exkluzivně přiřazená k procesům monitorované aplikace |
| `performanceCounter.request_execution_time.value` |Doba provádění požadavku ASP.NET |Doba provádění nejnovější žádosti |
| `performanceCounter.requests_in_application_queue.value` |Požadavky ASP.NET ve frontě na spuštění |Délka fronty žádostí na aplikace |
| `performanceCounter.requests_per_sec.value` |Frekvence požadavků ASP.NET |Počet všech žádostí na aplikaci za sekundu z ASP.NET |
| `remoteDependencyFailed.durationMetric.count` |Chyby závislostí |Počet neúspěšných volání prováděných aplikací serveru vůči externím prostředkům. |
| `request.duration` |Doba odezvy serveru |Doba mezi přijetím žádosti HTTP a dokončením odesílání odpovědi |
| `request.rate` |Frekvence žádostí |Počet všech žádostí na aplikaci za sekundu. |
| `requestFailed.count` |Neúspěšné požadavky |Žádosti o počet protokolu HTTP, z kterých vzniklo kódem odpovědi > = 400 |
| `view.count` |Zobrazení stránek |Počet žádostí uživatele klienta pro webovou stránku. Syntetický provoz je odfiltrována. |
| {váš vlastní název metriky} |{Váš název metriky} |Metriky hodnotu hlášenou [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric) nebo [měření parametr sledování volání](app-insights-api-custom-events-metrics.md#properties). |

Metriky odesílá telemetrická data různých modulů:

| Metriky skupiny | Modul kolekcí |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>zobrazit |[JavaScript prohlížeče](app-insights-javascript.md) |
| PerformanceCounter |[Výkon](app-insights-configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Závislost](app-insights-configuration-with-applicationinsights-config.md) |
| žádost<br/>requestFailed |[Žádost serveru](app-insights-configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooky
Je možné [automatizovat reakce na výstrahy](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure bude volat webovou adresu podle vašeho výběru, pokud je vyvolána výstraha.

## <a name="see-also"></a>Další informace najdete v tématech
* [Skript pro konfiguraci Application Insights](app-insights-powershell-script-create-resource.md)
* [Vytvořit Application Insights a webových testů prostředků ze šablony](app-insights-powershell.md)
* [Automatizace párování Microsoft Azure Diagnostics do Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Automatizovat reakce na výstrahy](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
