---
title: Vytváření výstrah metrik pro protokoly v Azure Monitor
description: Kurz týkající se vytváření upozornění na metriky téměř v reálném čase u oblíbených dat Log Analytics.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 06/17/2020
ms.subservice: alerts
ms.openlocfilehash: 23ebb513e55e6b61f608354ae1cb3bf3864a54ae
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108843"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Vytváření upozornění na metriky pro protokoly v Azure Monitor

## <a name="overview"></a>Přehled

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor podporuje [Typ výstrahy metriky](./alerts-metric-near-real-time.md) , který má oproti [klasickým výstrahám](./alerts-classic-portal.md)výhody. K dispozici jsou metriky pro [velký seznam služeb Azure](./metrics-supported.md). Tento článek vysvětluje použití podmnožiny (tj.) pro prostředek – `Microsoft.OperationalInsights/workspaces` .

Výstrahy metriky můžete použít u oblíbených protokolů Log Analytics extrahovaných jako metriky v rámci metriky od protokolů, včetně prostředků v Azure nebo v místním prostředí. Podporovaná řešení Log Analytics jsou uvedená níže:

- [Čítače výkonu](./data-sources-performance-counters.md) pro počítače se systémem Windows & Linux
- [Záznamy prezenčního signálu pro Agent Health](../insights/solution-agenthealth.md)
- [Aktualizovat záznamy správy](../../automation/update-management/update-mgmt-overview.md)
- Protokoly [dat událostí](./data-sources-windows-events.md)

Existuje mnoho výhod používání **Upozornění metrik pro protokoly** přes [výstrahy protokolu](./alerts-log.md) založené na dotazech v Azure. Některé z nich jsou uvedeny níže:

- Výstrahy metriky nabízejí možnosti monitorování téměř v reálném čase a výstrahy metriky pro protokoly rozvětvení dat ze zdroje protokolů, aby bylo zajištěno stejné.
- Upozornění na metriku jsou stavová oznámení, jenom když se aktivuje výstraha a když se výstraha vyřeší; na rozdíl od výstrah protokolu, které jsou bezstavové, a udržujte je v každém intervalu, pokud je splněna podmínka výstrahy.
- Výstrahy metriky pro protokol poskytují více dimenzí, což umožňuje filtrování na konkrétní hodnoty jako počítače, typ operačního systému atd. jednodušší; bez nutnosti dotazování Penning v analýzách.

> [!NOTE]
> Specifická metrika a/nebo dimenze se zobrazí jenom v případě, že data pro ni existují ve zvoleném období. Tyto metriky jsou k dispozici pro zákazníky s pracovními prostory Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metriky a dimenze podporované pro protokoly

 Výstrahy metrik podporují upozorňování na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Zobrazí se úplný seznam metrik podporovaných pro protokoly z [log Analyticsch pracovních prostorů](./metrics-supported.md#microsoftoperationalinsightsworkspaces) . v podporovaných řešeních.

> [!NOTE]
> Chcete-li zobrazit podporovanou metriku extrahovanou z Log Analytics pracovního prostoru prostřednictvím [metriky Azure monitor](./metrics-charts.md), je nutné vytvořit výstrahu metriky pro protokol v této konkrétní metrikě. Dimenze zvolené v upozornění metriky pro protokoly – budou zobrazeny pouze pro průzkum prostřednictvím metrik Azure Monitor.

## <a name="creating-metric-alert-for-log-analytics"></a>Vytváření upozornění na metriku pro Log Analytics

Data metriky z oblíbených protokolů se před zpracováním v Log Analytics do metriky Azure Monitor. Díky tomu mohou uživatelé využívat možnosti Metrikové platformy i výstrahy metriky – včetně výstrah s frekvencí nižší než 1 minuta.
Níže jsou uvedené prostředky pro vytvoření výstrahy metriky pro protokoly.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Předpoklady pro upozornění na metriky pro protokoly

Předtím, než bude metrika pro protokoly shromážděná na Log Analytics fungovat, musí být nastavená a dostupná:

1. **Pracovní prostor Active Log Analytics**: musí být přítomen platný a aktivní Log Analytics pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../learn/quick-create-workspace.md).
2. **Agent je nakonfigurovaný pro Log Analytics pracovní prostor**: Agent musí být nakonfigurovaný pro virtuální počítače Azure (a/nebo), aby odesílal data do pracovního prostoru Log Analytics, který jste používali v předchozím kroku. Další informace najdete v tématu [Log Analytics – přehled agenta](./agents-overview.md).
3. **Jsou nainstalována podporovaná řešení Log Analytics**: Log Analytics řešení by mělo být nakonfigurované a odesílat data do Log Analytics řešení podporovaná v pracovním prostoru jsou [čítače výkonu pro systém Windows & Linux](./data-sources-performance-counters.md), [záznamy prezenčních signálů pro Agent Health](../insights/solution-agenthealth.md), [správu aktualizací](../../automation/update-management/update-mgmt-overview.md)a [data událostí](./data-sources-windows-events.md).
4. **Log Analytics řešení nakonfigurovaných pro odesílání protokolů**: Log Analytics řešení by mělo mít požadované protokoly/data odpovídající [metrikám podporovaným pro Log Analytics pracovní prostory](./metrics-supported.md#microsoftoperationalinsightsworkspaces) . Například pro čítač *% dostupné paměti* je třeba nejprve nakonfigurovat v řešení [čítače výkonu](./data-sources-performance-counters.md) .

## <a name="configuring-metric-alert-for-logs"></a>Konfigurace upozornění na metriky pro protokoly

 Výstrahy metriky se dají vytvářet a spravovat pomocí Azure Portal, Správce prostředků šablon, REST API, PowerShellu a rozhraní příkazového řádku Azure CLI. Vzhledem k tomu, že výstrahy metriky pro protokoly, je variantou výstrahy metriky – po dokončení požadovaných součástí se dá pro zadaný Log Analytics pracovní prostor vytvořit upozornění na metriky pro protokoly. Všechny charakteristiky a funkce upozornění na [metriky](./alerts-metric-near-real-time.md) se budou uplatňovat i na výstrahy metriky pro protokoly. včetně schématu datové části, platných omezení kvót a fakturované ceny.

Podrobné informace a ukázky – viz [vytváření a Správa upozornění na metriky](./alerts-metric.md). Konkrétně pro výstrahy metrik pro protokoly – postupujte podle pokynů pro správu výstrah metrik a zajistěte následující:

- Cíl pro výstrahu metriky je platný *pracovní prostor Log Analytics* .
- Signál vybraný pro výstrahu metriky pro vybraný *Log Analytics pracovní prostor* je typu **metrika** .
- Filtrovat konkrétní podmínky nebo prostředek pomocí filtrů dimenzí; metriky pro protokoly jsou multidimenzionální
- Při konfiguraci *logiky signálu*se dá vytvořit jedna výstraha pro rozsah více hodnot dimenze (například počítač).
- Pokud **nepoužíváte** Azure Portal k vytvoření upozornění na metriku pro vybraný *pracovní prostor Log Analytics*; Uživatel pak musí ručně vytvořit explicitní pravidlo pro převod dat protokolu do metriky pomocí [naplánovaných pravidel dotazu Azure monitor](/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Při vytváření upozornění na metriku pro Log Analytics pracovní prostor prostřednictvím pravidla Azure Portal, které slouží k převodu dat protokolu do metriky prostřednictvím [Azure monitor-plánovaná pravidla dotazování](/rest/api/monitor/scheduledqueryrules) se automaticky vytvoří na pozadí *bez nutnosti zásahu uživatele nebo akce*. Upozornění na metriku pro vytváření protokolů pomocí jiných prostředků než Azure Portal najdete v části [Šablona prostředků pro výstrahy metriky pro protokoly](#resource-template-for-metric-alerts-for-logs) o vzorech, které se týkají vytvoření protokolu založeného na ScheduledQueryRule do pravidla převodu metriky před vytvořením upozornění na metriku – jinak nebudou pro výstrahu metriky vytvořené protokoly k dispozici žádná data.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Šablona prostředků pro výstrahy metriky pro protokoly

Jak bylo uvedeno dříve, proces pro vytváření výstrah metrik z protokolů je ze dvou kombinovaných:

1. Vytvoření pravidla pro extrakci metrik z podporovaných protokolů pomocí rozhraní scheduledQueryRule API
2. Vytvoření upozornění na metriku pro metriku extrahovanou z log (v Krok 1) a Log Analytics pracovního prostoru jako cílový prostředek

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Výstrahy metriky pro protokoly se statickou prahovou hodnotou

Aby bylo možné dosáhnout stejné hodnoty, může se použít vzor Azure Resource Manager níže, kde se vytvoří výstraha metriky se statickou prahovou hodnotou, která závisí na úspěšném vytvoření pravidla pro extrakci metrik z protokolů prostřednictvím scheduledQueryRule.

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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
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

Řekněme, že výše uvedený kód JSON je uložený jako metricfromLogsAlertStatic.jsna a pak může být spojený s parametrem souboru JSON pro vytvoření založeného na šablonách prostředků. Ukázkový parametr soubor JSON je uveden níže:

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

Za předpokladu, že se výše uvedený soubor parametrů uloží jako metricfromLogsAlertStatic.parameters.js. pak může jeden vytvořit upozornění metriky pro protokoly pomocí [šablony prostředků pro vytvoření v Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Případně může použít i příkaz Azure PowerShell níže:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Nebo použijte šablonu prostředků nasadit pomocí Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Výstrahy metriky pro protokoly s dynamickými mezními hodnotami

Aby bylo možné dosáhnout stejného počtu, může použít následující šablonu Azure Resource Manager níže, kde vytváření výstrah metriky dynamických prahových hodnot závisí na úspěšném vytvoření pravidla pro extrakci metrik z protokolů prostřednictvím scheduledQueryRule.

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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
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

Řekněme, že výše uvedený kód JSON je uložený jako metricfromLogsAlertDynamic.jsna a pak může být spojený s parametrem souboru JSON pro vytvoření založeného na šablonách prostředků. Ukázkový parametr soubor JSON je uveden níže:

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

Za předpokladu, že se výše uvedený soubor parametrů uloží jako metricfromLogsAlertDynamic.parameters.js. pak může jeden vytvořit upozornění metriky pro protokoly pomocí [šablony prostředků pro vytvoření v Azure Portal](../../azure-resource-manager/templates/deploy-portal.md).

Případně může použít i příkaz Azure PowerShell níže:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Nebo použijte šablonu prostředků nasadit pomocí Azure CLI:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [výstrahách metriky](alerts-metric.md).
- Přečtěte si informace o [upozorněních protokolu v Azure](./alerts-unified-log.md).
- Přečtěte si o [výstrahách v Azure](alerts-overview.md).