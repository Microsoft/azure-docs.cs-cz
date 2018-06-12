---
title: Vytvoření upozornění na protokol pomocí šablony Resource Manageru
description: Naučte se vytvořit výstrahu protokolu pomocí služby šablony Azure Resource Manageru a rozhraní API.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5afa34a5eadf5367b3ab28749735197ca6ed82bd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263197"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Vytvoření upozornění na protokol pomocí šablony Resource Manageru
Tento článek ukazuje, jak můžete spravovat [protokolu výstrahy](monitor-alerts-unified-log.md) prostřednictvím kódu programu ve velkém měřítku, v Azure pomocí [šablony Azure Resource Manageru](..//azure-resource-manager/resource-group-authoring-templates.md) prostřednictvím [prostředí Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) a [Rozhraní příkazového řádku azure](../azure-resource-manager/resource-group-template-deploy-cli.md). Aktuálně Azure výstrahy, podporuje protokolu výstrahy na dotazy z [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) a [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Správa výstrah protokolu analýzy protokolů
Výstraha protokolu pro [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) je integrována do [nové výstrahy Azure prostředí](monitoring-overview-unified-alerts.md); zatímco stále přesahuje přes rozhraní API analýzy protokolů a zůstane kompatibilitu s schématu dříve používat ke správě [výstrahy na portálu OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> Od 14 může 2018 všechny výstrahy v pracovním prostoru se začnou automaticky rozšířit do Azure. Uživatel může odpojit iniciovat rozšíření výstrahy do Azure před 14 může 2018. Další informace najdete v tématu [výstrahy rozšířit do Azure z OMS](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Protokol oznámení pro analýzy protokolů jsou vytvořené pomocí pravidla výstrah, které běží uloženého hledání v pravidelných intervalech. Pokud výsledky dotazu odpovídal zadaná kritéria, se vytvoří záznam výstrah a spouštějí jednu nebo více akcí. 

Šablony prostředků pro [hledání uložit analýzy protokolů](../log-analytics/log-analytics-log-searches.md) a [protokolu analýzy výstrahy](../log-analytics/log-analytics-alerts.md) jsou k dispozici v části analýzy protokolů dokumentace. Další informace o, [přidání analýzy protokolů uložené hledání a výstrahy](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md); což zahrnuje ilustrativní ukázky a také informace o schématu.

### <a name="using-resource-template-via-apipowershell"></a>Pomocí šablony prostředků prostřednictvím rozhraní API nebo prostředí Powershell
Log Analytics výstrahy REST API je dosáhl standardu RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manager. Rozhraní API je proto přístupná z příkazového řádku prostředí PowerShell a bude vypsání výsledků vyhledávání pro vás ve formátu JSON, budete moci použít výsledky v mnoha různými způsoby prostřednictvím kódu programu.

Další informace o [vytvářet a spravovat pravidla výstrah v analýzy protokolů pomocí rozhraní REST API](../log-analytics/log-analytics-api-alerts.md), včetně příkladů přístup k rozhraní API z prostředí Powershell.

## <a name="managing-log-alert-on-application-insights"></a>Správa výstrah protokolu Application Insights
Jako součást nové výstrahy Azure v části monitorování Azure byly zavedeny protokolu výstrahy pro Azure Application Insights. Proto jeho spuštění ve rozhraní API služby Azure monitorování jako [naplánované pravidla dotazu](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) skupina operace REST.

### <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Výstraha protokolu pro prostředky Application Insights má typ `Microsoft.Insights/scheduledQueryRules/`. Další informace o tento typ prostředku, najdete v části [monitorování Azure – referenční dokumentace rozhraní API naplánované pravidla dotazu](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Toto je struktury [vytváření pravidel dotazu naplánované](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) na základě šablony prostředků s vzorové sadě jako proměnné.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Pole značky se skryté odkaz na cílový prostředek je povinné použití [naplánované pravidla dotazu ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) rozhraní API volání nebo prostředku šablony. 

Výše uvedené ukázkové json lze uložit jako (například) sampleScheduledQueryRule.json pro účely tohoto návodu a dá se nasadit pomocí [Azure Resource Manageru na portálu Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Pomocí šablony prostředků prostřednictvím rozhraní příkazového řádku nebo prostředí Powershell
Azure monitorování – naplánované dotazu pravidla rozhraní API je rozhraní API REST a plně kompatibilní s rozhraním REST API Azure Resource Manager. Proto může sloužit pomocí prostředí Powershell pomocí rutiny správce prostředků, jakož i rozhraní příkazového řádku Azure.

Znázorněné dole využití pomocí rutiny Powershellu pro Azure Resource Manager pro ukázku uvedena výše šablony prostředků (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Znázorněné dole využití pomocí příkazu v Azure Resource Manageru v Azure CLI pro ukázku uvedena výše šablony prostředků (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Úspěšná operace 201 se vrátí stav vytvoření nové pravidlo výstrahy nebo 200 bude vrácen, pokud existující pravidlo výstrahy bylo změněno.


## <a name="next-steps"></a>Další postup
* Pochopení [akce Webhooku protokolu výstrahy](monitor-alerts-unified-log-webhook.md)
* Další informace o nové [výstrahách Azure](monitoring-overview-unified-alerts.md)
* Další informace o [Application Insights](../application-insights/app-insights-analytics.md)
* Další informace o [analýzy protokolů](../log-analytics/log-analytics-overview.md).   
