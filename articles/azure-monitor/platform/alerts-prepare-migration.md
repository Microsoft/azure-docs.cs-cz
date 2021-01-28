---
title: Aktualizace Logic Apps & runbooků pro migraci výstrah
description: Naučte se, jak upravit Webhooky, Logic Apps a runbooky pro přípravu na dobrovolné migrace.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 60a721af9acf980e88ad60504e75d2488c8a4d81
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944480"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Příprava aplikací logiky a runbooků na migraci klasických pravidel upozornění

> [!NOTE]
> Jak [už jsme oznámili](monitoring-classic-retirement.md), klasické výstrahy v Azure monitor jsou vyřazené, i když jsou pořád v omezeném rozsahu použití pro prostředky, které ještě nepodporují nové výstrahy. Datum vyřazení těchto výstrah bylo ještě rozšířeno. Brzy bude oznámeno nové datum.
>

Pokud se rozhodnete, že chcete migrovat pravidla vašich klasických výstrah na nová pravidla upozornění, pamatujte na to, že mezi těmito dvěma systémy existují rozdíly. V tomto článku se dozvíte o těchto rozdílech a o tom, jak můžete tuto změnu připravit.

## <a name="api-changes"></a>Změny rozhraní API

Rozhraní API, která vytvářejí a spravují klasická pravidla upozornění ( `microsoft.insights/alertrules` ), se liší od rozhraní API, která vytvářejí a spravují nové výstrahy metriky ( `microsoft.insights/metricalerts` ). Pokud máte v dnešní době programově vytvářet a spravovat pravidla výstrah, aktualizujte skripty pro nasazení tak, aby fungovaly s novými rozhraními API.

Následující tabulka je odkazem na programová rozhraní pro klasické a nové výstrahy:

| Typ skriptu nasazení | Klasická upozornění | Nové výstrahy metriky |
| ---------------------- | -------------- | ----------------- |
|REST API     | [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules)         | [Microsoft. Insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Azure CLI     | [AZ monitor Alert](/cli/azure/monitor/alert)        | [AZ monitor Metrics Alert](/cli/azure/monitor/metrics/alert)        |
|PowerShell      | [Odkaz](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Odkaz](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Šablona Azure Resource Manageru | [Pro klasické výstrahy](./alerts-enable-template.md)|[Nové výstrahy metriky](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Změny datové části oznámení

Formát datové části oznámení se mírně liší mezi [pravidly upozornění Classic](alerts-webhooks.md) a [novými výstrahami metriky](alerts-metric-near-real-time.md#payload-schema). Pokud máte žádné Webhooky, aplikace logiky nebo akce sady Runbook aktivované klasickými pravidly upozornění, je nutné aktualizovat tyto koncové body oznámení tak, aby přijímaly formát datové části nových výstrah metrik.

Pomocí následující tabulky namapujte pole datové části Webhooku z klasického formátu do nového formátu:

| Typ koncového bodu oznámení | Klasická upozornění | Nové výstrahy metriky |
| -------------------------- | -------------- | ----------------- |
|Aktivovala se výstraha nebo se vyřešila?    | **stav**       | **data. status** |
|Kontextové informace o výstraze     | **souvislost**        | **data. Context**        |
|Časové razítko, u kterého se výstraha aktivovala nebo vyřešila     | **Context. timestamp**       | **data. Context. timestamp**        |
| ID pravidla výstrahy | **context.id** | **data.context.id** |
| Název pravidla upozornění | **context.name** | **data.context.name** |
| Popis pravidla výstrahy | **Context. Description** | **data. Context. Description** |
| Podmínka pravidla výstrahy | **Context. Condition** | **data. Context. Condition** |
| Název metriky | **Context. Condition. metric** | **data. Context. Condition. allOf [0]. metric** |
| Časová agregace (jak je metrika agregována v okně vyhodnocení)| **Context. Condition. timeAggregation** | **Context. Condition. timeAggregation** |
| Zkušební období | **Context. Condition. windowSize** | **data. Context. Condition. windowSize** |
| (Způsob porovnání agregované hodnoty metriky s prahovou hodnotou) | **Context. Condition. operator** | **data. Context. Condition. operator** |
| Prahová hodnota | **Context. Condition. Threshold** | **data. Context. Condition. allOf [0]. prahová hodnota** |
| Hodnota metriky | **Context. Condition. metricValue** | **data. Context. Condition. allOf [0]. metricValue** |
| ID předplatného | **Context. subscriptionId** | **data. Context. subscriptionId** |
| Skupina prostředků ovlivněného prostředku | **Context. resourceName** | **data. Context. resourceName** |
| Název ovlivněného prostředku | **Context. resourceName** | **data. Context. resourceName** |
| Typ ovlivněného prostředku | **Context. ResourceType** | **data. Context. ResourceType** |
| ID prostředku ovlivněného prostředku | **Context. resourceId** | **data. Context. resourceId** |
| Přímý odkaz na stránku souhrnu prostředků na portálu | **Context. portalLink** | **data. Context. portalLink** |
| Vlastní pole datové části, která se mají předat Webhooku nebo aplikaci logiky | **vlastnosti** | **data. Properties** |

Datová část je podobná, jak vidíte. Následující část nabízí:

- Podrobnosti o úpravách Logic Apps pro práci s novým formátem.
- Příklad Runbooku, který analyzuje datovou část oznámení pro nové výstrahy.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Úprava aplikace logiky pro příjem upozornění na upozornění metriky

Pokud používáte Logic Apps s klasickými výstrahami, musíte upravit logiku – kód aplikace a analyzovat novou datovou část upozornění metrik. Postupujte následovně:

1. Vytvořte novou aplikaci logiky.

1. Použijte šablonu obslužná rutina výstrahy Azure Monitor – metriky. Tato šablona obsahuje aktivační událost **požadavku HTTP** s definovaným příslušným schématem.

    ![Snímek obrazovky ukazuje dvě tlačítka, prázdná aplikace logiky a Azure Monitor – obslužná rutina výstrahy metriky.](media/alerts-migration/logic-app-template.png "Šablona výstrahy metriky")

1. Přidejte akci pro hostování logiky zpracování.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Použití Runbooku služby Automation, který obdrží oznámení výstrahy metriky

Následující příklad poskytuje kód PowerShellu pro použití v Runbooku. Tento kód může analyzovat datové části pro pravidla upozornění na klasickou metriku a nová pravidla upozornění na metriky.

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

Úplný příklad Runbooku, který zastaví virtuální počítač, když se aktivuje výstraha, najdete v [dokumentaci k Azure Automation](../../automation/automation-create-alert-triggered-runbook.md).

## <a name="partner-integration-via-webhooks"></a>Integrace partnerů prostřednictvím webhooků

Většina [našich partnerů, kteří se integrují s klasickými výstrahami,](./partners.md) už v rámci svých integrací podporuje novější výstrahy metriky. Známé integrace, které už fungují s novými výstrahami metriky:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Pokud používáte integraci partnera, která zde není uvedená, potvrďte u poskytovatele Integration zprostředkovatele, že integrace funguje s novými výstrahami metriky.

## <a name="next-steps"></a>Další kroky

- [Způsoby použití nástroje pro migraci](alerts-using-migration-tool.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)
