---
title: Předávání dat úloh Azure Automation do protokolů Azure Monitoru
description: Tento článek ukazuje, jak odesílat stav úlohy a datové proudy úloh sady Runbook do protokolů Azure Monitor, aby poskytovaly další přehledy a správu.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457684"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Předávat stav úlohy a datové proudy úloh z protokolů automatizace do Azure Monitoru

Automatizace může odesílat stav úlohy runbooku a toky úloh do pracovního prostoru Log Analytics. Tento proces nezahrnuje propojení pracovního prostoru a je zcela nezávislý. Protokoly úloh a datové proudy úloh jsou viditelné na webu Azure Portal nebo s Prostředím PowerShell pro jednotlivé úlohy a to umožňuje provádět jednoduché šetření. Nyní s protokoly Azure Monitor můžete:

* Získejte přehled o stavu úloh automatizace.
* Aktivujte e-mail nebo výstrahu na základě stavu úlohy runbooku (například se nezdařilo nebo pozastaveno).
* Pište pokročilé dotazy ve svých datových proudech úloh.
* Korelujte úlohy napříč účty automatizace.
* Pomocí vlastních zobrazení a vyhledávacích dotazů můžete vizualizovat výsledky sady Runbook, stav úlohy sady Runbook a další související klíčové ukazatele nebo metriky.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Požadavky a aspekty nasazení

Chcete-li začít odesílat protokoly automatizace do protokolů Azure Monitor, potřebujete:

* Nejnovější verze [Azure PowerShellu](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Pracovní prostor služby Log Analytics. Další informace najdete [v tématu Začínáme s protokoly Azure Monitor](../log-analytics/log-analytics-get-started.md).
* ID prostředku pro váš účet Azure Automation.

Pomocí následujícího příkazu vyhledejte ID prostředku pro váš účet Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Pokud chcete najít ID prostředku pro pracovní prostor Log Analytics, spusťte následující příkaz PowerShellu:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Pokud máte více než jeden účet automatizace nebo pracovní prostor ve výstupu předchozích příkazů, najděte název, který potřebujete nakonfigurovat a zkopírovat hodnotu Pro ID prostředku.

1. Na webu Azure Portal vyberte účet Automation v okně **účtu Automatizace** a vyberte **Všechna nastavení**. 
2. V okně **Všechna nastavení** vyberte v části **Nastavení účtu** **položku Vlastnosti**.  
3. V okně **Vlastnosti** si poznamenejte vlastnosti uvedené níže.

    ![Vlastnosti účtu automatizace](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Diagnostika Azure Automation vytvoří dva typy záznamů v protokolech Azure Monitoru, označených jako `AzureDiagnostics`. Tabulky v následujících částech jsou příklady záznamů, které generuje Azure Automation a datové typy, které se zobrazí ve výsledcích hledání protokolu.

### <a name="job-logs"></a>Protokoly úloh

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Volající, který inicioval operaci. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| Tenant_g | GUID, který identifikuje klienta pro volajícího. |
| JobId_g |IDENTIFIKÁTOR GUID, který identifikuje úlohu runbooku. |
| Type výsledku |Stav úlohy runbooku. Možné hodnoty:<br>- Nové<br>- Vytvořeno<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>- Dokončeno |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs. |
| OperationName | Typ operace prováděné v Azure. Pro automatizaci je hodnota Job. |
| Prostředek | Název účtu automatizace |
| SourceSystem | Systém, který Azure Monitor protokoly použít ke shromažďování dat. Hodnota je vždy Diagnostika Azure pro Azure. |
| VýsledekPopis |Stav výsledku úlohy sady Runbook. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená |
| CorrelationId |Identifikátor GUID korelace úlohy runbooku. |
| ResourceId |ID prostředku účtu Azure Automation sady Runbook. |
| SubscriptionId | Identifikátor GUID předplatného Azure pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet automatizace. |
| ResourceProvider | Zprostředkovatel prostředků. Hodnota je microsoft. Automatizace. |
| ResourceType | Typ prostředku. Hodnota je AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Datové proudy úlohy
| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Volající, který inicioval operaci. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| StreamType_s |Typ datového proudu úlohy. Možné hodnoty:<br>- Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné |
| Tenant_g | GUID, který identifikuje klienta pro volajícího. |
| JobId_g |IDENTIFIKÁTOR GUID, který identifikuje úlohu runbooku. |
| Type výsledku |Stav úlohy runbooku. Možné hodnoty:<br>- Probíhá |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams. |
| OperationName | Typ operace prováděné v Azure. Pro automatizaci je hodnota Job. |
| Prostředek | Název účtu automatizace. |
| SourceSystem | Systém, který Azure Monitor protokoly použít ke shromažďování dat. Hodnota je vždy Diagnostika Azure pro Azure. |
| VýsledekPopis |Popis, který obsahuje výstupní datový proud z runbooku. |
| CorrelationId |Identifikátor GUID korelace úlohy runbooku. |
| ResourceId |ID prostředku účtu Azure Automation sady Runbook. |
| SubscriptionId | Identifikátor GUID předplatného Azure pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet automatizace. |
| ResourceProvider | Zprostředkovatel prostředků. Hodnota je microsoft. Automatizace. |
| ResourceType | Typ prostředku. Hodnota je AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure Monitoru

1. V počítači spusťte prostředí Windows PowerShell z **úvodní** obrazovky.
2. Spusťte následující příkazy prostředí PowerShell `[your resource ID]` `[resource ID of the log analytics workspace]` a upravte hodnoty pro a s hodnotami z předchozího oddílu.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po spuštění tohoto skriptu může trvat hodinu, než začnete `JobLogs` vidět `JobStreams` záznamy v protokolech Azure Monitor nové nebo zapisování.

Chcete-li zobrazit protokoly, spusťte následující dotaz ve vyhledávání protokolu analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Ověřit konfiguraci

Chcete-li ověřit, zda váš účet Automation odesílá protokoly do pracovního prostoru Analýzy protokolů, zkontrolujte, zda je diagnostika správně nakonfigurována v účtu Automatizace pomocí následujícího příkazu Prostředí PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Ve výstupu zajistěte, aby:

* V `Logs`části je `Enabled` hodnota true.
* `WorkspaceId`je nastavena `ResourceId` na hodnotu pracovního prostoru Log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Zobrazení protokolů automatizace v protokolech Azure Monitor

Teď, když jste začali odesílat protokoly úloh automatizace do protokolů Azure Monitor, podívejme se, co můžete dělat s těmito protokoly v protokolech Azure Monitor.

Chcete-li zobrazit protokoly, spusťte následující dotaz:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslání e-mailu, když se úloha runbooku nezdaří nebo pozastaví

Jeden z nejlepších zákazníků žádá o možnost odeslat e-mail nebo text, když se něco pokazí s úlohou runbooku.

Chcete-li vytvořit pravidlo výstrahy, začněte vytvořením hledání protokolu pro záznamy úloh sady Runbook, které by měly vyvolat výstrahu. Klepnutím na tlačítko **Výstraha** vytvořte a nakonfigurujte pravidlo výstrahy.

1. Na stránce Přehled pracovního prostoru Analýzy protokolů klikněte na **zobrazit protokoly**.
2. Vytvořte vyhledávací dotaz protokolu pro výstrahu zadáním následujícího hledání do pole dotazu:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Můžete také seskupit podle názvu runbooku pomocí:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud nastavíte protokoly z více než jednoho účtu automatizace nebo předplatného do pracovního prostoru, můžete seskupit výstrahy podle předplatného a účtu Automatizace. Název účtu automatizace naleznete `Resource` v poli `JobLogs`při hledání .
3. Pokud chcete otevřít obrazovku **Vytvořit pravidlo,** klikněte v horní části stránky na **Nové pravidlo výstrahy.** Další informace o možnostech konfigurace výstrahy najdete [v tématu Výstrahy protokolu v Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Najít všechny úlohy, které byly dokončeny s chybami

Kromě upozornění na chyby můžete zjistit, kdy úloha runbooku má neukončující chybu. V těchto případech prostředí PowerShell vytvoří datový proud chyb, ale neukončující chyby nezpůsobí pozastavení nebo selhání úlohy.

1. V pracovním prostoru Log Analytics klikněte na **Protokoly**.
2. Do pole dotazu `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`zadejte .
3. Klepněte na tlačítko **Hledat.**

### <a name="view-job-streams-for-a-job"></a>Zobrazit datové proudy úloh pro úlohu

Při ladění úlohy můžete také chtít podívat do datových proudů úloh. Následující dotaz zobrazuje všechny datové proudy pro jednu úlohu s GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Zobrazit stav historické úlohy

Nakonec možná budete chtít vizualizovat historii práce v průběhu času. Tento dotaz můžete použít k vyhledání stavu úloh v průběhu času.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Graf stavu historické úlohy analýzy protokolů](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Odebrání nastavení diagnostiky

Chcete-li odebrat diagnostické nastavení z účtu Automatizace, spusťte následující příkaz:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Další kroky

* Nápovědu k řešení potíží s protokolem Log Analytics [najdete v tématu Řešení potíží s tím, proč služba Log Analytics již neshromažďuje data](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Další informace o tom, jak vytvářet různé vyhledávací dotazy a prohlížet protokoly úloh automatizace pomocí protokolů Azure Monitor, najdete [v tématu Protokoly protokolů protokolu protokolu.](../log-analytics/log-analytics-log-searches.md)
* Informace o tom, jak vytvářet a načítat výstupní a chybové zprávy ze sady Runbook, naleznete v [tématu Výstup sady Runbook a zprávy](automation-runbook-output-and-messages.md).
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Další informace o protokolech Azure Monitoru a zdrojích shromažďování dat najdete [v tématu Shromažďování dat úložiště Azure v přehledu protokolů Azure Monitoru](../azure-monitor/platform/collect-azure-metrics-logs.md).