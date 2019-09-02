---
title: Předávání dat úloh Azure Automation do protokolů Azure Monitoru
description: V tomto článku se dozvíte, jak odesílat datové proudy úloh Runbooku a Azure Monitor protokoly pro zajištění dalšího přehledu a správy.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff455ed355d4412bcf042208d2fd1e7a2a11b965
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186777"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Přeposlání datových proudů úloh a úloh z automatizace do protokolů Azure Monitor

Automatizace může odesílat streamy stavů a úloh Runbooku do vašeho pracovního prostoru Log Analytics. Tento proces nezahrnuje propojení pracovního prostoru a je zcela nezávislý. Protokoly úloh a datové proudy úloh jsou viditelné v Azure Portal nebo v prostředí PowerShell pro jednotlivé úlohy a umožňují provádět jednoduché šetření. Teď díky protokolům Azure Monitor můžete:

* Získání přehledu o vašich úlohách Automation
* Aktivace e-mailu nebo výstrahy na základě stavu úlohy Runbooku (například selhání nebo pozastaveno).
* Vytváření pokročilých dotazů napříč datovými proudy úloh
* Korelace úloh napříč účty Automation
* Vizualizace historie úloh v průběhu času

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Předpoklady a požadavky na nasazení

Pokud chcete začít odesílat protokoly pro automatizaci Azure Monitor protokolů, budete potřebovat:

* Nejnovější verzi [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Pracovní prostor Log Analytics. Další informace najdete v tématu [Začínáme s protokoly Azure monitor](../log-analytics/log-analytics-get-started.md).
* ResourceId pro váš účet Azure Automation.

Pro vyhledání ResourceId pro váš Azure Automation účet:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Pokud chcete najít ResourceId pro váš pracovní prostor Log Analytics, spusťte následující PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Pokud máte více než jeden účet služby Automation nebo pracovní prostory, vyhledejte ve výstupu předchozích příkazů *název* , který budete muset nakonfigurovat, a zkopírujte hodnotu pro *ResourceID*.

Pokud potřebujete najít *název* svého účtu Automation, v Azure Portal vyberte účet Automation v okně **účet Automation** a vyberte **všechna nastavení**. V okně **Všechna nastavení** v části **Nastavení účtu** vyberte **Vlastnosti**.  V okně **Vlastnosti** si můžete tyto hodnoty opsat.<br> ![Vlastnosti](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)účtu Automation.

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitor

1. V počítači spusťte **prostředí Windows PowerShell** z obrazovky **Start** .
2. Spusťte následující PowerShell a upravte hodnotu pro `[your resource id]` a `[resource id of the log analytics workspace]` s hodnotami z předchozího kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po spuštění tohoto skriptu může trvat hodinu, než začnete zobrazovat záznamy v Azure Monitor protokoly nových JobLogs nebo JobStreams zápisu.

Pokud chcete zobrazit protokoly, spusťte následující dotaz v hledání protokolu Log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Ověření konfigurace

Pokud chcete potvrdit, že váš účet Automation odesílá protokoly do vašeho pracovního prostoru Log Analytics, zkontrolujte, jestli je v účtu Automation správně nakonfigurovaná diagnostika pomocí tohoto prostředí PowerShell:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Ve výstupu zajistěte, aby:

* V části *protokoly*je hodnota *Enabled* nastavena na *true (pravda*).
* Hodnota *ID pracovního prostoru* je nastavená na ResourceID vašeho pracovního prostoru Log Analytics.

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Diagnostika z Azure Automation vytváří dva typy záznamů v protokolech Azure Monitor a jsou označeny jako **AzureDiagnostics**. Následující dotazy používají pro Azure Monitor protokolů upgradovaný dotazovací jazyk. Informace o běžných dotazech mezi starším dotazovacím jazykem a novým dotazovacím jazykem Azure Kusto najdete v části [starší verze nového dotazovacího jazyka Azure Kusto pro tahák](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language) .

### <a name="job-logs"></a>Protokoly úloh

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| ResultType |Stav úlohy runbooku. Možné hodnoty jsou:<br>– Nové<br>– Vytvořeno<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>-Dokončeno |
| Category | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs. |
| OperationName | Určuje typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha. |
| Resource | Název účtu Automation |
| SourceSystem | Jak Azure Monitor protokoly shromáždily data. Vždy *Azure* pro diagnostiku Azure. |
| ResultDescription |Popisuje výsledný stav úlohy runbooku. Možné hodnoty jsou:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| Prostředku |Určuje ID prostředku Azure Automation účtu sady Runbook. |
| SubscriptionId | ID předplatného Azure (GUID) pro účet Automation. |
| Skupina prostředků | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| Typ prostředku | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Datové proudy úloh
| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| StreamType_s |Typ datového proudu úlohy. Možné hodnoty jsou:<br>\- Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné |
| Tenant_g | Identifikátor GUID, který identifikuje tenanta pro daného volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| ResultType |Stav úlohy runbooku. Možné hodnoty jsou:<br>– Probíhá |
| Category | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams. |
| OperationName | Určuje typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha. |
| Resource | Název účtu Automation |
| SourceSystem | Jak Azure Monitor protokoly shromáždily data. Vždy *Azure* pro diagnostiku Azure. |
| ResultDescription |Zahrnuje výstupní datový proud z runbooku. |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| Prostředku |Určuje ID prostředku Azure Automation účtu sady Runbook. |
| SubscriptionId | ID předplatného Azure (GUID) pro účet Automation. |
| Skupina prostředků | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| Typ prostředku | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Zobrazení protokolů automatizace v protokolech Azure Monitor

Teď, když jste začali odesílat protokoly úloh služby Automation, abyste Azure Monitor protokoly, Podívejme se na to, co můžete s těmito protokoly dělat v protokolech Azure Monitor.

Pokud chcete zobrazit protokoly, spusťte následující dotaz:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslání e-mailu, když dojde k chybě nebo pozastavení úlohy Runbooku
Jedním z nejdůležitějších povedených zákazníků je možnost Odeslat e-mailem nebo text v případě, že se něco pokazilo s úlohou Runbooku.

Chcete-li vytvořit pravidlo výstrahy, začněte tím, že vytvoříte hledání v protokolu pro záznamy úloh sady Runbook, které by měly vyvolat výstrahu. Kliknutím na tlačítko **výstrahy** vytvoříte a nakonfigurujete pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Log Analytics klikněte na **Zobrazit protokoly**.
2. Vytvořte dotaz hledání protokolu pro upozornění zadáním následujícího hledání do pole dotazu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`Můžete také seskupit podle RunbookName pomocí:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud jste v pracovním prostoru nastavili protokoly z více než jednoho účtu Automation nebo předplatného, můžete své výstrahy seskupit podle předplatného a účtu Automation. Název účtu Automation najdete v poli prostředků v hledání JobLogs.
3. Obrazovku **vytvořit pravidlo** otevřete kliknutím na **+ nové pravidlo výstrahy** v horní části stránky. Další informace o možnostech konfigurace výstrahy najdete v tématu [protokolování výstrah v Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Vyhledá všechny úlohy, které byly dokončeny s chybami.
Kromě upozorňování na chyby můžete zjistit, kdy má úloha Runbooku neukončující chybu. V těchto případech PowerShell vytvoří chybový proud, ale neukončující chyby nezpůsobí, že by vaše úloha mohla pozastavit nebo selhat.

1. V pracovním prostoru Log Analytics klikněte na **protokoly**.
2. Do pole dotaz zadejte `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` a potom klikněte na tlačítko **Hledat** .

### <a name="view-job-streams-for-a-job"></a>Zobrazení datových proudů úloh pro úlohu
Při ladění úlohy můžete také chtít vyhledat streamy úloh. Následující dotaz zobrazí všechny datové proudy pro jednu úlohu s identifikátorem GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Zobrazit historický stav úlohy
Nakonec můžete chtít vizualizovat historii úloh v průběhu času. Pomocí tohoto dotazu můžete v průběhu času vyhledat stav svých úloh.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Graf stavu Log Analytics historické úlohy](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Odebrat nastavení diagnostiky

Chcete-li odebrat nastavení diagnostiky z účtu Automation, spusťte následující příkazy:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Souhrn

Odesláním stavu úlohy automatizace a dat streamu do Azure Monitor protokolů můžete získat lepší přehled o stavu úloh automatizace pomocí:
+ Nastavení výstrah, které vás upozorní, když dojde k nějakému problému.
+ Použití vlastních zobrazení a vyhledávacích dotazů k vizualizaci výsledků Runbooku, stavu úlohy Runbooku a dalších souvisejících klíčových ukazatelů nebo metrik.

Protokoly Azure Monitor poskytují větší provozní přehled úloh automatizace a můžou rychleji řešit incidenty.

## <a name="next-steps"></a>Další postup

* Pomoc při řešení potíží s Log Analytics najdete v tématu [řešení potíží, proč Log Analytics už neshromažďují data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Další informace o tom, jak vytvářet různé vyhledávací dotazy a kontrolovat protokoly úloh služby Automation pomocí protokolů Azure Monitor, najdete [v tématu prohledávání protokolů v protokolech Azure monitor](../log-analytics/log-analytics-log-searches.md).
* Chcete-li pochopit, jak vytvořit a načíst výstupní a chybové zprávy ze sad Runbook, přečtěte si téma [výstup a zprávy Runbooku](automation-runbook-output-and-messages.md).
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Další informace o Azure Monitor protokolů a zdrojích shromažďování dat najdete [v tématu shromažďování dat služby Azure Storage v protokolech Azure monitor přehled](../azure-monitor/platform/collect-azure-metrics-logs.md).

