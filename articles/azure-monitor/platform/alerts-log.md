---
title: Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor | Microsoft Docs
description: Použití Azure Monitor k vytvoření, zobrazení a správě pravidel upozornění protokolu
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 950482a96604165a8f1ff20cab9e3eafe1224c90
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028994"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Vytváření, zobrazování a správa výstrah protokolu pomocí Azure Monitor

## <a name="overview"></a>Přehled

Výstrahy protokolu umožňují uživatelům pomocí [log Analyticsho](../log-query/log-analytics-tutorial.md) dotazu vyhodnotit protokoly prostředků každou nastavenou frekvencí a vyvolat výstrahu na základě výsledků. Pravidla mohou aktivovat jednu nebo více akcí pomocí [skupin akcí](./action-groups.md). [Přečtěte si další informace o funkcích a terminologii výstrah protokolu](alerts-unified-log.md).

V tomto článku se dozvíte, jak vytvářet a spravovat výstrahy protokolu pomocí Azure Monitor. Pravidla výstrah jsou definována třemi součástmi:
- Cíl: konkrétní prostředek Azure, který se má monitorovat.
- Kritéria: logika, která má být vyhodnocena. Pokud je splněno, výstraha se aktivuje.  
- Akce: oznámení nebo automatizace – e-mail, SMS, Webhook atd.

Pravidla upozornění protokolů můžete vytvořit také pomocí šablon Azure Resource Manager, které jsou popsány v [samostatném článku](alerts-log-create-templates.md).

> [!NOTE]
> Data protokolu z [Log Analytics pracovního prostoru](../log-query/log-analytics-tutorial.md) lze odeslat do úložiště metrik Azure monitor. Výstrahy metrik mají [různé chování](alerts-metric-overview.md), což může být více žádoucí v závislosti na datech, se kterými pracujete. Informace o tom, co a jak můžete směrovat protokoly do metrik, najdete v tématu [Upozornění na metriky pro protokoly](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Vytvořte pravidlo upozornění protokolu s Azure Portal

Tady je postup, jak začít psát dotazy na výstrahy:

1. Přejít na prostředek, na kterém byste chtěli upozornit. Pokud je to možné, zvažte nastavení pravidel upozornění na více prostředků výběrem předplatného nebo rozsahu skupiny prostředků. Upozornění na více prostředků snižuje náklady a nutnost spravovat více pravidel výstrah.
1. V části **monitorování** vyberte **protokoly**.
1. Zadejte dotaz na data protokolu, která mohou problém indikovat. V [tématu Příklady dotazů na výstrahy](../log-query/example-queries.md) můžete pochopit, co můžete zjistit nebo začít [psát vlastní dotaz](../log-query/log-analytics-tutorial.md). Naučíte se také, [jak vytvářet optimalizované dotazy na upozornění](alerts-log-query.md).
1. Kliknutím na tlačítko + nové pravidlo výstrahy spustíte tok vytváření výstrah.

    ![Výstraha Log Analytics-set](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Při použití režimu přístupu k prostředkům pro protokoly, které běží na několika prostředcích pomocí skupiny prostředků nebo oboru předplatného, se doporučuje vytvářet výstrahy ve velkém měřítku. Upozorňování se škálováním snižuje režijní náklady na správu pravidel. Aby bylo možné cílit na prostředky, uveďte do výsledků sloupec ID prostředku. [Přečtěte si další informace o rozdělení výstrah podle dimenzí](alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Výstraha protokolu pro Log Analytics a Application Insights

1. Pokud je syntaxe dotazu správná, pak se historická data pro dotaz zobrazí jako graf s možností upravit období grafu za posledních šest hodin a minulý týden.
 
    Pokud výsledky dotazu obsahují souhrnná data nebo sloupce specifické pro [projekt](/azure/kusto/query/projectoperator) bez časového sloupce, zobrazí se v grafu jedna hodnota.

    ![Konfigurovat pravidlo výstrahy](media/alerts-log/AlertsPreviewAlertLog.png)

1. Vyberte časový rozsah, ve kterém se má vyhodnotit zadaná podmínka, a to pomocí možnosti [**perioda**](alerts-unified-log.md#query-time-range) .

1. Výstrahy protokolu můžou být založené na dvou typech [**měr**](alerts-unified-log.md#measure):
    1. **Počet výsledků** – počet záznamů vrácených dotazem.
    1. **Měření**  -  metriky *Agregovaná hodnota* počítaná pomocí souhrnu seskupené podle zvolených výrazů a výběru z [přihrádky ()](/azure/kusto/query/binfunction) Příklad:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. V případě logiky výstrah pro měření metriky můžete volitelně určit, jak mají být [výstrahy rozděleny podle dimenzí](alerts-unified-log.md#split-by-alert-dimensions) pomocí možnosti **agregace zapnuto** . Výraz seskupování řádků musí být jedinečný a seřazený.

    > [!NOTE]
    > Funkce AS [bin ()](/azure/kusto/query/binfunction) může mít za následek nerovnoměrné časové intervaly, služba Alert automaticky převede funkci [bin ()](/azure/kusto/query/binfunction) na [bin_at ()](/azure/kusto/query/binatfunction) s odpovídajícím časem za běhu, aby se zajistilo, že výsledky budou s pevným bodem.

    > [!NOTE]
    > Dimenze rozdělené podle výstrah jsou k dispozici pouze pro aktuální rozhraní scheduledQueryRules API. Pokud používáte starší [rozhraní API Log Analytics výstrah](api-alerts.md), budete muset přepnout. [Přečtěte si další informace o přepínání](./alerts-log-api-switch.md). Upozorňování na střed prostředků ve velkém měřítku se podporuje jenom ve verzi rozhraní API `2020-05-01-preview` a výše.

    ![možnost agregace při](media/alerts-log/aggregate-on.png)

1. Dále na základě dat ve verzi Preview nastavte [ **operátor**, **prahovou hodnotu**](alerts-unified-log.md#threshold-and-operator)a [**frekvenci**](alerts-unified-log.md#frequency).

1. Volitelně můžete také nastavit [počet porušení pro aktivaci výstrahy](alerts-unified-log.md#number-of-violations-to-trigger-alert) pomocí **celkového nebo po sobě jdoucích porušení**.

1. Vyberte **Hotovo**. 

1. Zadejte název a **Popis** **pravidla výstrahy** a vyberte **závažnost** výstrahy. Tyto podrobnosti se používají ve všech akcích výstrah. Kromě toho můžete zvolit možnost neaktivovat pravidlo výstrahy při vytváření výběrem možnosti **Povolit pravidlo při vytvoření**.

1. Vyberte, zda chcete po spuštění výstrahy potlačit akce s pravidly, a použijte možnost [**potlačit výstrahy**](alerts-unified-log.md#state-and-resolving-alerts) . Pravidlo se pořád spustí a vytvoří upozornění, ale akce se neaktivují, aby se zabránilo hluku. Hodnota akcí ztlumení musí být větší, než frekvence výstrahy, která má být účinná.

    ![Potlačit výstrahy pro výstrahy protokolu](media/alerts-log/AlertsPreviewSuppress.png)

1. Určete, zda má pravidlo výstrahy aktivovat jednu nebo více [**skupin akcí**](action-groups.md#webhook) , pokud je splněna podmínka upozornění.

    > [!NOTE]
    > Omezení akcí, které je možné provést, najdete v části [omezení služby předplatného Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .  

1. Volitelně můžete upravit akce v pravidlech upozornění protokolu:

    - **Předmět vlastního e-mailu**: přepíše *Předmět e* -mailu e-mailových akcí. Text e-mailu nelze upravit a toto pole **není pro e-mailové adresy**.
    - **Zahrnout vlastní datovou část JSON**: přepíše Webhook JSON používaný skupinami akcí, když skupina akcí obsahuje akci Webhooku. Přečtěte si další informace o [akci Webhooku pro výstrahy protokolu](./alerts-log-webhook.md).

    ![Přepsání akcí pro výstrahy protokolu](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Pokud jsou všechna pole správně nastavená, můžete kliknout na tlačítko **vytvořit pravidlo výstrahy** a vytvoří se výstraha.

    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

    ![Vytvoření pravidla](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Vytváření výstrahy protokolu pro Log Analytics a Application Insights ze správy výstrah

> [!NOTE]
> Vytváření ze správy výstrah se v současné době nepodporuje u protokolů orientovaných na prostředky.

1. Na [portálu](https://portal.azure.com/)vyberte **monitorování** a pak zvolte **výstrahy**.

    ![Monitorování](media/alerts-log/AlertsPreviewMenu.png)

1. Vyberte **nové pravidlo výstrahy**. 

    ![Přidat výstrahu](media/alerts-log/AlertsPreviewOption.png)

1. Zobrazí se podokno **vytvořit výstrahu** . Obsahuje čtyři části: 
    - Prostředek, na který se výstraha vztahuje.
    - Podmínka, která má být zkontrolována
    - Akce, které se mají provést, pokud je podmínka pravdivá
    - Podrobnosti o pojmenování a popsání výstrahy. 

    ![Vytvořit pravidlo](media/alerts-log/AlertsPreviewAdd.png)

1. Stiskněte tlačítko **Vybrat prostředek** . Filtrovat podle výběru *předplatného*, *typu prostředku* a výběru prostředku. Zajistěte, aby měl prostředek k dispozici protokoly.

   ![Vybrat prostředek](media/alerts-log/Alert-SelectResourceLog.png)

1. Pak použijte tlačítko Přidat **podmínku** k zobrazení seznamu možností signálu dostupných pro daný prostředek. Vyberte možnost **hledání vlastního protokolu** .

   ![Výběr prostředku – hledání vlastního protokolu](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > Portál výstrahy obsahuje seznam uložených dotazů z Log Analytics a Application Insights a lze je použít jako dotazy na šablony.

1. Po výběru do pole **vyhledávací dotaz** zapište, vložte nebo upravte dotaz na upozornění.

1. Pokračujte dalším postupem popsaným v [poslední části](#log-alert-for-log-analytics-and-application-insights).

### <a name="log-alert-for-all-other-resource-types"></a>Výstraha protokolu pro všechny ostatní typy prostředků

> [!NOTE]
> Pro verzi rozhraní API `2020-05-01-preview` a výstrahy protokolu orientované na prostředky se momentálně neúčtují žádné další poplatky.  Ceny pro funkce, které jsou ve verzi Preview, budou v budoucnu ohlášeny a oznámení poskytované před zahájením fakturace. Pokud se rozhodnete dál používat novou verzi rozhraní API a výstrahy protokolu orientované na prostředky po období oznámení, bude se vám účtovat příslušná sazba.

1. Začněte na kartě **Podmínka** :

    1. Ověřte správnost [**míry**](alerts-unified-log.md#measure), [**typu agregace**](alerts-unified-log.md#aggregation-type)a [**členitosti agregace**](alerts-unified-log.md#aggregation-granularity) . 
        1. Ve výchozím nastavení pravidlo počítá počet výsledků za posledních 5 minut.
        1. Pokud zjistíme souhrnné výsledky dotazu, pravidlo se během několika sekund aktualizuje automaticky a zachytí se.

    1. V případě potřeby vyberte v případě potřeby možnost [rozdělování výstrah podle dimenzí](alerts-unified-log.md#split-by-alert-dimensions): 
       - **Sloupec ID prostředku** je vybrán automaticky, pokud byl zjištěn, a změní kontext výstrahy aktivované na prostředek záznamu. 
       - Pokud chcete aktivovat výstrahy pro předplatné nebo skupiny prostředků, můžete zrušit výběr **sloupce ID prostředku** . Zrušení výběru je užitečné v případě, že výsledky dotazu jsou založené na různých prostředcích. Například dotaz, který kontroluje, jestli 80% virtuálních počítačů ve skupině prostředků má vysoké využití procesoru.
       - Pro libovolný typ sloupce Number nebo text se dá vybrat také až šest dalších rozdělení pomocí tabulky Dimensions.
       - Výstrahy se aktivují samostatně podle rozdělení na základě jedinečných kombinací a datové části výstrahy obsahují tyto informace.
    
        ![Výběr parametrů agregace a rozdělení](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. Graf **náhledu** zobrazuje výsledky vyhodnocení dotazu v průběhu času. Můžete změnit období grafu nebo vybrat jinou časovou řadu, která je výsledkem jedinečného rozdělení výstrahy podle dimenzí.

        ![Náhled grafu](media/alerts-log/preview-chart.png)

    1. Dále na základě dat ve verzi Preview nastavte **logiku výstrah**. [ **Operátor**, **prahová hodnota**](alerts-unified-log.md#threshold-and-operator)a [**frekvence**](alerts-unified-log.md#frequency).

        ![Náhled grafu s použitím prahové hodnoty a logiky výstrah](media/alerts-log/chart-and-alert-logic.png)

    1. Volitelně můžete nastavit [**počet porušení pro aktivaci upozornění**](alerts-unified-log.md#number-of-violations-to-trigger-alert) v části **Upřesnit možnosti** .
    
        ![Upřesnit možnosti](media/alerts-log/advanced-options.png)

1. Na kartě **Akce** vyberte nebo vytvořte požadované [skupiny akcí](action-groups.md).

    ![Karta akce](media/alerts-log/actions-tab.png)

1. Na kartě **Podrobnosti** definujte **Podrobnosti pravidla výstrahy** a **Podrobnosti projektu**. Volitelně můžete nastavit, zda chcete **Spustit nyní**, nebo [**ztlumení akcí**](alerts-unified-log.md#state-and-resolving-alerts) po určité době, kdy pravidlo výstrahy bude aktivováno.

    > [!NOTE]
    > Pravidla upozornění protokolu jsou momentálně Bezstavová a aktivují se akce pokaždé, když je vytvořena výstraha, pokud není ztlumený.

    ![Karta Podrobnosti](media/alerts-log/details-tab.png)

1. Na kartě **značky** nastavte všechny požadované značky v prostředku pravidla výstrahy.

    ![Karta značky](media/alerts-log/tags-tab.png)

1. Na kartě **Revize + vytvořit** se spustí ověřování a bude informovat o všech problémech. Zkontrolujte a schvalte definici pravidla.
1. Pokud jsou všechna pole správná, vyberte tlačítko **vytvořit** a dokončete vytvoření pravidla upozornění. Všechny výstrahy lze zobrazit ze správy výstrah.
 
    ![Karta pro kontrolu a vytvoření](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Zobrazení & Správa výstrah protokolu v Azure Portal

1. Na [portálu](https://portal.azure.com/)vyberte příslušný prostředek nebo **monitorovaná** služba. Pak v části monitorování vyberte **výstrahy** .

1. Správa výstrah zobrazuje všechny aktivované výstrahy. [Přečtěte si další informace o správě výstrah](alerts-managing-alert-instances.md).

    > [!NOTE]
    > Pravidla upozornění protokolu jsou momentálně [Bezstavová a neřeší](alerts-unified-log.md#state-and-resolving-alerts)se.

1. V horním panelu vyberte tlačítko **Spravovat pravidla výstrah** a upravte pravidla:

    ![ Správa pravidel výstrah](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Správa výstrah protokolu pomocí prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell se v současnosti ve verzi rozhraní API nepodporuje. `2020-05-01-preview`

Níže uvedené rutiny prostředí PowerShell jsou k dispozici pro správu pravidel pomocí [naplánovaných rozhraní API pravidel dotazů](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell Rutina pro vytvoření nového pravidla upozornění protokolu
- Rutina [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell aktualizuje existující pravidlo upozornění protokolu.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu, který určuje zdrojové parametry výstrahy protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry plánu pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry akce pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) a [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu, který určuje parametry skupin akcí pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry podmínky triggeru pro výstrahu protokolu. Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : rutina prostředí PowerShell pro vytvoření nebo aktualizaci objektu určujícího parametry podmínky triggeru metriky pro [výstrahu protokolu typu měření metriky](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value). Používá se jako vstup pomocí rutiny [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell Rutina pro výpis stávajících pravidel upozornění protokolů nebo konkrétního pravidla výstrahy protokolu
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : rutina prostředí PowerShell pro povolení nebo zakázání pravidla upozornění protokolu
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell Rutina pro odstranění existujícího pravidla upozornění protokolu

> [!NOTE]
> Rutiny PowerShellu pro ScheduledQueryRules můžou spravovat jenom pravidla vytvořená v aktuálně [naplánovaných rozhraních API pravidel dotazů](/rest/api/monitor/scheduledqueryrules/). Pravidla upozornění protokolu vytvořená pomocí [rozhraní API pro upozornění](api-alerts.md) starší verze Log Analytics se dají spravovat jenom pomocí prostředí PowerShell po [Přepnutí na naplánovaná pravidla dotazů na rozhraní API](alerts-log-api-switch.md).

Tady je příklad kroků pro vytvoření pravidla upozornění protokolu pomocí prostředí PowerShell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

Můžete také vytvořit výstrahu protokolu pomocí [šablony a souborů parametrů](./alerts-log-create-templates.md) pomocí prostředí PowerShell:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Správa výstrah protokolu pomocí rozhraní příkazového řádku

> [!NOTE]
> Podpora Azure CLI je dostupná jenom pro rozhraní API scheduledQueryRules `2020-05-01-preview` a novější. Verze rozhraní API zkontrolují může používat Azure Resource Manager CLI se šablonami, jak je popsáno níže. Pokud používáte starší verzi [rozhraní API Log Analytics výstrah](api-alerts.md), budete muset přepnout na použití rozhraní příkazového řádku. [Přečtěte si další informace o přepínání](./alerts-log-api-switch.md).

Předchozí části popisují, jak vytvářet, zobrazovat a spravovat pravidla upozornění protokolu pomocí Azure Portal. V této části se dozvíte, jak to samé provést pomocí [Azure CLI](/cli/azure/get-started-with-azure-cli)pro různé platformy. Nejrychlejší způsob, jak začít používat Azure CLI, je prostřednictvím [Azure Cloud Shell](../../cloud-shell/overview.md). V tomto článku budeme používat Cloud Shell.

1. Přejít na Azure Portal vyberte **Cloud Shell**.

1. V příkazovém řádku můžete ``--help`` k získání dalších informací o příkazu a jeho použití použít příkazy s možností. Například následující příkaz zobrazí seznam příkazů, které jsou k dispozici pro vytváření, zobrazování a správu výstrah protokolu:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. Můžete vytvořit pravidlo upozornění protokolu, které monitoruje počet chyb systémových událostí:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. Všechny výstrahy protokolu ve skupině prostředků můžete zobrazit pomocí následujícího příkazu:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. Podrobnosti konkrétního pravidla výstrahy protokolu můžete zobrazit pomocí názvu nebo ID prostředku pravidla:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. Pravidlo upozornění protokolu můžete zakázat pomocí následujícího příkazu:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. Pravidlo upozornění protokolu můžete odstranit pomocí následujícího příkazu:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Můžete také použít Azure Resource Manager CLI se soubory [šablon](./alerts-log-create-templates.md) :

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Po úspěšném vytvoření se vrátí 201. Po úspěšném dokončení aktualizace se vrátí 200.

## <a name="next-steps"></a>Další kroky

* Přečtěte si informace o [výstrahách protokolu](./alerts-unified-log.md).
* Vytvářejte výstrahy protokolu pomocí [šablon Azure Resource Manager](./alerts-log-create-templates.md).
* Porozumění [akcím Webhooku pro výstrahy protokolu](./alerts-log-webhook.md)
* Přečtěte si další informace o [dotazech protokolu](../log-query/log-query-overview.md).
