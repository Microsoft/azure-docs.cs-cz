---
title: Předávání dat úloh Azure Automation do Log Analytics
description: Tento článek ukazuje, jak odesílat stav úlohy a runbook proudy úlohy k Azure Log Analytics k poskytování správy a další aspekty.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f96419ea1e6cb8a6f15ba67948b2a139f647dd6c
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics"></a>Předávání zpráv o stavu úlohy a datové proudy úlohy ze služby Automation k analýze protokolů
Automatizace můžete odeslat runbook datové proudy úlohy stavu a úlohu do pracovního prostoru analýzy protokolů. Protokoly úlohy a datové proudy úlohy jsou viditelné na portálu Azure nebo v prostředí PowerShell pro jednotlivé úlohy a to umožňuje provádět jednoduché šetření. Pomocí analýzy protokolů můžete nyní:

* Pohled na vaše úlohy automatizace.
* Aktivační událost e-mailem nebo výstrahy podle runbook stav úlohy (například chybných nebo pozastavených).
* Zápis pokročilými dotazy napříč vaše datové proudy úlohy.
* Vazbu mezi úlohy v účtech Automation.
* Vizualizace historii úlohy v čase.

## <a name="prerequisites-and-deployment-considerations"></a>Požadavky a důležité informace o nasazení
Chcete-li zahájit odesílání protokolů služby Automation k analýze protokolů, je třeba:

* Listopadu 2016 nebo novější vydání [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Pracovní prostor analýzy protokolů. Další informace najdete v tématu [začít pracovat s analýzy protokolů](../log-analytics/log-analytics-get-started.md). 
* ID prostředku pro váš účet Azure Automation.


Najít ResourceId pro váš účet Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Najít ResourceId pro pracovní prostor analýzy protokolů, spusťte následující prostředí PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Pokud máte více než jeden účty Automation nebo najít pracovní prostory v výstup z předchozích příkazů *název* budete muset nakonfigurovat a zkopírujte hodnotu pro *ResourceId*.

Pokud potřebujete najít *název* účtu Automation na portálu Azure vyberte svůj účet Automation z **účet Automation** a vyberte **všechna nastavení**. V okně **Všechna nastavení** v části **Nastavení účtu** vyberte **Vlastnosti**.  V okně **Vlastnosti** si můžete tyto hodnoty opsat.<br> ![Vlastnosti účtu Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Nastavení integrace s analýzy protokolů

1. V počítači, spusťte **prostředí Windows PowerShell** z **spustit** obrazovky.
2. Spusťte následující prostředí PowerShell a upravit její hodnotu `[your resource id]` a `[resource id of the log analytics workspace]` s hodnotami z předchozího kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Po spuštění tohoto skriptu, zobrazí se záznamy v analýzy protokolů během deseti minut nové JobLogs nebo JobStreams zapisovaný.

Pokud chcete zobrazit protokoly, spusťte následující dotaz ve vyhledávání protokolu analýzy protokolů: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Ověření konfigurace
Pokud chcete potvrdit, že váš účet Automation odesílá protokoly do pracovního prostoru analýzy protokolů, zkontrolujte, zda diagnostiky jsou správně nakonfigurovány na účtu Automation pomocí prostředí PowerShell následující:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Ve výstupu zajistěte, aby:
+ V části *protokoly*, hodnota *povoleno* je *True*.
+ Hodnota *WorkspaceId* je nastaven na ResourceId pracovního prostoru analýzy protokolů.

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics

Diagnostika z Azure Automation vytvoří dva typy záznamů v analýzy protokolů a jsou označené jako **AzureDiagnostics**. Následující dotazy pomocí upgradovaný dotazovací jazyk k analýze protokolů. Informace o běžných dotazů mezi starší verze dotazovací jazyk a nové dotazovací jazyk Azure Log Analytics najdete [starší verze pro nové Azure Log Analytics Query Language tahák](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>V protokolech úloh
| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| ResultType |Stav úlohy runbooku. Možné hodnoty:<br>– Nový<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>-Byla dokončena |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs. |
| OperationName | Určuje typ operace prováděné v Azure. Hodnota pro automatizaci je úloha. |
| Prostředek | Název účtu Automation. |
| SourceSystem | Jak analýzy protokolů shromáždit data. Vždy *Azure* Azure Diagnostics. |
| ResultDescription |Popisuje výsledný stav úlohy runbooku. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| ID prostředku |Určuje id prostředku účet Azure Automation runbook. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet služby Automation. |
| Skupina prostředků | Název skupiny prostředků pro účet služby Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Datové proudy úlohy
| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| StreamType_s |Typ datového proudu úlohy. Možné hodnoty:<br>- Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| ResultType |Stav úlohy runbooku. Možné hodnoty:<br>– V průběhu |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams. |
| OperationName | Určuje typ operace prováděné v Azure. Hodnota pro automatizaci je úloha. |
| Prostředek | Název účtu Automation. |
| SourceSystem | Jak analýzy protokolů shromáždit data. Vždy *Azure* Azure Diagnostics. |
| ResultDescription |Zahrnuje výstupní datový proud z runbooku. |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| ID prostředku |Určuje id prostředku účet Azure Automation runbook. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet služby Automation. |
| Skupina prostředků | Název skupiny prostředků pro účet služby Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Zobrazení automatizace přihlásí analýzy protokolů
Teď, když jste spustili, odesílání protokolů úlohy služby Automation k analýze protokolů, podíváme se, co můžete dělat s tyto protokoly uvnitř analýzy protokolů.

Pokud chcete zobrazit protokoly, spusťte následující dotaz: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslat e-mail, dojde k selhání úlohy runbooku nebo pozastaví
Jeden z horní zákazník požádá, je pro možnost odesílat e-mailem nebo jako text v případě problémů s úlohy runbooku.   

Pokud chcete vytvořit pravidlo výstrahy, začněte vytvořením hledání protokolů pro záznamy úlohy sady runbook, které by měla vyvolat výstrahu. Klikněte **výstraha** tlačítko Vytvořit a nakonfigurovat pravidlo výstrahy.

1. Na stránce Přehled protokolu Analytics klikněte na tlačítko **hledání protokolů**.
2. Vytvoření vyhledávací dotaz protokolu pro upozornění zadáním následujících hledání do pole dotazu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")` můžete taky Seskupit podle RunbookName pomocí: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud jste nastavili protokolů z více než jeden účet Automation nebo odběr do pracovního prostoru, můžete je seskupovat vaše předplatné a účet Automation. Název účtu Automation naleznete v poli prostředků v hledání JobLogs.
1. Chcete-li otevřít **přidat pravidlo výstrahy** obrazovky, klikněte na tlačítko **výstrah** v horní části stránky. Další informace o možnostech konfigurace upozornění najdete v tématu [výstrahy v analýzy protokolů](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Najít všechny úlohy, které byly dokončeny s chybami
Kromě zobrazení výstrah o selhání, můžete najít při úlohy runbooku se neukončující chybu. V těchto případech prostředí PowerShell vytvoří chybový proud, ale chyby neukončující nemáte způsobit, že pozastavení nebo selže.    

1. V pracovním prostoru analýzy protokolů, klikněte na tlačítko **hledání protokolů**.
2. V poli dotazu zadejte `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` a klikněte **vyhledávání** tlačítko.

### <a name="view-job-streams-for-a-job"></a>Zobrazení datové proudy úlohy pro úlohu
Když ladíte úlohu, můžete také viděl datové proudy úlohy. Následující dotaz zobrazí všechny datové proudy pro jednu úlohu s identifikátorem GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Zobrazit stav historie úlohy
Nakonec můžete vizualizovat historii úlohy v čase. Tento dotaz můžete použít k vyhledání stav úloh v čase.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Graf stav historie úlohy analýzy protokolů](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Shrnutí
Odeslání dat datového proudu a stav úlohy automatizace k analýze protokolů, lze získat lepší přehled o stavu vaší automatizace úloh podle:
+ Nastavení výstrah upozornění v případě, že se vyskytl problém.
+ Pomocí vlastních zobrazení a vyhledávací dotazy k vizualizaci výsledky sady runbook, stav úlohy sady runbook a další související klíčové ukazatele nebo metriky.  

Log Analytics poskytuje větší provozní viditelnost do automatizace úloh a může pomoct adresu incidenty rychlejší.  

## <a name="next-steps"></a>Další postup
* Další informace o tom, jak vytvořit různé vyhledávací dotazy a kontrolujte protokoly úlohy automatizace s analýzy protokolů najdete v tématu [přihlásit analýzy protokolů hledání](../log-analytics/log-analytics-log-searches.md).
* Chcete-li pochopit, jak vytvořit a ze sady runbook načíst výstupní a chybové zprávy, přečtěte si téma [Runbook výstup a zprávy](automation-runbook-output-and-messages.md).
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Další informace o analýzy protokolů a kolekci zdrojů dat naleznete v tématu [shromažďování Azure úložiště dat v přehledu analýzy protokolů](../log-analytics/log-analytics-azure-storage.md).
