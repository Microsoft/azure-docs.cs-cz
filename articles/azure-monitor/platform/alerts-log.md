---
title: Vytvoření, zobrazení a správa protokolu upozornění pomocí Azure monitoru
description: Použití Azure monitoru k vytváření, zobrazení a Správa pravidel upozornění protokolů v Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 46fc0202fe8e04cd7caefeeca948ebef251822fc
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562279"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Vytvoření, zobrazení a Správa upozornění protokolů pomocí Azure monitoru  

## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak nastavit upozornění protokolů pomocí rozhraní výstrah v portálu Azure portal. Definice pravidla upozornění není ve třech částech:
- Cíl: Konkrétní prostředek Azure, který je k monitorování
- Kritéria: Konkrétní podmínky nebo logiku, která při vidět v signál, by měly aktivovat akce
- Akce: Konkrétní volání odesílat příjemce oznámení – e-mail, SMS, webhooku atd.

Termín **upozornění protokolů** k popisu výstrahy, pokud je signál vlastní dotaz na základě [Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) nebo [Application Insights](../../azure-monitor/app/analytics.md). Další informace o funkci, terminologie a typy z [upozornění - Přehled protokolů](../../azure-monitor/platform/alerts-unified-log.md).

> [!NOTE]
> Data z oblíbených protokolů [Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) je teď taky dostupná na platformě metriky ve službě Azure Monitor. Pro zobrazení podrobností o [upozornění metriky pro protokoly](../../azure-monitor/platform/alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Správa upozornění protokolů z portálu Azure portal

Další podrobné je podrobný návod k použití protokolu výstrah pomocí rozhraní Azure portal.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Vytváření pravidel upozornění protokolů pomocí webu Azure portal
1. V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  
    ![Monitorování](media/alerts-log/AlertsPreviewMenu.png)

1. Vyberte **nové pravidlo upozornění** pro vytvoření nového upozornění v Azure.
    ![Přidat výstrahu](media/alerts-log/AlertsPreviewOption.png)

1. V části Vytvoření upozornění se zobrazí s tři části, který se skládá z: *Definujte podmínku upozornění*, *definujte podrobnosti o upozornění*, a *definujte skupinu akcí*.

    ![Vytvořit pravidlo](media/alerts-log/AlertsPreviewAdd.png)

1.  Definujte podmínku upozornění pomocí **vybrat prostředek** odkaz a zadáte cíl tak, že vyberete prostředku. Filtrovat podle výběru _předplatné_, _typ prostředku_a požadované _prostředků_. 

    >[!NOTE]

    > Pro vytvoření protokolu upozornění: ověření **protokolu** signálu je k dispozici pro vybraný prostředek, než budete pokračovat.
    ![Vybrat prostředek](media/alerts-log/Alert-SelectResourceLog.png)

 
1. *Upozornění protokolů*: Zajištění **typ prostředku** je zdrojem analytics jako *Log Analytics* nebo *Application Insights* a signalizuje, že typ jako **protokolu**, než jednou odpovídající **prostředků** je vybrán, klikněte na tlačítko *provádí*. Potom pomocí **přidat kritéria** tlačítko Zobrazit seznam možností signál k dispozici pro prostředek a v seznamu signál **prohledávání protokolů vlastní** možnosti zvolené protokolu monitorování službě, jako je *protokolu Analytics* nebo *Application Insights*.

   ![Vyberte prostředek - hledání vlastního protokolu](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Upozornění seznamy můžete importovat dotaz analytics jako typ signálu - **protokolu (uložený dotaz)**, jak je znázorněno výše obrázku. Tak, že uživatelé mohou zdokonalujete dotazu v Analytics a pak je ukládejte pro budoucí použití v upozornění – další podrobnosti o použití uložení dotazu, které jsou k dispozici na [pomocí prohledávání protokolů v log analytics](../../azure-monitor/log-query/log-query-overview.md) nebo [sdíleného dotazu ve službě application insights Analytics](../../azure-monitor/log-query/log-query-overview.md). 

1.  *Upozornění protokolů*: Po výběru dotazu pro generování výstrah můžou zobrazovat v **vyhledávací dotaz** pole; Pokud syntaxe dotazu není správná, pole se zobrazí chyba červeně. Pokud je správná syntaxe dotazu – pro referenci historických dat stanovených dotazu se zobrazuje jako graf s možností pro upravit časový interval z posledních šest hodin na poslední týden.

 ![Konfigurace pravidla výstrahy](media/alerts-log/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Vizualizaci historických dat mohou být zobrazeny pouze pokud výsledky dotazu obsahovat podrobnosti času. Pokud váš dotaz vrátí souhrnná data nebo konkrétní sloupec hodnot – stejný se zobrazuje jako jednotný vykreslení.

    >  Pro typ měření metriky upozornění protokolů pomocí Application insights, můžete zadat které určité proměnné k seskupení dat pomocí **agregační na** možnosti, jak je znázorněno níže:

    ![agregace na možnost](media/alerts-log/aggregate-on.png)

1.  *Upozornění protokolů*: S vizualizací na místě **Alert Logic** lze vybrat ze zobrazených možností podmínku, agregace a nakonec prahovou hodnotu. Nakonec zadejte v logice, čas pro zadanou podmínku pomocí **období** možnost. Spolu s jak často výstraha spouštět tak, že vyberete **frekvence**.

Pro **upozornění protokolů** výstrahy může být založené na:
   - *Počet záznamů, které*: Pokud je počet záznamů vrácených dotazem větší nebo menší než hodnota zadaná, vytvoří se výstraha.
   - *Metriky měření*: Vytvoří se výstraha, pokud každý *agregovat hodnotu* ve výsledcích překračuje prahovou hodnotu k dispozici a je *seskupené podle* zvolena hodnota. Počet porušení upozornění je počet pokusů, které se ve zvoleném časovém období překročil prahovou hodnotu. Můžete určit celkový počet porušení pro libovolnou kombinací těchto porušení přes sadu výsledků nebo po sobě jdoucí porušení tak, aby vyžadovala, porušení se musí vyskytovat v po sobě jdoucích vzorků. Další informace o [upozornění protokolů a jejich typy](../../azure-monitor/platform/alerts-unified-log.md).


1. Jako druhý krok definuje název pro upozornění v **název pravidla upozornění** pole společně s **popis** s podrobnostmi o podrobné informace o upozornění a **závažnost** hodnotu možnosti k dispozici. Tyto podrobnosti údaje znovu použijí ve všech výstrah e-mailů, oznámení nebo push provádí Azure Monitor. Kromě toho uživatel může zvolit okamžitě aktivovat pravidlo upozornění na vytvoření odpovídajícím způsobem přepnutím **Povolit pravidlo po vytvoření** možnost.

    Pro **upozornění protokolů** pouze, některé další funkce jsou dostupné v podrobnostech výstrahy:

    - **Potlačit výstrahy**: Při zapnutí potlačení pro pravidla upozornění jsou zakázané akce pro pravidlo definované dobu, po vytvoření nové výstrahy. Toto pravidlo je pořád spuštěný a vytvoří záznamy upozornění, pokud je splněna kritéria. Umožňuje vám čas k opravě problému bez spuštění duplicitní akce.

        ![Potlačit oznámení pro výstrahy protokolu](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Zadat potlačit výstrahy hodnotu větší než četnost upozornění, ujistěte se, že oznámení jsou ukončeny bez překrytí

1. Jako třetí a poslední krok, zadejte případná **skupiny akcí** je potřeba pro pravidlo upozornění aktivuje, když je splněná podmínka výstrahy. Můžete zvolit žádné existující skupiny akcí s výstrahou nebo vytvořte novou skupinu akcí. Podle vybrané skupiny akcí, když je výstraha se aktivační událost Azure: Odeslat email(s), odeslat SMS(s), volání webhooky, opravit pomocí Runbooků Azure, nabízených oznámení do vaší nástroji ITSM atd. Další informace o [skupiny akcí](action-groups.md).

    > [!NOTE]
    > Odkazovat [limity předplatného Azure](../../azure-subscription-service-limits.md) omezení datových částí Runbooku pro upozornění protokolů přes skupiny akcí Azure pro

    Pro **upozornění protokolů** některé další funkce, je možné přepsat výchozí akce:

    - **E-mailová oznámení**: Přepíše *předmět e-mailu* v e-mailu, prostřednictvím skupiny akcí; Pokud je jeden nebo více e-mailových akcí v uvedené skupiny akcí. Nelze změnit text e-mailu a toto pole je **není** e-mailových adres.
    - **Zahrnout vlastní datovou část Json**: Přepíše webhooku JSON používá skupiny akcí; Pokud je jeden nebo více akcí webhooku v uvedené skupiny akcí. Uživatel může určit formát JSON, který má být použit pro všechny nakonfigurované ve skupině přidružené akce; webhooků Další informace o formátech webhooku, najdete v části [akce webhooku pro upozornění protokolu](../../azure-monitor/platform/alerts-log-webhook.md). Možnost zobrazení Webhooku je k dispozici ke kontrole formátu pomocí ukázkových dat JSON.

        ![Akce přepsání pro výstrahy protokolu](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Pokud všechna pole jsou platná a s zelené značky **vytvořit pravidlo upozornění** kliknutí na tlačítko a výstrahy se vytvoří ve službě Azure Monitor – výstrahy. Všechny výstrahy můžete zobrazit z výstrah řídicí panel.

    ![Vytvoření pravidla](media/alerts-log/AlertsPreviewCreate.png)

    Během několika minut upozornění je aktivní a aktivuje jak bylo popsáno dříve.

Uživatelé můžou také finalizován jejich analytického dotazu v [protokoly analýzy stránky na webu Azure portal](../../azure-monitor/log-query/portals.md#log-analytics-page
) a potom je nabídnout k vytvoření výstrahy prostřednictvím "+ nové pravidlo upozornění" tlačítko – potom postupujte podle pokynů v kroku 6 a vyšší v tomto kurzu výše.

 ![Log Analytics – nastavení upozornění](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Zobrazení a Správa upozornění protokolů na webu Azure portal

1. V [portál](https://portal.azure.com/)vyberte **monitorování** a v části monitorování – tlačítko **výstrahy**.  

1. **Řídicí panel výstrah** se zobrazí – ve které všechny výstrahy Azure (včetně upozornění protokolů) se zobrazují v jednotném panel; včetně každou instanci když pravidlo výstrah protokolu byla aktivována. Další informace najdete v tématu [Správa výstrah](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Pravidla upozornění protokolů tvoří poskytuje uživatelům vlastní logiky založenou na dotazu a tím i bez vyřešeném stavu. To pokaždé, když jsou splněny podmínky uvedené v pravidel upozornění protokolů, to se aktivuje. 


1. Vyberte **spravovat pravidla** tlačítko na horním panelu přejděte do části Správa pravidlo – kde jsou uvedeny všechny pravidla upozornění vytvořená; včetně výstrahy, které byly zakázány.
    ![ Spravovat pravidla výstrah](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Správa výstrah protokolu pomocí šablony Azure Resource

Upozornění protokolů ve službě Azure Monitor jsou spojeny s typem prostředku `Microsoft.Insights/scheduledQueryRules/`. Další informace v tomto typu prostředku, naleznete v tématu [Azure Monitor – reference k rozhraní API naplánované pravidla dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Upozornění protokolů pro Application Insights nebo Log Analytics, můžete vytvořit pomocí [naplánované pravidla rozhraní API pro dotazy](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Upozornění protokolů pro Log Analytics je také možné spravovat pomocí starší verze [API upozornění Log Analytics](../../azure-monitor/platform/api-alerts.md) a starší verze šablony [uložené výsledky hledání a upozornění Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md) také. Další informace o použití nového rozhraní API ScheduledQueryRules pomocí zde podrobně ve výchozím nastavení, najdete v části [přepnout na nové rozhraní API pro upozornění Log Analytics](alerts-log-api-switch.md).

Tady je struktury [vytváření pravidel dotazu naplánované](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) na základě šablony prostředků s ukázkovou datovou sadu jako proměnné.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
    "alertName": "sample log alert",
    "alertDescr": "Sample log search alert",
    "alertStatus": "true",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
    "alertSource":{
        "Query":"union workspace("servicews").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
        "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews", 
        "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4",
         "SuppressTimeinMin": 20
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
      "metricMeasurement": {
          "thresholdOperator": "Equal",
          "threshold": "1",
          "metricTriggerType": "Consecutive",
          "metricColumn": "Classification"
      },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
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
       "description": "[variables('alertDescr')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
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
           "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
           "aznsAction":{
               "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]",
               "metricTrigger":{
                   "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                   "threshold": "[variables('metricMeasurement').threshold]",
                   "metricColumn": "[variables('metricMeasurement').metricColumn]",
                   "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
               }
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Značka pole Skrytá odkazy na cílový prostředek je povinné použití [naplánované pravidla dotazu ](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) šablonu volání nebo prostředku rozhraní API. 

Výše uvedené ukázky json se dají uložit jako (Řekněme) sampleScheduledQueryRule.json pro účely Tento názorný postup vás provede a je možné nasadit s použitím [Azure Resource Manageru na webu Azure portal](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Správa výstrah protokolu pomocí Powershellu, rozhraní příkazového řádku nebo rozhraní API

[Azure Monitor – naplánovaných pravidel dotazu API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) je rozhraní REST API a plně kompatibilní s rozhraním REST API Azure Resource Manageru. Proto může sloužit prostřednictvím Powershellu pomocí rutiny Resource Manageru a Azure CLI.

> [!NOTE]
> Upozornění protokolů pro Log Analytics je také možné spravovat pomocí starší verze [API upozornění Log Analytics](../../azure-monitor/platform/api-alerts.md) a starší verze šablony [uložené výsledky hledání a upozornění Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md) také. Další informace o použití nového rozhraní API ScheduledQueryRules pomocí zde podrobně ve výchozím nastavení, najdete v části [přepnout na nové rozhraní API pro upozornění Log Analytics](alerts-log-api-switch.md).


Upozornění protokolů nemají vyhrazené příkazy prostředí PowerShell nebo rozhraní příkazového řádku aktuálně; jak je znázorněno níže je možné pomocí rutiny Powershellu pro Azure Resource Manager pro ukázku výše uvedenou šablonu prostředků (sampleScheduledQueryRule.json) v, ale [oddíl prostředků šablony](#azure-resource-template-for-application-insights) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "contosoRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```

Znázorněno níže použití pomocí příkazu Azure Resource Manageru v Azure CLI v ukázce výše uvedenou šablonu prostředků (sampleScheduledQueryRule.json) [oddíl prostředků šablony](#azure-resource-template-for-application-insights) :

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Úspěšná operace 201 se vrátit k vytvoření nového pravidla upozornění stavu nebo 200 bude vrácen, pokud byla změněna stávající pravidla upozornění.
  
## <a name="next-steps"></a>Další postup

* Další informace o [upozornění protokolů ve výstrahách Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Vysvětlení [akce Webhooku pro výstrahy protokolu](../../azure-monitor/platform/alerts-log-webhook.md)
* Další informace o [Application Insights](../../azure-monitor/app/analytics.md)
* Další informace o [Log Analytics](../../azure-monitor/log-query/log-query-overview.md). 


