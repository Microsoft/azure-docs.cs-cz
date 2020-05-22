---
title: Dotazování protokolů z Azure Automation Start/Stop VMs during off-hours
description: V tomto článku se dozvíte, jak používat Azure Monitor k dotazování na data protokolu generovaná pomocí Start/Stop VMs during off-hours.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 49b573c5cd0125ba378f01db05b925f8e5eef3eb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743726"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Dotazování protokolů z Start/Stop VMs during off-hours

Azure Automation přepošle dva typy záznamů do propojeného pracovního prostoru Log Analytics: protokoly úloh a datové proudy úloh. Tento článek kontroluje data, která jsou k dispozici pro [dotazy](../azure-monitor/log-query/log-query-overview.md) v Azure monitor.

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
|Time | Datum a čas provedení úlohy runbooku.|

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
|Time | Datum a čas provedení úlohy runbooku.|

Když provedete jakékoli prohledávání protokolů, které vrátí záznamy kategorie **JobLogs** nebo **JobStreams**, můžete vybrat zobrazení **JobLogs** nebo **JobStreams** , které zobrazí sadu dlaždic shrnujících aktualizace vrácené hledáním.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné nástrojem Start/Stop VMs during off-hours.

|Dotaz | Popis|
|----------|----------|
|Najde úlohy pro Runbook ScheduledStartStop_Parent, které byly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook ScheduledStartStop_Parent, které nebyly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook SequencedStartStop_Parent, které byly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najde úlohy pro Runbook SequencedStartStop_Parent, které nebyly úspěšně dokončeny. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Další kroky

* [Vytváření výstrah protokolu pomocí Azure Monitor](../azure-monitor/platform/alerts-log.md)
* [Konfigurace virtuálních počítačů zastavit/spustit v době mimo špičku](automation-solution-vm-management-config.md)
* [Řešení potíží s Start/Stop VMs during off-hours](troubleshoot/start-stop-vm.md)