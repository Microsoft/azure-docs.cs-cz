---
title: Dotazování protokolů z Azure Automation Start/Stop VMs during off-hours
description: V tomto článku se dozvíte, jak používat Azure Monitor k dotazování na data protokolu generovaná pomocí Start/Stop VMs during off-hours.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3e9e924d6626d9f0dcd2db8a5e8b8f90a0aa01ce
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593851"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Protokoly dotazů z funkce Start/Stop VMs during off-hours

Azure Automation přepošle dva typy záznamů do propojeného pracovního prostoru Log Analytics: protokoly úloh a datové proudy úloh. Tento článek kontroluje data, která jsou k dispozici pro [dotazy](../azure-monitor/logs/log-query-overview.md) v Azure monitor.

## <a name="job-logs"></a>Protokoly úloh

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs.|
|CorrelationId | Identifikátor GUID, který představuje ID korelace úlohy Runbooku.|
|JobId | Identifikátor GUID, který je ID úlohy Runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha.|
|resourceId | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Stav úlohy runbooku. Možné hodnoty:<br>- Spuštěno<br>- Zastaveno<br>- Pozastaveno<br>- Neúspěch<br>- Úspěch|
|resultDescription | Popisuje výsledný stav úlohy runbooku. Možné hodnoty:<br>- Úloha se spustila<br>- Zpracování úlohy se nezdařilo<br>- Úloha je dokončená|
|RunbookName | Určuje název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro automatizaci je hodnota OpsManager|
|StreamType | Určuje typ události. Možné hodnoty:<br>- Podrobné<br>- Výstup<br>- Chyba<br>- Varování|
|SubscriptionId | Určuje ID předplatného úlohy.
|Čas | Datum a čas provedení úlohy runbooku.|

## <a name="job-streams"></a>Datové proudy úlohy

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobStreams.|
|JobId | Identifikátor GUID, který je ID úlohy Runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je tato hodnota úloha.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|resourceId | Určuje ID prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Výsledek úlohy runbooku v době, kdy byla událost vygenerována. Možná hodnota je:<br>- Probíhá zpracování|
|resultDescription | Zahrnuje výstupní datový proud z runbooku.|
|RunbookName | Název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro automatizaci je hodnota OpsManager.|
|StreamType | Typ datového proudu úlohy. Možné hodnoty:<br>– Průběh<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné|
|Čas | Datum a čas provedení úlohy runbooku.|

Když provedete jakékoli prohledávání protokolů, které vrátí záznamy kategorie **JobLogs** nebo **JobStreams**, můžete vybrat zobrazení **JobLogs** nebo **JobStreams** , které zobrazí sadu dlaždic shrnujících aktualizace vrácené hledáním.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné nástrojem Start/Stop VMs during off-hours.

|Dotaz | Description|
|----------|----------|
|Najde úlohy pro Runbook ScheduledStartStop_Parent, které byly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook ScheduledStartStop_Parent, které nebyly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook SequencedStartStop_Parent, které byly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook SequencedStartStop_Parent, které nebyly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Další kroky

* Pokud chcete tuto funkci nastavit, přečtěte si téma [Konfigurace virtuálních počítačů zastavit/spustit v době mimo špičku](automation-solution-vm-management-config.md).
* Informace o výstrahách protokolů během nasazování funkcí najdete v tématu [vytvoření výstrah protokolu pomocí Azure monitor](../azure-monitor/alerts/alerts-log.md).
* Informace o řešení chyb funkcí najdete v tématu [řešení potíží s Start/Stop VMS during off-hours](troubleshoot/start-stop-vm.md).