---
title: Příprava na migraci klasických upozornění Azure monitoru aktualizací logic apps a sady runbook
description: Zjistěte, jak upravit webhooku, aplikace logiky a sady runbook pro přípravu migrace dobrovolná.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632103"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Příprava aplikace logiky a spusťte seznamy pro migraci klasického pravidla upozornění

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje v červenci 2019. Nástroj pro migraci k aktivaci migrace dobrovolně je k dispozici na webu Azure portal a zavádí pro zákazníky, kteří používají klasický pravidla upozornění.

Pokud budete chtít dobrovolně migrovat do nového pravidla upozornění classic pravidel upozornění, existují určité rozdíly mezi těmito dvěma systémy, které byste měli vědět. Tento článek vás provede rozdíly mezi těmito dvěma systémy a jak můžete připravit změny.

## <a name="api-changes"></a>Změny rozhraní API

Rozhraní API umožňuje vytvořit a spravovat klasické pravidla výstrah (`microsoft.insights/alertrules`) se liší od rozhraní API umožňuje vytvořit a spravovat nové upozornění metrik (`microsoft.insights/metricalerts`). Pokud jste prostřednictvím kódu programu vytvořit a spravovat klasické pravidla upozornění ještě dnes, aktualizujte vaše skripty nasazení pro práci s nová rozhraní API.

Následující tabulka obsahuje odkaz na příkazových rozhraních pro klasické i nové výstrahy.

|         |Klasická upozornění  |Nová upozornění metriky |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [Výstraha monitorování az](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [upozornění metrik az monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referenční informace](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Šablona Azure Resource Manageru | [U klasických upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Pro nové upozornění na metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Změny datová část oznámení

Formát datové části oznámení se mírně liší mezi [classic pravidla upozornění](alerts-webhooks.md) a [nové upozornění na metriky](alerts-metric-near-real-time.md#payload-schema). Pokud máte jakékoli webhooku, aplikace logiky nebo classic pravidla upozornění se aktivuje runbook akce, budete muset aktualizovat těchto koncových bodů oznámení tak, aby přijímal Formát datové části nové upozornění na metriky.

V následující tabulce můžete použít k mapování polí mezi datová část webhooku klasické pravidlo upozornění a nová datová část webhooku metriky upozornění.

|  |Klasická upozornění  |Nová upozornění metriky |
|---------|---------|---------|
|Byla výstraha aktivována nebo přeložit     | status       | data.status |
|Kontextové informace o výstraze     | Kontext        | data.context        |
|Časové razítko, kdy byla výstraha aktivuje nebo vyřešit      | context.timestamp       | data.context.timestamp        |
| ID upozornění pravidla | Context.ID | data.context.id |
| Název pravidla upozornění | Context.Name | data.context.name |
| Popis pravidla výstrahy | context.description | data.context.description |
| Podmínka pro pravidlo výstrahy | context.condition | data.context.condition|
| Název metriky | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Časová agregace (jak metrika je agregován v okně vyhodnocení)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Zkušební období | context.condition.windowSize | data.context.condition.windowSize|
| (Jak agregovaná hodnota metriky se porovná s prahovou hodnotou) – operátor | context.condition.operator | data.context.condition.operator|
| Prahová hodnota | context.condition.threshold| data.context.condition.allOf[0].threshold|
| Hodnota metriky | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| ID předplatného | context.subscriptionId | data.context.subscriptionId|
| Skupiny prostředků tohoto zdroje | context.resourceGroup | data.context.resourceGroup|
| Název zdroje | context.resourceName | data.context.resourceName |
| Typ zdroje | context.resourceType | data.context.resourceType |
|  ID prostředku daný prostředek | context.resourceId | data.context.resourceId |
| Přímý odkaz na souhrnné stránce portálu prostředků | context.portalLink | data.context.portalLink|
| Vlastní datová část pole se mají předat webhook nebo aplikace logiky | properties |data.Properties |

Jak je vidět datové části jsou podobné. Následující části obsahuje podrobnosti o ukázkové aplikace logiky a ukázkové sady runbook k analýze datová část oznámení pro nové výstrahy.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Pomocí aplikace logiky, která přijímá metriky oznámení výstrah

Pokud používáte logic apps s využitím upozornění classic, musíte upravit svou aplikaci logiky k analýze datové části nové upozornění na metriku.

1. Vytvořte novou aplikaci logiky.

2. Pomocí šablony "Azure Monitor – metriky upozornění obslužná rutina". Tato šablona obsahuje **požadavku HTTP** aktivační událost s odpovídající definované schéma

    ![šablony aplikace logiky](media/alerts-migration/logic-app-template.png "metriky šablonu oznámení")

3. Přidání akce pro hostovat svoji logiku zpracování.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Pomocí runbook služby automation, která přijímá metriky oznámení výstrah

Následující vzorový kód Powershellu, který je možné v sadě runbook, který můžete analyzovat datové části pro klasické metriky upozornění pravidla i nová pravidla upozornění metrik.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Podívejte se na úplný příklad sady runbook, který zastaví virtuální počítač, když se aktivuje upozornění v [dokumentace ke službě Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integrace partnerských řešení prostřednictvím webhooků

Většina [našich partnerů, které se integrují s klasických upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/partners) již podporu novějších upozornění metrik prostřednictvím jejich integrace. Níže jsou uvedeny známé integrace, které už pracují s novou upozornění metrik.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Pokud používáte integrace partnerských řešení, která není uvedená tady, potvrďte u poskytovatele integrace, integrace funguje se nové upozornění na metriky.

## <a name="next-steps"></a>Další postup

- [Jak používat nástroj pro migraci](alerts-using-migration-tool.md)
- [Pochopit, jak funguje nástroj pro migraci](alerts-understand-migration.md)
