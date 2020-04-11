---
title: Aktualizace aplikací logiky & runbooky pro migraci výstrah
description: Přečtěte si, jak upravit webhooky, aplikace logiky a runbooky a připravit se na dobrovolnou migraci.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114409"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Příprava aplikací logiky a runbooků na migraci klasických pravidel upozornění

Jak [již bylo oznámeno](monitoring-classic-retirement.md), klasické výstrahy ve službě Azure Monitor se vyřazují v září 2019 (původně v červenci 2019). Nástroj pro migraci je k dispozici na webu Azure Portal zákazníkům, kteří používají klasická pravidla výstrah a chtějí migraci spustit sami.

> [!NOTE]
> Kvůli zpoždění při zavádění migračního nástroje bylo datum odchodu do důchodu pro klasickou výstrahu prodlouženo na 31.

Pokud se rozhodnete dobrovolně migrovat klasická pravidla výstrah na nová pravidla výstrah, uvědomte si, že mezi těmito dvěma systémy existují určité rozdíly. Tento článek vysvětluje tyto rozdíly a jak se můžete připravit na změnu.

## <a name="api-changes"></a>Změny rozhraní API

Pravidla API, která vytvářejí a`microsoft.insights/alertrules`spravují klasická pravidla výstrah ( ) se`microsoft.insights/metricalerts`liší od api, která vytvářejí a spravují nové výstrahy metrik ( ). Pokud programově vytvoříte a spravujete klasická pravidla výstrah ještě dnes, aktualizujte skripty nasazení tak, aby fungovaly s novými pravidly API.

Následující tabulka je odkazem na programová rozhraní pro klasické i nové výstrahy:

|         |Klasická upozornění  |Nová upozornění na metriky |
|---------|---------|---------|
|REST API     | [Microsoft.Insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor výstraha](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [upozornění na metriky az monitoru](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referenční informace](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Odkaz](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Šablona Azure Resource Manageru | [Pro klasická upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Pro nová upozornění na metriky](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Změny datové části oznámení

Formát datové části oznámení se mírně liší mezi [klasickými pravidly výstrah](alerts-webhooks.md) a [novými upozorněními metriky](alerts-metric-near-real-time.md#payload-schema). Pokud máte nějaké webhooku, aplikace logiky nebo akce sady Runbook, které jsou spouštěny klasickými pravidly výstrah, je nutné aktualizovat tyto koncové body oznámení přijmout formát datové části nové výstrahy metriky.

Následující tabulka slouží k mapování polí datové části webového háku z klasického formátu na nový formát:

|  |Klasická upozornění  |Nová upozornění na metriky |
|---------|---------|---------|
|Bylo upozornění aktivováno nebo vyřešeno?    | **Stav**       | **data.status** |
|Kontextové informace o výstraze     | **Kontextu**        | **data.context**        |
|Časové razítko, při kterém bylo upozornění aktivováno nebo vyřešeno     | **context.timestamp**       | **data.context.timestamp**        |
| ID pravidla výstrahy | **context.id** | **data.context.id** |
| Název pravidla upozornění | **context.name** | **data.context.name** |
| Popis pravidla výstrahy | **context.description** | **data.context.description** |
| Podmínka pravidla výstrahy | **context.condition** | **data.context.condition** |
| Název metriky | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agregace času (jak se metrika agreguje v okně hodnocení)| **context.condition.timeAgregace** | **context.condition.timeAgregace** |
| Období hodnocení | **context.condition.windowVelikost** | **data.context.condition.windowVelikost** |
| Operátor (jak se porovnávaná hodnota metriky s prahovou hodnotou) | **context.condition.operator** | **data.context.condition.operator** |
| Prahová hodnota | **context.condition.threshold** | **threshold data.context.condition.allOf[0].** |
| Metrická hodnota | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID předplatného | **context.subscriptionId** | **data.context.subscriptionId** |
| Skupina prostředků ohroženého zdrojem | **context.resourceGroup** | **data.context.resourceGroup** |
| Název ohroženého prostředku | **context.resourceName** | **data.context.resourceName** |
| Typ ovlivněného zdroje | **context.resourceType** | **data.context.resourceType** |
| ID prostředku postiženého prostředku | **context.resourceId** | **data.context.resourceId** |
| Přímý odkaz na stránku souhrnu prostředků portálu | **context.portalLink** | **data.context.portalLink** |
| Vlastní pole datové části, která mají být předána webhooku nebo aplikaci logiky | **Vlastnosti** | **data.properties** |

Užitečné zatížení jsou podobné, jak můžete vidět. Následující sekce nabízí:

- Podrobnosti o úpravě aplikací logiky pro práci s novým formátem.
- Příklad runbooku, který analyzuje datovou část oznámení pro nové výstrahy.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Úprava aplikace logiky pro příjem upozornění na metriku

Pokud používáte aplikace logiky s klasickými výstrahami, musíte upravit kód aplikace logiky, abyste analýzu nové metriky výstrahy datové části. Postupujte následovně:

1. Vytvořte novou aplikaci logiky.

1. Použijte šablonu "Azure Monitor – metriky Obslužná rutina výstrah". Tato šablona obsahuje aktivační událost **požadavku HTTP** s definovaným příslušným schématem.

    ![šablona logika-aplikace](media/alerts-migration/logic-app-template.png "Šablona upozornění metriky")

1. Přidejte akci pro hostování logiky zpracování.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Použití runbooku automatizace, který obdrží upozornění na metriku

Následující příklad obsahuje kód prostředí PowerShell, který se má použít v runbooku. Tento kód může analyzovat datové části pro klasická pravidla upozornění metriky a nová pravidla upozornění metriky.

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

Úplný příklad sady Runbook, která zastaví virtuální počítač při aktivaci výstrahy, najdete v [dokumentaci k Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integrace partnerů prostřednictvím webhooků

Většina [našich partnerů, kteří se integrují s klasickými výstrahami,](https://docs.microsoft.com/azure/azure-monitor/platform/partners) již prostřednictvím svých integrací podporuje novější upozornění na metriky. Známé integrace, které již fungují s novými upozorněními na metriky, jsou:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Pokud používáte integraci partnera, která zde není uvedena, potvrďte u poskytovatele integrace, že integrace funguje s novými upozorněními na metriky.

## <a name="next-steps"></a>Další kroky

- [Způsoby použití nástroje pro migraci](alerts-using-migration-tool.md)
- [Princip fungování nástroje pro migraci](alerts-understand-migration.md)
