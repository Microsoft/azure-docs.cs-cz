---
title: Předávání dat úloh Azure Automation do protokolů Azure Monitoru
description: V tomto článku se dozvíte, jak odesílat datové proudy úloh Runbooku a Azure Monitor protokoly.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 6dcd2005971927de30ca96173cb2bdb063e46663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397422"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Předávání dat úloh Azure Automation do protokolů Azure Monitoru

Azure Automation může odesílat datové proudy úloh sady Runbook a streamování úloh do pracovního prostoru Log Analytics. Tento proces nezahrnuje propojení pracovního prostoru a je zcela nezávislý. Protokoly úloh a datové proudy úloh jsou viditelné v Azure Portal nebo v prostředí PowerShell pro jednotlivé úlohy a umožňují provádět jednoduché šetření. Teď díky protokolům Azure Monitor můžete:

* Získejte přehled o stavu úloh automatizace.
* Aktivace e-mailu nebo výstrahy na základě stavu úlohy Runbooku (například selhání nebo pozastaveno).
* Zapište pokročilé dotazy napříč datovými proudy úloh.
* Korelujte úlohy mezi účty Automation.
* Pomocí vlastních zobrazení a vyhledávacích dotazů můžete vizualizovat výsledky Runbooku, stav úlohy Runbooku a další související klíčové ukazatele nebo metriky.

## <a name="prerequisites"></a>Požadavky

Pokud chcete začít odesílat protokoly pro automatizaci Azure Monitor protokolů, budete potřebovat:

* Nejnovější verzi [Azure PowerShell](/powershell/azure/).

* Log Analytics pracovní prostor a ID prostředku. Další informace najdete v tématu [Začínáme s protokoly Azure monitor](../azure-monitor/overview.md).

* ID prostředku vašeho účtu Azure Automation.

## <a name="how-to-find-resource-ids"></a>Jak najít ID prostředků

1. Pomocí následujícího příkazu Najděte ID prostředku pro Azure Automation účet:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Zkopírujte hodnotu pro **ResourceID**.

3. Pomocí následujícího příkazu Najděte ID prostředku Log Analytics pracovního prostoru:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Zkopírujte hodnotu pro **ResourceID**.

Pokud chcete vrátit výsledky z konkrétní skupiny prostředků, zahrňte `-ResourceGroupName` parametr. Další informace najdete v tématu [Get-AzResource](/powershell/module/az.resources/get-azresource).

Pokud máte ve výstupu předchozích příkazů více než jeden účet služby Automation nebo pracovní prostor, můžete najít název a další související vlastnosti, které jsou součástí úplného ID prostředku vašeho účtu Automation, a to následujícím způsobem:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. V Azure Portal na stránce **účty Automation** vyberte svůj účet Automation.
1. Na stránce vybraného účtu Automation v části **Nastavení účtu**vyberte **vlastnosti**.
1. Na stránce **vlastnosti** si všimněte níže uvedených podrobností.

    ![Vlastnosti účtu Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Nastavení diagnostiky automatizace podporuje předávání následujících protokolů platforem a dat metrik:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metriky – celkový počet úloh, celkový počet spuštěných počítačů pro nasazení aktualizací, celkový počet spuštění nasazení aktualizací

Pokud chcete začít odesílat protokoly služby Automation do protokolů Azure Monitor, přečtěte si téma [Vytvoření nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) , které vám pomůže pochopit funkce a metody, které jsou k dispozici pro konfiguraci nastavení diagnostiky pro odesílání protokolů

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Diagnostika Azure Automation v protokolech Azure Monitor vytvoří dva typy záznamů, které jsou označené jako `AzureDiagnostics` . Tabulky v dalších částech jsou příklady záznamů, které Azure Automation generuje, a typy dat, které se zobrazí ve výsledcích prohledávání protokolu.

### <a name="job-logs"></a>Protokoly úloh

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Volající, který inicioval operaci Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| JobId_g |Identifikátor GUID, který identifikuje úlohu Runbooku. |
| ResultType |Stav úlohy runbooku. Možné hodnoty:<br>– Nové<br>– Vytvořeno<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>-Dokončeno |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs. |
| OperationName | Typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha. |
| Prostředek | Název účtu Automation |
| SourceSystem | Systém, který Azure Monitor protokoly použít ke shromáždění dat. Tato hodnota je vždy Azure pro diagnostiku Azure. |
| ResultDescription |Stav výsledku úlohy Runbooku Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená |
| CorrelationId |Identifikátor GUID korelace úlohy Runbooku |
| ResourceId |ID prostředku Azure Automation účtu sady Runbook. |
| SubscriptionId | Identifikátor GUID předplatného Azure pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | Poskytovatel prostředků. Hodnota je MICROSOFT. Automation. |
| ResourceType | Typ prostředku. Hodnota je AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Datové proudy úlohy
| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Volající, který inicioval operaci Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| StreamType_s |Typ datového proudu úlohy. Možné hodnoty:<br>- Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| JobId_g |Identifikátor GUID, který identifikuje úlohu Runbooku. |
| ResultType |Stav úlohy runbooku. Možné hodnoty:<br>– Probíhá |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams. |
| OperationName | Typ operace provedené v Azure Pro automatizaci je tato hodnota úloha. |
| Prostředek | Název účtu Automation. |
| SourceSystem | Systém, který Azure Monitor protokoly použít ke shromáždění dat. Tato hodnota je vždy Azure pro diagnostiku Azure. |
| ResultDescription |Popis, který obsahuje výstupní datový proud z Runbooku. |
| CorrelationId |Identifikátor GUID korelace úlohy Runbooku |
| ResourceId |ID prostředku Azure Automation účtu sady Runbook. |
| SubscriptionId | Identifikátor GUID předplatného Azure pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | Poskytovatel prostředků. Hodnota je MICROSOFT. Automation. |
| ResourceType | Typ prostředku. Hodnota je AUTOMATIONACCOUNTS. |

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Zobrazení protokolů automatizace v protokolech Azure Monitor

Teď, když jste začali odesílat streamy úloh a protokoly automatizace, abyste Azure Monitor protokoly, Podívejme se na to, co můžete s těmito protokoly dělat v Azure Monitorch protokolech.

Pokud chcete zobrazit protokoly, spusťte následující dotaz: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslání e-mailu, když dojde k chybě nebo pozastavení úlohy Runbooku

Následující kroky ukazují, jak nastavit výstrahy v Azure Monitor, které vás upozorní, když se něco pokazilo s úlohou Runbooku.

Chcete-li vytvořit pravidlo výstrahy, začněte tím, že vytvoříte hledání v protokolu pro záznamy úloh sady Runbook, které by měly vyvolat výstrahu. Kliknutím na tlačítko **výstrahy** vytvoříte a nakonfigurujete pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **Zobrazit protokoly**.

2. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Můžete také seskupit podle názvu Runbooku pomocí: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud jste v pracovním prostoru nastavili protokoly z více než jednoho účtu Automation nebo předplatného, můžete své výstrahy seskupit podle předplatného a účtu Automation. Název účtu Automation najdete v `Resource` poli hledání `JobLogs` .

3. Obrazovku **vytvořit pravidlo** otevřete kliknutím na **nové pravidlo výstrahy** v horní části stránky. Další informace o možnostech konfigurace výstrahy najdete v tématu [protokolování výstrah v Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Vyhledá všechny úlohy, které byly dokončeny s chybami.

Kromě upozorňování na chyby můžete zjistit, kdy má úloha Runbooku neukončující chybu. V těchto případech PowerShell vytvoří chybový proud, ale neukončující chyby nezpůsobí, že by vaše úloha mohla pozastavit nebo selhat.

1. V pracovním prostoru Log Analytics klikněte na **protokoly**.

2. Do pole dotazu zadejte `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Klikněte na tlačítko **Hledat** .

### <a name="view-job-streams-for-a-job"></a>Zobrazení datových proudů úloh pro úlohu

Při ladění úlohy můžete také chtít vyhledat streamy úloh. Následující dotaz zobrazí všechny datové proudy pro jednu úlohu s identifikátorem GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Zobrazit historický stav úlohy

Nakonec můžete chtít vizualizovat historii úloh v průběhu času. Pomocí tohoto dotazu můžete v průběhu času vyhledat stav svých úloh.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Graf stavu Log Analytics historické úlohy](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrovat výstup stavu úlohy převedený na objekt JSON

Nedávno jsme změnili chování způsobu, jakým jsou data protokolu automatizace zapsána do `AzureDiagnostics` tabulky ve službě Log Analytics, kde již nedělí vlastnosti JSON na samostatné pole. Pokud jste Runbook nakonfigurovali tak, aby naformátoval objekty ve formátu JSON jako samostatné sloupce, je nutné znovu nakonfigurovat vaše dotazy, aby toto pole analyzovalo na objekt JSON, aby bylo možné tyto vlastnosti přistupovat. To se provádí pomocí [parseJSON](../azure-monitor/log-query/json-data-structures.md#parsejson) pro přístup k určitému prvku JSON ve známé cestě.

Sada Runbook například formátuje vlastnost *ResultDescription* ve výstupním datovém proudu ve formátu JSON s více poli. Pokud chcete vyhledat stav úloh, které jsou ve stavu selhání, jak je uvedeno v poli s názvem **stav**, použijte tento ukázkový dotaz k hledání *ResultDescription* se stavem **selhání**:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics formát JSON historických streamů úloh](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření vyhledávacích dotazů a kontrole protokolů úloh služby Automation pomocí protokolů Azure Monitor najdete [v tématu prohledávání protokolů v protokolech Azure monitor](../azure-monitor/log-query/log-query-overview.md).
* Informace o vytváření a načítání výstupních a chybové zprávy ze sad Runbook najdete v tématu [monitorování výstupu Runbooku](automation-runbook-output-and-messages.md).
* Další informace o spuštění sady Runbook, způsobu monitorování úloh sady Runbook a dalších technických informací naleznete v tématu [spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).
* Další informace o Azure Monitor protokolů a zdrojích shromažďování dat najdete [v tématu shromažďování dat služby Azure Storage v protokolech Azure monitor přehled](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* Pomoc při řešení potíží s Log Analytics najdete v tématu [řešení potíží, proč Log Analytics už neshromažďují data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
