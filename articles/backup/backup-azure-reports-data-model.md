---
title: Datový model pro zálohování Azure
description: V tomto článku bude zmíněn podrobnosti modelu dat Power BI pro Azure Backup sestavy.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a17e011452f9b87c1201cea12f394a9cdd18e54b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606218"
---
# <a name="data-model-for-azure-backup-reports"></a>Datový model pro sestavy Azure Backup
Tento článek popisuje datový model Power BI, který se používá pro vytváření sestav Azure Backup. Používá tento datový model, na základě příslušných polí existující sestavy můžete filtrovat a více je nejdůležitější, vytvořte vlastní sestavy pomocí tabulky a pole v modelu. 

## <a name="creating-new-reports-in-power-bi"></a>Vytvoření nové sestavy v Power BI
Přizpůsobení funkcí, pomocí kterého můžete poskytuje Power BI [vytváření sestav pomocí datový model](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Pomocí Azure Backup datový model
Přizpůsobení stávajících sestav a vytváření sestav můžete použít následující pole, které jsou k dispozici jako součást datového modelu.

### <a name="alert"></a>Výstrahy
Tato tabulka obsahuje základní pole a agregace přes různé výstrahy související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Celé číslo |Počet výstrah, které jsou vytvořené v vybrané časové období |
| % ActiveAlertsCreatedInPeriod |Procenta |Procento aktivní výstrahy za vybrané časové období |
| % CriticalAlertsCreatedInPeriod |Procenta |Procento kritické výstrahy za vybrané časové období |
| AlertOccurenceDate |Datum |Datum vytvoření výstrahy |
| AlertSeverity |Text |Závažnost výstrahy například kritický |
| AlertStatus |Text |Stav výstrahy například aktivní |
| AlertType |Text |Typ vygenerovaných výstrah, například zálohování |
| AlertUniqueId |Text |Jedinečné Id generované výstrahy |
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Desetinné číslo |Průměrná doba (v minutách) k vyřešení výstrahy pro vybrané časové období |
| EntityState |Text |Aktuální stav výstrahy objektu, například aktivní, odstraněno |

### <a name="backup-item"></a>Zálohování položek
Tato tabulka poskytuje základní pole a agregace přes různé zálohování pole související s položkou.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #BackupItems |Celé číslo |Počet zálohování položek |
| #UnprotectedBackupItems |Celé číslo |Počet zálohování položek, které bylo zastaveno pro ochranu nebo nakonfigurovat pro zálohování, ale zálohování není spuštěna|
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| BackupItemFriendlyName |Text |Popisný název položky zálohování |
| BackupItemId |Text |ID položky zálohování |
| BackupItemName |Text |Název položky zálohování |
| BackupItemType |Text |Typ zálohování položky například virtuální počítač, FileFolder |
| EntityState |Text |Aktuální stav objektu zálohování položek, například aktivní, odstraněno |
| LastBackupDateTime |Datum/čas |Čas poslední zálohy pro vybranou položku Zálohování |
| LastBackupState |Text |Stav poslední zálohy pro vybranou položku zálohování například bylo úspěšné, neúspěšné |
| LastSuccessfulBackupDateTime |Datum/čas |Čas poslední úspěšné zálohy pro vybranou položku Zálohování |
| ProtectionState |Text |Aktuální stav ochrany zálohování položky například chráněné, ProtectionStopped |

### <a name="calendar"></a>Kalendář
Tato tabulka obsahuje podrobnosti o pole související s kalendáři.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Datum |Datum |Data vybraná pro filtrování dat |
| DateKey |Text |Jedinečný klíč pro každou položku Datum |
| DayDiff |Desetinné číslo |Rozdíl v den pro filtrování dat, například, 0 znamená data aktuálního dne, -1 označuje předchozí jeden den na data, 0 a -1 označuje dat pro aktuální a předchozí den  |
| Měsíc |Text |Měsíc v roce vybrané pro filtrování dat, měsíci začínajícího první den a končí na 31 dní |
| MonthDate | Datum |Datum v měsíci, kdy skončí měsíc, vybraný pro filtrování dat |
| MonthDiff |Desetinné číslo |Rozdíl v měsíci pro filtrování dat, například, 0 znamená data aktuálního měsíce, -1 označuje data předchozího měsíce, 0 a -1 označuje dat pro aktuální a předchozí měsíc |
| Týden |Text |Týden vybraný pro filtrování dat, týden začíná v neděli a končí na sobotu |
| WeekDate |Datum |Datum v týdnu, kdy skončí týden, vybraný pro filtrování dat |
| WeekDiff |Desetinné číslo |Rozdíl v týdnu pro filtrování dat, například, 0 znamená data aktuálního týdne, -1 označuje předchozího týdne data, 0 a -1 označuje dat pro aktuální a předchozí týden |
| Rok |Text |Kalendářní rok vybrané pro filtrování dat |
| YearDate |Datum |Datum v roce při ukončení roku, vybraný pro filtrování dat |

### <a name="job"></a>Úloha
Tato tabulka poskytuje základní pole a agregace přes různá pole související úlohy.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #JobsCreatedInPeriod |Celé číslo |Počet úloh vytvořených v zvolené časové období |
| % FailuresForJobsCreatedInPeriod |Procenta |Procento celkového selháním úloh ve zvolené časové období |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Desetinné číslo |80. hodnota percentilu dat přenesených v MB pro **zálohování** úlohy vytvořené v zvolené časové období |
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **dokončené zálohování** úlohy vytvořené v vybrané časové období |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Desetinné číslo |Průměrná doba v minutách pro **bylo dokončeno obnovení** úlohy vytvořené v vybrané časové období |
| BackupStorageDestination |Text |Cíl zálohování úložiště cloudu, například Disk  |
| EntityState |Text |Aktuální stav objektu úlohy, například aktivní, odstraněno |
| JobFailureCode |Text |Selhání kódu řetězec kvůli které došlo k selhání úlohy |
| JobOperation |Text |Operaci, pro kterou úlohy je třeba spustit zálohování, obnovení, nakonfigurujte zálohování |
| JobStartDate |Datum |Datum při spuštění úlohy |
| JobStartTime |Čas |Čas při spuštění úlohy |
| JobStatus |Text |Stav dokončení úlohy například dokončeno, se nezdařilo |
| JobUniqueId |Text |Jedinečné Id pro identifikaci úlohy |

### <a name="policy"></a>Zásada
Tato tabulka obsahuje základní pole a agregace přes různá pole související se zásadami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Policies |Celé číslo |Počet zásady zálohování, které existují v systému |
| #PoliciesInUse |Celé číslo |Počet zásad, které jsou právě používány pro konfiguraci zálohování |
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| BackupDaysOfTheWeek |Text |Dny v týdnu, kdy bylo naplánováno zálohování |
| BackupFrequency |Text |Frekvence, se kterým se spouštějí zálohování, například denně, týdně |
| BackupTimes |Text |Datum a čas, kdy jsou naplánované zálohy |
| DailyRetentionDuration |Celé číslo |Doba celkový uchování ve dnech pro nastavená zálohování |
| DailyRetentionTimes |Text |Datum a čas, kdy byl nakonfigurován denní uchování |
| EntityState |Text |Aktuální stav objektu zásad, například aktivní, odstraněno |
| MonthlyRetentionDaysOfTheMonth |Text |Kalendářních dat v měsíci vybrané pro měsíční uchování |
| MonthlyRetentionDaysOfTheWeek |Text |Vybrané dny v týdnu pro měsíční uchování |
| MonthlyRetentionDuration |Desetinné číslo |Doba uchování celkový počet měsíců pro nastavená zálohování |
| MonthlyRetentionFormat |Text |Zadejte konfigurace pro měsíční uchování například denně za den, každý týden základě za týden na základě |
| MonthlyRetentionTimes |Text |Datum a čas, když je nakonfigurovaný měsíční uchování |
| MonthlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy měsíční uchování je třeba nakonfigurovat First, Last atd. |
| PolicyName |Text |Název zásady definované |
| PolicyUniqueId |Text |Jedinečné Id pro identifikaci zásady |
| RetentionType |Text |Zadejte zásady uchovávání informací například denně, týdně, měsíčně, ročně |
| WeeklyRetentionDaysOfTheWeek |Text |Vybrané dny v týdnu pro týdenní uchování |
| WeeklyRetentionDuration |Desetinné číslo |Celková doba uchování týdenní týdnů pro nastavená zálohování |
| WeeklyRetentionTimes |Text |Datum a čas, když je nakonfigurovaný týdenního uchovávání |
| YearlyRetentionDaysOfTheMonth |Text |Kalendářních dat v měsíci vybrané pro roční uchování |
| YearlyRetentionDaysOfTheWeek |Text |Vybrané dny v týdnu pro roční uchování |
| YearlyRetentionDuration |Desetinné číslo |Doba uchování celkový počet roků pro nastavená zálohování |
| YearlyRetentionFormat |Text |Zadejte konfigurace pro roční uchování například denně za den, každý týden základě za týden na základě |
| YearlyRetentionMonthsOfTheYear |Text |Měsíce v roce vybrané pro roční uchování |
| YearlyRetentionTimes |Text |Datum a čas, když je nakonfigurovaný roční uchování |
| YearlyRetentionWeeksOfTheMonth |Text |Týdny v měsíci, kdy roční uchování je třeba nakonfigurovat First, Last atd. |

### <a name="protected-server"></a>Chráněného serveru
Tato tabulka poskytuje základní pole a agregace přes různé chráněná pole související serveru.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedServers |Celé číslo |Počet chráněných serverů |
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| AzureBackupAgentOSType |Text |Typ operačního systému, aplikace Azure Backup Agent |
| AzureBackupAgentOSVersion |Text |Verze operačního systému, aplikace Azure Backup Agent |
| AzureBackupAgentUpdateDate |Text |Datum aktualizace agenta agenta zálohování |
| AzureBackupAgentVersion |Text |Číslo verze agenta zálohování |
| BackupManagementType |Text |Typ zprostředkovatele pro provádění zálohování například IaaSVM FileFolder |
| EntityState |Text |Aktuální stav objektu chráněný server například aktivní, odstraněno |
| ProtectedServerFriendlyName |Text |Popisný název chráněného serveru |
| ProtectedServerName |Text |Název chráněného serveru |
| ProtectedServerType |Text |Typ chráněného serveru zálohovat například IaaSVMContainer |
| ProtectedServerName |Text |Název chráněného serveru, na která zálohování položka patří |
| RegisteredContainerId |Text |ID kontejneru zaregistrované pro zálohování |

### <a name="storage"></a>Úložiště
Tato tabulka obsahuje základní pole a agregace přes různá pole související s úložištěm.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #ProtectedInstances |Desetinné číslo |Počet sloužící k výpočtu front-endu úložiště v fakturace, počítaný na základě nejnovější hodnotu ve vybraném časovém chráněné instance |
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| CloudStorageInMB |Desetinné číslo |Cloudové zálohování úložiště používané zálohování, počítá na základě nejnovější hodnoty ve vybraném časovém |
| EntityState |Text |Aktuální stav objektu, například aktivní, odstraněno |
| LastUpdatedDate |Datum |Datum poslední aktualizace vybraného řádku |

### <a name="time"></a>Čas
Tato tabulka obsahuje podrobné informace o souvisejících s časem pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| Hodina |Čas |Hodina dne, například 1:00:00 PM |
| HourNumber |Desetinné číslo |Číslo hodinu dne například 13,00 |
| Minuta |Desetinné číslo |Minutu v hodině |
| PeriodOfTheDay |Text |Časový úsek období, za den, například AM 12-3 |
| Čas |Čas |Čas, například 12:00:01: 00 |
| TimeKey |Text |Hodnota klíče představují čas |

### <a name="vault"></a>Trezor
Tato tabulka nabízí v porovnání s různá pole související trezoru základní pole a agregace.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| #Vaults |Celé číslo |Počet trezorů |
| AsOnDateTime |Datum/čas |Nejnovější aktualizace dobu vybraného řádku |
| AzureDataCenter |Text |Datové centrum, kde se nachází úložiště |
| EntityState |Text |Aktuální stav objektu úložiště, například aktivní, odstraněné |
| StorageReplicationType |Text |Typ replikace úložiště pro trezor například GeoRedundant |
| SubscriptionId |Text |Id předplatného zákazníka vybrané pro generování sestav |
| VaultName |Text |Název trezoru |
| VaultTags |Text |Značky přidružené k trezoru |

## <a name="next-steps"></a>Další postup
Po prostudování datový model pro vytváření sestav Azure Backup, naleznete v následujících článcích pro další informace o vytváření a zobrazování sestav ve službě Power BI.

* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrování sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
