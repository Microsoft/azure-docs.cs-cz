---
title: Použití PowerShellu k nastavení výstrah v Application Insights | Microsoft Docs
description: Automatizujte konfiguraci Application Insights, abyste získali e-maily o změnách metriky.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: f35658b08eff7574448e3c72b103178b66acbbe0
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701828"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Použití prostředí PowerShell k nastavení výstrahy v nástroji Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Můžete automatizovat konfiguraci [výstrah](../../azure-monitor/platform/alerts-log.md) v [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Kromě toho můžete [nastavit Webhooky pro automatizaci odpovědi na výstrahu](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Pokud chcete současně vytvořit prostředky a výstrahy, zvažte [použití šablony Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Nastavení jednorázového času
Pokud jste ještě nepoužili prostředí PowerShell s předplatným Azure, postupujte takto:

Na počítač, na který chcete spouštět skripty, nainstalujte modul Azure PowerShell.

* Nainstalujte [Instalace webové platformy Microsoft (verze 5 nebo novější)](https://www.microsoft.com/web/downloads/platform.aspx).
* Použijte ho k instalaci Microsoft Azure PowerShellu.

## <a name="connect-to-azure"></a>Připojení k Azure
Začněte Azure PowerShell a [Připojte se k předplatnému](/powershell/azure/overview):

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Získat výstrahy
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
Poslat mi e-mail, pokud odpověď serveru na požadavky HTTP, průměrně za 5 minut, je pomalejší než 1 sekunda. Můj prostředek Application Insights se nazývá IceCreamWebApp a je ve skupině prostředků Fabrikam. Jsem vlastníkem předplatného Azure.

Identifikátor GUID je ID předplatného (ne klíč instrumentace aplikace).

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
Mám aplikaci, ve které používám [TrackMetric ()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) k hlášení metriky s názvem "salesPerHour". Pošle e-mail kolegům, pokud "salesPerHour" klesne pod 100, průměrně za 24 hodin.

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

Stejné pravidlo lze použít pro metriku nahlášenou pomocí [parametru měření](../../azure-monitor/app/api-custom-events-metrics.md#properties) jiného sledovacího volání, jako je například TrackEvent nebo trackPageView.

## <a name="metric-names"></a>Názvy metrik
| Název metriky | Název obrazovky | Description |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Výjimky prohlížečů |Počet nezachycených výjimek vyvolaných v prohlížeči |
| `basicExceptionServer.count` |Výjimky serveru |Počet neošetřených výjimek vyvolaných aplikací |
| `clientPerformance.clientProcess.value` |Doba zpracování klienta |Doba mezi přijetím posledního bajtu dokumentu, dokud není načten DOM. Je možné, že se stále zpracovávají asynchronní požadavky. |
| `clientPerformance.networkConnection.value` |Doba připojení k síti – načtení stránky |Čas, který prohlížeč potřebuje k připojení k síti. Pokud ukládáte do mezipaměti, může být 0. |
| `clientPerformance.receiveRequest.value` |Doba přijetí odezvy |Doba mezi prohlížečem, který odesílá požadavek na přijetí odpovědi. |
| `clientPerformance.sendRequest.value` |Čas požadavku na odeslání |Doba, kterou prohlížeč odešle požadavek. |
| `clientPerformance.total.value` |Doba načítání stránky v prohlížeči |Čas od žádosti uživatele do načtení DOM, šablon stylů, skriptů a imagí. |
| `performanceCounter.available_bytes.value` |Dostupná paměť |K okamžitému zpřístupnění fyzické paměti pro proces nebo pro použití systémem. |
| `performanceCounter.io_data_bytes_per_sec.value` |Rychlost zpracování v/v |Celkový počet bajtů za sekundu přečtených a zapsaných do souborů, sítě a zařízení. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |míra výjimek |Výjimky vyvolané za sekundu. |
| `performanceCounter.percentage_processor_time.value` |PROCESOR procesů |Procentuální hodnota uplynulého času všech vláken procesu používaných procesorem k provádění instrukcí pro proces aplikace. |
| `performanceCounter.percentage_processor_total.value` |Čas procesoru |Procento času, které procesor stráví v nečinných vláknech |
| `performanceCounter.process_private_bytes.value` |Nesdílené bajty procesu |Paměť exkluzivně přiřazená k procesům monitorovaných aplikací. |
| `performanceCounter.request_execution_time.value` |Doba provádění žádosti ASP.NET |Čas provedení posledního požadavku. |
| `performanceCounter.requests_in_application_queue.value` |Žádosti ASP.NET ve frontě spuštění |Délka fronty požadavků aplikace |
| `performanceCounter.requests_per_sec.value` |Počet požadavků ASP.NET |Míra všech požadavků na aplikaci za sekundu z ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Selhání závislosti |Počet neúspěšných volání prováděných serverovou aplikací vůči externím prostředkům |
| `request.duration` |Doba odezvy serveru |Doba mezi přijetím požadavku HTTP a dokončením odesílání odpovědi |
| `request.rate` |Počet požadavků |Míra všech žádostí na aplikaci za sekundu. |
| `requestFailed.count` |Neúspěšné požadavky |Počet požadavků HTTP, jejichž výsledkem byl kód odpovědi >= 400 |
| `view.count` |Zobrazení stránek |Počet požadavků uživatelů klienta na webovou stránku. Syntetická přenosová data se odfiltrují. |
| {vlastní název metriky} |{Název metriky} |Hodnota metriky hlášené [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) nebo v [parametru měření sledovacího hovoru](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Metriky jsou odesílány různými moduly telemetrie:

| Skupina metrik | Modul sběrače |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>zobrazení |[JavaScript v prohlížeči](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Výkon](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Závislost](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Request<br/>requestFailed |[Žádost serveru](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooky
[Odpověď na výstrahu můžete automatizovat](../../azure-monitor/platform/alerts-webhooks.md). Když se vygeneruje výstraha, Azure zavolá webovou adresu dle vašeho výběru.

## <a name="see-also"></a>Viz také
* [Skript pro konfiguraci Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Vytvoření prostředků Application Insights a webového testu ze šablon](powershell.md)
* [Automatizace Diagnostika Microsoft Azure Application Insights](powershell-azure-diagnostics.md)
* [Automatizace odpovědi na výstrahu](../../azure-monitor/platform/alerts-webhooks.md)
