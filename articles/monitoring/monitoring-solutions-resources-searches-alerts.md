---
title: Uložené hledání a upozornění v řešení pro správu | Microsoft Docs
description: Řešení pro správu měl obvykle zahrnovat uložená hledání v Log Analytics k analýze dat shromážděných řešením.  Mohou také definovat výstrahy, které uživatele nebo v reakci na kritický problém automaticky provést akci.  Tento článek popisuje, jak definovat analýzy protokolů, které jsou uložené hledání a upozornění v šabloně Resource Manager, můžou být součástí řešení pro správu.
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
ms.date: 04/16/2018
ms.author: bwren, vinagara
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b16c88b5ec45dec7bf0fe40da24e817ae325a3e
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>Přidání analýzy protokolů uložit výstrahy a hledání řešení pro správu (Preview)

> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma popsané níže se mohou změnit.   


[Řešení pro správu](monitoring-solutions.md) by měl obvykle zahrnovat [uložená hledání](../log-analytics/log-analytics-log-searches.md) v Log Analytics k analýze dat shromážděných řešením.  Může také definovat [výstrahy](../log-analytics/log-analytics-alerts.md) upozornit uživatele, nebo v reakci na kritický problém automaticky provést akci.  Tento článek popisuje, jak definovat uložená hledání analýzy protokolů a výstrahy [Správa prostředků šablony](../resource-manager-template-walkthrough.md) tak můžou být je součástí [řešení pro správu](monitoring-solutions-creating.md).

> [!NOTE]
> Ukázky v tomto článku použít parametry a proměnné, které jsou nutné nebo společné pro řešení pro správu a jsou popsány v [návrhu a sestavení řešení pro správu v Azure](monitoring-solutions-creating.md)  

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s postupy [vytvoření řešení správy](monitoring-solutions-creating.md) a strukturu [šablony Resource Manageru](../resource-group-authoring-templates.md) a soubor řešení.


## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Všechny prostředky v analýzy protokolů jsou součástí [prostoru](../log-analytics/log-analytics-manage-access.md).  Jak je popsáno v [pracovní prostor analýzy protokolů a účet Automation](monitoring-solutions.md#log-analytics-workspace-and-automation-account), pracovním prostoru není zahrnutý v řešení pro správu, ale musí existovat před instalací řešení.  Pokud není k dispozici, řešení instalace se nezdaří.

Název pracovního prostoru je názvu každého prostředku analýzy protokolů.  To se provádí v řešení s **prostoru** parametr jako v následujícím příkladu SavedSearch prostředku.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Verze rozhraní API analýzy protokolů
Všechny prostředky analýzy protokolů, které jsou definované v šabloně Resource Manager mít vlastnost **apiVersion** verzi rozhraní API by měl použít na prostředek, který definuje.  Tato verze je pro prostředky, které používají jiné [starší verze a upgradovaný dotazovací jazyk](../log-analytics/log-analytics-log-search-upgrade.md).  

 Následující tabulka obsahuje verze rozhraní API analýzy protokolů pro uložená hledání ve starší verzi a upgradovali pracovních prostorů: 

| Verze pracovního prostoru | Verze API | Dotaz |
|:---|:---|:---|
| V1 (starší)   | 2015-11-01-preview | Starší verze formátu.<br> Příklad: Zadejte = událostí EventLevelName = chyby  |
| v2 (upgradu) | 2015-11-01-preview | Starší verze formátu.  Převedeny na formát upgradované na instalaci.<br> Příklad: Zadejte = událostí EventLevelName = chyby<br>Převést na: událost &#124; kde EventLevelName == "Error.  |
| v2 (upgradu) | 2017-03-03-preview | Upgrade formátu. <br>Příklad: Událost &#124; kde EventLevelName == "Error.  |



## <a name="saved-searches"></a>Uložená hledání
Zahrnout [uložená hledání](../log-analytics/log-analytics-log-searches.md) v řešení, aby uživatelé mohli dotaz na data shromažďovaná společností řešení.  Uložené hledání se zobrazí pod **Oblíbené** na portálu OMS a **uložená hledání** na portálu Azure.  Uložené hledání je také nutný pro každou výstrahu.   

[Analýzy protokolů uložené hledání](../log-analytics/log-analytics-log-searches.md) prostředky mít typ `Microsoft.OperationalInsights/workspaces/savedSearches` a mít následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 

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



Každou vlastnost uloženého hledání je popsané v následující tabulce. 

| Vlastnost | Popis |
|:--- |:--- |
| category | Kategorie pro uložené hledání.  Všechny uložená hledání ve stejném řešení, bude často sdílet jednu kategorii, jsou seskupeny dohromady v konzole. |
| DisplayName | Název zobrazení pro uložené hledání na portálu. |
| query | Dotaz spustit. |

> [!NOTE]
> Musíte použít řídicí znaky v dotazu, pokud obsahuje znaky, které je možné interpretovat jako JSON.  Například, pokud se dotaz **typu: AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, by měly být zapsány v souboru řešení jako **typu: AzureActivity OperationName:\" Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Výstrahy
[Protokolu analýzy výstrahy](../log-analytics/log-analytics-alerts.md) jsou vytvořené pomocí pravidla výstrah, které běží uloženého hledání v pravidelných intervalech.  Pokud výsledky dotazu odpovídal zadaná kritéria, se vytvoří záznam výstrah a spouštějí jednu nebo více akcí.  

> [!NOTE]
> Od 14 může 2018 všechny výstrahy v pracovním prostoru se začnou automaticky rozšířit do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou nyní akce řídí ve službě Azure akce skupiny. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny - šablony Azure Resource Manageru](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

Pravidla výstrah v rámci řešení pro správu se skládá z těchto tří různých prostředků.

- **Uložené hledání.**  Definuje vyhledávání protokolu, který se spouští.  Více pravidla výstrah můžete sdílet jeden uloženého hledání.
- **Plán.**  Definuje, jak často se spustí vyhledávání protokolu.  Každé pravidlo výstrahy obsahuje pouze jeden plán.
- **Výstrahy akce.**  Každé pravidlo výstrahy má jeden prostředek skupiny akce nebo akce prostředek (starší) s typem **výstraha** , který definuje podrobnosti výstrahy například kritéria pro vytvoření záznamu výstrah a závažnosti výstrah. [Akce skupiny](../monitoring-and-diagnostics/monitoring-action-groups.md) prostředků může mít seznam nakonfigurované akcí při vyvolání výstrahy – například telefonní hovor, SMS, e-mailu, webhooku, nástroj ITSM, sady automation runbook, aplikace logiky, atd.
 
Akce prostředku (starší), volitelně definuje odpověď e-mailu a runbook.
- **Akce Webhooku (zastaralé).**  Pokud pravidlo výstrahy volá webhook, jehož, pak vyžaduje prostředek další akce s typem **Webhooku**.    

Uložené hledání, které prostředky jsou popsané výše.  Dále jsou uvedená na další prostředky.


### <a name="schedule-resource"></a>Plán prostředku

Uložené hledání může mít jeden nebo více plánů s každý plán reprezentující samostatné pravidlo výstrahy. Plán definuje, jak často hledání je spustit a časový interval, za které se načítají data.  Plán prostředky mít typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` a mít následující strukturu. Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 


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
| povoleno       | Ano | Určuje, zda je povoleno výstrahu, když je vytvořeno. |
| interval      | Ano | Jak často spuštění dotazu v minutách. |
| QueryTimeSpan | Ano | Časový interval v minutách, přes který vyhodnotit výsledky. |

Plán prostředku by měl závisí na uloženého hledání tak, aby se vytvoří před plán.

> [!NOTE]
> Název plánu musí být jedinečný v daném prostoru; dva plány nemůže mít stejné ID, i když jsou spojeny s jinou uložená hledání. Název pro všechny uložená hledání, plány a akce, které jsou vytvořené pomocí rozhraní API Log Analytics musí být také na malá písmena.


### <a name="actions"></a>Akce
Plán může mít více akcí. Akce může definovat jeden nebo více procesy provést například odesílání e-mailu nebo spuštění sady runbook, nebo se může definovat prahové hodnoty, která určuje, kdy výsledky hledání kritériím některé.  Některé akce obě bude definovat, aby procesy provede, když je splněna prahovou hodnotu.

Akce lze definovat pomocí [akce skupiny] prostředků nebo prostředek akce.

> [!NOTE]
> Od 14 může 2018 všechny výstrahy v pracovním prostoru se začnou automaticky rozšířit do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou nyní akce řídí ve službě Azure akce skupiny. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny - šablony Azure Resource Manageru](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).


Existují dva typy prostředků akce určeného **typ** vlastnost.  Plán vyžaduje jednu **výstraha** akce, která definuje podrobnosti pravidlo výstrahy a jaké akce jsou provedeny, když se vytvoří výstraha. Akce prostředky mít typ `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

Akce výstrah mají následující strukturu.  Jedná se o běžné parametry a proměnné tak, aby můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změnit názvy parametrů. 


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

Vlastnosti výstrah akce prostředky jsou popsané v následujících tabulkách.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Typ | Ano | Typ akce.  Toto je **výstraha** pro akce výstrah. |
| Název | Ano | Zobrazovaný název výstrahy.  Toto je název, který se zobrazí v konzole pro pravidlo výstrahy. |
| Popis | Ne | Volitelný popis výstrahy. |
| Závažnost | Ano | Závažnost výstrahy záznam z následujících hodnot:<br><br> **Kritické**<br>**Upozornění**<br>**Informační**


#### <a name="threshold"></a>Mezní hodnota
Tento oddíl je povinný.  Definuje vlastnosti prahové hodnoty pro výstrahu.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Operátor | Ano | Operátor pro porovnání z následujících hodnot:<br><br>**gt = větší než<br>lt = menší než** |
| Hodnota | Ano | Hodnoty k porovnání výsledků. |

##### <a name="metricstrigger"></a>MetricsTrigger
Tato část je volitelné.  Zahrňte pro upozornění na metriky měření.

> [!NOTE]
> Metriky měření výstrahy jsou aktuálně ve verzi public preview. 

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| TriggerCondition | Ano | Určuje, zda prahová hodnota pro celkový počet narušení nebo po sobě jdoucích narušení z následujících hodnot:<br><br>**Celkový počet<br>po sobě jdoucích** |
| Operátor | Ano | Operátor pro porovnání z následujících hodnot:<br><br>**gt = větší než<br>lt = menší než** |
| Hodnota | Ano | Počet přístupů, které musí být splněna kritéria pro aktivování upozornění. |


#### <a name="throttling"></a>Throttling
Tato část je volitelné.  Pokud chcete pro potlačení výstrahy ze stejného pravidla pro určitou dobu, po vytvoření výstrahy, zahrnují v této části.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Doba trvání v minutách | Ano, pokud omezení zahrnuté – element | Počet minut pro potlačení výstrahy po vytvoření jedné ze stejné pravidlo výstrahy. |


#### <a name="azure-action-group"></a>Skupina Azure akcí
Všechny výstrahy v Azure, použijte akci skupiny jako výchozího mechanismu pro zpracování akce. Akce skupin můžete zadat vaše akce jednou a potom přidružení skupiny akce k více výstrah – v Azure. Bez nutnosti opakovaně opakovaně deklarovat stejné akce. Akce skupiny podporují různé akce – včetně e-mailu, SMS, hlasový hovor, ITSM připojení, sady Automation Runbook, identifikátor URI Webhooku a dalších. 

Pro uživatele, kteří rozšířili výstrahy do Azure – nyní plánu měli mít akce skupiny podrobnosti předán společně s prahovou hodnotou, abyste mohli vytvořit výstrahu. Podrobnosti o e-mailu, adresy URL Webhooku, Runbook automatizace podrobnosti a další akce musí být definován na straně skupinu akcí nejdříve před vytvořením výstrahu; můžete vytvořit jeden [akce skupiny z Azure monitorování](../monitoring-and-diagnostics/monitoring-action-groups.md) v portálu nebo pomocí [akce skupiny - šablony prostředků](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| AzNsNotification | Ano | ID prostředku Azure akce skupiny přiřazené upozornění pro pořízení potřebné akce při splnění kritérií výstrah. |
| CustomEmailSubject | Ne | Vlastní předmět e-mailu odeslány na všechny adresy zadané ve skupině přidružená akce. |
| CustomWebhookPayload | Ne | Vlastní datové části pro odeslání do všechny koncové body webhooku definovaná ve skupině přidružená akce. Formát závisí na co webhooku očekává a musí být platný kód JSON serializovaných. |


#### <a name="actions-for-oms-legacy"></a>Akce pro OMS (zastaralé)

Každý plán má jeden **výstrahy** akce.  Definuje podrobnosti výstrahy a volitelně oznámení a nápravné akce.  Oznámení se odešle e-mail na jeden nebo více adres.  Nápravy spuštění sady runbook ve službě Azure Automation se pokusit o napravit zjištěné potíže.

> [!NOTE]
> Od 14 může 2018 všechny výstrahy v pracovním prostoru se začnou automaticky rozšířit do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Pro uživatele, které rozšiřují výstrahy do Azure jsou nyní akce řídí ve službě Azure akce skupiny. Při jeho výstrahy a pracovního prostoru jsou rozšířené a Azure, můžete načíst nebo přidání akcí pomocí [akce skupiny - šablony Azure Resource Manageru](../monitoring-and-diagnostics/monitoring-create-action-group-with-resource-manager-template.md).

##### <a name="emailnotification"></a>EmailNotification
 Tato část je volitelný ho zahrňte, pokud chcete výstrahu odesílat poštu jeden nebo více příjemcům.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| Příjemci | Ano | Čárkami oddělený seznam e-mailové adresy k odesílání oznámení, pokud je výstrahy vytvořeny jako v následujícím příkladu.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Předmět | Ano | Řádek předmětu e-mailu. |
| Příloha | Ne | Přílohy nejsou aktuálně podporovány.  Pokud tento element je zahrnuto, by měla být **žádné**. |


##### <a name="remediation"></a>Náprava
Tato část je volitelný ho zahrňte, pokud chcete sadu runbook spustit v reakci na výstrahy. |

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| RunbookName | Ano | Název sady runbook spustit. |
| WebhookUri | Ano | Identifikátor URI služby webhooku pro sadu runbook. |
| Vypršení platnosti | Ne | Datum a čas, kdy vyprší platnost náprava. |

##### <a name="webhook-actions"></a>Akce Webhooku

Akce Webhooku spuštění procesu voláním adresu URL a volitelně poskytuje datové části k odeslání. Jsou podobná nápravné akce s výjimkou jsou určené pro webhooků, který může vyvolat procesy než Azure Automation runbook. Obsahují taky další možnost poskytnout datové části který bude doručen do vzdálený proces.

Pokud upozornění bude volat webhook, jehož, pak bude je nutné prostředek akce s typem **Webhooku** kromě **výstraha** akce prostředků.  

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

Vlastnosti Webhooku akce prostředky jsou popsané v následujících tabulkách.

| Název elementu | Požaduje se | Popis |
|:--|:--|:--|
| type | Ano | Typ akce.  Toto je **Webhooku** pro akce webhooku. |
| jméno | Ano | Zobrazovaný název akce.  To se nezobrazí v konzole. |
| wehookUri | Ano | Identifikátor URI pro webhook. |
| CustomPayload | Ne | Vlastní datovou část k odeslání do webhooku. Formát závisí na co webhooku očekává. |


## <a name="sample"></a>Ukázka

Tady je ukázka řešení, které obsahuje, který obsahuje následující zdroje:

- Uložené hledání
- Plán
- Skupina akcí

Příklad používá [standardní řešení parametry]( monitoring-solutions-solution-file.md#parameters) proměnné, které by běžně používané v řešení oproti hardcoding hodnoty v definicích prostředků.

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
    
          "LogAnalyticsApiVersion": "2015-03-20",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
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

Následující soubor parametrů obsahuje hodnoty ukázky pro toto řešení.
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
* [Přidání zobrazení](monitoring-solutions-resources-views.md) do řešení pro správu.
* [Přidat runbooků služeb automatizace a dalším prostředkům](monitoring-solutions-resources-automation.md) do řešení pro správu.

