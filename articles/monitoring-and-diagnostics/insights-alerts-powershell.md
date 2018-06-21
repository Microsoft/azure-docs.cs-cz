---
title: Vytvoření klasického upozornění pro služby Azure pomocí prostředí PowerShell | Microsoft Docs
description: Aktivaci e-mailů nebo oznámení, nebo se telefonicky adresy URL webu (webhooky) nebo automatizace při splnění podmínek, které zadáte.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286195"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Vytvořte výstrahy pro služby Azure pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [Rozhraní příkazového řádku](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> Tento článek popisuje postup vytvoření starší classic metriky výstrahy. Azure nyní podporuje monitorování [novější, lepší metriky výstrahy](monitoring-near-real-time-metric-alerts.md). Tyto výstrahy můžete sledovat více metriky a povolit pro výstrahy na dimenzí metriky. Podpora prostředí PowerShell pro novější metriky výstrahy tu bude brzo dostupná.
>
>

Tento článek ukazuje, jak nastavit Azure classic metriky výstrah pomocí prostředí PowerShell.  

Můžete dostávat upozornění na základě metriky pro služeb Azure, nebo můžete přijímat výstrahy pro události, které nastaly v Azure.

* **Metriky hodnoty**: výstrahy aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu přiřadíte v obou směrech. To znamená, aktivuje, i když nejprve splnění podmínky a pak když už se splnění podmínky.    
* **Události protokolu aktivit**: výstrahu můžete aktivovat pro *každých* události nebo když dojde k určité události. Další informace o výstrahách protokolu aktivit najdete v tématu [vytvořit aktivitu protokolu výstrahy (klasické)](monitoring-activity-log-alerts.md).

Můžete nakonfigurovat classic metriky výstrahu při aktivaci udělat následující úkoly:

* Správce služeb a spolusprávci odešlete e-mailová oznámení.
* Odeslat e-mail na další e-mailové adresy, které zadáte.
* Volejte webhook, jehož.
* Spusťte provádění runbook služby Azure (pouze z portálu Azure).

Můžete nakonfigurovat a získat informace o pravidla výstrah v následujících umístěních: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Rozhraní příkazového řádku Azure (CLI)](insights-alerts-command-line-interface.md)
* [Rozhraní API REST Azure monitorování](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Další informace najdete vždy zadáte ```Get-Help``` následovaný příkazem prostředí PowerShell, který potřebujete pomoci s.

## <a name="create-alert-rules-in-powershell"></a>Vytvořit pravidla výstrah v prostředí PowerShell
1. Přihlaste se k Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Získejte seznam odběry, které jsou k dispozici. Ověřte, že pracujete s správné předplatné. Pokud ne, získat na správné předplatné pomocí výstup `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Seznam existujících pravidel ve skupině prostředků. pomocí následujícího příkazu:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. K vytvoření pravidla, musíte nejprve mít několik důležitých informací.

    - **ID prostředku** pro kterou chcete nastavit upozornění pro prostředek.
    - **Definice metrik** které jsou k dispozici pro tento prostředek.

     Jeden způsob, jak získat ID prostředku je použití portálu Azure. Za předpokladu, že byl vytvořen prostředek, vyberte ho v portálu. Potom v okně Další v **nastavení** vyberte **vlastnosti**. **ID prostředku** je pole v okně Další. 
     
     ID prostředku můžete získat také pomocí [Průzkumníka prostředků Azure](https://resources.azure.com/).

     Tady je příklad ID prostředků pro webovou aplikaci:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Můžete použít `Get-AzureRmMetricDefinition` zobrazíte seznam všechny definice metrik pro konkrétní zdroje:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     Následující příklad vytvoří tabulku se název metriky a jednotky pro tuto metriku:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Chcete-li získat úplný seznam dostupných možností pro Get-AzureRmMetricDefinition, spusťte `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. Následující příklad nastaví výstrahy na prostředku webu. Výstrahy aktivačních událostí, kdykoliv konzistentně obdrží veškerou komunikaci pro 5 minut a opakujte při přijetí žádný provoz 5 minut.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. K vytvoření webhook, jehož nebo odeslat e-mail, když se aktivuje výstraha, je nutné nejprve vytvořte e-mailu nebo webhooku. Hned vytvořit pravidlo později s značkou-akce, jak je znázorněno v následujícím příkladu. Nelze přidružit webhooky nebo e-mailů s pravidla, které již byly vytvořeny.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Podívejte se na jednotlivá pravidla, chcete-li ověřit, zda byly správně vytvořeny upozornění.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Oznámení odstraňte. Tyto příkazy odstranit pravidla, které byly vytvořeny dříve v tomto článku.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Další postup
* [Získat přehled o Azure monitorování](monitoring-overview.md), včetně typy informací, můžete sledovat a shromažďovat.
* Naučte se [konfigurace webhooky ve výstrahách](insights-webhooks-alerts.md).
* Naučte se [nakonfigurujte upozornění na aktivitu protokolu události](monitoring-activity-log-alerts.md).
* Další informace o [Azure Automation runbook](../automation/automation-starting-a-runbook.md).
* Získat [přehled shromažďování diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md) ke shromažďování metrik podrobné vysoká frekvence vaší služby.
* Získat [přehled metriky kolekce](insights-how-to-customize-monitoring.md) zkontrolovat služby je k dispozici a dobře reagovaly.
