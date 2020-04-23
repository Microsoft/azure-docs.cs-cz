---
title: Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor | Microsoft Docs
description: Pomocí Azure Monitor můžete vytvářet, zobrazovat a spravovat pravidla výstrah protokolů v Azure.
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
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor

## <a name="overview"></a>Přehled
V tomto článku se dozvíte, jak nastavit výstrahy protokolu pomocí rozhraní výstrahy v Azure Portal. Definice pravidla výstrahy je ve třech částech:
- Cíl: konkrétní prostředek Azure, který se má monitorovat
- Kritéria: specifická podmínka nebo logika, která se zobrazila v signálu, by měla aktivovat akci.
- Action: konkrétní volání odeslané příjemci oznámení – e-mail, SMS, Webhook atd.

Pojem **výstrahy protokolu** , které popisují výstrahy v případě, že je signál dotazem protokolu v [log Analyticsm pracovním prostoru](../learn/tutorial-viewdata.md) nebo [Application Insights](../app/analytics.md). Přečtěte si další informace o funkcích, terminologii a typech z [Upozornění protokolů – přehled](alerts-unified-log.md).

> [!NOTE]
> Oblíbená data protokolu z [Log Analytics pracovního prostoru](../../azure-monitor/learn/tutorial-viewdata.md) jsou teď dostupná také na platformě metrik v Azure monitor. Pro zobrazení podrobností, [Výstraha metriky pro protokoly](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Správa výstrah protokolu z Azure Portal

Podrobný návod, jak používat výstrahy protokolu pomocí rozhraní Azure Portal.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Vytvořte pravidlo upozornění protokolu s Azure Portal

1. Na [portálu](https://portal.azure.com/)vyberte **monitorování** a v části Monitorování – zvolte **výstrahy**.

    ![Monitorování](media/alerts-log/AlertsPreviewMenu.png)

1. Kliknutím na tlačítko **nové pravidlo výstrahy** vytvoříte novou výstrahu v Azure.

    ![Přidat výstrahu](media/alerts-log/AlertsPreviewOption.png)

1. Oddíl vytvořit výstrahu se zobrazí se třemi částmi, které se skládají z: *definovat podmínku upozornění*, *definovat podrobnosti výstrahy*a *definovat skupinu akcí*.

    ![Vytvořit pravidlo](media/alerts-log/AlertsPreviewAdd.png)

1. Určete podmínku upozornění pomocí odkazu **Vybrat prostředek** a zadáním cíle výběrem prostředku. Filtrovat podle výběru _předplatného_, _typu prostředku_a požadovaného _prostředku_.

   > [!NOTE]
   > Pro vytvoření upozornění protokolu – před pokračováním ověřte, že je signál **protokolu** pro vybraný prostředek k dispozici.
   >  ![Vybrat prostředek](media/alerts-log/Alert-SelectResourceLog.png)

1. *Výstrahy protokolu*: Ujistěte se, že **typ prostředku** je zdroj analýz, jako je *Log Analytics* nebo *Application Insights* a typ signálu jako **log**, potom klikněte na *hotový* **prostředek** . V dalším kroku použijte tlačítko **Přidat kritéria** k zobrazení seznamu možností signálu dostupných pro prostředek a ze zvolené možnosti **hledání vlastního protokolu** v seznamu signálů pro vybranou službu log monitor, jako je *Log Analytics* nebo *Application Insights*.

   ![Výběr prostředku – hledání vlastního protokolu](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Seznamy výstrah můžou importovat dotaz Analytics jako typ signálu – **protokol (uložený dotaz)**, jak vidíte na obrázku výše. Takže si uživatelé můžou dokonalý dotaz analyzovat a pak je uložit pro budoucí použití v upozorněních – další podrobnosti o použití ukládání dotazů, které jsou k dispozici [v Azure monitor](../log-query/log-query-overview.md) nebo ve [sdílených dotazech služby Application Insights Analytics](../app/app-insights-overview.md).

1. *Výstrahy protokolu*: po výběru se dotaz na upozorňování dá zadat v poli **vyhledávacího dotazu** . Pokud je syntaxe dotazu nesprávná, pole zobrazí ČERVENou chybu. Pokud je syntaxe dotazu správná – pro odkaz na historické údaje uvedeného dotazu se zobrazí jako graf s možností pro vylepšení časového okna z posledních šesti hodin do minulého týdne.

    ![Konfigurovat pravidlo výstrahy](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > Historická vizualizace dat se dá zobrazit, jenom pokud výsledky dotazu mají časové údaje. Pokud je výsledkem dotazu souhrnná data nebo konkrétní hodnoty sloupce – stejné, jako v jednotném vykreslení se zobrazí stejné.
   > Pro měření metriky, které protokolují výstrahy pomocí Application Insights nebo [Přepnutí na nové rozhraní API](alerts-log-api-switch.md), můžete určit, která konkrétní proměnná má seskupovat data pomocí možnosti **agregace on** ; Jak je znázorněno níže:
   > 
   > ![možnost agregace při](media/alerts-log/aggregate-on.png)

1. *Výstrahy protokolu*: když je vybraná vizualizace, můžete **logiku výstrahy** vybrat ze zobrazených možností podmínka, agregace a konečné prahové hodnoty. Nakonec zadejte v logice čas, který se má vyhodnotit pro zadanou podmínku, a to za použití možnosti **perioda** . Společně s tím, jak často se má výstraha spouštět, vyberte **četnost**. **Výstrahy protokolu** můžou být založené na:
    - [Počet záznamů](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): výstraha se vytvoří, pokud je počet záznamů vrácených dotazem větší nebo menší než poskytnutá hodnota.
    - [Měření metriky](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): je vytvořena výstraha, pokud každá *agregovaná hodnota* ve výsledcích překračuje stanovenou prahovou hodnotu a je *seskupena podle* zvolené hodnoty. Počet porušení pro výstrahu je počet překročení prahové hodnoty ve zvoleném časovém období. Můžete určit celkový počet porušení pro libovolnou kombinaci porušení v rámci sady výsledků nebo po sobě jdoucí porušení předpisů, aby se v následných vzorcích vyžadovalo, aby v nich došlo k narušení.


1. Jako druhý krok definujte v poli **název pravidla výstrahy** název vaší výstrahy spolu s **popisem** podrobností o konkrétní výstraze a hodnotě **závažnosti** z dostupných možností. Tyto podrobnosti se znovu použijí ve všech e-mailech výstrah, oznámeních nebo nabízených oznámeních Azure Monitor. Kromě toho může uživatel zvolit, že se pravidlo výstrahy při vytváření okamžitě aktivuje, a to tak, že **po vytvoření zvolí pravidlo Povolit** .

    V případě **výstrah pouze protokolu** jsou v podrobnostech výstrahy k dispozici některé další funkce:

    - **Potlačit výstrahy**: Když zapnete potlačení pro pravidlo výstrahy, akce pro pravidlo se po vytvoření nové výstrahy vypnou po určenou dobu. Pravidlo je stále spuštěno a vytváří záznamy výstrah za předpokladu, že jsou splněna kritéria. Umožňuje vám čas opravit problém bez spuštění duplicitních akcí.

        ![Potlačit výstrahy pro výstrahy protokolu](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Zadejte potlačit hodnotu výstrahy větší než frekvence výstrahy, aby se zajistilo, že se oznámení zastaví bez překrytí.

1. Jako třetí a poslední krok určete, jestli se má pro pravidlo upozornění při splnění podmínky upozornění aktivovat jakákoli **Skupina akcí** . Můžete zvolit libovolnou existující skupinu akcí s výstrahou nebo vytvořit novou skupinu akcí. V závislosti na vybrané skupině akcí se při aktivaci výstrahy Azure: Odeslat e-mail, poslat SMS, zavolat webhookům, opravit je pomocí runbooků Azure, nahrajte ho do nástroje ITSM atd. Přečtěte si další informace o [skupinách akcí](action-groups.md).

    > [!NOTE]
    > Omezení počtu datových částí sady Runbook aktivovaných pro výstrahy protokolu pomocí skupin akcí Azure najdete v tématu [omezení služby předplatného Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .

    Pro **výstrahy protokolu** jsou k dispozici některé další funkce pro přepsání výchozích akcí:

    - **E-mailové oznámení**: přepíše *Předmět e-mailu* v e-mailu odeslaného prostřednictvím skupiny akcí; Pokud v dané skupině akcí existuje jedna nebo více e-mailových akcí. Text e-mailu nelze upravit a toto pole **není** pro e-mailovou adresu.
    - **Zahrnout vlastní datovou část JSON**: přepíše Webhook JSON používaný skupinami akcí; Pokud v dané skupině akcí existuje jedna nebo více akcí Webhooku. Uživatel může určit formát JSON, který se má použít pro všechny Webhooky nakonfigurované v přidružené skupině akcí. Další informace o formátech webhooků najdete v tématu [Akce Webhooku pro výstrahy protokolu](../../azure-monitor/platform/alerts-log-webhook.md). Možnost zobrazení Webhooku je k dispozici pro kontrolu formátu pomocí ukázkových dat JSON.

        ![Přepsání akcí pro výstrahy protokolu](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Pokud jsou všechna pole platná a se zelenou značkou, můžete kliknout na tlačítko **vytvořit pravidlo upozornění** a v Azure monitor-výstrahy se vytvoří výstraha. Všechny výstrahy si můžete prohlédnout na řídicím panelu výstrahy.

     ![Vytvoření pravidla](media/alerts-log/AlertsPreviewCreate.png)

     Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

Uživatelé můžou také dokončit dotaz Analytics v [Log Analytics](../log-query/portals.md) a pak ho vložit, aby se vytvořila výstraha prostřednictvím tlačítka nastavit výstrahu. potom postupujte podle pokynů v kroku 6 výše v předchozím kurzu.

 ![Výstraha Log Analytics-set](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Zobrazení & Správa výstrah protokolu v Azure Portal

1. Na [portálu](https://portal.azure.com/)vyberte **monitorování** a v části Monitorování – zvolte **výstrahy**.

1. Zobrazuje se **řídicí panel výstrahy** – všechny výstrahy Azure (včetně výstrah protokolu) se zobrazují v jednotném panelu. včetně všech instancí, kdy se pravidlo upozornění protokolu vyvolalo. Další informace najdete v tématu [Alert Management](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Pravidla upozornění protokolů se skládají z vlastní logiky založené na dotazech, kterou poskytují uživatelé, a proto bez vyřešeného stavu. Vzhledem k tomu, že všechny podmínky zadané v pravidle protokolu výstrahy jsou splněné, je aktivována.

1. Výběrem tlačítka **Spravovat pravidla** na horním panelu přejděte do části Správa pravidel – kde jsou uvedena všechna vytvořená pravidla výstrahy. včetně výstrah, které byly zakázány.
    ![Správa pravidel výstrah](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Správa výstrah protokolu pomocí šablony prostředků Azure

Výstrahy protokolu v Azure Monitor jsou přidruženy k typu `Microsoft.Insights/scheduledQueryRules/`prostředku. Další informace o tomto typu prostředku najdete v tématu [Azure monitor – Reference k rozhraní API pro pravidla dotazů na naplánované dotazy](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Výstrahy protokolu pro Application Insights nebo Log Analytics můžete vytvořit pomocí [naplánovaných rozhraní API pravidel dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Výstrahy protokolu pro Log Analytics je taky možné spravovat pomocí starších [Log Analytics rozhraní API pro upozornění](api-alerts.md) a starších šablon [Log Analytics uložená hledání a upozornění](../insights/solutions-resources-searches-alerts.md) . Další informace o tom, jak se ve výchozím nastavení používá nové rozhraní API ScheduledQueryRules, najdete v tématu [Přepnutí na nové rozhraní API pro Log Analytics výstrahy](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Ukázka vytvoření upozornění protokolu pomocí šablony prostředků Azure

Následuje struktura šablony prostředků založené na [vytváření pravidel pro vytváření pravidel dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) pomocí standardního vyhledávacího dotazu protokolu s [počtem výstrah protokolu typu výsledků](alerts-unified-log.md#number-of-results-alert-rules)a ukázkovou datovou sadou jako proměnné.

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

Vzorový JSON výše se dá uložit jako (řekněme) sampleScheduledQueryRule. JSON pro účely tohoto procházení a dá se nasadit pomocí [Azure Resource Manager v Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Výstraha protokolu s dotazem mezi prostředky pomocí šablony prostředků Azure

Následuje struktura šablony prostředků založené na [vytváření pravidel pro vytváření pravidel dotazu](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) pomocí [vyhledávacího dotazu protokolu](../../azure-monitor/log-query/cross-workspace-query.md) pro [měření metriky protokolu typu měření metriky](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)s ukázkovou datovou sadou jako proměnné.

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
> Při použití dotazu mezi prostředky v upozornění protokolu je použití [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) povinné a uživatel musí mít přístup k seznamu uvedených prostředků.

Vzorový JSON výše se dá uložit jako (řekněme) sampleScheduledQueryRule. JSON pro účely tohoto procházení a dá se nasadit pomocí [Azure Resource Manager v Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Správa výstrah protokolu pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor – [rozhraní API pro plánovaná pravidla dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) je REST API a plně kompatibilní s Azure Resource Manager REST API. A níže uvedené rutiny prostředí PowerShell jsou k dispozici pro využití [naplánovaných rozhraní API pravidel dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell Rutina pro vytvoření nového pravidla upozornění protokolu
1. Rutina [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell aktualizuje existující pravidlo upozornění protokolu.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu, který určuje zdrojové parametry výstrahy protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) a [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry plánu pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) a [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry akce pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) a [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu, který určuje parametry skupin akcí pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry podmínky triggeru pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry podmínky triggeru metriky pro [výstrahu protokolu typu měření metriky](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell Rutina pro výpis stávajících pravidel upozornění protokolů nebo konkrétního pravidla výstrahy protokolu
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : rutina prostředí PowerShell pro povolení nebo zakázání pravidla upozornění protokolu
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell Rutina pro odstranění existujícího pravidla upozornění protokolu

> [!NOTE]
> Rutiny PowerShellu pro ScheduledQueryRules můžou spravovat jenom pravidla vytvořená samotným rutinou nebo [rozhraní API pro naplánovaná pravidla dotazování](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)pomocí Azure monitor. Pravidla upozornění protokolů vytvořená pomocí starších [Log Analytics rozhraní API výstrah](api-alerts.md) a starších šablon [Log Analytics uložených hledání a upozornění](../insights/solutions-resources-searches-alerts.md) se dají spravovat pomocí rutin prostředí ScheduledQueryRules PowerShellu, jenom když uživatel [přepne předvolby rozhraní API pro Log Analytics výstrahy](alerts-log-api-switch.md).

V následujícím seznamu jsou uvedené kroky pro vytvoření ukázkového pravidla upozornění protokolu pomocí rutin PowerShellu pro scheduledQueryRules.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Správa výstrah protokolu pomocí rozhraní příkazového řádku nebo rozhraní příkazového řádku

Azure Monitor – [rozhraní API pro plánovaná pravidla dotazů](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) je REST API a plně kompatibilní s Azure Resource Manager REST API. Proto se dá použít přes PowerShell pomocí příkazů Správce prostředků pro Azure CLI.


> [!NOTE]
> Výstrahy protokolu pro Log Analytics je taky možné spravovat pomocí starších [Log Analytics rozhraní API pro upozornění](api-alerts.md) a starších šablon [Log Analytics uložená hledání a upozornění](../insights/solutions-resources-searches-alerts.md) . Další informace o tom, jak se ve výchozím nastavení používá nové rozhraní API ScheduledQueryRules, najdete v tématu [Přepnutí na nové rozhraní API pro Log Analytics výstrahy](alerts-log-api-switch.md).

Výstrahy protokolu aktuálně nemají vyhrazené příkazy rozhraní příkazového řádku. Jak je znázorněno níže, je možné použít příkaz Azure Resource Manager CLI pro ukázkovou šablonu prostředků, která se v oddílu šablony prostředků zobrazila dříve (sampleScheduledQueryRule. JSON):

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Po úspěšné operaci bude 201 vrácen do stavu nové vytvoření pravidla výstrahy, nebo pokud se změní existující pravidlo upozornění, bude vrácena 200.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [výstrahách protokolů v Azure Alerts](../../azure-monitor/platform/alerts-unified-log.md) .
* Porozumění [akcím Webhooku pro výstrahy protokolu](../../azure-monitor/platform/alerts-log-webhook.md)
* Další informace o [Application Insights](../../azure-monitor/app/analytics.md)
* Přečtěte si další informace o [dotazech protokolu](../log-query/log-query-overview.md).
