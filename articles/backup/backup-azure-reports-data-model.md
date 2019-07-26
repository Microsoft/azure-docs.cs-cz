---
title: Datový model pro Azure Backup
description: Tento článek pojednává o Power BI podrobností o datových modelech pro sestavy Azure Backup.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: 66245a25af000d999ded87c58e3ba225b373b101
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465600"
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
| AlertSeverity |Text |Závažnost výstrahy, například kritická |
| AlertStatus |Text |Stav výstrahy, například aktivní |
| AlertType |Text |Typ generované výstrahy, například zálohování |
| AlertUniqueId |Text |Jedinečné ID generované výstrahy |
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Desetinné číslo |Průměrná doba (v minutách) pro vyřešení výstrahy pro vybrané časové období |
| EntityState |Text |Aktuální stav objektu výstrahy, například aktivní, odstraněno |

### <a name="backup-item"></a>Zálohovaná položka
Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s položkami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #BackupItems |Celé číslo |Počet zálohovaných položek |
| #UnprotectedBackupItems |Celé číslo |Počet zálohovaných položek, které se zastavily pro ochranu nebo jsou nakonfigurované pro zálohování, ale zálohování se nezahájil|
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| BackupItemFriendlyName |Text |Popisný název zálohované položky |
| BackupItemId |Text |ID zálohované položky |
| BackupItemName |Text |Název zálohované položky |
| BackupItemType |Text |Typ zálohované položky, třeba VM, Folder |
| EntityState |Text |Aktuální stav objektu zálohované položky, například Active, Deleted |
| LastBackupDateTime |Date/Time |Čas posledního zálohování pro vybranou zálohovanou položku |
| LastBackupState |Text |Stav poslední zálohy pro vybranou zálohovanou položku, například úspěch, selhání |
| LastSuccessfulBackupDateTime |Date/Time |Čas posledního úspěšného zálohování pro vybranou zálohovanou položku |
| ProtectionState |Text |Aktuální stav ochrany zálohované položky, například Protected, ProtectionStopped |

### <a name="calendar"></a>Kalendář
Tato tabulka poskytuje podrobnosti o polích souvisejících s kalendářem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Date |Date |Datum vybrané pro filtrování dat |
| DateKey |Text |Jedinečný klíč pro každou položku data |
| DayDiff |Desetinné číslo |Rozdíl v den filtrování dat například 0 indikuje data aktuálního dne, hodnota-1 označuje předchozí data dne, 0 a-1 značí data pro aktuální a předchozí den.  |
| Měsíc |Text |Měsíc roku vybraného pro filtrování dat, měsíc začíná prvním dnem a končí dnem 31. |
| MonthDate | Date |Datum v měsíci, kdy měsíc končí, vybrané pro filtrování dat |
| MonthDiff |Desetinné číslo |Rozdíl v měsíci pro filtrování dat například 0 označuje data aktuálního měsíce,-1 znamená data předchozího měsíce, 0 a-1 značí data pro aktuální a předchozí měsíc. |
| Týden |Text |Týden vybraný pro filtrování dat, týden začíná v neděli a končí v sobotu. |
| WeekDate |Date |Datum v týdnu, kdy týden končí výběr pro filtrování dat |
| WeekDiff |Desetinné číslo |Rozdíl v týdnu pro filtrování dat například 0 označuje data z aktuálního týdne,-1 znamená data z předchozího týdne, 0 a-1 značí data pro aktuální a předchozí týden. |
| Rok |Text |Kalendářní rok vybraný pro filtrování dat |
| YearDate |Date |Datum v roce, kdy končí rok, vybrané pro filtrování dat |

### <a name="job"></a>Úloha
Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s úlohami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Celé číslo |Počet úloh vytvořených ve vybraném časovém období |
| %FailuresForJobsCreatedInPeriod |Procento |Procento celkového počtu selhání úloh ve vybraném časovém období |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Desetinné číslo |hodnota 80th percentilu dat přenesených v MB pro úlohy **zálohování** vytvořené ve vybraném časovém období |
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **dokončené úlohy zálohování** vytvořené ve vybraném časovém období |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **dokončené úlohy obnovení** , které se vytvořily ve vybraném časovém období |
| BackupStorageDestination |Text |Cíl úložiště zálohování, třeba Cloud, disk  |
| EntityState |Text |Aktuální stav objektu úlohy, například aktivní, odstraněno |
| JobFailureCode |Text |Řetězec kódu chyby, protože došlo k selhání úlohy |
| JobOperation |Text |Operace, pro kterou je úloha spuštěná, například zálohování, obnovení, konfigurace zálohování |
| JobStartDate |Date |Datum spuštění úlohy |
| JobStartTime |Time |Čas spuštění úlohy |
| JobStatus |Text |Stav dokončené úlohy, například dokončeno, neúspěšné |
| JobUniqueId |Text |Jedinečné ID pro identifikaci úlohy |

### <a name="policy"></a>Zásada
Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s zásadami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Policies |Celé číslo |Počet zásad zálohování, které existují v systému |
| #PoliciesInUse |Celé číslo |Počet zásad, které se aktuálně používají ke konfiguraci záloh |
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| BackupDaysOfTheWeek |Text |Dny v týdnu, kdy bylo naplánováno zálohování |
| BackupFrequency |Text |Frekvence, se kterou jsou zálohy spouštěny například denně, týdně |
| BackupTimes |Text |Datum a čas plánování zálohování |
| DailyRetentionDuration |Celé číslo |Celková doba uchování ve dnech pro nakonfigurované zálohy |
| DailyRetentionTimes |Text |Datum a čas, kdy se nakonfigurovali denní uchovávání |
| EntityState |Text |Aktuální stav objektu zásad, například Active, Deleted |
| MonthlyRetentionDaysOfTheMonth |Text |Data měsíce vybraná pro měsíční uchování |
| MonthlyRetentionDaysOfTheWeek |Text |Dny v týdnu vybrané pro měsíční uchování |
| MonthlyRetentionDuration |Desetinné číslo |Celková doba uchování v měsících pro nakonfigurované zálohy |
| MonthlyRetentionFormat |Text |Typ konfigurace pro měsíční uchovávání, například denně pro každý den na základě dne, týdně na základě týdne |
| MonthlyRetentionTimes |Text |Datum a čas, kdy se nakonfiguruje měsíční uchování |
| MonthlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy je nastaveno měsíční uchování, například první, poslední atd. |
| PolicyName |Text |Název definované zásady |
| PolicyUniqueId |Text |Jedinečné ID pro identifikaci zásad |
| RetentionType |Text |Typ zásad uchovávání dat, například denně, týdně, měsíčně, ročně |
| WeeklyRetentionDaysOfTheWeek |Text |Dny v týdnu vybrané pro týdenní uchování |
| WeeklyRetentionDuration |Desetinné číslo |Celková doba uchování v týdnu pro nakonfigurované zálohy v týdnech |
| WeeklyRetentionTimes |Text |Datum a čas, kdy je nastaveno týdenní uchovávání |
| YearlyRetentionDaysOfTheMonth |Text |Data v měsíci vybraná pro roční uchování |
| YearlyRetentionDaysOfTheWeek |Text |Dny v týdnu vybrané pro roční uchování |
| YearlyRetentionDuration |Desetinné číslo |Celková doba uchování v letech pro nakonfigurovaná zálohování |
| YearlyRetentionFormat |Text |Typ konfigurace pro roční uchování, například denně na základě dne, týdně na základě týdne |
| YearlyRetentionMonthsOfTheYear |Text |Měsíců v roce vybraných pro roční uchování |
| YearlyRetentionTimes |Text |Datum a čas, kdy se nakonfiguruje roční uchování |
| YearlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy je nastaveno roční uchování, například první, poslední atd. |

### <a name="protected-server"></a>Chráněný Server
Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s chráněným serverem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedServers |Celé číslo |Počet chráněných serverů |
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| AzureBackupAgentOSType |Text |Typ operačního systému Azure Backup agenta |
| AzureBackupAgentOSVersion |Text |Verze operačního systému agenta Azure Backup |
| AzureBackupAgentUpdateDate |Text |Datum, kdy se Agent zálohování agenta aktualizoval |
| AzureBackupAgentVersion |Text |Číslo verze zálohy agenta |
| BackupManagementType |Text |Typ poskytovatele pro provádění zálohování, například IaaSVM, složka adresáře |
| EntityState |Text |Aktuální stav objektu chráněného serveru, například aktivní, odstraněno |
| ProtectedServerFriendlyName |Text |Popisný název chráněného serveru |
| ProtectedServerName |Text |Název chráněného serveru |
| ProtectedServerType |Text |Typ chráněného serveru se zálohoval například IaaSVMContainer |
| ProtectedServerName |Text |Název chráněného serveru, ke kterému patří záložní položka |
| RegisteredContainerId |Text |ID kontejneru registrovaného pro zálohování |

### <a name="storage"></a>Storage
Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s úložištěm.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedInstances |Desetinné číslo |Počet chráněných instancí použitých pro výpočet front-endu úložiště ve vyúčtování vypočítaný na základě nejnovější hodnoty ve vybraném čase |
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| CloudStorageInMB |Desetinné číslo |Cloudové úložiště záloh používané v zálohách vypočítané na základě nejnovější hodnoty ve vybraném čase |
| EntityState |Text |Aktuální stav objektu, například Active, Deleted |
| LastUpdatedDate |Date |Datum poslední aktualizace vybraného řádku |

### <a name="time"></a>Time
Tato tabulka poskytuje podrobnosti o polích souvisejících s časem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Hodina |Time |Hodina dne, například 1:00:00 odp. |
| HourNumber |Desetinné číslo |Číslo hodiny v den, například 13,00 |
| Minuta |Desetinné číslo |Minuta v hodinách |
| PeriodOfTheDay |Text |Slot pro časové období v den, například 12-3 dop. |
| Time |Time |Denní doba, například 12:00:01 dop. |
| TimeKey |Text |Hodnota klíče představující čas |

### <a name="vault"></a>Trezor
Tato tabulka poskytuje základní pole a agregace v různých polích souvisejících s trezorem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Vaults |Celé číslo |Počet trezorů |
| AsOnDateTime |Date/Time |Čas poslední aktualizace pro vybraný řádek |
| AzureDataCenter |Text |Datové centrum, kde se nachází trezor |
| EntityState |Text |Aktuální stav objektu trezoru, například Active, Deleted |
| StorageReplicationType |Text |Typ replikace úložiště pro trezor, například, Neredundantní |
| SubscriptionId |Text |ID předplatného zákazníka vybraného pro generování sestav |
| VaultName |Text |Název trezoru |
| VaultTags |Text |Značky přidružené k trezoru |

## <a name="next-steps"></a>Další kroky
Až si prohlédnete datový model pro vytváření sestav Azure Backup, přečtěte si následující články, kde najdete další podrobnosti o vytváření a zobrazování sestav v Power BI.

* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrování sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
