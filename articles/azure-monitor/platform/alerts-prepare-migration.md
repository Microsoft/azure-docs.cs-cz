---
title: Příprava na migraci klasických upozornění Azure monitoru aktualizací logic apps a sady runbook
description: Zjistěte, jak upravit webhooky, logic apps a sady runbook pro přípravu migrace dobrovolná.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: bdbd45c2b10dec8f1c0a85110747a470e818dbf9
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015609"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Příprava na migraci klasického pravidel upozornění logic apps a sady runbook

Jako [jsme oznámili už dřív](monitoring-classic-retirement.md), klasických upozornění ve službě Azure Monitor se vyřazuje v září 2019 (byla původně 2019. července). Migrační nástroj je dostupný na webu Azure Portal pro zákazníky používající klasický pravidla upozornění a který chcete spustit migrace sami.

> [!NOTE]
> Kvůli zpoždění při uvádění nástroj pro migraci vyřazením pro migraci klasických upozornění rozšířilo do 31. srpna 2019 od původně oznámené data 30. června 2019.

Pokud budete chtít dobrovolně migrovat do nového pravidla upozornění classic pravidel upozornění, mějte na paměti, že existují určité rozdíly mezi těmito dvěma systémy. Tento článek vysvětluje tyto rozdíly a jak můžete připravit změny.

## <a name="api-changes"></a>Změny rozhraní API

Rozhraní API, která vytvářet a spravovat klasické pravidla výstrah (`microsoft.insights/alertrules`) se liší od rozhraní API, která vytvářet a spravovat nové upozornění metrik (`microsoft.insights/metricalerts`). Pokud prostřednictvím kódu programu vytvořit a spravovat klasické pravidla upozornění ještě dnes, aktualizujte vaše skripty nasazení pro práci s nová rozhraní API.

V následující tabulce je odkaz na příkazových rozhraních pro klasické i nové výstrahy:

|         |Klasická upozornění  |Nová upozornění metriky |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [Výstraha monitorování az](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [upozornění metrik az monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referenční informace](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referenční informace](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Šablona Azure Resource Manageru | [U klasických upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Pro nové upozornění na metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Změny datová část oznámení

Formát datové části oznámení se mírně liší mezi [classic pravidla upozornění](alerts-webhooks.md) a [nové upozornění na metriky](alerts-metric-near-real-time.md#payload-schema). Pokud máte jakékoli webhooku, aplikace logiky nebo sady runbook akce, které jsou aktivovány classic pravidel upozornění, je nutné aktualizovat těchto koncových bodů oznámení tak, aby přijímal Formát datové části nové upozornění na metriky.

Použijte následující tabulku k mapování polí datová část webhooku z formátu classic na nový formát:

|  |Klasická upozornění  |Nová upozornění metriky |
|---------|---------|---------|
|Byla výstraha aktivována nebo přeložit?    | **status**       | **data.status** |
|Kontextové informace o výstraze     | **context**        | **data.context**        |
|Časové razítko, kdy byla výstraha aktivuje nebo vyřešit     | **context.timestamp**       | **data.context.timestamp**        |
| ID upozornění pravidla | **context.id** | **data.context.id** |
| Název pravidla upozornění | **context.name** | **data.context.name** |
| Popis pravidla výstrahy | **context.description** | **data.context.description** |
| Podmínka pro pravidlo výstrahy | **context.condition** | **data.context.condition** |
| Název metriky | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Časová agregace (jak metrika je agregován v okně vyhodnocení)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Zkušební období | **context.condition.windowSize** | **data.context.condition.windowSize** |
| (Jak agregovaná hodnota metriky se porovná s prahovou hodnotou) – operátor | **context.condition.operator** | **data.context.condition.operator** |
| Prahová hodnota | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Hodnota metriky | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID předplatného | **context.subscriptionId** | **data.context.subscriptionId** |
| Skupiny prostředků tohoto zdroje | **context.resourceGroup** | **data.context.resourceGroup** |
| Název zdroje | **context.resourceName** | **data.context.resourceName** |
| Typ zdroje | **context.resourceType** | **data.context.resourceType** |
| ID prostředku daný prostředek | **context.resourceId** | **data.context.resourceId** |
| Přímý odkaz na souhrnné stránce portálu prostředků | **context.portalLink** | **data.context.portalLink** |
| Vlastní datová část pole se mají předat webhook nebo logic app | **Vlastnosti** | **data.properties** |

Datové části jsou podobné, jak je vidět. Následující část nabízí:

- Podrobnosti o úpravách aplikací logiky pro práci s novým formátem.
- Příklad runbooku, který analyzuje datová část oznámení pro nové výstrahy.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Upravit aplikaci logiky, která zobrazí metriky oznámení výstrah

Pokud používáte logic apps s využitím upozornění classic, je třeba upravit kód aplikace logiky do analyzovat novou datovou část upozornění na metriku. Postupujte následovně:

1. Vytvořte novou aplikaci logiky.

1. Pomocí šablony "Azure Monitor – metriky upozornění obslužná rutina". Tato šablona obsahuje **požadavku HTTP** aktivační událost pomocí příslušného schématu definice.

    ![šablony aplikace logiky](media/alerts-migration/logic-app-template.png "metriky šablonu oznámení")

1. Přidání akce pro hostovat svoji logiku zpracování.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Použít runbook služby automation, která přijímá metriky oznámení výstrah

Následující příklad uvádí kód Powershellu pro použití v sadě runbook. Tento kód můžete analyzovat datové části pro klasické metriky upozornění pravidla i nová pravidla upozornění metrik.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

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

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
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

Úplný příklad sady runbook, která zastaví virtuální počítač, když se aktivuje upozornění, najdete v článku [dokumentace ke službě Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integrace partnerských řešení prostřednictvím webhooků

Většina [našich partnerů, které se integrují s klasických upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/partners) již podporu novějších upozornění metrik prostřednictvím jejich integrace. Jsou známé integrace, které již využívají nové upozornění na metriky:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Pokud používáte integrace partnerských řešení, která není uvedená tady, potvrďte u poskytovatele integrace, integrace funguje se nové upozornění na metriky.

## <a name="next-steps"></a>Další postup

- [Jak používat nástroj pro migraci](alerts-using-migration-tool.md)
- [Pochopit, jak funguje nástroj pro migraci](alerts-understand-migration.md)
