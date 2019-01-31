---
title: Datový model pro službu Azure Backup
description: Tento článek pojednává o podrobnosti modelu Power BI data pro sestavy Azure Backup.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: adigan
ms.openlocfilehash: c6160570644da108ba713e8229b38f9587495c92
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299414"
---
# <a name="data-model-for-azure-backup-reports"></a>Datový model pro sestavy Azure Backup
Tento článek popisuje datový model Power BI používá k vytváření sestav Azure Backup. Pomocí tohoto modelu, můžete filtrovat existující sestavy na základě příslušných polí a další důležité je, vytvořit vlastní sestavy pomocí tabulek a polí v modelu. 

## <a name="creating-new-reports-in-power-bi"></a>Vytváření nových sestav v Power BI
Power BI poskytuje přizpůsobení funkce, pomocí které můžete [vytváření sestav pomocí modelu](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Pomocí Azure Backup datového modelu
Následující pole k dispozici jako součást modelu dat můžete použít k vytváření sestav a přizpůsobení stávajících sestav.

### <a name="alert"></a>Výstrahy
Tato tabulka obsahuje základní polí a agregací přes různé výstrahy související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Celé číslo |Počet upozornění vytvořená ve vybraném časovém období |
| %ActiveAlertsCreatedInPeriod |Procentuální podíl |Procento aktivních výstrah ve vybraném časovém období |
| %CriticalAlertsCreatedInPeriod |Procentuální podíl |Procento kritické výstrahy za vybrané časové období |
| AlertOccurrenceDate |Datum |Datum, kdy byla výstraha vytvořena |
| AlertSeverity |Text |Závažnost výstrahy, například kritický |
| AlertStatus |Text |Stav výstrahy například aktivní |
| AlertType |Text |Typ vygenerovaných výstrah, například zálohování |
| AlertUniqueId |Text |Jedinečné Id generované výstrahy |
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Desetinné číslo |Průměrná doba (v minutách) k vyřešení výstrahy pro vybrané časové období |
| EntityState |Text |Aktuální stav výstrahy objektu, například aktivní, odstraněno |

### <a name="backup-item"></a>Zálohovaná položka
Tato tabulka obsahuje základní polí a agregací přes různá backup pole související položku.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #BackupItems |Celé číslo |Počet zálohovaných položek |
| #UnprotectedBackupItems |Celé číslo |Počet zálohovaných položek zastaveno z důvodu ochrany nebo nakonfigurovaná pro zálohování, ale nebylo zahájeno zálohování|
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| BackupItemFriendlyName |Text |Popisný název zálohované položky |
| BackupItemId |Text |ID zálohovaná položka |
| BackupItemName |Text |Název zálohované položky |
| BackupItemType |Text |Typ položky zálohování například virtuální počítač, FileFolder |
| EntityState |Text |Aktuální stav objektu zálohovaná položka například aktivní, odstraněno |
| LastBackupDateTime |Datum a čas |Čas poslední zálohy pro vybranou položku Zálohování |
| LastBackupState |Text |Stav poslední zálohy pro vybrané záložní položky například úspěšné, neúspěšné |
| LastSuccessfulBackupDateTime |Datum a čas |Čas posledního úspěšného zálohování pro vybrané záložní položky |
| ProtectionState |Text |Aktuální stav ochrany pro příklad, Protected, ProtectionStopped zálohované položky |

### <a name="calendar"></a>Kalendář
Tato tabulka obsahuje podrobné informace o kalendáři související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Datum |Datum |Datum vybraného pro filtrování dat |
| DateKey |Text |Jedinečný klíč pro každé datum položky |
| DayDiff |Desetinné číslo |Například rozdíl v den pro filtrování dat, 0 označuje data aktuální den, jeden z předchozího dne a data označuje hodnota -1, 0 a -1 označuje data pro aktuální a předchozí den  |
| Měsíc |Text |Měsíc roku vybraného pro filtrování dat začínajícího první den měsíce a končí 31. den |
| MonthDate | Datum |Data za měsíc po měsíci ukončení, vybraného pro filtrování dat |
| MonthDiff |Desetinné číslo |Rozdíl v měsíci pro filtrování dat, například, hodnota 0 znamená data z aktuálního měsíce, data z předchozího měsíce označuje hodnota -1, 0 a -1 označuje data pro aktuální a předchozí měsíc |
| Týden |Text |Týden vybraného pro filtrování dat, týden začíná v neděli a končí v sobotu |
| WeekDate |Datum |Datum v týdnu po týdnu ukončení, vybraného pro filtrování dat |
| WeekDiff |Desetinné číslo |Například rozdíl v týdnu pro filtrování dat, 0 označuje aktuální týden data, data předchozího týdne označuje hodnota -1, 0 a -1 označuje data pro aktuální a předchozí týden |
| Rok |Text |Kalendářní rok vybraného pro filtrování dat |
| YearDate |Datum |Datum ukončení rok roku vybraného pro filtrování dat |

### <a name="job"></a>Úloha
Tato tabulka obsahuje základní polí a agregací v různých oblastech souvisejících s úlohami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Celé číslo |Počet úloh vytvořených ve vybraném časovém období |
| %FailuresForJobsCreatedInPeriod |Procentuální podíl |Procento celkové selháním úloh ve vybraném časovém období |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Desetinné číslo |80 hodnoty percentilu data přenesená v MB pro **zálohování** úlohy vytvořené ve vybraném časovém období |
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **dokončené zálohování** úlohy vytvořené ve vybraném časovém období |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **bylo dokončeno obnovení** úlohy vytvořené ve vybraném časovém období |
| BackupStorageDestination |Text |Cílové úložiště pro zálohování o cloudu, například disku  |
| EntityState |Text |Aktuální stav objektu úlohy, například aktivní, odstraněno |
| JobFailureCode |Text |Řetězec kódu chyby kvůli které došlo k selhání úlohy |
| JobOperation |Text |Operace, pro který se úloha spustí například zálohování, obnovení, nakonfigurujte zálohování |
| JobStartDate |Datum |Datum spuštění úlohy |
| JobStartTime |Čas |Při spuštění úlohy |
| JobStatus |Text |Stav dokončené úlohy pro příklad, Completed, Failed |
| JobUniqueId |Text |Jedinečné Id pro identifikaci úlohy |

### <a name="policy"></a>Zásada
Tato tabulka obsahuje základní polí a agregací přes různé zásady související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Policies |Celé číslo |Počet zásad zálohování, které existují v systému |
| #PoliciesInUse |Celé číslo |Počet aktuálně používá pro konfiguraci zálohování zásad |
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| BackupDaysOfTheWeek |Text |Dny v týdnu, kdy bylo naplánováno zálohy |
| BackupFrequency |Text |Frekvence, se kterým se spouštět zálohování, třeba každý den, každý týden |
| BackupTimes |Text |Datum a čas, kdy jsou naplánované zálohy |
| DailyRetentionDuration |Celé číslo |Doba trvání celkový uchování ve dnech pro nastavená zálohování |
| DailyRetentionTimes |Text |Datum a čas, kdy byl nakonfigurován denní uchovávání |
| EntityState |Text |Aktuální stav objektu zásad, například aktivní, odstraněno |
| MonthlyRetentionDaysOfTheMonth |Text |Data v měsíci vybrali měsíční uchovávání |
| MonthlyRetentionDaysOfTheWeek |Text |Vybrané dny v týdnu pro měsíční uchovávání |
| MonthlyRetentionDuration |Desetinné číslo |Doba uchování celkový počet měsíců pro nastavená zálohování |
| MonthlyRetentionFormat |Text |Zadejte konfiguraci pro měsíční uchovávání například každý den podle týdně podle týdnů dnů |
| MonthlyRetentionTimes |Text |Datum a čas, kdy je nakonfigurován měsíční uchovávání |
| MonthlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy měsíční uchovávání dat je třeba nakonfigurovat First, Last atd. |
| PolicyName |Text |Název definice zásady |
| PolicyUniqueId |Text |Jedinečný identifikátor zásad |
| RetentionType |Text |Zadejte zásady uchovávání informací například, denně, týdně, měsíčně, ročně |
| WeeklyRetentionDaysOfTheWeek |Text |Vybrané dny v týdnu pro týdenní uchovávání |
| WeeklyRetentionDuration |Desetinné číslo |Celková doba uchování týdenní v týdnech nakonfigurované zálohování |
| WeeklyRetentionTimes |Text |Datum a čas, kdy nakonfigurovat týdenní uchovávání |
| YearlyRetentionDaysOfTheMonth |Text |Data v měsíci vybraných pro roční uchovávání |
| YearlyRetentionDaysOfTheWeek |Text |Vybrané dny v týdnu pro roční uchovávání |
| YearlyRetentionDuration |Desetinné číslo |Doba uchování celkový počet v letech nastavená zálohování |
| YearlyRetentionFormat |Text |Zadejte konfiguraci pro roční uchovávání například každý den podle týdně podle týdnů dnů |
| YearlyRetentionMonthsOfTheYear |Text |Měsíce roku vybraných pro roční uchovávání |
| YearlyRetentionTimes |Text |Datum a čas, kdy je nakonfigurován roční uchovávání |
| YearlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy roční uchovávání dat je třeba nakonfigurovat First, Last atd. |

### <a name="protected-server"></a>Chráněný Server
Tato tabulka obsahuje základní polí a agregací přes různé chráněné pole týkající se serveru.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedServers |Celé číslo |Počet chráněných serverů |
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| AzureBackupAgentOSType |Text |Typ operačního systému agenta Azure Backup |
| AzureBackupAgentOSVersion |Text |Verze operačního systému agenta Azure Backup |
| AzureBackupAgentUpdateDate |Text |Datum, kdy došlo k aktualizaci agenta Backup Agent |
| AzureBackupAgentVersion |Text |Číslo verze agenta zálohování |
| BackupManagementType |Text |Typ zprostředkovatele pro provádění zálohování například IaaSVM FileFolder |
| EntityState |Text |Aktuální stav objektu chráněného serveru, například aktivní, odstraněno |
| ProtectedServerFriendlyName |Text |Popisný název chráněného serveru |
| ProtectedServerName |Text |Název chráněného serveru |
| ProtectedServerType |Text |Typ chráněného serveru zálohovat například IaaSVMContainer |
| ProtectedServerName |Text |Název chráněného serveru, na které zálohovaná položka patří |
| RegisteredContainerId |Text |ID kontejneru zaregistrované pro zálohování |

### <a name="storage"></a>Storage
Tato tabulka obsahuje základní polí a agregací přes různá pole týkající se úložiště.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedInstances |Desetinné číslo |Počet chráněných instancí sloužící k výpočtu front-endu úložiště fakturace, vypočítané podle nejnovější hodnoty ve vybraném časovém |
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| CloudStorageInMB |Desetinné číslo |Cloudové zálohování úložiště využitá službou zálohování, které se počítá na základě nejnovější hodnoty ve vybraném časovém |
| EntityState |Text |Aktuální stav objektu, například aktivní, odstraněno |
| LastUpdatedDate |Datum |Datum poslední aktualizace vybraný řádek |

### <a name="time"></a>Čas
Tato tabulka obsahuje podrobné informace o souvisejících s časem pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Hodina |Čas |Hodina dne, například 1:00:00 PM |
| HourNumber |Desetinné číslo |Hodina číslo za den, například 13,00 |
| Minuta |Desetinné číslo |Minutu v hodině |
| PeriodOfTheDay |Text |Časový úsek období během dne, například 12 3 AM |
| Čas |Čas |Čas, například 12:00:01 dop. |
| TimeKey |Text |Hodnota klíče k reprezentaci čas |

### <a name="vault"></a>Trezor
Tato tabulka obsahuje základní polí a agregací přes různá pole týkající se trezoru.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Vaults |Celé číslo |Počet trezorů |
| AsOnDateTime |Datum a čas |Nejnovější čas aktualizace pro vybraný řádek |
| AzureDataCenter |Text |Datové centrum, ve kterém se úložiště nachází |
| EntityState |Text |Aktuální stav objektu úložiště například aktivní, odstraněno |
| StorageReplicationType |Text |Typ replikace úložiště pro trezor například GeoRedundant |
| SubscriptionId |Text |Id předplatného zákazníka vybraného generování sestav |
| VaultName |Text |Název úložiště |
| VaultTags |Text |Značky přiřazeny do trezoru |

## <a name="next-steps"></a>Další postup
Jakmile zkontrolujete datový model pro vytváření sestav Azure Backup, další podrobnosti o vytváření a zobrazování sestav Power BI najdete v následujících článcích.

* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrování sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
