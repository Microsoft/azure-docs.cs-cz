---
title: Uložení hledání a výstrahy v řešení pro správu | Dokumentace Microsoftu
description: Řešení pro správu obvykle zahrnují uložené výsledky hledání v Log Analytics k analýze data shromážděná tímto řešením.  Se může také definovat výstrahy upozorní uživatele, nebo automaticky provést akce v reakci na kritický problém.  Tento článek popisuje, jak definovat uložit hledání a výstrahy v šabloně Resource Manageru, proto mohou být součástí řešení pro správu Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/18/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 74987d09a9b8979d3c3596c87764f8f3bd4b5795
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846661"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Přidání Log Analytics uložené hledání a výstrahy do řešení pro správu (Preview)

> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.   


[Řešení pro správu](solutions.md) by měl obvykle zahrnovat [uložená hledání](../../azure-monitor/log-query/log-query-overview.md) v Log Analytics k analýze data shromážděná tímto řešením.  Můžou také definovat [výstrahy](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) upozornit uživatele, nebo automaticky provést akce v reakci na kritický problém.  Tento článek popisuje, jak definovat uložené výsledky hledání Log Analytics a upozornění [šablony Resource Manageru](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) tak mohou být zahrnuty v [řešení pro správu](solutions-creating.md).

> [!NOTE]
> Ukázky v tomto článku použijte parametry a proměnné, které jsou povinné nebo společné pro řešení pro správu a jsou popsány v [návrh a sestavení řešení pro správu v Azure](solutions-creating.md)  

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s postupy [vytvořte řešení pro správu](solutions-creating.md) a strukturu [šablony Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) a soubor řešení.


## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Všechny prostředky ve službě Log Analytics jsou obsaženy v [pracovní prostor](../../log-analytics/log-analytics-manage-access.md).  Jak je popsáno v [pracovní prostor Log Analytics a účet Automation](solutions.md#log-analytics-workspace-and-automation-account), pracovní prostor není zahrnutý v řešení pro správu, ale musí existovat před instalací řešení.  Pokud není k dispozici, řešení instalace se nezdaří.

Název pracovního prostoru je název každého prostředku Log Analytics.  To se provádí v řešení se **pracovní prostor** parametr jako v následujícím příkladu elementu SavedSearch prostředku.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Verze rozhraní API pro analýzu protokolů
Všechny prostředky Log Analytics, které jsou definované v šabloně Resource Manageru, mít vlastnost **apiVersion** , který určuje verzi rozhraní API prostředku by měl používat.   

Následující tabulka uvádí verze rozhraní API pro prostředek, který používá v tomto příkladu.

| Typ prostředku | Verze API | Dotaz |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Událost &#124; kde EventLevelName == "Chyba"  |


## <a name="saved-searches"></a>Uložená hledání
Zahrnout [uložená hledání](../../azure-monitor/log-query/log-query-overview.md) v řešení, které umožňují uživatelům provádět dotazy na data shromážděná z vašeho řešení.  Uložená hledání se zobrazí v rámci **uložená hledání** na webu Azure Portal.  Uložené výsledky hledání je také nutný pro každou výstrahu.   

[Uložené výsledky hledání log Analytics](../../azure-monitor/log-query/log-query-overview.md) prostředky mají typ `Microsoft.OperationalInsights/workspaces/savedSearches` a mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 

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



Uložené výsledky hledání se jednotlivé vlastnosti je popsané v následující tabulce. 

| Vlastnost | Popis |
|:--- |:--- |
| category | Kategorie pro uložené výsledky hledání.  Všechny uložené výsledky hledání ve stejném řešení často sdílí jednu kategorii, takže jsou seskupené dohromady v konzole. |
| DisplayName | Název má být zobrazen pro uložené výsledky hledání na portálu. |
| query | Spustit dotaz. |

> [!NOTE]
> Budete muset použít řídicí znaky v dotazu, pokud obsahuje znaky, které může být interpretován jako JSON.  Například, pokud byl váš dotaz **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, by měl být zadaný v souboru řešení, které **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Výstrahy
[Upozornění Azure Log](../../monitoring-and-diagnostics/monitor-alerts-unified-log.md) jsou vytvořené pravidly upozornění Azure, které v pravidelných intervalech spouští dotazy zadaný protokol.  Pokud výsledky dotazu splňují zadaná kritéria, se vytvoří záznam o upozornění a jednu nebo více akcí se spouštějí pomocí [skupiny akcí](../../monitoring-and-diagnostics/monitoring-action-groups.md).  

> [!NOTE]
> Od 14. května 2018, všechna upozornění v instanci pracovního prostoru Log Analytics veřejného cloudu Azure začal rozšířit do Azure. Další informace najdete v tématu [upozornění rozšířit do Azure](../../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou teď akce provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [skupiny akcí – šablony Azure Resource Manageru](../../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

Pravidla výstrah v rámci řešení pro správu se skládá z následujících tří různých prostředků.

- **Uložené výsledky hledání.**  Definuje prohledávání protokolu, který se spouští.  Více pravidel upozornění můžete sdílet jeden uložené výsledky hledání.
- **Plán.**  Definuje, jak často je spustit prohledávání protokolů.  Každé pravidlo výstrahy, má jeden a pouze jeden plán.
- **Akce upozornění.**  Každé pravidlo výstrahy má jeden prostředek skupiny akcí nebo akce prostředek (starší verze) s typem **výstraha** , který definuje podrobnosti výstrahy, jako jsou kritéria pro vytvoření záznam o upozornění a závažnost výstrahy. [Skupina akcí](../../monitoring-and-diagnostics/monitoring-action-groups.md) prostředek může mít seznam nakonfigurovaných akcí má provést, když se aktivuje upozornění – například hlasový hovor, SMS, e-mailu, webhooku, nástroji ITSM, runbook služby automation, aplikace logiky, atd.
 
Zdroj akce (starší verze) bude volitelně definovala odpověď e-mailu a sady runbook.
- **Akce Webhooku (starší verze).**  Pokud pravidlo upozornění volá webhook, pak vyžaduje prostředek další akce s typem **Webhooku**.    

Uložené výsledky hledání, které prostředky jsou popsané výše.  Další prostředky jsou popsané níže.


### <a name="schedule-resource"></a>Plán prostředku

Uložené výsledky hledání může mít jeden nebo více plánů s každý plán reprezentující samostatné pravidlo upozornění. Plán definuje, jak často se hledání spuštění a časový interval nad tím, které jsou data načtena.  Plánovat prostředky mají typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 


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



Vlastnosti pro plán prostředky jsou popsány v následující tabulce.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| povoleno       | Ano | Určuje, zda je povoleno výstrahu, když se vytvoří. |
| interval      | Ano | Jak často dotaz spustí během několika minut. |
| QueryTimeSpan | Ano | Časový interval v minutách, přes které se má vyhodnotit výsledky. |

Plán prostředku by měl záviset na uložené výsledky hledání tak, aby se vytvořené před plán.

> [!NOTE]
> Název plánu musí být jedinečný v daném pracovním prostoru; dva plány nemůže mít stejné ID, i když jsou přidruženy k jiné uložená hledání. Název pro všechny uložené výsledky hledání, plány a akce, které jsou vytvořené pomocí rozhraní API pro analýzu protokolů také musí být malými písmeny.


### <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesy provádět například poslání e-mailu nebo spuštění sady runbook nebo ji může definovat prahové hodnoty, která určuje, kdy výsledky hledání odpovídají kritérií.  Některé akce budou definovat i tak, aby procesy, které jsou prováděny při splnění prahovou hodnotu.

Akce lze definovat pomocí [skupiny akcí] prostředku nebo akce.

> [!NOTE]
> Od 14. května 2018, všechna upozornění v instanci pracovního prostoru Log Analytics veřejného cloudu Azure začal automaticky rozšíří do Azure. Další informace najdete v tématu [upozornění rozšířit do Azure](../../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou teď akce provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [skupiny akcí – šablony Azure Resource Manageru](../../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


Existují dva typy akcí prostředek určený souborem **typ** vlastnost.  Plán vyžaduje jednu **výstraha** akce, která definuje podrobnosti pravidlo upozornění a jaké akce se udělají, když se vytvoří výstraha. Akce prostředky mají typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Akce upozornění mají následující strukturu.  To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů. 


```
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

Vlastnosti pro akce upozornění prostředky jsou popsány v následujících tabulkách.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Typ | Ano | Typ akce.  Toto je **výstraha** pro akce výstrah. |
| Název | Ano | Zobrazovaný název výstrahy.  Toto je název, který se zobrazí v konzole pro pravidla upozornění. |
| Popis | Ne | Popis výstrahy. |
| Severity | Ano | Závažnost výstrahy záznam z následujících hodnot:<br><br> **Kritická**<br>**Upozornění**<br>**Informační**


#### <a name="threshold"></a>Prahová hodnota
Tento oddíl je povinný.  Definuje vlastnosti prahové hodnoty pro výstrahu.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Operátor | Ano | Operátor porovnání z následujících hodnot:<br><br>**gt = je větší než<br>lt = menší než** |
| Hodnota | Ano | Hodnota určená k porovnání výsledků. |

##### <a name="metricstrigger"></a>MetricsTrigger
Tato část je nepovinná.  Zahrňte pro oznámení na základě měření metriky.

> [!NOTE]
> Upozornění metriky měření jsou aktuálně ve verzi public preview. 

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| TriggerCondition | Ano | Určuje, zda je prahová hodnota pro celkový počet porušení nebo po sobě jdoucí porušení z následujících hodnot:<br><br>**Celkový počet<br>po sobě jdoucích** |
| Operátor | Ano | Operátor porovnání z následujících hodnot:<br><br>**gt = je větší než<br>lt = menší než** |
| Hodnota | Ano | Počet pokusů, které musí být splněna kritéria pro aktivaci upozornění. |


#### <a name="throttling"></a>Throttling
Tato část je nepovinná.  Zahrňte v této části, pokud chcete potlačit výstrahy ze stejného pravidla pro určitou část času, po vytvoření výstrahy.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Doba trvání v minutách | Ano, pokud omezování zahrnuté – element | Kolik minut se má potlačit výstrahy, když je vytvořen ze stejné pravidlo upozornění. |


#### <a name="azure-action-group"></a>Skupiny akcí Azure
Všechna upozornění v Azure, použijte skupiny akcí jako výchozího mechanismu pro zpracování akce. Pomocí skupiny akcí můžete zadat vaše akce jednou a přidružte skupinu akcí více výstrah – napříč Azure. Bez nutnosti opakovaně opětovně deklarovat stejné akce. Skupiny akcí podporovat více akcí – včetně e-mailu, SMS, hlasovým hovorem, připojení ITSM, Runbook služby Automation, Webhooku URI a dalších. 

Pro uživatele, kteří mají svá upozornění rozšíří do Azure – plánu teď měli mít podrobnosti skupiny akcí předána spolu s prahovou hodnotou, bude moct vytvořit výstrahu. Podrobnosti o e-mailu, adresy URL Webhooku, automatických postupů Runbook. Podrobnosti a další akce, musí být definován na straně nejdříve výstrahu; před vytvořením skupiny akcí můžete vytvořit jednu [skupiny akcí ze služby Azure Monitor](../../monitoring-and-diagnostics/monitoring-action-groups.md) portálu nebo pomocí [skupiny akcí – šablona Resource](../../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| AzNsNotification | Ano | ID prostředku Azure akce skupiny souviset s výstrahou pro pořízení potřebné akce při splnění kritérií výstrah. |
| CustomEmailSubject | Ne | Vlastní předmět e-mailu odeslaného na všechny adresy zadaná ve skupině přidružené akcí. |
| CustomWebhookPayload | Ne | Vlastní datová část k odeslání do všech webhooku koncové body definované ve skupině přidružené akce. Formát závisí na co webhook očekává a by měl být platný kód JSON serializované. |


#### <a name="actions-for-oms-legacy"></a>Akce pro OMS (starší verze)

Každý plán obsahuje jednu **výstrah** akce.  Definuje podrobnosti výstrahy a volitelně oznámení a nápravné akce.  Oznámení se odešle e-mail na jeden nebo více adres.  Nápravy spuštění sady runbook ve službě Azure Automation se pokusit k nápravě zjištěného problému.

> [!NOTE]
> Od 14. května 2018, všechna upozornění v instanci pracovního prostoru Log Analytics veřejného cloudu Azure začal automaticky rozšíří do Azure. Další informace najdete v tématu [upozornění rozšířit do Azure](../../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, kteří rozšíření upozornění do Azure jsou teď akce provádí na skupiny akcí Azure. Jakmile pracovního prostoru a jeho výstrahy se rozšíří do Azure, můžete načíst nebo přidání akcí s použitím [skupiny akcí – šablony Azure Resource Manageru](../../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Tato část je nepovinná zahrnout, pokud chcete výstrahu odesílat poštu do jednoho nebo více příjemců.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Příjemci | Ano | Čárkami oddělený seznam e-mailové adresy k odeslání oznámení, když výstraha se vytvoří, jako v následujícím příkladu.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Subjekt | Ano | Řádek předmětu e-mailu. |
| Příloha | Ne | Přílohy se momentálně nepodporují.  Pokud tento prvek je součástí, mělo by být **žádný**. |


##### <a name="remediation"></a>Náprava
Tato část je nepovinná zahrnout, pokud chcete runbook spustit v reakci na upozornění. |

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| RunbookName | Ano | Název spuštění sady runbook. |
| WebhookUri | Ano | Identifikátor URI webhook pro runbook. |
| Vypršení platnosti | Ne | Datum a čas, jejíž platnost vyprší náprava. |

##### <a name="webhook-actions"></a>Akce Webhooku

Akce Webhooku spuštění procesu pomocí volání adresy URL a volitelně poskytuje datovou část, která je k odeslání. Když se podobají nápravné akce, s výjimkou jsou určeny pro webhooky, které mohou vyvolat procesy než runbooky Azure Automation. Obsahují taky další možnost poskytnout datovou část, která bude doručen do vzdáleného procesu.

Pokud bude zavolání webhooku po upozornění a bude je nutné prostředek akce s typem **Webhooku** kromě **výstraha** zdroj akce.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

Vlastnosti pro prostředky akce Webhooku jsou popsány v následujících tabulkách.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| type | Ano | Typ akce.  Toto je **Webhooku** pro akce webhooku. |
| jméno | Ano | Zobrazovaný název této akce.  To se nezobrazí v konzole. |
| wehookUri | Ano | Identifikátor URI pro webhook. |
| customPayload | Ne | Vlastní datová část odeslání k webhooku. Formát závisí na co se očekává se webhook. |


## <a name="sample"></a>Ukázka

Tady je příklad řešení, které obsahuje následující zdroje:

- Uložené hledání
- Plán
- Skupina akcí

Ukázka používá [standardní řešení parametry]( solutions-solution-file.md#parameters) proměnné, které používají ho většinou v řešení, na rozdíl od hodnoty hardcoding v definicích prostředků.

```
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
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
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

Následující soubor parametrů obsahuje hodnoty vzorky pro toto řešení.
```
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
* [Přidání zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidání runbooků služeb automatizace a dalším prostředkům](solutions-resources-automation.md) do řešení pro správu.

