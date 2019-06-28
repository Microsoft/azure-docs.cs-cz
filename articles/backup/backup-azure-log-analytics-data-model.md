---
title: Azure Monitor protokoly datový model pro službu Azure Backup
description: Tento článek týká Azure Monitor protokoluje podrobné informace o modelu dat pro zálohování Azure – uložená data.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 801516ae2cfad891098c16f8cd6e9a4c7f157a93
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342016"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Datový model log Analytics pro Azure zálohování – uložená data

Použijte datový model Log Analytics vytvořit vlastní upozornění z Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Pomocí Azure Backup datového modelu

Následující pole k dispozici jako součást modelu dat můžete použít k vytváření vizuálů, vlastní dotazy a řídicí panel podle vašich požadavků.

### <a name="alert"></a>Výstrahy

Tato tabulka obsahuje podrobnosti o upozornění související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| AlertUniqueId_s |Text |Jedinečný identifikátor vygenerovaných výstrah |
| AlertType_s |Text |Typ upozornění, například zálohování |
| AlertStatus_s |Text |Stav výstrahy, například aktivní |
| AlertOccurrenceDateTime_s |Datum a čas |Datum a čas, kdy byla výstraha vytvořena |
| AlertSeverity_s |Text |Závažnost výstrahy, například kritický |
|AlertTimeToResolveInMinutes_s    | Číslo        |Čas potřebný k vyřešení upozornění. Prázdné, pokud aktivní výstrahy.         |
|AlertConsolidationStatus_s   |Text         |Určit, jestli je výstraha konsolidovaná výstraha nebo ne         |
|CountOfAlertsConsolidated_s     |Číslo         |Počet výstrah konsolidovat, pokud je konsolidovaná výstraha          |
|AlertRaisedOn_s     |Text         |Typ entity, upozornění je vyvoláno na         |
|AlertCode_s     |Text         |Kód k jednoznačné identifikaci typ výstrahy         |
|RecommendedAction_s   |Text         |Tuto výstrahu vyřešíte Doporučená akce         |
| EventName_s |Text |Název události. Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky přiřazené k výstraze |
| SchemaVersion_s |Text |Aktuální verze schématu, například **V2** |
| State_s |Text |Aktuální stav výstrahy objektu, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, ke kterému patří tato výstraha |
| OperationName |Text |Název aktuální operace, například výstrahy |
| Category |Text |Kategorie diagnostická data do protokoly Azure monitoru. Vždy AzureBackupReport |
| Resource |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedContainerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru souvisejícího s výstrahou (ProtectedServerUniqueId_s byl ve V1)|
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněné trezor souvisejícího s výstrahou |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Jedinečný identifikátor pro prostředek, o tom, které se shromažďují data. Například id prostředku služby Recovery Services vault |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="backupitem"></a>BackupItem

Tato tabulka obsahuje podrobnosti o zálohování související položku pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Název události. Vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky |
| BackupItemId_s |Text |Identifikátor položky zálohování (v tomto poli je pouze pro schéma v1) |
| BackupItemName_s |Text |Název zálohované položky |
| BackupItemFriendlyName_s |Text |Popisný název zálohované položky |
| BackupItemType_s |Text |Typ zálohování položky, například virtuální počítač, FileFolder |
| BackupItemProtectionState_s |Text |Stav ochrany zálohované položky |
| BackupItemAppVersion_s |Text |Verze aplikace pro zálohovaná položka |
| ProtectionState_s |Text |Aktuální stav ochrany zálohované položky, třeba chráněné, ProtectionStopped |
| ProtectionGroupName_s |Text | Název skupiny ochrany zálohovaná položka není chráněná ve, SC DPM a službu MABS, pokud je k dispozici|
| SecondaryBackupProtectionState_s |Text |Určuje, zda je povoleno sekundární ochranu zálohované položky|
| SchemaVersion_s |Text |Verze schématu, například **V2** |
| State_s |Text |Stav objektu zálohované položky, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, ke kterému patří této zálohované položky |
| OperationName |Text |Název operace, například BackupItem |
| Category |Text |Kategorie diagnostická data do protokoly Azure monitoru. Vždy AzureBackupReport |
| Resource |Text |Prostředek pro jaká data se shromažďují, například název trezoru služby Recovery Services |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Id prostředku pro shromažďování dat, například trezor služby Recovery Services id prostředku |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro příklad. Trezor služby Recovery Services) pro shromažďování dat |
| ResourceGroup |Text |Skupina prostředků prostředku (pro příklad. Trezor služby Recovery Services) pro shromažďování dat |
| ResourceProvider |Text |Poskytovatel prostředků pro data shromažďují, například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku pro data shromažďují, například trezory. |

### <a name="backupitemassociation"></a>BackupItemAssociation

Tato tabulka obsahuje podrobné informace o přidružení zálohovaná položka s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečné Id zálohované položky |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V2** |
| State_s |Text |Aktuální stav objektu přidružení zálohovaná položka, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Front-end velikost zálohované položky |
| BackupManagementServerUniqueId_s |Text | Pole k jednoznačné identifikaci serveru pro správu zálohování zálohovaná položka je chráněný, pokud je k dispozici |
| Category |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| OperationName |Text |Toto pole představuje název aktuální operace - BackupItemAssociation |
| Resource |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedContainerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidružené k zálohované položky (ProtectedServerUniqueId_s byl ve V1) |
| VaultUniqueId_s |Text |Jedinečný identifikátor trezoru, který obsahuje zálohované položky |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro příklad. Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro příklad. Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků pro data shromažďují, například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku pro data se shromažďují, například trezorů |

### <a name="backupmanagementserver"></a>BackupManagementServer

Tato tabulka obsahuje podrobné informace o přidružení zálohovaná položka s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Název serveru pro správu zálohování        |
|AzureBackupAgentVersion_s     |Text         |Verze agenta Azure Backup na serveru pro správu zálohování          |
|BackupManagementServerVersion_s     |Text         |Verze serveru pro správu zálohování|
|BackupManagementServerOSVersion_s    |Text            |Verze operačního systému serveru pro správu zálohování|
|BackupManagementServerType_s     |Text         |Typ správy zálohování serveru DPM MABS, SC|
|BackupManagementServerUniqueId_s     |Text         |Pole k jednoznačné identifikaci serveru pro správu zálohování       |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro příklad. Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro příklad. Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků pro data shromažďují, například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku pro data se shromažďují, například trezorů |

### <a name="job"></a>Úloha

Tato tabulka obsahuje podrobné informace o souvisejících s úlohami pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Název události. Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky |
| SchemaVersion_s |Text |Verze schématu, například **V2** |
| State_s |Text |Aktuální stav objektu úlohy, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace – úloha |
| Category |Text |Toto pole představuje kategorie diagnostická data do Azure monitoru protokoly, je AzureBackupReport |
| Resource |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečný identifikátor na chráněném serveru související úlohy |
| ProtectedContainerUniqueId_s |Text | Jedinečné Id pro identifikaci kontejneru chráněné úlohy běží na |
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněné trezoru |
| JobOperation_s |Text |Operace, pro který se úloha spustí například zálohování, obnovení, nakonfigurujte zálohování |
| JobStatus_s |Text |Stav dokončené úlohy, například Completed, Failed |
| JobFailureCode_s |Text |Řetězec kódu chyby kvůli které došlo k selhání úlohy |
| JobStartDateTime_s |Datum a čas |Datum a čas, kdy úlohy Začínáme spuštění |
| BackupStorageDestination_s |Text |Cílové úložiště záloh, například Cloud, disku  |
| AdHocOrScheduledJob_s |Text | Pole k určení, zda je úloha Ad Hoc nebo naplánovaná |
| JobDurationInSecs_s | Číslo |Celkový počet úloh doba trvání v sekundách |
| DataTransferredInMB_s | Číslo |Data přenesená v MB pro tuto úlohu|
| JobUniqueId_g |Text |Jedinečné Id pro identifikaci úlohy |
| RecoveryJobDestination_s |Text | Cíle úlohy obnovení, kde data budou obnovena |
| RecoveryJobRPDateTime_s |DateTime | Datum, čas vytvoření bodu obnovení, který se obnovuje |
| RecoveryJobRPLocation_s |Text | Umístění uloženého bodu obnovení, který se obnovuje|
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services|
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="policy"></a>Zásada

Tato tabulka obsahuje podrobnosti o týkající se zásad pole.

| Pole | Typ dat | Příslušné verze | Popis |
| --- | --- | --- | --- |
| EventName_s |Text ||Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text ||Toto pole označuje aktuální verzi schématu, ale **V2** |
| State_s |Text ||Aktuální stav objektu zásad, například aktivní, odstraněno |
| BackupManagementType_s |Text ||Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text ||Toto pole představuje název aktuální akce – zásady |
| Category |Text ||Toto pole představuje kategorie diagnostická data do Azure monitoru protokoly, je AzureBackupReport |
| Resource |Text ||Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| PolicyUniqueId_g |Text ||Jedinečný identifikátor zásad |
| PolicyName_s |Text ||Název definice zásady |
| BackupFrequency_s |Text ||Frekvence, se kterým se spouštět zálohování, třeba každý den, každý týden |
| BackupTimes_s |Text ||Datum a čas, kdy jsou naplánované zálohy |
| BackupDaysOfTheWeek_s |Text ||Dny v týdnu, kdy bylo naplánováno zálohy |
| RetentionDuration_s |Celé číslo ||Doba uchování pro nastavená zálohování |
| DailyRetentionDuration_s |Celé číslo ||Doba trvání celkový uchování ve dnech pro nastavená zálohování |
| DailyRetentionTimes_s |Text ||Datum a čas, kdy byl nakonfigurován denní uchovávání |
| WeeklyRetentionDuration_s |Desetinné číslo ||Celková doba uchování týdenní v týdnech nakonfigurované zálohování |
| WeeklyRetentionTimes_s |Text ||Datum a čas, kdy nakonfigurovat týdenní uchovávání |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Vybrané dny v týdnu pro týdenní uchovávání |
| MonthlyRetentionDuration_s |Desetinné číslo ||Doba uchování celkový počet měsíců pro nastavená zálohování |
| MonthlyRetentionTimes_s |Text ||Datum a čas, kdy je nakonfigurován měsíční uchovávání |
| MonthlyRetentionFormat_s |Text ||Typ konfigurace pro měsíční uchovávání informací, například každý den za den základě týdně podle týdnů |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Vybrané dny v týdnu pro měsíční uchovávání |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Týdny v měsíci, když je nakonfigurovaná měsíční uchovávání informací, například první, poslední atd. |
| YearlyRetentionDuration_s |Desetinné číslo ||Doba uchování celkový počet v letech nastavená zálohování |
| YearlyRetentionTimes_s |Text ||Datum a čas, kdy je nakonfigurován roční uchovávání |
| YearlyRetentionMonthsOfTheYear_s |Text ||Měsíce roku vybraných pro roční uchovávání |
| YearlyRetentionFormat_s |Text ||Typ konfigurace pro roční uchovávání informací, například každý den za den základě týdně podle týdnů | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Data v měsíci vybraných pro roční uchovávání |
| SynchronisationFrequencyPerDay_s |Celé číslo |v2|Počet, kolikrát za den, synchronizaci souboru zálohy SC DPM a MABS |
| DiffBackupFormat_s |Text |v2|Formát pro rozdílové zálohování pro SQL v zálohování virtuálních počítačů Azure |
| DiffBackupTime_s |Čas |v2|Čas pro rozdílové zálohování pro SQL v Azure Backup pro virtuální počítače|
| DiffBackupRetentionDuration_s |Desetinné číslo |v2|Doba uchování pro rozdílové zálohování pro SQL v Azure Backup pro virtuální počítače|
| LogBackupFrequency_s |Desetinné číslo |v2|Frekvence zálohování protokolu SQL|
| LogBackupRetentionDuration_s |Desetinné číslo |v2|Doba uchování pro zálohy protokolu SQL v Azure Backup pro virtuální počítače|
| DiffBackupDaysofTheWeek_s |Text |v2|Dny v týdnu pro rozdílové zálohování pro SQL v Azure Backup pro virtuální počítače|
| SourceSystem |Text ||Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text ||Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text ||Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text ||Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text ||Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text ||Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="policyassociation"></a>PolicyAssociation

Tato tabulka obsahuje podrobné informace o přidružení zásady s různými entitami.

| Pole | Typ dat | Příslušné verze | Popis |
| --- | --- | --- | --- |
| EventName_s |Text ||Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text ||Toto pole označuje aktuální verzi schématu, ale **V2** |
| State_s |Text ||Aktuální stav objektu zásad, například aktivní, odstraněno |
| BackupManagementType_s |Text ||Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text ||Toto pole představuje název aktuální operace - PolicyAssociation |
| Category |Text ||Toto pole představuje kategorie diagnostická data do Azure monitoru protokoly, je AzureBackupReport |
| Resource |Text ||Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| PolicyUniqueId_g |Text ||Jedinečný identifikátor zásad |
| VaultUniqueId_s |Text ||Jedinečné Id trezoru, ke kterému patří tato zásada |
| BackupManagementServerUniqueId_s |Text |v2 |Pole k jednoznačné identifikaci serveru pro správu zálohování zálohovaná položka je chráněný, pokud je k dispozici        |
| SourceSystem |Text ||Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text ||Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text ||Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text ||Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text ||Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text ||Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="protected-container"></a>Chráněné kontejneru

Tato tabulka obsahuje základní pole o kontejnerech chráněné. (Byl ProtectedServer ve v1)

| Pole | Typ dat | Popis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Pole k jednoznačné identifikaci chráněné kontejneru |
| ProtectedContainerOSType_s |Text |Typ operačního systému chráněné kontejneru |
| ProtectedContainerOSVersion_s |Text |Verze operačního systému chráněné kontejneru |
| AgentVersion_s |Text |Číslo verze agenta zálohování nebo agenta ochrany (v případě aplikace DPM SC a MABS) |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování. Například IaaSVM, FileFolder |
| EntityState_s |Text |Aktuální stav objektu chráněného serveru. Například aktivní, odstraněno |
| ProtectedContainerFriendlyName_s |Text |Popisný název chráněného serveru |
| ProtectedContainerName_s |Text |Název chráněného kontejneru |
| ProtectedContainerWorkloadType_s |Text |Typ kontejneru chráněná zálohovat. Například IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Kontejner chráněné nacházejí na místních nebo v Azure |
| ProtectedContainerType_s |Text |Určuje, zda je kontejner chráněný server nebo kontejneru |
| ProtectedContainerProtectionState_s’  |Text |Stav ochrany z chráněného kontejneru |

### <a name="storage"></a>Úložiště

Tato tabulka obsahuje podrobnosti o polích týkající se úložiště.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| CloudStorageInBytes_s |Desetinné číslo |Cloudové zálohování úložiště využitá službou zálohování, které se počítá na základě nejnovější hodnoty (v tomto poli je pouze pro schéma v1)|
| ProtectedInstances_s |Desetinné číslo |Počet chráněných instancí sloužící k výpočtu front-endu úložiště fakturace, vypočítané hodnoty na základě nejnovější |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V2** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - úložiště |
| Category |Text |Toto pole představuje kategorie diagnostická data do Azure monitoru protokoly, je AzureBackupReport |
| Resource |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru, pro které se počítá úložiště |
| VaultUniqueId_s |Text |Jedinečné Id trezoru pro úložiště se počítá. |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |
| StorageUniqueId_s |Text |Jedinečné Id, které slouží k identifikaci entity úložiště |
| StorageType_s |Text |Typ úložiště, například Cloud, svazku disku |
| StorageName_s |Text |Název entity úložiště, např. E:\ |
| StorageTotalSizeInGBs_s |Text |Celková velikost úložiště v GB, používané entity úložiště|

### <a name="storageassociation"></a>StorageAssociation

Tato tabulka obsahuje základní pole úložiště připojení úložiště k jiné entity.

| Pole | Typ dat | Popis |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Jedinečné Id, které slouží k identifikaci entity úložiště |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V2** |
| BackupItemUniqueId_s |Text |Jedinečné Id, které slouží k identifikaci zálohované položky související s entitou úložiště |
| BackupManagementServerUniqueId_s |Text |Jedinečné Id, které slouží k identifikaci serveru pro správu záloh související s entitou úložiště|
| VaultUniqueId_s |Text |Jedinečné Id pro identifikaci trezoru související s entitou úložiště|
| StorageConsumedInMBs_s |Číslo|Velikost úložiště spotřebovávané odpovídající zálohovaná položka v odpovídající úložiště |
| StorageAllocatedInMBs_s |Číslo |Velikost úložiště přidělené odpovídající zálohované položky v úložišti odpovídající typ disku|

### <a name="vault"></a>Trezor

Tato tabulka obsahuje podrobnosti o trezoru související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V2** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněno |
| OperationName |Text |Toto pole představuje název aktuální operace - Vault |
| Category |Text |Toto pole představuje kategorie diagnostická data do Azure monitoru protokoly, je AzureBackupReport |
| Resource |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| VaultUniqueId_s |Text |Jedinečné Id trezoru |
| VaultName_s |Text |Název úložiště |
| AzureDataCenter_s |Text |Datové centrum, ve kterém se úložiště nachází |
| StorageReplicationType_s |Text |Typ replikace úložiště pro trezor, například GeoRedundant |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="backup-management-server"></a>Záložní Server pro správu

Tato tabulka obsahuje základní pole o servery pro správu zálohování.

|Pole  |Typ dat  | Popis  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Název serveru pro správu zálohování        |
|AzureBackupAgentVersion_s     |Text         |Verze agenta Azure Backup na serveru pro správu zálohování          |
|BackupManagmentServerVersion_s     |Text         |Verze serveru pro správu zálohování|
|BackupManagmentServerOSVersion_s     |Text            |Verze operačního systému serveru pro správu zálohování|
|BackupManagementServerType_s     |Text         |Typ správy zálohování serveru DPM MABS, SC|
|BackupManagmentServerUniqueId_s     |Text         |Pole k jednoznačné identifikaci serveru pro správu zálohování       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Tato tabulka určuje workload(s) svazku je přidružený.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| StorageUniqueId_s |Text |Jedinečné Id, které slouží k identifikaci entity úložiště |
| BackupItemType_s |Text |Úlohy, pro které tento svazek je požadované úložiště|

### <a name="protectedinstance"></a>ProtectedInstance

Tato tabulka obsahuje základní chráněná pole související s instancí.

| Pole | Typ dat |Příslušné verze | Popis |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Jedinečné Id, které slouží k identifikaci zálohované položky virtuálních počítačů zálohovat pomocí DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Jedinečné Id, které slouží k identifikaci chráněné kontejneru pro všechno, co s výjimkou virtuálních počítačů zálohovat pomocí DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Počet z chráněné instance pro přidružené zálohovaná položka nebo chráněné kontejneru na datum a čas|

### <a name="recoverypoint"></a>RecoveryPoint

Tato tabulka obsahuje základní obnovení bodu související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Jedinečné Id, které slouží k identifikaci zálohované položky virtuálních počítačů zálohovat pomocí DPM, MABS|
| OldestRecoveryPointTime_s |Text |Datum a čas nejstarší bod obnovení pro zálohovaná položka|
| OldestRecoveryPointLocation_s |Text |Umístění nejstarší bod obnovení pro zálohovaná položka|
| LatestRecoveryPointTime_s |Text |Datum a čas posledního bodu obnovení pro zálohovaná položka|
| LatestRecoveryPointLocation_s |Text |Umístění nejnovějším dostupným bodem obnovení zálohované položky|

## <a name="next-steps"></a>Další postup

Jakmile zkontrolujete datového modelu, můžete začít [vytváření vlastních dotazů](../azure-monitor/learn/tutorial-logs-dashboards.md) v protokoly Azure monitoru a vytvářet vlastní řídicí panel.
