---
title: Vytváření upozornění metrik pro protokoly ve službě Azure Monitor
description: Kurz týkající se vytváření upozornění metrik v reálném čase na oblíbené analytická data.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 7fd3ace1acf8442b7df2af90f458e69daf0c270c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966700"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Vytvářet upozornění metrik, protokolů ve službě Azure Monitor  

## <a name="overview"></a>Přehled
Azure podporuje monitorování [výstrahu typu metrika](monitoring-near-real-time-metric-alerts.md) které má výhody [klasických upozornění](alert-metric-classic.md). Metriky jsou k dispozici pro [velké seznam služeb Azure](monitoring-supported-metrics.md). Tento článek vysvětluje použití podmnožiny (to znamená) pro prostředek - `Microsoft.OperationalInsights/workspaces`. 

Upozornění na metriku můžete použít na oblíbené Log Analytics protokoly extrahovat jako metriky jako součást metriky z protokolů, včetně prostředků v Azure nebo místním. Podporované řešení Log Analytics jsou uvedeny níže:
- [Čítače výkonu](../azure-monitor/platform/data-sources-performance-counters.md) u počítačů s Windows a Linuxu
- [Záznamy prezenčního signálu služby Agent Health](../azure-monitor/insights/solution-agenthealth.md)
- [Správa aktualizací](../automation/automation-update-management.md) záznamů
- [Data události](../azure-monitor/platform/data-sources-windows-events.md) protokoly
 
Existuje mnoho výhod pro používání **upozornění metriky pro protokoly** přes na základě dotazu [upozornění protokolů](alert-log.md) v Azure; některé z nich jsou uvedeny níže:
- Upozornění na metriku nabízejí monitorovací schopností v téměř reálném čase a upozornění metriky pro forky data protokolů ze zdroje protokolu pro zajištění stejné
- Upozornění na metriky jsou stavová – jenom oznámení, až když se aktivuje upozornění, a poté, kdy se výstraha vyřeší; na rozdíl od upozornění protokolů, které jsou bezstavové a zachovat ohlásí na každý interval, pokud je splněná podmínka výstrahy
- Upozornění metrik pro protokol poskytují více dimenzí umožňující filtrování k určitým hodnotám, jako jsou počítače, typ operačního systému, jednodušší; atd. bez nutnosti penning dotazu analytics

> [!NOTE]
> Určité metriky a/nebo dimenze pouze zobrazí-li data pro něj existuje za vybrané období. Tyto metriky jsou k dispozici pro zákazníky s pracovních prostorů Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metriky a dimenze podporována pro protokoly
 upozornění na metriku podporují výstrahy pro metriky, které dimenze použít. Dimenze můžete použít k filtrování vaší metrika správnou úroveň. Úplný seznam metrik pro protokoly z nepodporuje [pracovních prostorů Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces) je uvedena; napříč podporovaných řešení.

> [!NOTE]
> Chcete-li zobrazit podporované metriky pro extrahují z pracovního prostoru Log Analytics prostřednictvím [Azure monitorování – metrika](monitoring-metric-charts.md); metriky upozornění pro protokol musí být vytvořeny pro uvedené metriku. Dimenze, které jste zvolili v upozornění metriky pro protokoly – zobrazí jenom prostřednictvím služby Azure Monitor – metriky exploration.

# <a name="creating-metric-alert-for-log-analytics"></a>Vytváření upozornění na metriku pro Log Analytics
Data metriky z oblíbených protokolů je přesměrovaná před zpracováním v Log Analytics do služby Azure Monitor – metriky. To umožňuje uživatelům využívat možnosti metriky platformy, stejně jako upozornění na metriku – včetně výstrah s frekvencí v rozsahu od 1 minuta. Tady je způsob vytváření upozornění na metriku pro protokoly.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Předpoklady pro upozornění metriky pro protokoly
Před metriky pro protokoly se shromažďují v Log Analytics data funguje, následující musí být nastavena spuštěné a dostupné:
1. **Aktivní pracovní prostor Log Analytics**: musí být platné a aktivní pracovní prostor Log Analytics. Další informace najdete v tématu [vytvořit pracovní prostor Log Analytics na webu Azure portal](../azure-monitor/learn/quick-create-workspace.md).
2. **Agent je nakonfigurován pro pracovní prostor Log Analytics**: Agent musí být nakonfigurovaná pro virtuální počítače Azure (a/nebo) na místním virtuálním počítačům k odesílání dat do pracovního prostoru Log Analytics používá v dřívějším kroku. Další informace najdete v tématu [Log Analytics – přehled agenta](../azure-monitor/platform/agents-overview.md).
3. **Je nainstalovaná podporovaných řešení Log Analytics**: řešení Log Analytics by měla být nakonfigurovaná a odesílání dat do pracovního prostoru Log Analytics – podporované řešení jsou [čítače výkonu pro Windows a Linuxu](../azure-monitor/platform/data-sources-performance-counters.md), [Záznamy prezenčního signálu služby Agent Health](../azure-monitor/insights/solution-agenthealth.md), [Update management, a [data události](../azure-monitor/platform/data-sources-windows-events.md).
4. **Protokolu analytická řešení, které jsou nakonfigurované k odeslání protokolů**: řešení Log Analytics by měly mít odpovídající požadované protokoly a data na [metriky podporovat pro pracovní prostory Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces) povolena. Například pro *% dostupné paměti* čítač ho musí být nakonfigurovány v [čítače výkonu](../azure-monitor/platform/data-sources-performance-counters.md) řešení první.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurace upozornění na metriku pro protokoly
 upozornění metrik je možné vytvořit a spravovat pomocí webu Azure portal, šablon Resource Manageru, rozhraní REST API, Powershellu a rozhraní příkazového řádku Azure. Protože upozornění metriky pro protokoly, hodnotu typu variant upozornění na metriku – po dokončení požadavků, vytvářet upozornění na metriku pro protokoly pro zadaný pracovní prostor Log Analytics. Všechny vlastnosti a funkce [ upozornění na metriku](monitoring-near-real-time-metric-alerts.md) bude možné použít k upozornění na metriku pro protokoly, i, včetně datové části schéma, příslušné kvóty a ceny se fakturuje.

Podrobné informace a ukázky – naleznete v tématu [vytváření a Správa upozornění na metriku](https://aka.ms/createmetricalert). Konkrétně pro upozornění metriky pro protokoly – postupujte podle pokynů pro správu upozornění na metriku a zkontrolujte tyto skutečnosti:
- Cíl pro upozornění na metriku je platný *pracovní prostor Log Analytics*
- Signál, zvolená pro upozornění metriky pro vybraný *pracovní prostor Log Analytics* je typu **metrika**
- Filtr pro konkrétní podmínky nebo prostředku pomocí filtrů dimenzí; metriky pro protokoly jsou multidimenzionální
- Při konfiguraci *logiku signálů*, jediná výstraha je možné vytvořit rozložit více hodnot dimenzí (například počítač)
- Pokud **není** pomocí webu Azure portal k vytvoření upozornění metriky pro vybrané *pracovní prostor Log Analytics*; potom musí uživatel ručně nejprve vytvořit explicitní pravidla pro převod data protokolu na metriku pomocí [Azure Monitor – pravidla naplánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Při vytváření upozornění na metriku pro pracovní prostor Log Analytics prostřednictvím webu Azure portal – odpovídající pravidlo pro převod dat protokolu do metrika prostřednictvím [Azure Monitor – naplánované pravidla dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) se automaticky vytvoří na pozadí,  *bez nutnosti jakéhokoli zásahu uživatele nebo akce*. Upozornění na metriku pro vytvoření protokoly pomocí znamená, že kromě webu Azure portal, najdete v části [prostředků šablony pro upozornění metriky pro protokoly](#resource-template-for-metric-alerts-for-logs) věnované ukázkové prostředky vytváření ScheduledQueryRule na základě protokolu pro pravidlo převod metriky dřív, než upozornění na metriku Vytvoření - else nebude žádná data pro upozornění metriky na protokoly vytvořené.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Prostředek šablony pro upozornění metrik pro protokoly
Jak bylo uvedeno dříve, je proces vytváření upozornění metrik z protokolů dvě kanálů:
1. Vytvořit pravidlo pro extrakci metriky z podporovaných protokolů pomocí rozhraní API scheduledQueryRule
2. Vytvoření upozornění na metriku pro metriku extrahují z protokolu (v stažená v kroku 1) a pracovní prostor Log Analytics jako cílový prostředek

To samé, jeden použitím této ukázky níže – šablony Azure Resource Manageru a kde vytváření upozornění na metriku závisí na úspěšném vytvoření pravidla pro extrakci metriky z protokolů přes scheduledQueryRule.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],   
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"                
                    }
                ]
            }
        }
    ]
}

```
Řekněme, že výše uvedený text JSON je uložen jako metricfromLogsAlert.json – potom je možné kombinovat s parametrem soubor JSON pro vytvoření šablony prostředků na základě. Ukázkový soubor JSON parametru jsou uvedeny níže:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan" 
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }    
}
```
Za předpokladu, že výše uvedené soubor parametrů je uložen jako metricfromLogsAlert.parameters.json; pak lze vytvořit upozornění metriky pro protokoly použití [prostředků šablony pro vytvoření webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md). 

Můžete také jeden můžete také pomocí následujícího příkazu prostředí Azure Powershell:
```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlert.json TemplateParameterFile metricfromLogsAlert.parameters.json
```

Nebo použijte nasazení prostředku šablony pomocí rozhraní příkazového řádku Azure:
```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlert.json --parameters @metricfromLogsAlert.parameters.json
```

## <a name="next-steps"></a>Další postup

* Další informace o [ upozornění na metriku](https://aka.ms/createmetricalert).
* Další informace o [upozornění protokolů ve službě Azure](monitor-alerts-unified-log.md).
* Další informace o [výstrah ve službě Azure](monitoring-overview-alerts.md).
