---
title: Předávání dat úloh Azure Automation do protokolů Azure Monitoru
description: Tento článek ukazuje, jak odesílat stav úlohy a datové proudy úloh sady Runbook do protokolů Azure Monitor, aby poskytovaly další přehledy a správu.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: beb69edc57b5a13db0f6d2e5e1536804f3472aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421903"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Předávat stav úlohy a datové proudy úloh z protokolů automatizace do Azure Monitoru

Automatizace může odesílat stav úlohy runbooku a toky úloh do pracovního prostoru Log Analytics. Tento proces nezahrnuje propojení pracovního prostoru a je zcela nezávislý. Protokoly úloh a datové proudy úloh jsou viditelné na webu Azure Portal nebo s Prostředím PowerShell pro jednotlivé úlohy a to umožňuje provádět jednoduché šetření. Nyní s protokoly Azure Monitor můžete:

* Získejte přehled o svých úlohách automatizace.
* Aktivujte e-mail nebo výstrahu na základě stavu úlohy runbooku (například se nezdařilo nebo pozastaveno).
* Pište pokročilé dotazy ve svých datových proudech úloh.
* Korelujte úlohy napříč účty automatizace.
* Vizualizujte historii úloh v průběhu času.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Požadavky a aspekty nasazení

Chcete-li začít odesílat protokoly automatizace do protokolů Azure Monitor, potřebujete:

* Nejnovější verze [Azure PowerShellu](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Pracovní prostor služby Log Analytics. Další informace najdete [v tématu Začínáme s protokoly Azure Monitor](../log-analytics/log-analytics-get-started.md).
* ResourceId pro váš účet Azure Automation.

Pokud chcete najít ResourceId pro váš účet Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Pokud chcete najít ResourceId pro váš pracovní prostor Analýzy protokolů, spusťte následující Prostředí PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Pokud máte více než jeden účty automatizace nebo pracovní prostory, ve výstupu předchozích příkazů, najděte *název,* který potřebujete ke konfiguraci a zkopírování hodnoty *ResourceId*.

Pokud potřebujete najít *název* svého účtu Automation, vyberte na portálu Azure účet Automatizace z okna **účtu Automatizace** a vyberte **Všechna nastavení**. V okně **Všechna nastavení** v části **Nastavení účtu** vyberte **Vlastnosti**.  V okně **Vlastnosti** si můžete tyto hodnoty opsat.<br> ![Vlastnosti](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)účtu automatizace .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitoru

1. V počítači spusťte **prostředí Windows PowerShell** z **úvodní** obrazovky.
2. Spusťte následující Prostředí PowerShell a `[your resource id]` `[resource id of the log analytics workspace]` upravte hodnotu a s hodnotami z předchozího kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po spuštění tohoto skriptu může trvat hodinu, než začnete vidět záznamy v protokolech Azure Monitor nových JobLogs nebo JobStreams, které se zapisují.

Chcete-li zobrazit protokoly, spusťte následující dotaz ve vyhledávání protokolu analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Ověřit konfiguraci

Chcete-li ověřit, zda váš účet Automation odesílá protokoly do pracovního prostoru Analýzy protokolů, zkontrolujte, zda je diagnostika správně nakonfigurována v účtu Automatizace pomocí následujícího prostředí PowerShell:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Ve výstupu zajistěte, aby:

* V *části Protokoly*je hodnota položky *Enabled* *True*.
* Hodnota *WorkspaceId* je nastavena na ResourceId pracovního prostoru Analýzy protokolů.

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Diagnostika z Azure Automation vytvoří dva typy záznamů v protokolech Azure Monitor a jsou označeny jako **AzureDiagnostics**. Následující dotazy používají upgradovaný dotazovací jazyk na protokoly Azure Monitor. Informace o běžných dotazech mezi starším dotazovacím jazykem a novým dotazovacím jazykem Azure Kusto najdete [na stránce Starší verze nového informačního listu pro dotazovací jazyk Azure Kusto](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Protokoly úloh

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| Tenant_g | GUID, který identifikuje klienta pro volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| Type výsledku |Stav úlohy runbooku. Možné hodnoty:<br>- Nové<br>- Vytvořeno<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>- Dokončeno |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs. |
| OperationName | Určuje typ operace prováděné v Azure. Pro automatizaci je hodnota Job. |
| Prostředek | Název účtu Automatizace |
| SourceSystem | Jak azure monitor protokoly shromažďují data. Vždy *diagnostika Azure* pro Azure. |
| VýsledekPopis |Popisuje výsledný stav úlohy runbooku. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| ResourceId |Určuje id prostředku účtu Azure Automation sady Runbook. |
| SubscriptionId | Id předplatného Azure (GUID) pro účet automatizace. |
| ResourceGroup | Název skupiny prostředků pro účet automatizace. |
| ResourceProvider | Microsoft. Automatizace |
| ResourceType | AUTOMATIZAČNÍ ÚČTY |


### <a name="job-streams"></a>Toky úloh
| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| StreamType_s |Typ datového proudu úlohy. Možné hodnoty:<br>- Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné |
| Tenant_g | GUID, který identifikuje klienta pro volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| Type výsledku |Stav úlohy runbooku. Možné hodnoty:<br>- Probíhá |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams. |
| OperationName | Určuje typ operace prováděné v Azure. Pro automatizaci je hodnota Job. |
| Prostředek | Název účtu Automatizace |
| SourceSystem | Jak azure monitor protokoly shromažďují data. Vždy *diagnostika Azure* pro Azure. |
| VýsledekPopis |Zahrnuje výstupní datový proud z runbooku. |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| ResourceId |Určuje id prostředku účtu Azure Automation sady Runbook. |
| SubscriptionId | Id předplatného Azure (GUID) pro účet automatizace. |
| ResourceGroup | Název skupiny prostředků pro účet automatizace. |
| ResourceProvider | Microsoft. Automatizace |
| ResourceType | AUTOMATIZAČNÍ ÚČTY |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Zobrazení protokolů automatizace v protokolech Azure Monitor

Teď, když jste začali odesílat protokoly úloh automatizace do protokolů Azure Monitor, podívejme se, co můžete dělat s těmito protokoly v protokolech Azure Monitor.

Chcete-li zobrazit protokoly, spusťte následující dotaz:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslání e-mailu, když se úloha runbooku nezdaří nebo pozastaví
Jeden z nejlepších zákazníků žádá o možnost odeslat e-mail nebo text, když se něco pokazí s úlohou runbooku.

Chcete-li vytvořit pravidlo výstrahy, začněte vytvořením hledání protokolu pro záznamy úloh sady Runbook, které by měly vyvolat výstrahu. Klepnutím na tlačítko **Výstraha** vytvořte a nakonfigurujte pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Analýzy protokolů klikněte na **zobrazit protokoly**.
2. Vytvořte vyhledávací dotaz protokolu pro výstrahu zadáním následujícího `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` hledání do pole dotazu: Můžete také seskupit podle názvu runbooku pomocí:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud nastavíte protokoly z více než jednoho účtu automatizace nebo předplatného do pracovního prostoru, můžete seskupit výstrahy podle předplatného a účtu Automatizace. Název účtu automatizace naleznete v poli Zdroj při hledání joblogů.
3. Chcete-li otevřít obrazovku **Vytvořit pravidlo,** klikněte v horní části stránky na **+ Nové pravidlo výstrahy.** Další informace o možnostech konfigurace výstrahy najdete [v tématu Výstrahy protokolu v Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Najít všechny úlohy, které byly dokončeny s chybami
Kromě upozornění na chyby můžete zjistit, kdy úloha runbooku má neukončující chybu. V těchto případech prostředí PowerShell vytvoří datový proud chyb, ale neukončující chyby nezpůsobí pozastavení nebo selhání úlohy.

1. V pracovním prostoru Log Analytics klikněte na **Protokoly**.
2. Do pole dotazu `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` zadejte a klikněte na tlačítko **Hledat.**

### <a name="view-job-streams-for-a-job"></a>Zobrazit datové proudy úloh pro úlohu
Při ladění úlohy můžete také chtít podívat do datových proudů úloh. Následující dotaz zobrazuje všechny datové proudy pro jednu úlohu s GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Zobrazit stav historické úlohy
Nakonec můžete chtít vizualizovat historii úloh v průběhu času. Tento dotaz můžete použít k vyhledání stavu úloh v průběhu času.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Graf stavu historické úlohy analýzy protokolů](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Odebrání nastavení diagnostiky

Chcete-li odebrat diagnostické nastavení z účtu automatizace, spusťte následující příkazy:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Souhrn

Odesláním stavu úlohy automatizace a datových proudů dat do protokolů Azure Monitoru můžete získat lepší přehled o stavu úloh automatizace takto:
+ Nastavení upozornění, která vás upozorní na problém.
+ Pomocí vlastních zobrazení a vyhledávacích dotazů vizualizovat výsledky sady Runbook, stav úlohy sady Runbook a další související klíčové ukazatele nebo metriky.

Protokoly Azure Monitor udává lepší provozní přehled úloh automatizace a mohou pomoci rychleji řešit incidenty.

## <a name="next-steps"></a>Další kroky

* Nápovědu k řešení potíží s protokolem Log Analytics [najdete v tématu Řešení potíží s tím, proč služba Log Analytics již neshromažďuje data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Další informace o tom, jak vytvářet různé vyhledávací dotazy a prohlížet protokoly úloh automatizace pomocí protokolů Azure Monitor, najdete [v tématu Protokoly protokolů protokolu protokolu.](../log-analytics/log-analytics-log-searches.md)
* Informace o tom, jak vytvářet a načítat výstupní a chybové zprávy ze sady Runbook, naleznete v [tématu Výstup sady Runbook a zprávy](automation-runbook-output-and-messages.md).
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Další informace o protokolech Azure Monitoru a zdrojích shromažďování dat najdete [v tématu Shromažďování dat úložiště Azure v přehledu protokolů Azure Monitoru](../azure-monitor/platform/collect-azure-metrics-logs.md).

