---
title: Uložená vyhledávání v řešeních pro správu | Dokumenty společnosti Microsoft
description: Řešení pro správu obvykle zahrnují uložené dotazy protokolu k analýze dat shromážděných řešením. Tento článek popisuje, jak definovat log analytics uložená hledání v šabloně Správce prostředků.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/29/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61fc64e140af091b5ff3f631398daf901557791b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663024"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Přidání log Analytics uložená vyhledávání a výstrahy pro řešení správy (Preview)

> [!IMPORTANT]
> Jak [bylo oznámeno dříve](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), pracovní prostory analýzy protokolů vytvořené po *1.* **only** [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) [Azure Resource Manager Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) Zákazníci mohou snadno [přepnout své upřednostňované prostředky správy pravidel výstrah](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) pro starší pracovní prostory, aby využili pravidla ScheduledQueryRules Azure Monitor jako výchozí a získali mnoho nových [výhod,](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) jako je možnost používat nativní rutiny Prostředí PowerShell, delší doba zpětného pohledu v pravidlech, vytváření pravidel v samostatné skupině prostředků nebo předplatnéa a mnoho dalšího.

> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.

[Řešení pro správu](solutions.md) obvykle zahrnují [uložená hledání](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics k analýze dat shromážděných řešením. Mohou také definovat [výstrahy,](../../azure-monitor/platform/alerts-overview.md) které uživatele upozorní, nebo automaticky podniknou akci v reakci na kritický problém. Tento článek popisuje, jak definovat log Analytics uložená vyhledávání a výstrahy v [šabloně správa prostředků,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) aby mohly být zahrnuty do [řešení pro správu](solutions-creating.md).

> [!NOTE]
> Ukázky v tomto článku používají parametry a proměnné, které jsou povinné nebo společné pro řešení správy a popsané v [návrhu a sestavení řešení pro správu v Azure](solutions-creating.md)

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s tím, jak [vytvořit řešení pro správu](solutions-creating.md) a strukturu [šablony správce prostředků](../../azure-resource-manager/templates/template-syntax.md) a souboru řešení.


## <a name="log-analytics-workspace"></a>Pracovní prostor služby Log Analytics
Všechny prostředky v Log Analytics jsou obsaženy v [pracovním prostoru](../../azure-monitor/platform/manage-access.md). Jak je popsáno v [pracovním prostoru Log Analytics a účet automatizace](solutions.md#log-analytics-workspace-and-automation-account), pracovní prostor není součástí řešení pro správu, ale musí existovat před instalací řešení. Pokud není k dispozici, instalace řešení se nezdaří.

Název pracovního prostoru je v názvu každého prostředku Log Analytics. To se provádí v řešení s parametrem **pracovního prostoru** jako v následujícím příkladu prostředku Uložené vyhledávání.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Verze rozhraní API analýzy protokolů
Všechny prostředky Log Analytics definované v šabloně Správce prostředků mají **vlastnost apiVersion,** která definuje verzi rozhraní API, které by měl prostředek používat.

V následující tabulce je uvedena verze rozhraní API pro prostředek použitý v tomto příkladu.

| Typ prostředku | Verze rozhraní API | Dotaz |
|:---|:---|:---|
| uloženoVyhledávání | 2017-03-15-náhled | Událost &#124; kde EventLevelName == "Chyba"  |


## <a name="saved-searches"></a>Uložená hledání
Zahrňte [uložená hledání](../../azure-monitor/log-query/log-query-overview.md) do řešení, které uživatelům umožní dotazovat data shromážděná vaším řešením. Uložená hledání se zobrazí v části **Uložená hledání** na webu Azure Portal. Pro každou výstrahu je také vyžadováno uložené hledání.

[Log Analytics uložené vyhledávací](../../azure-monitor/log-query/log-query-overview.md) prostředky `Microsoft.OperationalInsights/workspaces/savedSearches` mají typ a mají následující strukturu. To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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

| Vlastnost | Popis |
|:--- |:--- |
| category | Kategorie pro uložené hledání.  Všechna uložená hledání ve stejném řešení často sdílejí jednu kategorii, takže jsou seskupena v konzole. |
| Displayname | Název, který se zobrazí pro uložené hledání na portálu. |
| query | Dotaz ke spuštění. |

> [!NOTE]
> Možná budete muset použít řídicí znaky v dotazu, pokud obsahuje znaky, které by mohly být interpretovány jako JSON. Pokud byl například váš dotaz **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, by měl být zapsán v souboru řešení jako **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Výstrahy
[Výstrahy protokolu Azure](../../azure-monitor/platform/alerts-unified-log.md) jsou vytvářeny pomocí pravidel výstrah Azure, která v pravidelných intervalech spouštějí zadané dotazy protokolu. Pokud výsledky dotazu odpovídají zadaným kritériím, je vytvořen záznam výstrahy a jedna nebo více akcí je spuštěno pomocí [skupin akcí](../../azure-monitor/platform/action-groups.md).

Pro uživatele, kteří rozšiřují výstrahy do Azure, se teď akce řídí ve skupinách akcí Azure. Když se pracovní prostor a jeho výstrahy rozšíří na Azure, můžete načíst nebo přidat akce pomocí [skupiny akcí – šablona Azure Resource Manager .](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)
Pravidla výstrah v řešení starší verze správy se skládají z následujících tří různých prostředků.

- **Uložené hledání.** Definuje hledání protokolu, který je spuštěn. Více pravidel výstrah může sdílet jedno uložené hledání.
- **Plán.** Definuje, jak často je spouštěno hledání protokolu. Každé pravidlo výstrahy má jeden a pouze jeden plán.
- **Výstražná akce.** Každé pravidlo výstrahy má jeden prostředek skupiny akcí nebo prostředek akce (starší verze) s typem **výstrahy,** která definuje podrobnosti výstrahy, jako jsou kritéria pro vytvoření záznamu výstrahy a závažnost výstrahy. [Prostředek skupiny akcí](../../azure-monitor/platform/action-groups.md) může mít seznam nakonfigurovaných akcí, které je třeba provést při spuštění výstrahy – například hlasové volání, SMS, e-mail, webhook, nástroj ITSM, runbook automatizace, aplikace logiky atd.

Uložené vyhledávací zdroje jsou popsány výše. Ostatní zdroje jsou popsány níže.

### <a name="schedule-resource"></a>Naplánovat zdroj

Uložené hledání může mít jeden nebo více plánů s každým plánem představujícím samostatné pravidlo výstrahy. Plán definuje, jak často je hledání spuštěno a časový interval, přes který jsou data načtena. Plánování zdrojů mají `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` typ a mají následující strukturu. To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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
Vlastnosti zdrojů plánu jsou popsány v následující tabulce.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| enabled       | Ano | Určuje, zda je výstraha při vytvoření povolena. |
| interval      | Ano | Jak často se dotaz spouští během několika minut. |
| queryTimeSpan | Ano | Doba v minutách, po kterou chcete vyhodnotit výsledky. |

Zdroj plánu by měl záviset na uloženém hledání, aby byl vytvořen před plánem.
> [!NOTE]
> Název plánu musí být v daném pracovním prostoru jedinečný. dva plány nemohou mít stejné ID, i když jsou přidruženy k různým uloženým vyhledáváním. Název pro všechna uložená hledání, plány a akce vytvořené pomocí rozhraní API analýzy protokolů musí být také malá písmena.

### <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesů k provedení, jako je například odeslání pošty nebo spuštění runbooku, nebo může definovat prahovou hodnotu, která určuje, kdy výsledky hledání odpovídají některým kritériím. Některé akce budou definovat tak, aby procesy jsou prováděny při splnění prahové hodnoty.
Akce lze definovat pomocí prostředku [skupiny akcí] nebo prostředku akce.

Existují dva typy prostředků akce určené **Type** vlastnost. Plán vyžaduje jednu akci **výstrahy,** která definuje podrobnosti pravidla výstrahy a jaké akce jsou prováděny při vytvoření výstrahy. Zdroje akcí mají `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`typ .

Akce výstrah mají následující strukturu. To zahrnuje běžné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů.

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

Vlastnosti prostředků akce výstrahy jsou popsány v následujících tabulkách.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| `type` | Ano | Typ akce.  Tady je **Výstraha** pro výstražné akce. |
| `name` | Ano | Zobrazovaný název výstrahy.  Toto je název, který se zobrazí v konzole pro pravidlo výstrahy. |
| `description` | Ne | Volitelný popis výstrahy. |
| `severity` | Ano | Závažnost záznamu výstrahy z následujících hodnot:<br><br> **Kritické**<br>**upozornění**<br>**Informační**

#### <a name="threshold"></a>Prahová hodnota
Tato část je povinná. Definuje vlastnosti prahové hodnoty výstrahy.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| `Operator` | Ano | Operátor pro porovnání z následujících hodnot:<br><br>**gt =<br>větší než lt = menší než** |
| `Value` | Ano | Hodnota porovnat výsledky. |

##### <a name="metricstrigger"></a>Aktivační událost metriky
Tato část je nepovinná. Zahrňte ji pro upozornění na měření metriky.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| `TriggerCondition` | Ano | Určuje, zda je prahová hodnota pro celkový počet porušení nebo po sobě jdoucích porušení z následujících hodnot:<br><br>**Celkem<br>po sobě jdoucích** |
| `Operator` | Ano | Operátor pro porovnání z následujících hodnot:<br><br>**gt =<br>větší než lt = menší než** |
| `Value` | Ano | Počet, kolikrát musí být splněna kritéria pro aktivaci výstrahy. |


#### <a name="throttling"></a>Throttling
Tato část je nepovinná. Do této části zahrňte, pokud chcete po určitou dobu po vytvoření výstrahy potlačit výstrahy ze stejného pravidla.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| DurationInMinutes | Ano, pokud je zahrnut prvek omezení | Počet minut k potlačení výstrah po vytvoření jednoho ze stejného pravidla výstrahy. |

#### <a name="azure-action-group"></a>Skupina akcí Azure
Všechny výstrahy v Azure, použijte skupinu akcí jako výchozí mechanismus pro zpracování akcí. Pomocí skupiny akcí můžete zadat své akce jednou a pak přidružit skupinu akcí k více výstrahám – v rámci Azure. Bez nutnosti, opakovaně deklarovat stejné akce znovu a znovu. Skupiny akcí podporují více akcí – včetně e-mailu, SMS, hlasového volání, připojení ITSM, runbooku automation, identifikátoru URI webhooku a dalších.

Pro uživatele, kteří rozšířili své výstrahy do Azure – plán by měl mít nyní podrobnosti skupiny akcí předány spolu s prahovou hodnotou, aby bylo možné vytvořit výstrahu. Podrobnosti o e-mailu, adresy URL webhooku, podrobnosti o automatizaci sady Runbook a další akce musí být před vytvořením výstrahy definovány na straně skupiny akcí. jeden můžete vytvořit [skupinu akcí z Azure Monitor](../../azure-monitor/platform/action-groups.md) na portálu nebo použít [skupinu akcí - šablona prostředků](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Oznámení AzNs | Ano | ID prostředku skupiny akcí Azure, které mají být přidruženy k výstrahě pro přijetí nezbytných akcí při splnění kritérií výstrahy. |
| Vlastní emailsubject | Ne | Vlastní řádek předmětu e-mailu odeslaného na všechny adresy zadané v přidružené skupině akcí. |
| CustomWebhookPayload | Ne | Přizpůsobená datová část, která má být odeslána všem koncovým bodům webhooku definovaným v přidružené skupině akcí. Formát závisí na tom, co webhook očekává a měl by být platný serializovaný JSON. |

## <a name="sample"></a>Ukázka

Následuje ukázka řešení, která obsahuje následující zdroje:

- Uložené hledání
- Plán
- Skupina akcí

Ukázka používá standardní [parametry řešení]( solutions-solution-file.md#parameters) proměnné, které by se běžně používají v řešení na rozdíl od hardcoding hodnoty v definicích prostředků.

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

Následující soubor parametrů obsahuje ukázkové hodnoty pro toto řešení.

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

## <a name="next-steps"></a>Další kroky
* [Přidejte zobrazení](solutions-resources-views.md) do řešení správy.
* Přidejte do svého řešení pro správu [runbooky automation a další prostředky.](solutions-resources-automation.md)
