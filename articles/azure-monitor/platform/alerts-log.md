---
title: Vytváření, zobrazení a správa výstrah protokolu pomocí Azure Monitoru | Dokumenty společnosti Microsoft
description: Azure Monitor slouží k vytváření, zobrazení a správě pravidel upozornění protokolu v Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249422"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Vytváření, zobrazení a správa výstrah protokolu pomocí Azure Monitoru

## <a name="overview"></a>Přehled
Tento článek ukazuje, jak nastavit výstrahy protokolu pomocí rozhraní výstrah uvnitř portálu Azure. Definice pravidla výstrahy je ve třech částech:
- Cíl: Konkrétní prostředek Azure, který se má monitorovat
- Kritéria: Konkrétní podmínka nebo logika, která při pohledu v signálu, by měla vyvolat akci
- Akce: Konkrétní hovor zaslaný příjemci oznámení - e-mail, SMS, webhook atd.

Termín **Výstrahy protokolu** k popisu výstrah, kde signál je dotaz protokolu v [pracovním prostoru Log Analytics](../learn/tutorial-viewdata.md) nebo [Application Insights](../app/analytics.md). Další informace o funkcích, terminologii a typech naleznete v [části Výstrahy protokolu – Přehled](alerts-unified-log.md).

> [!NOTE]
> Oblíbená data protokolu z [pracovního prostoru Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) jsou teď dostupná taky na metrické platformě ve Službě Azure Monitor. Zobrazení [podrobností, Upozornění na metriky pro protokoly](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Správa upozornění protokolu z webu Azure Portal

Podrobné další je podrobný průvodce pomocí výstrah protokolu pomocí rozhraní portálu Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Vytvoření pravidla výstrahy protokolu pomocí portálu Azure

1. Na [portálu](https://portal.azure.com/)vyberte **Monitor** a v části MONITOR - zvolte **Výstrahy**.

    ![Monitorování](media/alerts-log/AlertsPreviewMenu.png)

1. Kliknutím na tlačítko **Nové pravidlo výstrahy** vytvořte novou výstrahu v Azure.

    ![Přidat výstrahu](media/alerts-log/AlertsPreviewOption.png)

1. Oddíl Vytvořit výstrahu se zobrazí se třemi částmi, které se skládají z: *Definovat výstražnou podmínku*, *Definovat podrobnosti výstrahy*a *Definovat skupinu akcí*.

    ![Vytvořit pravidlo](media/alerts-log/AlertsPreviewAdd.png)

1. Definujte podmínku výstrahy pomocí odkazu **Vybrat zdroj** a určením cíle výběrem zdroje. Filtrujte výběrem _předplatného_, _typu prostředku_a _požadovaného prostředku_.

   > [!NOTE]
   > Pro vytvoření výstrahy protokolu - před pokračováním ověřte, zda je pro vybraný prostředek k dispozici signál **protokolu.**
   >  ![Vybrat zdroj](media/alerts-log/Alert-SelectResourceLog.png)

1. *Upozornění protokolu*: Ujistěte **se, že typ prostředku** je analytický zdroj, jako je Log *Analytics* nebo *Application Insights* a typ signálu jako **Protokol**, pak po výběru **příslušného prostředku** klepněte na *tlačítko Hotovo*. Dále použijte tlačítko **Přidat kritéria** k zobrazení seznamu možností signálu, které jsou k dispozici pro daný prostředek, a ze seznamu signálů Možnost vlastního **vyhledávání protokolů** pro vybranou službu sledování protokolu, jako je *Log Analytics* nebo *Application Insights*.

   ![Výběr prostředku – vlastní hledání protokolu](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Seznamy výstrah lze importovat analytický dotaz jako typ signálu – **protokol (uložený dotaz)**, jak je znázorněno na obrázku výše. Takže uživatelé mohou zdokonalit váš dotaz v Analytics a pak je uložit pro budoucí použití ve výstrahách – další podrobnosti o použití ukládání dotazu k dispozici na [základě log dotazu v Azure Monitor](../log-query/log-query-overview.md) nebo [sdíleného dotazu v application insights analytics](../app/app-insights-overview.md).

1. *Výstrahy protokolu*: Po výběru lze dotaz na výstrahy uvést v poli **Vyhledávací dotaz.** Pokud je syntaxe dotazu nesprávná, zobrazí se v poli RED chyba. Pokud je syntaxe dotazu správná - Pro referenční historická data uvedeného dotazu je zobrazena jako graf s možností vyladit časové okno od posledních šesti hodin do minulého týdne.

    ![Konfigurace pravidla výstrahy](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Vizualizaci historických dat lze zobrazit pouze v případě, že výsledky dotazu mají podrobnosti o čase. Pokud dotaz obsahuje souhrnná data nebo konkrétní hodnoty sloupců , je toto zobrazeno jako jednoznačný obrázek.
   > Pro typ metrického měření výstrah protokolu pomocí application insights nebo [přepnutého na nové rozhraní API](alerts-log-api-switch.md)můžete určit, která konkrétní proměnná má data seskupit pomocí možnosti **Agregovat;** jak je znázorněno níže:
   > 
   > ![agregovat na opci](media/alerts-log/aggregate-on.png)

1. *Výstrahy protokolu*: S vizualizací na místě, **alert logiku** lze vybrat z zobrazených možností podmínka, agregace a nakonec práh. Nakonec zadejte v logice čas k posouzení pro zadanou podmínku pomocí **možnosti Období.** Spolu s tím, jak často by se výstraha měla spouštět výběrem **možnosti Frekvence**. **Výstrahy protokolu** mohou být založeny na:
    - [Počet záznamů](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): Výstraha je vytvořena, pokud je počet záznamů vrácených dotazem větší nebo menší než zadaný počet.
    - [Měření metriky](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): Výstraha se vytvoří, pokud každá *agregovaná hodnota* ve výsledcích překročí zadaný práh a je *seskupena podle* zvolené hodnoty. Počet porušení výstrahy je počet překročení prahové hodnoty ve zvoleném časovém období. Můžete určit Celkové porušení pro libovolnou kombinaci porušení v rámci sady výsledků nebo po sobě jdoucích porušení vyžadovat, aby porušení musí dojít v po sobě jdoucích vzorků.


1. Jako druhý krok definujte název výstrahy v poli **Název pravidla výstrahy** spolu s **popisem,** který podrobně popisuje specifika výstrahy a hodnoty **závažnosti** z poskytnutých možností. Tyto podrobnosti se znovu používají ve všech e-mailech s výstrahami, oznámeních nebo nabízených oznámeních provedených službou Azure Monitor. Kromě toho se uživatel může rozhodnout okamžitě aktivovat pravidlo výstrahy při vytváření vhodným přepnutím **možnosti Povolit pravidlo při vytvoření.**

    Pouze **pro výstrahy protokolu** jsou některé další funkce k dispozici v podrobnostech výstrah:

    - **Potlačit výstrahy**: Když zapnete potlačení pro pravidlo výstrahy, akce pro pravidlo jsou zakázány po definovanou dobu po vytvoření nové výstrahy. Pravidlo je stále spuštěno a vytváří záznamy výstrah za předpokladu, že jsou splněna kritéria. Umožňuje čas opravit problém bez spuštění duplicitních akcí.

        ![Potlačit výstrahy pro výstrahy protokolu](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Zadejte hodnotu potlačení výstrahy větší než četnost výstrah, aby bylo zajištěno, že oznámení budou zastavena bez překrytí.

1. Jako třetí a poslední krok určete, zda je třeba aktivovat skupinu **akcí** pro pravidlo výstrahy při splnění podmínky výstrahy. Můžete zvolit libovolnou existující skupinu akcí s výstrahou nebo vytvořit novou skupinu akcí. Podle vybrané skupiny akcí, když výstraha je aktivační Azure bude: odeslat e-maily, posílat SMS, volání Webhook (s), nápravu pomocí Azure Runbook, push na nástroj ITSM, atd. Další informace o [akčních skupinách](action-groups.md).

    > [!NOTE]
    > Omezení [datových](../../azure-resource-manager/management/azure-subscription-service-limits.md) částí sady Runbook saktivovaná pro výstrahy protokolů prostřednictvím skupin akcí Azure pomocí skupin akcí Azure

    Pro **výstrahy protokolu** jsou k dispozici některé další funkce pro přepsání výchozích akcí:

    - **E-mailové oznámení**: Přepíše *předmět e-mailu* v e-mailu, odeslaný prostřednictvím skupiny akcí; pokud v uvedené akční skupině existuje jedna nebo více e-mailových akcí. Tělo pošty nelze změnit a toto pole **není** určeno pro e-mailovou adresu.
    - **Zahrnout vlastní datovou část Json:** Přepíše webhookJSON používaný akčními skupinami; pokud v uvedené akční skupině existuje jedna nebo více akcí s webovým hákem. Uživatel může určit formát JSON, který se použije pro všechny webhooky nakonfigurované v přidružené skupině akcí; Další informace o formátech webhooku naleznete v [tématu webhooková akce pro výstrahy protokolů](../../azure-monitor/platform/alerts-log-webhook.md). View Webhook možnost je k dispozici pro kontrolu formátu pomocí ukázkových json dat.

        ![Přepsání akcí pro výstrahy protokolu](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Pokud jsou všechna pole platná a se zeleným zaškrtnutím můžete kliknout na tlačítko **pravidlo vytvoření a** vytvoří se výstraha v Azure Monitor – výstrahy. Všechny výstrahy lze zobrazit z řídicího panelu výstrah.

     ![Vytvoření pravidla](media/alerts-log/AlertsPreviewCreate.png)

     Během několika minut je výstraha aktivní a aktivuje se, jak bylo popsáno dříve.

Uživatelé mohou také dokončit svůj analytický dotaz v [analýze protokolů](../log-query/portals.md) a pak jej stisknout k vytvoření výstrahy pomocí tlačítka "Nastavit upozornění" - a pak podle pokynů od kroku 6 dále ve výše uvedeném kurzu.

 ![Log Analytics – nastavit výstrahu](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Zobrazení & správa výstrah protokolu na webu Azure Portal

1. Na [portálu](https://portal.azure.com/)vyberte **Monitor** a v části MONITOR - zvolte **Výstrahy**.

1. **Řídicí panel výstrah** se zobrazí – kde jsou všechny výstrahy Azure (včetně upozornění protokolu) zobrazeny v jednotné tabuli; včetně každé instance, kdy bylo aktivováno pravidlo upozornění protokolu. Další informace naleznete v [tématu Správa výstrah](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Pravidla výstrah protokolu se skládají z vlastní logiky založené na dotazu poskytované uživateli, a tedy bez vyřešeného stavu. Vzhledem k tomu, že pokaždé, když jsou splněny podmínky zadané v pravidle výstrahy protokolu, je aktivována.

1. Vyberte tlačítko **Spravovat pravidla** na horním panelu, chcete-li přejít do oddílu správy pravidel , kde jsou uvedena všechna vytvořená pravidla výstrah; včetně upozornění, která byla zakázána.
    ![správa pravidel výstrah](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Správa výstrah protokolu pomocí šablony prostředků Azure

Výstrahy protokolu v Azure Monitoru jsou přidružené k typu `Microsoft.Insights/scheduledQueryRules/`prostředku . Další informace o tomto typu prostředku najdete v tématu [Azure Monitor – odkaz na pravidla naplánovaných dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Protokolovat výstrahy pro Application Insights nebo Log Analytics, lze vytvořit pomocí [rozhraní API pravidel naplánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Protokol upozornění pro Log Analytics lze také spravovat pomocí starší [log Analytics Upozornění API](api-alerts.md) a starší šablony Log [Analytics uložená vyhledávání a výstrahy](../insights/solutions-resources-searches-alerts.md) stejně. Další informace o použití nového rozhraní API ScheduledQueryRules, které je ve výchozím nastavení podrobně popsáno, naleznete v [tématu Přepnutí na nové rozhraní API pro výstrahy analýzy protokolů](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Vytvoření výstrah ukázkového protokolu pomocí šablony prostředků Azure

Následuje struktura pro šablonu prostředků založených na [vytváření pravidel plánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) pomocí [standardního](alerts-unified-log.md#number-of-results-alert-rules)dotazu pro vyhledávání protokolů o počtu výstrah y protokolu typu výsledků s ukázkovými daty jako proměnnými.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
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
        "properties":{
            "description": "[variables('alertDescription')]",
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
    } ]
}

```

Ukázka json výše lze uložit jako (řekněme) sampleScheduledQueryRule.json pro účely této procházky a lze nasadit pomocí [Azure Resource Manager na webu Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Protokolovat výstrahu s dotazem na příčný zdroj pomocí šablony prostředků Azure

Následuje struktura pro šablonu prostředků založených na [vytváření pravidel naplánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) pomocí [vyhledávacího dotazu protokolu](../../azure-monitor/log-query/cross-workspace-query.md) mezi prostředky upozornění na protokol [metriky](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)s ukázkovými daty jako proměnnými.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
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
    } ]
}

```

> [!IMPORTANT]
> Při použití dotazu mezi prostředky v protokolu výstrahy, použití [autorizovanézdroje](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) je povinné a uživatel musí mít přístup k seznamu prostředků uvedených

Ukázka json výše lze uložit jako (řekněme) sampleScheduledQueryRule.json pro účely této procházky a lze nasadit pomocí [Azure Resource Manager na webu Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Správa výstrah protokolu pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor – [rozhraní API pravidel plánovaných dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) je rozhraní REST API a plně kompatibilní s rozhraním REST Azure Resource Manager. A rutiny prostředí PowerShell uvedené níže jsou k dispozici pro využití [rozhraní API pravidel naplánovaného dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Rutina prostředí Powershell pro vytvoření nového pravidla výstrahy protokolu.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Rutina prostředí Powershell pro aktualizaci existujícího pravidla výstrahy protokolu.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Rutina prostředí Powershell pro vytvoření nebo aktualizaci objektu určujícího parametry zdroje pro výstrahu protokolu. Používá se jako vstup rutinou [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) a [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Rutina prostředí Powershell pro vytvoření nebo aktualizaci objektu určujícíparametry plánu pro výstrahu protokolu. Používá se jako vstup rutinou [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) a [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Rutina prostředí Powershell pro vytvoření nebo aktualizaci objektu určujícího parametry akce pro výstrahu protokolu. Používá se jako vstup rutinou [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) a [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Rutina prostředí Powershell pro vytvoření nebo aktualizaci objektu určujícího parametry skupin akcí pro výstrahu protokolu. Používá se jako vstup rutinou [New-AzScheduledQueryRuleAlertingAction.](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Rutina powershellu pro vytvoření nebo aktualizaci objektu určujícího parametry podmínky aktivační události pro výstrahu protokolu. Používá se jako vstup rutinou [New-AzScheduledQueryRuleAlertingAction.](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Rutina Powershellu pro vytvoření nebo aktualizaci objektu určujícíparametry podmínky aktivační události metriky pro [upozornění protokolu typu metriky měření](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Používá se jako vstup rutinou [New-AzScheduledQueryRuleTriggerCondition.](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition)
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Rutina prostředí Powershell zobrazí seznam existujících pravidel výstrah protokolu nebo konkrétního pravidla výstrahprotokolu
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Rutina prostředí Powershell pro povolení nebo zakázání pravidla výstrahprotokolu
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Rutina prostředí Powershell pro odstranění existujícího pravidla výstrahy protokolu

> [!NOTE]
> Rutiny prostředí ScheduledQueryRules powershellu můžou spravovat jenom pravidla vytvořená rutina sama nebo pomocí rozhraní Azure Monitor – [Rozhraní API pravidel naplánovaných dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Pravidla upozornění protokolu vytvořená pomocí staršího [rozhraní API upozornění služby Log Analytics](api-alerts.md) a starších šablon [uložených vyhledávání a výstrah služby Log Analytics](../insights/solutions-resources-searches-alerts.md) lze spravovat pomocí rutin prostředí ScheduledQueryRules PowerShell pouze po [přepnutí předvoleb rozhraní API pro výstrahy log analytics](alerts-log-api-switch.md).

Dále jsou uvedeny kroky pro vytvoření ukázkového pravidla výstrahy protokolu pomocí rutin prostředí ScheduledQueryRules PowerShell.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Správa výstrah protokolu pomocí rozhraní API nebo rozhraní API

Azure Monitor – [rozhraní API pravidel plánovaných dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) je rozhraní REST API a plně kompatibilní s rozhraním REST Azure Resource Manager. Proto jej lze použít prostřednictvím powershellu pomocí příkazů Správce prostředků pro Azure CLI.


> [!NOTE]
> Protokol upozornění pro Log Analytics lze také spravovat pomocí starší [log Analytics Upozornění API](api-alerts.md) a starší šablony Log [Analytics uložená vyhledávání a výstrahy](../insights/solutions-resources-searches-alerts.md) stejně. Další informace o použití nového rozhraní API ScheduledQueryRules, které je ve výchozím nastavení podrobně popsáno, naleznete v [tématu Přepnutí na nové rozhraní API pro výstrahy analýzy protokolů](alerts-log-api-switch.md).

Výstrahy protokolu aktuálně nemají vyhrazené příkazy příkazu příkazu cli aktuálně; ale jak je znázorněno níže lze použít pomocí příkazu Azure Resource Manager CLI pro ukázkovou šablonu prostředků zobrazené dříve (sampleScheduledQueryRule.json) v části Šablona prostředků:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Při úspěšné operaci bude 201 vráceno do stavu vytvoření nového pravidla výstrahy nebo 200 bude vráceno, pokud bylo změněno existující pravidlo výstrahy.

## <a name="next-steps"></a>Další kroky

* Další informace o [výstrahách protokolů ve výstrahách Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Principy [akcí Webhooku pro výstrahy protokolu](../../azure-monitor/platform/alerts-log-webhook.md)
* Další informace o [přehledech aplikací](../../azure-monitor/app/analytics.md)
* Další informace o [dotazech protokolu](../log-query/log-query-overview.md).
