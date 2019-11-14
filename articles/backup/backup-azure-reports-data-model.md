---
title: Datový model pro Azure Backup
description: Tento článek pojednává o Power BI podrobností o datových modelech pro sestavy Azure Backup.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: f3db0e6a78eada52f2b5936f5194138d97ebcca6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074893"
---
# <a name="data-model-for-azure-backup-reports"></a>Datový model pro sestavy Azure Backup

Tento článek popisuje Power BI datový model, který slouží k vytváření sestav Azure Backup. Pomocí tohoto datového modelu můžete filtrovat existující sestavy na základě relevantních polí a důležitější je vytvořit vlastní sestavy pomocí tabulek a polí v modelu.

## <a name="creating-new-reports-in-power-bi"></a>Vytváření nových sestav v Power BI

Power BI poskytuje funkce pro přizpůsobení, pomocí kterých můžete [vytvářet sestavy pomocí datového modelu](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Použití datového modelu Azure Backup

Následující pole, která jsou součástí datového modelu, můžete použít k vytvoření sestav a přizpůsobení existujících sestav.

### <a name="alert"></a>Výstrahy

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s výstrahami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Celé číslo |Počet výstrah vytvořených ve vybraném časovém období |
| %ActiveAlertsCreatedInPeriod |Procento |Procento aktivních výstrah ve vybraném časovém období |
| %CriticalAlertsCreatedInPeriod |Procento |Procento kritických výstrah ve vybraném časovém období |
| AlertOccurrenceDate |Date |Datum vytvoření výstrahy |
| AlertSeverity |Text |Závažnost výstrahy Například kritický |
| AlertStatus |Text |Stav výstrahy Například aktivní |
| AlertType |Text |Typ generované výstrahy. Například zálohování |
| AlertUniqueId |Text |Jedinečné ID generované výstrahy |
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Desetinné číslo |Průměrná doba (v minutách) pro vyřešení výstrahy pro vybrané časové období |
| EntityState |Text |Aktuální stav objektu výstrahy. Například aktivní, odstraněno |

### <a name="backup-item"></a>Zálohovaná položka

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s položkami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #BackupItems |Celé číslo |Počet zálohovaných položek |
| #UnprotectedBackupItems |Celé číslo |Počet zálohovaných položek, které se zastavily pro ochranu nebo jsou nakonfigurované pro zálohování, ale zálohování se nezahájil|
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| BackupItemFriendlyName |Text |Popisný název zálohované položky |
| BackupItemId |Text |ID zálohované položky |
| BackupItemName |Text |Název zálohované položky |
| BackupItemType |Text |Typ zálohované položky Například virtuální počítač, složka adresáře |
| EntityState |Text |Aktuální stav objektu zálohované položky. Například aktivní, odstraněno |
| LastBackupDateTime |Datum a čas |Čas posledního zálohování pro vybranou zálohovanou položku |
| LastBackupState |Text |Stav poslední zálohy pro vybranou zálohovanou položku. Například úspěch, selhání |
| LastSuccessfulBackupDateTime |Datum a čas |Čas posledního úspěšného zálohování pro vybranou zálohovanou položku |
| ProtectionState |Text |Aktuální stav ochrany zálohované položky Například Protected, ProtectionStopped |

### <a name="calendar"></a>Kalendář

Tato tabulka poskytuje podrobnosti o polích souvisejících s kalendářem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Date |Date |Datum vybrané pro filtrování dat |
| DateKey |Text |Jedinečný klíč pro každou položku data |
| DayDiff |Desetinné číslo |Rozdíl v den filtrování dat Například 0 označuje data aktuálního dne, hodnota-1 označuje předchozí data dne, 0 a-1, označují data pro aktuální a předchozí den.  |
| Month |Text |Měsíc roku vybraného pro filtrování dat, měsíc začíná prvním dnem a končí dnem 31. |
| MonthDate | Date |Datum v měsíci, kdy měsíc končí, vybrané pro filtrování dat |
| MonthDiff |Desetinné číslo |Rozdíl v měsíci pro filtrování dat. Například 0 označuje data aktuálního měsíce, hodnota-1 označuje data předchozího měsíce, 0 a-1, označují data pro aktuální a předchozí měsíc. |
| Týden |Text |Týden vybraný pro filtrování dat, týden začíná v neděli a končí v sobotu. |
| WeekDate |Date |Datum v týdnu, kdy týden končí výběr pro filtrování dat |
| WeekDiff |Desetinné číslo |Rozdíl v týdnu pro filtrování dat Například 0 označuje data aktuálního týdne, hodnota-1 označuje data z předchozího týdne, 0 a-1, označují data pro aktuální a předchozí týden. |
| Year |Text |Kalendářní rok vybraný pro filtrování dat |
| YearDate |Date |Datum v roce, kdy končí rok, vybrané pro filtrování dat |

### <a name="job"></a>Úloha

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s úlohami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Celé číslo |Počet úloh vytvořených ve vybraném časovém období |
| %FailuresForJobsCreatedInPeriod |Procento |Procento celkového počtu selhání úloh ve vybraném časovém období |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Desetinné číslo |hodnota 80th percentilu dat přenesených v MB pro úlohy **zálohování** vytvořené ve vybraném časovém období |
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **dokončené úlohy zálohování** vytvořené ve vybraném časovém období |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **dokončené úlohy obnovení** , které se vytvořily ve vybraném časovém období |
| BackupStorageDestination |Text |Cíl úložiště zálohování. Například Cloud, disk  |
| EntityState |Text |Aktuální stav objektu úlohy. Například aktivní, odstraněno |
| JobFailureCode |Text |Řetězec kódu chyby, protože došlo k selhání úlohy |
| JobOperation |Text |Operace, pro kterou je úloha spuštěna. Například zálohování, obnovení, konfigurace zálohování |
| JobStartDate |Date |Datum spuštění úlohy |
| JobStartTime |Čas |Čas spuštění úlohy |
| JobStatus |Text |Stav dokončené úlohy. Například dokončeno, selhalo |
| JobUniqueId |Text |Jedinečné ID pro identifikaci úlohy |

### <a name="policy"></a>Zásada

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s zásadami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Policies |Celé číslo |Počet zásad zálohování, které existují v systému |
| #PoliciesInUse |Celé číslo |Počet zásad, které se aktuálně používají ke konfiguraci záloh |
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| BackupDaysOfTheWeek |Text |Dny v týdnu, kdy bylo naplánováno zálohování |
| BackupFrequency |Text |Frekvence, se kterou se spouští zálohy. Například denně, týdně |
| BackupTimes |Text |Datum a čas plánování zálohování |
| DailyRetentionDuration |Celé číslo |Celková doba uchování ve dnech pro nakonfigurované zálohy |
| DailyRetentionTimes |Text |Datum a čas, kdy se nakonfigurovali denní uchovávání |
| EntityState |Text |Aktuální stav objektu zásad Například aktivní, odstraněno |
| MonthlyRetentionDaysOfTheMonth |Text |Data měsíce vybraná pro měsíční uchování |
| MonthlyRetentionDaysOfTheWeek |Text |Dny v týdnu vybrané pro měsíční uchování |
| MonthlyRetentionDuration |Desetinné číslo |Celková doba uchování v měsících pro nakonfigurované zálohy |
| MonthlyRetentionFormat |Text |Typ konfigurace pro měsíční uchování. Například denně pro každý den v týdnu na základě týdne |
| MonthlyRetentionTimes |Text |Datum a čas, kdy se nakonfiguruje měsíční uchování |
| MonthlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy je nastaveno měsíční uchovávání. Například First, Last atd. |
| PolicyName |Text |Název definované zásady |
| PolicyUniqueId |Text |Jedinečné ID pro identifikaci zásad |
| RetentionType |Text |Typ zásady uchovávání informací Například denně, týdně, měsíčně, ročně |
| WeeklyRetentionDaysOfTheWeek |Text |Dny v týdnu vybrané pro týdenní uchování |
| WeeklyRetentionDuration |Desetinné číslo |Celková doba uchování v týdnu pro nakonfigurované zálohy v týdnech |
| WeeklyRetentionTimes |Text |Datum a čas, kdy je nastaveno týdenní uchovávání |
| YearlyRetentionDaysOfTheMonth |Text |Data v měsíci vybraná pro roční uchování |
| YearlyRetentionDaysOfTheWeek |Text |Dny v týdnu vybrané pro roční uchování |
| YearlyRetentionDuration |Desetinné číslo |Celková doba uchování v letech pro nakonfigurovaná zálohování |
| YearlyRetentionFormat |Text |Typ konfigurace pro roční uchování Například denně pro každý den v týdnu na základě týdne |
| YearlyRetentionMonthsOfTheYear |Text |Měsíců v roce vybraných pro roční uchování |
| YearlyRetentionTimes |Text |Datum a čas, kdy se nakonfiguruje roční uchování |
| YearlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy je nastaveno roční uchovávání. Například First, Last atd. |

### <a name="protected-server"></a>Chráněný Server

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s chráněným serverem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedServers |Celé číslo |Počet chráněných serverů |
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| AzureBackupAgentOSType |Text |Typ operačního systému Azure Backup agenta |
| AzureBackupAgentOSVersion |Text |Verze operačního systému agenta Azure Backup |
| AzureBackupAgentUpdateDate |Text |Datum, kdy se Agent zálohování agenta aktualizoval |
| AzureBackupAgentVersion |Text |Číslo verze zálohy agenta |
| BackupManagementType |Text |Typ poskytovatele pro provádění zálohování. Například IaaSVM, složka. |
| EntityState |Text |Aktuální stav objektu chráněného serveru. Například aktivní, odstraněno |
| ProtectedServerFriendlyName |Text |Popisný název chráněného serveru |
| ProtectedServerName |Text |Název chráněného serveru |
| ProtectedServerType |Text |Byl zálohovaný typ chráněného serveru. Například IaaSVMContainer |
| ProtectedServerName |Text |Název chráněného serveru, ke kterému patří záložní položka |
| RegisteredContainerId |Text |ID kontejneru registrovaného pro zálohování |

### <a name="storage"></a>Storage

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s úložištěm.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedInstances |Desetinné číslo |Počet chráněných instancí použitých pro výpočet front-endu úložiště ve vyúčtování vypočítaný na základě nejnovější hodnoty ve vybraném čase |
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| CloudStorageInMB |Desetinné číslo |Cloudové úložiště záloh používané v zálohách vypočítané na základě nejnovější hodnoty ve vybraném čase |
| EntityState |Text |Aktuální stav objektu. Například aktivní, odstraněno |
| LastUpdatedDate |Date |Datum poslední aktualizace vybraného řádku |

### <a name="time"></a>Čas

Tato tabulka poskytuje podrobnosti o polích souvisejících s časem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Hodina |Čas |Hodina dne Například 1:00:00 odp. |
| HourNumber |Desetinné číslo |Počet hodin za den. Například 13,00 |
| Minuta |Desetinné číslo |Minuta v hodinách |
| PeriodOfTheDay |Text |Slot pro časové období v daném dni. Například 12-3 dop. |
| Čas |Čas |Čas dne Například 12:00:01 dop. |
| TimeKey |Text |Hodnota klíče představující čas |

### <a name="vault"></a>Trezor

Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s trezorem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Vaults |Celé číslo |Počet trezorů |
| AsOnDateTime |Datum a čas |Čas poslední aktualizace pro vybraný řádek |
| AzureDataCenter |Text |Datové centrum, kde se nachází trezor |
| EntityState |Text |Aktuální stav objektu trezoru. Například aktivní, odstraněno |
| StorageReplicationType |Text |Typ replikace úložiště pro trezor Například Neredundantní |
| SubscriptionId |Text |ID předplatného zákazníka vybraného pro generování sestav |
| vaultName |Text |Název trezoru |
| VaultTags |Text |Značky přidružené k trezoru |

## <a name="next-steps"></a>Další kroky

Až si prohlédnete datový model pro vytváření sestav Azure Backup, přečtěte si následující články, kde najdete další podrobnosti o vytváření a zobrazování sestav v Power BI.

* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrování sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
