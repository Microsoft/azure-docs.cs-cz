---
title: Jak dotazovat protokoly z řešení Start/Stop virtuálních her
description: Tento článek popisuje, jak dotazovat data protokolu generovaná řešením virtuálních počítačích Start/Stop z Azure Monitoru.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604724"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Jak dotazovat protokoly z řešení Start/Stop virtuálních her

Azure Automation předává dva typy záznamů do propojeného pracovního prostoru Log Analytics: protokoly úloh a toky úloh. Tato data jsou dostupná pro [dotaz](../azure-monitor/log-query/log-query-overview.md) v Azure Monitoru.

## <a name="job-logs"></a>Protokoly úloh

|Vlastnost | Popis|
|----------|----------|
|Volající |  Kdo operaci zahájil. Možnou hodnotou je e-mailová adresa nebo systém pro naplánované úlohy.|
|Kategorie | Klasifikace typu dat. Službě Automation odpovídá hodnota JobLogs.|
|CorrelationId | IDENTIFIKÁTOR GUID, který je ID korelace úlohy runbook.|
|JobId | IDENTIFIKÁTOR GUID, který je ID úlohy runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je hodnota Job.|
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
|JobId | IDENTIFIKÁTOR GUID, který je ID úlohy runbooku.|
|operationName | Určuje typ operace prováděné v Azure. Pro automatizaci je hodnota Job.|
|ResourceGroup | Určuje název skupiny prostředků příslušné úlohy runbooku.|
|resourceId | Určuje ID prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|ResourceProvider | Určuje službu Azure poskytující prostředky, které můžete nasadit a spravovat. Službě Automation odpovídá hodnota Azure Automation.|
|ResourceType | Určuje typ prostředku v Azure. V případě služby Automation je hodnotou účet služby Automation přidružený k příslušnému runbooku.|
|resultType | Výsledek úlohy runbooku v době, kdy byla událost vygenerována. Možná hodnota je:<br>- Probíhá zpracování|
|resultDescription | Zahrnuje výstupní datový proud z runbooku.|
|RunbookName | Název runbooku.|
|SourceSystem | Určuje zdrojový systém pro odeslaná data. Pro automatizaci je hodnota OpsManager.|
|StreamType | Typ datového proudu úlohy. Možné hodnoty:<br>- Pokrok<br>- Výstup<br>- Varování<br>- Chyba<br>- Ladění<br>- Podrobné|
|Time | Datum a čas provedení úlohy runbooku.|

Při každém hledání protokolu, který vrací záznamy kategorií **JobLogs** nebo **JobStreams**, můžete vybrat **JobLogs** nebo **JobStreams** zobrazení, které zobrazí sadu dlaždic shrnující aktualizace vrácené hledáním.

## <a name="sample-log-searches"></a>Ukázky hledání v protokolech

V následující tabulce jsou uvedeny ukázky hledání v protokolech pro záznamy úloh shromážděné tímto řešením.

|Dotazy | Popis|
|----------|----------|
|Najít úlohy pro ScheduledStartStop_Parent runbooku, které byly úspěšně dokončeny | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najít úlohy pro ScheduledStartStop_Parent runbooku, které nebyly úspěšně dokončeny | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najít úlohy pro SequencedStartStop_Parent runbooku, které byly úspěšně dokončeny | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Najít úlohy pro SequencedStartStop_Parent runbooku, které nebyly úspěšně dokončeny | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Další kroky

**Spuštění a zastavení virtuálních počítače během mimo pracovní dobu** řešení neobsahuje předdefinovanou sadu výstrah. Zkontrolujte [vytvořit výstrahy protokolu](../azure-monitor/platform/alerts-log.md) pomocí Azure Monitor se dozvíte, jak vytvořit úlohy se nezdařilo výstrahy pro podporu devOps nebo provozní procesy a postupy.