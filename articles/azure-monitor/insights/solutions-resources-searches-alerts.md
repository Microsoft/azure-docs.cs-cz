---
title: Uložená hledání v řešeních pro správu | Microsoft Docs
description: Řešení pro správu obvykle zahrnují uložená hledání v Log Analytics k analýze dat shromažďovaných řešením. Můžou také definovat výstrahy upozorňující uživatele nebo automaticky provádět akce v reakci na kritický problém. Tento článek popisuje, jak definovat Log Analytics uložených hledání v šabloně Správce prostředků, aby bylo možné je zahrnout do řešení pro správu.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2e32fb57a5ee34da8c342649cc1740d111723ec
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662910"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Přidání Log Analytics uložených hledání a upozornění do řešení pro správu (Preview)

> [!IMPORTANT]
> Jak [jsme oznámili dřív](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), pracovní prostory Log Analytics vytvořené od *1. června 2019* – budou moct spravovat pravidla výstrah **jenom** pomocí [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Azure scheduledQueryRules, [Azure Resource Manager šablony](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) a PowerShellu. [ rutina](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell). Zákazníci si můžou snadno [Přepnout do preferovaných způsobů správy pravidel výstrah](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) pro starší pracovní prostory a využít Azure monitor scheduledQueryRules jako výchozí a získat spoustu [nových výhod](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) , jako je možnost použití nativních rutin PowerShellu, zvýšené lookback časové období v pravidlech, vytváření pravidel v samostatné skupině prostředků nebo předplatném a mnohem víc.

> [!NOTE]
> Toto je předběžná dokumentace k vytváření řešení pro správu, která jsou momentálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.

[Řešení pro správu](solutions.md) obvykle zahrnují [uložená hledání](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics k analýze dat shromažďovaných řešením. Můžou také definovat [výstrahy](../../azure-monitor/platform/alerts-overview.md) upozorňující uživatele nebo automaticky provádět akce v reakci na kritický problém. Tento článek popisuje, jak definovat Log Analytics uložených hledání a upozornění v [šabloně správy prostředků](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) , aby je bylo možné zahrnout do [řešení pro správu](solutions-creating.md).

> [!NOTE]
> V ukázkách v tomto článku se používají parametry a proměnné, které jsou buď vyžadované, nebo běžné pro řešení pro správu, popsaná v článku [Návrh a sestavení řešení pro správu v Azure](solutions-creating.md) .

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že už jste obeznámeni s tím, jak [vytvořit řešení pro správu](solutions-creating.md) a strukturu [Správce prostředků šablony](../../azure-resource-manager/resource-group-authoring-templates.md) a souboru řešení.


## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Všechny prostředky v Log Analytics jsou obsaženy v [pracovním prostoru](../../azure-monitor/platform/manage-access.md). Jak je popsáno v [Log Analytics pracovní prostor a účet Automation](solutions.md#log-analytics-workspace-and-automation-account), pracovní prostor není zahrnutý do řešení pro správu, ale před instalací řešení musí existovat. Pokud není k dispozici, řešení se nepodařilo nainstalovat.

Název pracovního prostoru je v názvu každého prostředku Log Analytics. To se provádí v řešení s parametrem **pracovního prostoru** , jako v následujícím příkladu elementu savedsearch prostředku.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Verze rozhraní API pro Log Analytics
Všechny prostředky Log Analytics definované v šabloně Správce prostředků mají vlastnost **apiVersion** , která definuje verzi rozhraní API, kterou by měl prostředek použít.

Následující tabulka uvádí verzi rozhraní API pro prostředek použitý v tomto příkladu.

| Typ prostředku | Verze API | Dotaz |
|:---|:---|:---|
| savedSearches | 2017-03-15 – Preview | Událost &#124; , kde EventLevelName = = "Error"  |


## <a name="saved-searches"></a>Uložená hledání
Zahrnutí [uložených hledání](../../azure-monitor/log-query/log-query-overview.md) do řešení umožní uživatelům dotazovat se na data shromážděná řešením. Uložená hledání se zobrazí v části **uložená hledání** v Azure Portal. Pro každou výstrahu je také vyžadováno uložené hledání.

[Log Analytics uložené prostředky hledání](../../azure-monitor/log-query/log-query-overview.md) mají typ `Microsoft.OperationalInsights/workspaces/savedSearches` a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Každá vlastnost uloženého hledání je popsána v následující tabulce.

| Vlastnost | description |
|:--- |:--- |
| category | Kategorie uloženého hledání  Všechna uložená hledání ve stejném řešení budou často sdílet jednu kategorii, aby byly seskupeny dohromady v konzole nástroje. |
| displayName | Název, který se má zobrazit u uloženého hledání na portálu. |
| query | Dotaz, který se má spustit |

> [!NOTE]
> V dotazu může být nutné použít řídicí znaky, pokud obsahuje znaky, které by mohly být interpretovány jako JSON. Například pokud byl dotaz **AzureActivity | Operace: "Microsoft. COMPUTE/virtualMachines/Write"** , měl by se zapsat do souboru řešení jako **AzureActivity | OperationName\":/Microsoft. COMPUTE/\"virtualMachines/Write**.

## <a name="alerts"></a>Upozornění
[Výstrahy protokolu Azure](../../azure-monitor/platform/alerts-unified-log.md) se vytvářejí pomocí pravidel upozornění Azure, která v pravidelných intervalech spouštějí zadané dotazy protokolu. Pokud výsledky dotazu odpovídají zadaným kritériím, vytvoří se záznam výstrahy a jednou nebo více akcí se spustí pomocí [skupin akcí](../../azure-monitor/platform/action-groups.md).

Pro uživatele, kteří rozšíření upozornění do Azure jsou teď akce provádí na skupiny akcí Azure. Když se pracovní prostor a jeho výstrahy rozšíří na Azure, můžete načíst nebo přidat akce pomocí [šablony Azure Resource Manager skupiny akcí](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).
Pravidla výstrah ve starší verzi řešení pro správu se skládají z následujících tří různých prostředků.

- **Hledání bylo uloženo.** Definuje prohledávání protokolu, které je spuštěno. Více pravidel výstrah může sdílet jedno uložené hledání.
- **CXL.** Definuje, jak často se spouští prohledávání protokolů. Každé pravidlo výstrahy má jeden a jenom jeden plán.
- **Akce výstrahy** Každé pravidlo výstrahy má jeden prostředek skupiny akcí nebo prostředek akce (starší verze) s typem **výstrahy** , která definuje podrobnosti výstrahy, například kritéria pro vytvoření záznamu výstrahy a závažnost výstrahy. Prostředek [skupiny akcí](../../azure-monitor/platform/action-groups.md) může obsahovat seznam nakonfigurovaných akcí, které se mají provést při spuštění výstrahy – například hlasové volání, SMS, e-mail, Webhook, nástroj ITSM, Automation Runbook, aplikace logiky atd.

Uložené prostředky hledání jsou popsány výše. Další materiály jsou popsány níže.

### <a name="schedule-resource"></a>Plánování prostředku

Uložené hledání může mít jeden nebo více plánů s časovým plánem, který představuje samostatné pravidlo výstrahy. Plán definuje, jak často se spouští hledání, a časový interval, po který se data načítají. Prostředky plánu mají typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
Vlastnosti pro prostředky plánu jsou popsány v následující tabulce.

| Název elementu | Požaduje se | description |
|:--|:--|:--|
| enabled       | Ano | Určuje, zda je výstraha povolena, když je vytvořena. |
| interval      | Ano | Jak často se dotaz spouští v řádu minut. |
| queryTimeSpan | Ano | Doba v minutách, po které se mají vyhodnocovat výsledky. |

Prostředek plánu by měl záviset na uloženém hledání tak, aby byl vytvořen před plánem.
> [!NOTE]
> Název plánu musí být v daném pracovním prostoru jedinečný; dva plány nemohou mít stejné ID i v případě, že jsou přidruženy k různým uloženým hledáním. Také název všech uložených hledání, plánů a akcí vytvořených pomocí rozhraní Log Analytics API musí být malými písmeny.

### <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesy provádět například poslání e-mailu nebo spuštění sady runbook nebo ji může definovat prahové hodnoty, která určuje, kdy výsledky hledání odpovídají kritérií. Některé akce budou definovat i tak, aby procesy, které jsou prováděny při splnění prahovou hodnotu.
Akce lze definovat pomocí prostředku prostředků nebo prostředku akce [Action Group].

Vlastnost **Type** obsahuje dva typy prostředku akce. Plán vyžaduje jednu akci s výstrahou, která definuje Podrobnosti pravidla výstrahy a akce, které se mají provést při vytvoření výstrahy. Prostředky akce mají typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.

Akce výstrahy mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

Vlastnosti pro prostředky akce výstrah jsou popsány v následujících tabulkách.

| Název elementu | Požaduje se | description |
|:--|:--|:--|
| type | Ano | Typ akce.  Toto **Upozornění se upozorní** na akce výstrah. |
| name | Ano | Zobrazovaný název výstrahy.  Toto je název, který se zobrazí v konzole pro pravidlo výstrahy. |
| description | Ne | Volitelný popis výstrahy |
| severity | Ano | Závažnost záznamu výstrahy z následujících hodnot:<br><br> **kritické**<br>**Upozornění**<br>**informativní**


#### <a name="threshold"></a>Prahová hodnota
Tato část je povinná. Definuje vlastnosti prahové hodnoty pro výstrahu.

| Název elementu | Požaduje se | description |
|:--|:--|:--|
| Operator | Ano | Operátor pro porovnání z následujících hodnot:<br><br>**gt = větší než<br>lt = menší než** |
| Value | Ano | Hodnota pro porovnání výsledků. |

##### <a name="metricstrigger"></a>MetricsTrigger
Tato část je volitelná. Zahrňte pro upozornění měření metriky.

| Název elementu | Požaduje se | description |
|:--|:--|:--|
| TriggerCondition | Ano | Určuje, zda je prahová hodnota pro celkový počet porušení nebo po sobě jdoucí porušení z následujících hodnot:<br><br>**Celkový<br>počet po sobě jdoucích** |
| Operator | Ano | Operátor pro porovnání z následujících hodnot:<br><br>**gt = větší než<br>lt = menší než** |
| Value | Ano | Počet, kolikrát musí být splněna kritéria pro aktivaci výstrahy. |


#### <a name="throttling"></a>Omezování
Tato část je volitelná. Tuto část uveďte, pokud chcete potlačit výstrahy ze stejného pravidla po určitou dobu po vytvoření výstrahy.

| Název elementu | Požaduje se | description |
|:--|:--|:--|
| DurationInMinutes | Ano, pokud je zahrnutý element omezování | Počet minut, po které se mají potlačit výstrahy po jednom ze stejného pravidla výstrahy. |

#### <a name="azure-action-group"></a>Skupina akcí Azure
Všechna upozornění v Azure, použijte skupiny akcí jako výchozího mechanismu pro zpracování akce. Pomocí skupiny akcí můžete zadat vaše akce jednou a přidružte skupinu akcí více výstrah – napříč Azure. Bez nutnosti opakovaně opětovně deklarovat stejné akce. Skupiny akcí podporovat více akcí – včetně e-mailu, SMS, hlasovým hovorem, připojení ITSM, Runbook služby Automation, Webhooku URI a dalších.

Pro uživatele, kteří mají svá upozornění rozšíří do Azure – plánu teď měli mít podrobnosti skupiny akcí předána spolu s prahovou hodnotou, bude moct vytvořit výstrahu. Podrobnosti e-mailu, adresy URL Webhooku, podrobnosti automatizace sady Runbook a další akce musí být před vytvořením výstrahy nejprve definovány na straně skupiny akcí; jedna z nich může vytvořit [skupinu akcí z Azure monitor](../../azure-monitor/platform/action-groups.md) na portálu nebo použít [skupinu akcí – šablona prostředků](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Název elementu | Požaduje se | description |
|:--|:--|:--|
| AzNsNotification | Ano | ID prostředku skupiny akcí Azure, která má být přidružena k výstraze pro provedení nezbytných akcí při splnění kritérií výstrahy. |
| CustomEmailSubject | Ne | Vlastní řádek předmětu e-mailu odeslaný všem adresám uvedeným v přidružené skupině akcí |
| CustomWebhookPayload | Ne | Přizpůsobená datová část, která se má odeslat do všech koncových bodů webhooků definovaných v přidružené skupině akcí Formát závisí na tom, co očekává Webhook, a měl by být platný serializovaný JSON. |

## <a name="sample"></a>Ukázka

Následuje ukázka řešení, které obsahuje následující zdroje:

- Uložené výsledky hledání
- Plán
- Skupina akcí

Ukázka používá [standardní proměnné parametrů řešení]( solutions-solution-file.md#parameters) , které by se běžně používaly v řešení, a to na rozdíl od hodnot zakódujeme v definicích prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

Následující soubor parametrů poskytuje ukázkové hodnoty pro toto řešení.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>Další postup
* [Přidejte zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidejte Runbooky Automation a další prostředky](solutions-resources-automation.md) do řešení pro správu.
