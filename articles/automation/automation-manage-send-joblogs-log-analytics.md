---
title: Předávání dat úloh Azure Automation do protokolů Azure Monitoru
description: V tomto článku se dozvíte, jak odesílat datové proudy úloh Runbooku a Azure Monitor protokoly.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 6cd1983a6aa1ea942fb6f3154d8bb99e255f51e9
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715439"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Předávání dat úloh Azure Automation do protokolů Azure Monitoru

Azure Automation může odesílat datové proudy úloh sady Runbook a streamování úloh do pracovního prostoru Log Analytics. Tento proces nezahrnuje propojení pracovního prostoru a je zcela nezávislý. Protokoly úloh a datové proudy úloh jsou viditelné v Azure Portal nebo v prostředí PowerShell pro jednotlivé úlohy a umožňují provádět jednoduché šetření. Teď díky protokolům Azure Monitor můžete:

* Získejte přehled o stavu úloh automatizace.
* Aktivace e-mailu nebo výstrahy na základě stavu úlohy Runbooku (například selhání nebo pozastaveno).
* Zapište pokročilé dotazy napříč datovými proudy úloh.
* Korelujte úlohy mezi účty Automation.
* Pomocí vlastních zobrazení a vyhledávacích dotazů můžete vizualizovat výsledky Runbooku, stav úlohy Runbooku a další související klíčové ukazatele nebo metriky.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Předpoklady a požadavky na nasazení

Pokud chcete začít odesílat protokoly pro automatizaci Azure Monitor protokolů, budete potřebovat:

* Nejnovější verzi [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Pracovní prostor služby Log Analytics. Další informace najdete v tématu [Začínáme s protokoly Azure monitor](../log-analytics/log-analytics-get-started.md).
* ID prostředku pro váš účet Azure Automation.

Pomocí následujícího příkazu Najděte ID prostředku pro Azure Automation účet:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Chcete-li najít ID prostředku pro Log Analytics pracovní prostor, spusťte následující příkaz prostředí PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Pokud máte ve výstupu předchozích příkazů více než jeden účet služby Automation nebo pracovní prostor, vyhledejte název, který musíte nakonfigurovat, a zkopírujte hodnotu ID prostředku.

1. V Azure Portal v okně **účet Automation** vyberte svůj účet Automation a vyberte **všechna nastavení**. 
2. V okně **všechna nastavení** v části **Nastavení účtu**vyberte **vlastnosti**.  
3. V okně **vlastnosti** si všimněte vlastností, které jsou uvedeny níže.

    ![Vlastnosti účtu Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

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
| Hodnotu |Stav úlohy runbooku. Možné hodnoty:<br>– Nové<br>– Vytvořeno<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>-Dokončeno |
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
| Hodnotu |Stav úlohy runbooku. Možné hodnoty:<br>– Probíhá |
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

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitor

1. V počítači spusťte prostředí Windows PowerShell z obrazovky **Start** .
2. Spusťte následující příkazy prostředí PowerShell a upravte hodnoty pro `[your resource ID]` a `[resource ID of the log analytics workspace]` s hodnotami z předchozí části.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po spuštění tohoto skriptu může trvat hodinu, než začnete zobrazovat záznamy v Azure Monitor protokolu nových `JobLogs` nebo `JobStreams` zapsaných protokolů.

Pokud chcete zobrazit protokoly, spusťte následující dotaz v hledání protokolu Log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Ověřit konfiguraci

Pokud chcete potvrdit, že váš účet Automation odesílá protokoly do vašeho pracovního prostoru Log Analytics, zkontrolujte, jestli je na účtu Automation správně nakonfigurovaná diagnostika pomocí následujícího příkazu PowerShellu.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Ve výstupu se ujistěte, že:

* V části `Logs` `Enabled` je hodnota true.
* `WorkspaceId`hodnota je nastavená na `ResourceId` hodnotu pro váš pracovní prostor Log Analytics.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Zobrazení protokolů automatizace v protokolech Azure Monitor

Teď, když jste začali odesílat protokoly úloh služby Automation, abyste Azure Monitor protokoly, Podívejme se na to, co můžete s těmito protokoly dělat v protokolech Azure Monitor.

Pokud chcete zobrazit protokoly, spusťte následující dotaz:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslání e-mailu, když dojde k chybě nebo pozastavení úlohy Runbooku

Jedním z nejdůležitějších povedených zákazníků je možnost Odeslat e-mailem nebo text v případě, že se něco pokazilo s úlohou Runbooku.

Chcete-li vytvořit pravidlo výstrahy, začněte tím, že vytvoříte hledání v protokolu pro záznamy úloh sady Runbook, které by měly vyvolat výstrahu. Kliknutím na tlačítko **výstrahy** vytvoříte a nakonfigurujete pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **Zobrazit protokoly**.
2. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Můžete také seskupit podle názvu Runbooku pomocí:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud jste v pracovním prostoru nastavili protokoly z více než jednoho účtu Automation nebo předplatného, můžete své výstrahy seskupit podle předplatného a účtu Automation. Název účtu Automation najdete v `Resource` poli hledání `JobLogs` .
3. Obrazovku **vytvořit pravidlo** otevřete kliknutím na **nové pravidlo výstrahy** v horní části stránky. Další informace o možnostech konfigurace výstrahy najdete v tématu [protokolování výstrah v Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Vyhledá všechny úlohy, které byly dokončeny s chybami.

Kromě upozorňování na chyby můžete zjistit, kdy má úloha Runbooku neukončující chybu. V těchto případech PowerShell vytvoří chybový proud, ale neukončující chyby nezpůsobí, že by vaše úloha mohla pozastavit nebo selhat.

1. V pracovním prostoru Log Analytics klikněte na **protokoly**.
2. Do pole dotazu zadejte `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .
3. Klikněte na tlačítko **Hledat** .

### <a name="view-job-streams-for-a-job"></a>Zobrazení datových proudů úloh pro úlohu

Při ladění úlohy můžete také chtít vyhledat streamy úloh. Následující dotaz zobrazí všechny datové proudy pro jednu úlohu s identifikátorem GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Zobrazit historický stav úlohy

Nakonec můžete chtít vizualizovat historii úloh v průběhu času. Pomocí tohoto dotazu můžete v průběhu času vyhledat stav svých úloh.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Graf stavu Log Analytics historické úlohy](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Odebrat nastavení diagnostiky

Chcete-li odebrat nastavení diagnostiky z účtu Automation, spusťte následující příkaz:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>Další kroky

* Pomoc při řešení potíží s Log Analytics najdete v tématu [řešení potíží, proč Log Analytics už neshromažďují data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Další informace o tom, jak vytvářet různé vyhledávací dotazy a kontrolovat protokoly úloh služby Automation pomocí protokolů Azure Monitor, najdete [v tématu prohledávání protokolů v protokolech Azure monitor](../log-analytics/log-analytics-log-searches.md).
* Chcete-li pochopit, jak vytvořit a načíst výstupní a chybové zprávy ze sad Runbook, přečtěte si téma [výstup a zprávy Runbooku](automation-runbook-output-and-messages.md).
* Další informace o spuštění sady Runbook, způsobu monitorování úloh sady Runbook a dalších technických informací naleznete v tématu [spuštění sady Runbook v Azure Automation](automation-runbook-execution.md).
* Další informace o Azure Monitor protokolů a zdrojích shromažďování dat najdete [v tématu shromažďování dat služby Azure Storage v protokolech Azure monitor přehled](../azure-monitor/platform/collect-azure-metrics-logs.md).