---
title: Vytváření upozornění metrik pro protokoly v Azure Monitoru
description: Kurz na vytváření upozornění metriky téměř v reálném čase na populární data analýzy protokolů.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 6b21f228858954292e7a3bc5561d5e86fcfaaf41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055185"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Vytváření upozornění metrik pro protokoly v Azure Monitoru

## <a name="overview"></a>Přehled

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor podporuje [typ upozornění metriky,](../../azure-monitor/platform/alerts-metric-near-real-time.md) který má výhody oproti [klasickým výstrahám](../../azure-monitor/platform/alerts-classic-portal.md). Metriky jsou k dispozici pro [rozsáhlý seznam služeb Azure](../../azure-monitor/platform/metrics-supported.md). Tento článek vysvětluje použití podmnožiny (tj. `Microsoft.OperationalInsights/workspaces`pro prostředek - .

Můžete použít upozornění metriky na oblíbené protokoly Log Analytics extrahované jako metriky jako součást metriky z protokolů, včetně prostředků v Azure nebo místně. Podporovaná řešení Log Analytics jsou uvedena níže:

- [Čítače výkonu](../../azure-monitor/platform/data-sources-performance-counters.md) pro počítače se systémem Windows & Linux
- [Záznamy prezenčního signálu pro stav agenta](../../azure-monitor/insights/solution-agenthealth.md)
- [Aktualizovat](../../automation/automation-update-management.md) záznamy správy
- [Protokoly dat událostí](../../azure-monitor/platform/data-sources-windows-events.md)

Existuje mnoho výhod pro použití **upozornění metriky pro protokoly** přes [výstrahy protokolů](../../azure-monitor/platform/alerts-log.md) založené na dotazech v Azure; některé z nich jsou uvedeny níže:

- Upozornění na metriky nabízejí téměř v reálném čase možnosti monitorování a upozornění metriky pro logs vidlicí data ze zdroje protokolu, aby bylo zajištěno totéž.
- Upozornění na metriky jsou stavové – pouze upozornění jednou, když je výstraha aktivována a jednou, když je výstraha vyřešena; na rozdíl od protokolu výstrahy, které jsou bezstavové a udržet spouštění v každém intervalu, pokud je splněna podmínka výstrahy.
- Upozornění na metriky pro protokol poskytují více dimenzí, což umožňuje filtrování na konkrétní hodnoty, jako jsou počítače, typ operačního systému atd. bez nutnosti psát dotaz v analytice.

> [!NOTE]
> Konkrétní metrika a/nebo dimenze se zobrazí pouze v případě, že pro ni existují data ve zvoleném období. Tyto metriky jsou k dispozici pro zákazníky s pracovními prostory Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metriky a dimenze podporované pro protokoly

 Upozornění na metriky podporují upozornění na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Úplný seznam metrik podporovaných pro protokoly z [pracovních prostorů Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) je uveden; napříč podporovanými řešeními.

> [!NOTE]
> Chcete-li zobrazit podporovanou metriku extrahované z pracovního prostoru Analýzy protokolů prostřednictvím [Azure Monitor – metriky](../../azure-monitor/platform/metrics-charts.md), musí být na základě této konkrétní metriky vytvořena metrika pro protokol. Dimenze vybrané v upozornění metriky pro protokoly – zobrazí se pouze pro průzkum prostřednictvím Azure Monitor – metriky.

## <a name="creating-metric-alert-for-log-analytics"></a>Vytváření upozornění na metriku pro analýzu protokolů

Metrická data z oblíbených protokolů se před zpracováním v Log Analytics, do Azure Monitor – metriky. To umožňuje uživatelům využívat možnosti metrické platformy a upozornění na metriky – včetně upozornění s frekvencí až 1 minuta.
Níže jsou uvedeny prostředky pro vytvoření upozornění metriky pro protokoly.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Požadavky pro upozornění metriky pro protokoly

Před metrika pro protokoly shromážděné na data Log Analytics funguje, musí být nastavena následující a k dispozici:

1. **Aktivní pracovní prostor analýzy protokolů**: Musí být k dispozici platný a aktivní pracovní prostor Analýzy protokolů. Další informace najdete [v tématu Vytvoření pracovního prostoru analýzy protokolů na webu Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).
2. **Agent je nakonfigurovaný pro pracovní prostor Analýzy protokolů**: Agent musí být nakonfigurovaný pro virtuální počítače Azure (nebo) místní virtuální počítače k odesílání dat do pracovního prostoru Analýzy protokolů, který se používá v předchozím kroku. Další informace naleznete v [tématu Log Analytics - Agent Overview](../../azure-monitor/platform/agents-overview.md).
3. **Je nainstalována podporovaná řešení Log Analytics**: Řešení Log Analytics by mělo být nakonfigurováno a odesílání dat do pracovního prostoru Log Analytics – podporovaná řešení jsou [čítače výkonu pro Windows & Linux](../../azure-monitor/platform/data-sources-performance-counters.md), záznamy [prezenčního signálu pro stav agenta](../../azure-monitor/insights/solution-agenthealth.md), [správa aktualizací](../../automation/automation-update-management.md)a [data událostí](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Log Analytics řešení nakonfigurovaná pro odesílání protokolů**: Log Analytics řešení by měla mít požadované protokoly/data odpovídající [metriky podporované pro pracovní prostory Log Analytics povolena.](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) Například pro *% dostupné paměti* čítač musí být nakonfigurován v řešení [čítače výkonu](../../azure-monitor/platform/data-sources-performance-counters.md) jako první.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurace upozornění na metriky pro protokoly

 Metriky výstrahy lze vytvářet a spravovat pomocí portálu Azure, šablony Správce prostředků, rozhraní REST API, PowerShell a Azure CLI. Vzhledem k tomu, že upozornění na metriky pro protokoly je variantou upozornění na metriky – po dokončení požadavků lze pro určený pracovní prostor Log Analytics vytvořit upozornění na metriky pro protokoly. Všechny charakteristiky a funkce [upozornění metriky](../../azure-monitor/platform/alerts-metric-near-real-time.md) budou použitelné také pro upozornění na metriky pro protokoly. včetně schématu užitečného zatížení, příslušných limitů kvót a fakturované ceny.

Podrobné podrobnosti a ukázky – viz [vytváření a správa upozornění na metriky](https://aka.ms/createmetricalert). Konkrétně pro upozornění na metriky pro protokoly – postupujte podle pokynů pro správu upozornění na metriky a ujistěte se, že následující:

- Cíl pro upozornění na metriku je platný *pracovní prostor Log Analytics.*
- Signál vybraný pro upozornění na metriku pro vybraný *pracovní prostor Log Analytics* je typu **Metrika**
- Filtrovat pro určité podmínky nebo zdroj pomocí filtrů dimenzí; metriky pro protokoly jsou vícerozměrné
- Při konfiguraci *logiky signálu*lze vytvořit jednu výstrahu, která zahrnuje více hodnot dimenze (například Počítač)
- Pokud **nepoužíváte** portál Azure pro vytváření upozornění na metriku pro vybraný *pracovní prostor Analýzy protokolů*; pak uživatel musí ručně nejprve vytvořit explicitní pravidlo pro převod dat protokolu do metriky pomocí [Azure Monitor - Pravidla naplánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Při vytváření upozornění na metriku pro pracovní prostor Log Analytics prostřednictvím portálu Azure – odpovídající pravidlo pro převod dat protokolu na metriku prostřednictvím [Azure Monitoru – pravidla naplánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) se automaticky vytvoří na *pozadí, bez nutnosti zásahu nebo akce uživatele*. Upozornění na metriku pro vytváření protokolů pomocí jiných prostředků než portál Azure najdete v článku [Šablona prostředků pro metriky výstrahy pro protokoly](#resource-template-for-metric-alerts-for-logs) části na ukázkové prostředky vytvoření pravidla převodu na základě metriky na metriku pravidlo před vytvořením upozornění metriky – jinak nebudou žádná data pro metriku výstrahy na protokoly vytvořené.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Šablona prostředků pro upozornění metrik pro protokoly

Jak již bylo uvedeno dříve, proces pro vytváření upozornění metriky z protokolů je dva hroty:

1. Vytvoření pravidla pro extrahování metrik z podporovaných protokolů pomocí rozhraní ScheduledQueryRule API
2. Vytvoření upozornění na metriku extrahovanou z protokolu (v kroku 1) a pracovního prostoru Log Analytics jako cílového prostředku

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Upozornění metrik y pro protokoly se statickou prahovou hodnotou

Chcete-li dosáhnout stejné, můžete použít ukázkovou šablonu Azure Resource Manager níže – kde vytvoření výstrahy metriky statické prahové hodnoty závisí na úspěšném vytvoření pravidla pro extrahování metrikz protokolů prostřednictvím scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Řekněme, že výše json je uložen jako metricfromLogsAlertStatic.json - pak může být spojen s parametrem JSON soubor pro vytvoření šablony prostředků. Ukázkový parametr Soubor JSON je uveden níže:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Za předpokladu, že výše uvedený soubor parametru je uložen jako metricfromLogsAlertStatic.parameters.json; pak můžete vytvořit upozornění metriky pro protokoly pomocí [šablony prostředků pro vytvoření na webu Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Případně můžete použít příkaz Azure Powershell také níže:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Nebo použijte nasadit šablonu prostředků pomocí Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Upozornění metrik pro protokoly s dynamickými prahovými hodnotami

Chcete-li dosáhnout stejné, můžete použít ukázkovou šablonu Azure Resource Manager níže – kde vytvoření upozornění metriky dynamické prahové hodnoty závisí na úspěšném vytvoření pravidla pro extrahování metrikz protokolů prostřednictvím scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
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
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
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

Řekněme, že výše json je uložen jako metricfromLogsAlertDynamic.json - pak může být spojen s parametrem JSON soubor pro vytvoření šablony prostředků. Ukázkový parametr Soubor JSON je uveden níže:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
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

Za předpokladu, že výše uvedený soubor parametru je uložen jako metricfromLogsAlertDynamic.parameters.json; pak můžete vytvořit upozornění metriky pro protokoly pomocí [šablony prostředků pro vytvoření na webu Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Případně můžete použít příkaz Azure Powershell také níže:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Nebo použijte nasadit šablonu prostředků pomocí Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o upozorněních na [metriky](alerts-metric.md).
- Další informace o [výstrahách protokolů v Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Přečtěte si o [výstrahách v Azure](alerts-overview.md).
