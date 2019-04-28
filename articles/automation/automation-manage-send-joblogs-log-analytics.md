---
title: Předávání dat úloh Azure Automation do protokolů Azure Monitoru
description: Tento článek předvádí postup odesílání stavu úlohy a runbook datové proudy úlohy do protokolů Azure Azure Monitor k poskytování dalších přehledů a správu.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 82baef7ce0d91713c8bef202ab0ea0925d290f3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739101"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Dál stavu úlohy a datové proudy úlohy ze služby Automation na protokoly Azure monitoru

Automatizace můžete odeslat runbook datové proudy úlohy stavu a úlohu do pracovního prostoru Log Analytics. Tento proces nezahrnuje propojení pracovního prostoru a je zcela nezávislé. Protokoly úloh a datové proudy úlohy jsou viditelné na webu Azure Portal nebo pomocí Powershellu, pro jednotlivé úlohy, což umožňuje provádět jednoduché šetření. Nyní protokoly Azure Monitor vám umožní:

* Získání přehledu o vašich úlohách Automation
* Aktivační událost e-mailem nebo výstrahy založené na váš stav úlohy runbooku (například chybných nebo pozastavených).
* Vytváření pokročilých dotazů napříč datovými proudy úloh
* Korelace úloh napříč účty Automation
* Vizualizace historie úloh v průběhu času

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Požadavky a důležité informace o nasazení

Chcete-li zahájit odesílání protokolů služby Automation na protokoly Azure monitoru, potřebujete:

* Nejnovější vydaná verze [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Pracovní prostor služby Log Analytics. Další informace najdete v tématu [začít pracovat s protokoly Azure monitoru](../log-analytics/log-analytics-get-started.md). 
* ID prostředku účtu Azure Automation.

Vyhledání ID prostředku účtu Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Najít ID prostředku pro váš pracovní prostor Log Analytics, spusťte následující příkaz Powershellu:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Pokud máte více než jeden účty služby Automation nebo najít pracovní prostory, ve výstupu výše uvedených příkazů *název* budete potřebovat ke konfiguraci a zkopírujte hodnotu *ResourceId*.

Pokud je potřeba najít *název* vašeho účtu Automation na webu Azure Portal vyberte svůj účet Automation z **účtu Automation** okna a vyberte **všechna nastavení** . V okně **Všechna nastavení** v části **Nastavení účtu** vyberte **Vlastnosti**.  V okně **Vlastnosti** si můžete tyto hodnoty opsat.<br> ![Vlastnosti účtu Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Nastavení integrace s protokoly Azure monitoru

1. Na počítači, spusťte **prostředí Windows PowerShell** z **Start** obrazovky.
2. Spusťte následující příkaz Powershellu a upravit její hodnotu `[your resource id]` a `[resource id of the log analytics workspace]` s hodnotami z předchozího kroku.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Po spuštění tohoto skriptu, může trvat hodinu, než začnete zobrazit záznamy v protokolech Azure Monitor nové JobLogs nebo JobStreams probíhá zápis.

Pokud chcete zobrazit protokoly, spuštěním následujícího dotazu v prohledávání protokolu log analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Ověření konfigurace

Pokud chcete potvrdit, že svůj účet Automation posílá protokoly do pracovního prostoru Log Analytics, zkontrolujte, že Diagnostika je správně nakonfigurované na účtu Automation pomocí následující příkaz Powershellu:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Ve výstupu zajistěte následující:

* V části *protokoly*, hodnota *povoleno* je *True*.
* Hodnota *ID pracovního prostoru* je nastavena na ID prostředku pracovního prostoru Log Analytics.

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor

Diagnostika ve službě Azure Automation vytvoří dva typy záznamů v protokoly Azure monitoru a jsou označené jako **AzureDiagnostics**. Následující dotazy pomocí upgradovaný dotazovací jazyk pro protokoly Azure monitoru. Informace o běžných dotazů mezi starší verze dotazovací jazyk a nový dotazovací jazyk Azure Kusto [starší verze na nový dotazovací jazyk Azure Kusto tahák](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Protokoly úloh

| Vlastnost | Popis |
| --- | --- |
| TimeGenerated |Datum a čas provedení úlohy runbooku. |
| RunbookName_s |Název runbooku. |
| Caller_s |Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy. |
| Tenant_g | Identifikátor GUID, který identifikuje klienta pro volajícího. |
| JobId_g |Identifikátor GUID, který představuje ID úlohy runbooku. |
| Hodnota resultType |Stav úlohy runbooku. Možné hodnoty:<br>– Nové<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>– Dokončení |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs. |
| OperationName | Určuje typ operace prováděné v Azure. Hodnota pro automatizaci, je úloha. |
| Prostředek | Název účtu služby Automation |
| SourceSystem | Jak protokoly Azure monitoru shromažďuje data. Vždy *Azure* Azure Diagnostics. |
| resultDescription |Popisuje výsledný stav úlohy runbooku. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| ResourceId |Určuje id prostředku účtu Azure Automation runbook. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
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
| Hodnota resultType |Stav úlohy runbooku. Možné hodnoty:<br>– Probíhá |
| Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams. |
| OperationName | Určuje typ operace prováděné v Azure. Hodnota pro automatizaci, je úloha. |
| Prostředek | Název účtu služby Automation |
| SourceSystem | Jak protokoly Azure monitoru shromažďuje data. Vždy *Azure* Azure Diagnostics. |
| resultDescription |Zahrnuje výstupní datový proud z runbooku. |
| CorrelationId |Identifikátor GUID, který představuje ID korelace úlohy runbooku. |
| ResourceId |Určuje id prostředku účtu Azure Automation runbook. |
| SubscriptionId | Předplatné Azure Id (GUID) pro účet Automation. |
| ResourceGroup | Název skupiny prostředků pro účet Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Zobrazení protokolů automatizace v protokoly Azure monitoru

Teď, když jste začali, odesílání vaše protokoly úloh služby Automation na protokoly Azure monitoru, Podívejme se, co můžete dělat pomocí těchto protokolů uvnitř protokoly Azure monitoru.

Pokud chcete zobrazit protokoly, spusťte následující dotaz: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Odeslání e-mailu, když úlohu runbook selže nebo pozastaví
Mezi důležité zákazníky požádá, je pro možnost odeslání e-mailu nebo odeslání zprávy, když dojde k nějaké chybě s úlohy runbooku.   

Pokud chcete vytvořit pravidlo upozornění, začněte vytvořením prohledávání protokolu pro záznamy úloh sady runbook, které by měla vyvolat výstrahu. Klikněte na tlačítko **výstraha** tlačítko a vytvořte a nakonfigurujte pravidlo upozornění.

1. Na stránce Přehled pracovního prostoru Log Analytics, klikněte na tlačítko **zobrazit protokoly**.
2. Vytvoření vyhledávací dotaz protokolu upozornění tak, že zadáte následující vyhledávání do pole dotazu: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  Můžete taky Seskupit podle RunbookName pomocí: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Pokud jste nastavili protokoly z více než jeden účet služby Automation nebo odběru do pracovního prostoru, můžete je seskupovat vaše předplatné a účet Automation. Název účtu služby Automation najdete v poli prostředků ve službě search JobLogs.
3. Chcete-li otevřít **vytvořit pravidlo** obrazovce, klikněte na tlačítko **+ nové pravidlo upozornění** v horní části stránky. Další informace o možnostech konfigurace upozornění, najdete v části [upozornění protokolů ve službě Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Najít všechny úlohy, které byly dokončeny s chybami
Kromě upozornění na selhání, zjistíte po neukončující Chyba úlohy runbooku. V těchto případech prostředí PowerShell vytvoří chybový proud, ale s neukončujícími chybami nezpůsobí úlohu pozastavit nebo selhání.    

1. V pracovním prostoru Log Analytics, klikněte na tlačítko **protokoly**.
2. Do pole dotazu zadejte `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` a potom klikněte na tlačítko **hledání** tlačítko.

### <a name="view-job-streams-for-a-job"></a>Zobrazení datové proudy z úlohy úlohy
Při ladění projektu, můžete také prozkoumat datové proudy úlohy. Následující dotaz zobrazí všechny datové proudy pro jednu úlohu s identifikátorem GUID 2ebd22ea-e05e-4eb9 - 9d 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Stav zobrazení historie úlohy
Nakonec můžete chtít vizualizovat historii úloh v čase. Tento dotaz můžete použít k vyhledání stavu vašich úloh v čase.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Log Analytics historie úlohy stavu grafu](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Odebrání nastavení diagnostiky

K odebrání nastavení diagnostiky účtu Automation, spusťte následující příkazy:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Souhrn

Poslat data stavu a datový proud úlohy Automation na protokoly Azure monitoru, můžete získat lepší přehled o stavu vašich úloh služby Automation pomocí:
+ Nastavení výstrah, které vás upozorní, když dojde k problému.
+ Použití vlastní zobrazení a vyhledávací dotazy k vizualizaci výsledků sad runbook, stav úlohy runbooku a další související klíčových indikátorů nebo metriky.  

Protokoly Azure monitoru poskytuje větší provozní viditelnost do úloh služby Automation a pomůžou řešit incidenty rychlejší.  

## <a name="next-steps"></a>Další postup
* Další informace o tom, jak vytvářet různé vyhledávací dotazy a kontrolovat protokoly úloh služby Automation s protokoly Azure monitoru, naleznete v tématu [prohledávání protokolu ve službě Azure Monitor protokoly](../log-analytics/log-analytics-log-searches.md).
* Postup vytvoření a načtení výstupu a chybové zprávy z runbooků najdete v tématu [Runbook výstup a zprávy](automation-runbook-output-and-messages.md).
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Další informace o zdrojích pro shromažďování dat a protokolování Azure Monitor, naleznete v tématu [shromažďování dat úložiště Azure ve službě Azure Monitor protokoly přehled](../azure-monitor/platform/collect-azure-metrics-logs.md).

