---
title: Vytvoření upozornění na protokol pomocí šablony Resource Manageru
description: Zjistěte, jak pro vytvoření upozornění protokolu za použití šablony Azure Resource Manageru a rozhraní API.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 588a0686eda1966582b82a4673a8b6805453c94c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441438"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Vytvoření upozornění na protokol pomocí šablony Resource Manageru
Tento článek popisuje, jak můžete spravovat [upozornění protokolů](monitor-alerts-unified-log.md) prostřednictvím kódu programu ve velkém měřítku v Azure s využitím [šablony Azure Resource Manageru](..//azure-resource-manager/resource-group-authoring-templates.md) prostřednictvím [prostředí Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) a [Rozhraní příkazového řádku azure](../azure-resource-manager/resource-group-template-deploy-cli.md). Aktuálně Azure Alerts, podporuje upozornění protokolů na dotazy z [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) a [Azure Application Insights](../application-insights/app-insights-analytics-tour.md).

## <a name="managing-log-alert-on-log-analytics"></a>Správa upozornění protokolu v Log Analytics
Upozornění protokolu pro [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) je integrovaná do [vyzkoušet nové výstrahy Azure](monitoring-overview-unified-alerts.md); zatímco stále přesahuje protokol rozhraními API pro analýzu a zůstane kompatibility se schématem předtím použili ke správě [výstrah v portálu OMS](..//log-analytics/log-analytics-alerts-creating.md).

> [!NOTE]
> Od 14. května 2018, všechny výstrahy v pracovním prostoru automaticky začne rozšířit do Azure. Uživatel můžete odpojit zahájit rozšiřování upozornění do Azure před 14. května 2018. Další informace najdete v tématu [upozornění rozšířit do Azure od OMS](monitoring-alerts-extend.md). 

### <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Upozornění protokolů pro Log Analytics jsou vytvořené pravidly upozornění, na kterých běží uložené výsledky hledání v pravidelných intervalech. Pokud výsledky dotazu porovnávání zadaným kritériím, vytvoří záznam o upozornění a spuštění jednu nebo více akcí. 

Prostředek šablony pro protokol uložené výsledky hledání analytics a Log analytics alertsare k dispozici v části dokumentace ke službě Log Analytics. Další informace najdete tady [přidání Log Analytics uložené hledání a výstrahy](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md), který obsahuje ilustrativní ukázky, jakož i informace o schématu.

### <a name="using-resource-template-via-apipowershell"></a>Pomocí šablony Resource prostřednictvím rozhraní API nebo Powershellu
Log Analytics výstrah REST API je RESTful a je přístupný prostřednictvím rozhraní REST API Azure Resource Manageru. Proto je přístupný z příkazového řádku Powershellu a rozhraní API bude vypsání výsledků vyhledávání, ve formátu JSON, abyste mohli používat výsledky mnoha různými způsoby prostřednictvím kódu programu.

Další informace o [vytvářet a spravovat pravidla výstrah ve službě Log Analytics pomocí rozhraní REST API](../log-analytics/log-analytics-api-alerts.md), včetně příkladů přístup k rozhraní API z Powershellu.

## <a name="managing-log-alert-on-application-insights"></a>Správa upozornění protokolu v Application Insights
Upozornění protokolů pro Azure Application Insights byly zavedeny jako součást nové výstrahy Azure v části Azure Monitor. Proto poběží v rámci rozhraní API služby Azure Monitor jako [naplánované pravidla dotazu](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) skupina operace REST.

### <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Upozornění protokolu pro prostředky Application Insights má typ `Microsoft.Insights/scheduledQueryRules/`. Další informace v tomto typu prostředku, naleznete v tématu [Azure Monitor – reference k rozhraní API naplánované pravidla dotazu](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/).

Tady je struktury [vytváření pravidel dotazu naplánované](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate) na základě šablony prostředků s ukázkovou datovou sadu jako proměnné.

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
> Značka pole Skrytá odkazy na cílový prostředek je povinné použití [naplánované pravidla dotazu ](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/) šablonu volání nebo prostředku rozhraní API. 

Výše uvedené ukázky json se dají uložit jako (Řekněme) sampleScheduledQueryRule.json pro účely tohoto návodu a je možné nasadit s použitím [Azure Resource Manageru na webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

### <a name="using-resource-template-via-clipowershell"></a>Pomocí šablony Resource prostřednictvím rozhraní příkazového řádku nebo Powershellu
Azure Monitor – naplánované API pravidla dotazu je rozhraní REST API a plně kompatibilní s rozhraním REST API Azure Resource Manageru. Proto může sloužit prostřednictvím Powershellu pomocí rutiny Resource Manageru a Azure CLI.

Znázorněno níže využití přes rutiny Powershellu pro Azure Resource Manager pro ukázku výše uvedenou šablonu prostředků (sampleScheduledQueryRule.json):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Znázorněno níže použití pomocí příkazu Azure Resource Manageru v Azure CLI v ukázce výše uvedenou šablonu prostředků (sampleScheduledQueryRule.json):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Úspěšná operace 201 se vrátit k vytvoření nového pravidla upozornění stavu nebo 200 bude vrácen, pokud byla změněna stávající pravidla upozornění.


## <a name="next-steps"></a>Další postup
* Vysvětlení [akce Webhooku pro výstrahy protokolu](monitor-alerts-unified-log-webhook.md)
* Další informace o novém [Azure Alerts](monitoring-overview-unified-alerts.md)
* Další informace o [Application Insights](../application-insights/app-insights-analytics.md)
* Další informace o [Log Analytics](../log-analytics/log-analytics-overview.md).   
