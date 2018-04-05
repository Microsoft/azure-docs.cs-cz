---
title: Vytvořte výstrahy pro služby Azure – prostředí PowerShell | Microsoft Docs
description: Aktivační událost e-mailů, oznámení, weby adresy URL (webhooky), nebo volat automatizace při splnění zadané podmínky.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 8f7df424b27e6899821a9bdd2f1d8397a0de35a7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Vytvoření klasického metriky výstrahy v Azure monitorování pro služby Azure – prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Rozhraní příkazového řádku](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Přehled

> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější, lepší metriky výstrahy](monitoring-near-real-time-metric-alerts.md). Tyto výstrahy můžete sledovat více metriky a povolit pro výstrahy na dimenzí metriky. Podpora prostředí PowerShell pro novější metriky výstrahy tu bude brzo dostupná.
>
>

Tento článek ukazuje, jak nastavit Azure classic metriky výstrah pomocí prostředí PowerShell.  

Můžete zobrazit upozornění na základě monitorování metriky pro nebo událostí na služeb Azure.

* **Metriky hodnoty** -výstrahy aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu přiřadíte v obou směrech. To znamená, aktivuje obě při nejprve je splněna podmínka, a pak později, pokud podmínka je už plněny.    
* **Aktivity protokolu události** -výstrahu můžete aktivovat pro *každých* události nebo pouze tehdy, když dojde k určité události. Další informace o výstrahách aktivity protokolu [, klikněte sem](monitoring-activity-log-alerts.md)

Můžete nakonfigurovat classic metriky výstrahu při aktivaci, proveďte následující:

* odesílat oznámení e-mailu Správce služeb a spolusprávci
* Odeslat e-mail na další e-mailů, které zadáte.
* Volat webhook, jehož
* Spusťte provádění runbook služby Azure (pouze z portálu Azure)

Můžete nakonfigurovat a získat informace o použití pravidla výstrah

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Rozhraní příkazového řádku (CLI)](insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Další informace najdete vždy zadáte ```Get-Help``` a potom příkaz prostředí PowerShell chcete zobrazit nápovědu.

## <a name="create-alert-rules-in-powershell"></a>Vytvořit pravidla výstrah v prostředí PowerShell
1. Přihlásí se k Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Získání seznamu odběry, které máte k dispozici. Ověřte, že pracujete s správné předplatné. Pokud ne, nastavte ji na ten správný pomocí výstup `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. K zobrazení seznamu existujících pravidel ve skupině prostředků, použijte následující příkaz:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. K vytvoření pravidla, musíte nejprve mít několik důležitých informací.

  * **ID prostředku** pro prostředek, kterou chcete nastavit upozornění pro
  * **Definice metrik** k dispozici pro tento prostředek

     Jeden způsob, jak získat ID prostředku je použití portálu Azure. Za předpokladu, že byl již vytvořený, vyberte ho v portálu. V okně Další vyberte *vlastnosti* pod *nastavení* části. **ID prostředku** je pole v okně Další. Dalším způsobem je použít [Průzkumníka prostředků Azure](https://resources.azure.com/).

     Je například ID prostředku pro webovou aplikaci

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Můžete použít `Get-AzureRmMetricDefinition` zobrazíte seznam všechny definice metrik pro konkrétní zdroje.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     Následující příklad vytvoří tabulku s metrika název a jednotky pro tuto metriku.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Úplný seznam dostupných možností pro Get-AzureRmMetricDefinition je k dispozici spuštěním `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. Následující příklad nastaví výstrahu na webový server prostředku. Výstrahy aktivačních událostí, kdykoliv konzistentně obdrží veškerou komunikaci pro 5 minut a opakujte při přijetí žádný provoz 5 minut.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. K vytvoření webhooku nebo odeslat e-mail, když se aktivuje výstraha, je nutné nejprve vytvořte e-mailu nebo webhooky. Potom hned vytvořte pravidlo později se značkou - akce a jak je znázorněno v následujícím příkladu. Nelze přidružit webhooku nebo e-mailů s již vytvořili pravidla pomocí prostředí PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Chcete-li ověřit, že upozornění nebyly vytvořeny správně pohledem na jednotlivá pravidla.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Oznámení odstraňte. Tyto příkazy odstranit pravidla vytvořená dříve v tomto článku.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Další postup
* [Získat přehled o Azure monitorování](monitoring-overview.md) včetně typy informací, můžete sledovat a shromažďovat.
* Naučte se [konfigurace webhooky ve výstrahách](insights-webhooks-alerts.md).
* Naučte se [nakonfigurujte upozornění na aktivitu protokolu události](monitoring-activity-log-alerts.md).
* Další informace o [sad Azure Automation Runbook](../automation/automation-starting-a-runbook.md).
* Získat [přehled shromažďování diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md) ke shromažďování metrik podrobné vysoká frekvence vaší služby.
* Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
