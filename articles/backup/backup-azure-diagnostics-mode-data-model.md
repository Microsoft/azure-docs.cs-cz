---
title: Datový model Azure Monitor protokolů
description: V tomto článku se dozvíte informace o Azure Monitor Log Analytics datových modelů pro Azure Backup data.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 2d2146612763bac39eb636bb7da522586a0daee2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586782"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics datový model pro Azure Backup data

Pomocí Log Analytics datového modelu můžete vytvářet vlastní výstrahy z Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Tento datový model se odkazuje na režim Azure Diagnostics odesílání diagnostických událostí do Log Analytics (LA). Informace o datovém modelu pro nový režim konkrétního zdroje najdete v následujícím článku: [datový model pro Azure Backup diagnostické události](./backup-azure-reports-data-model.md) .

## <a name="using-azure-backup-data-model"></a>Použití datového modelu Azure Backup

Následující pole, která jsou součástí datového modelu, můžete použít k vytvoření vizuálů, vlastních dotazů a řídicích panelů podle vašich požadavků.

### <a name="alert"></a>Výstrahy

Tato tabulka poskytuje podrobnosti o polích souvisejících s výstrahami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| AlertUniqueId_s |Text |Jedinečný identifikátor vygenerované výstrahy |
| AlertType_s |Text |Typ výstrahy, například zálohování |
| AlertStatus_s |Text |Stav výstrahy, například aktivní |
| AlertOccurrenceDateTime_s |Datum/Čas |Datum a čas vytvoření výstrahy |
| AlertSeverity_s |Text |Závažnost výstrahy, například kritická |
|AlertTimeToResolveInMinutes_s    | Číslo        |Čas potřebný k vyřešení výstrahy. Prázdné pro aktivní výstrahy.         |
|AlertConsolidationStatus_s   |Text         |Zjistit, jestli je výstraha konsolidovaná výstraha nebo ne         |
|CountOfAlertsConsolidated_s     |Číslo         |Počet sloučených výstrah, pokud se jedná o konsolidovanou výstrahu          |
|AlertRaisedOn_s     |Text         |Typ entity, na které se aktivuje výstraha         |
|AlertCode_s     |Text         |Kód pro jedinečnou identifikaci typu výstrahy         |
|RecommendedAction_s   |Text         |Akce doporučená pro vyřešení výstrahy         |
| EventName_s |Text |Název události Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky přidružené k výstraze |
| SchemaVersion_s |Text |Aktuální verze schématu, například **v2** |
| State_s |Text |Aktuální stav objektu výstrahy, například aktivní, odstraněn |
| BackupManagementType_s |Text |Typ poskytovatele pro provádění zálohování, například IaaSVM, složku, do které patří tato výstraha |
| OperationName |Text |Název aktuální operace, například výstraha |
| Kategorie |Text |Kategorie dat diagnostiky nabízených do protokolů Azure Monitor Vždy AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| ProtectedContainerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidruženého k výstraze (byl ProtectedServerUniqueId_s v V1)|
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněného trezoru přidruženého k výstraze |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Jedinečný identifikátor prostředku, pro který se shromažďují data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro který se shromažďují data Například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který se shromažďují data Například trezory |

### <a name="backupitem"></a>BackupItem

Tato tabulka poskytuje podrobnosti o polích souvisejících s položkou zálohování.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Název události Vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky |
| BackupItemId_s |Text |Identifikátor zálohované položky (Toto pole je jen pro schéma V1) |
| BackupItemName_s |Text |Název zálohované položky |
| BackupItemFriendlyName_s |Text |Popisný název zálohované položky |
| BackupItemType_s |Text |Typ zálohované položky, třeba VM, Folder |
| BackupItemProtectionState_s |Text |Stav ochrany zálohované položky |
| BackupItemAppVersion_s |Text |Verze aplikace pro zálohovanou položku |
| ProtectionState_s |Text |Aktuální stav ochrany zálohované položky, například Protected, ProtectionStopped |
| ProtectionGroupName_s |Text | Název skupiny ochrany, ve které je chráněná položka zálohy, pro SC DPM a MABS, pokud je k dispozici|
| SecondaryBackupProtectionState_s |Text |Zda je pro zálohovanou položku povolena sekundární ochrana|
| SchemaVersion_s |Text |Verze schématu, například **v2** |
| State_s |Text |Stav objektu zálohované položky, například Active, Deleted |
| BackupManagementType_s |Text |Typ poskytovatele pro provádění zálohování, například IaaSVM, složku, do které patří tato položka zálohy |
| OperationName |Text |Název operace, například BackupItem |
| Kategorie |Text |Kategorie dat diagnostiky nabízených do protokolů Azure Monitor Vždy AzureBackupReport |
| Prostředek |Text |Prostředek, pro který se data shromažďují, například Recovery Services název trezoru |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |ID prostředku pro shromažďovaná data, například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro např. Recovery Services trezor) pro shromažďovaná data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro např. Recovery Services trezor) pro shromažďovaná data |
| ResourceProvider |Text |Poskytovatel prostředků pro shromažďovaná data, například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku pro shromažďovaná data, například trezory |

### <a name="backupitemassociation"></a>BackupItemAssociation

Tato tabulka poskytuje podrobnosti o přidružení zálohových položek s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události. Vždycky se AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečné ID zálohované položky |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu. Je **v2** |
| State_s |Text |Aktuální stav objektu přidružení zálohované položky, například aktivní, odstraněn |
| BackupManagementType_s |Text |Typ poskytovatele pro server, který provádí úlohu zálohování, například IaaSVM, složka adresáře |
| BackupItemSourceSize_s |Text | Velikost front-endu pro zálohovanou položku |
| BackupManagementServerUniqueId_s |Text | Pole k jednoznačné identifikaci záložního serveru pro správu, ve kterém je chráněná položka, pokud je k dispozici |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabízených do Log Analytics. Je AzureBackupReport |
| OperationName |Text |Toto pole představuje název aktuální operace – BackupItemAssociation |
| Prostředek |Text |Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| ProtectedContainerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidruženého k zálohované položce (ProtectedServerUniqueId_s v V1) |
| VaultUniqueId_s |Text |Jedinečný identifikátor trezoru obsahujícího zálohovanou položku |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků pro shromažďovaná data, například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku pro shromažďování dat, například trezory |

### <a name="backupmanagementserver"></a>BackupManagementServer

Tato tabulka poskytuje podrobnosti o přidružení zálohových položek s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Název serveru pro správu zálohování        |
|AzureBackupAgentVersion_s     |Text         |Verze agenta Azure Backup na serveru pro správu zálohování          |
|BackupManagementServerVersion_s     |Text         |Verze serveru pro správu zálohování|
|BackupManagementServerOSVersion_s    |Text            |Verze operačního systému serveru pro správu zálohování|
|BackupManagementServerType_s     |Text         |Typ záložního serveru pro správu, jako je MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Pole pro jedinečnou identifikaci serveru pro správu zálohování       |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků pro shromažďovaná data, například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku pro shromažďování dat, například trezory |

### <a name="job"></a>Úloha

Tato tabulka poskytuje podrobnosti o polích souvisejících s úlohou.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Název události Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky |
| SchemaVersion_s |Text |Verze schématu, například **v2** |
| State_s |Text |Aktuální stav objektu úlohy, například aktivní, odstraněn |
| BackupManagementType_s |Text |Typ poskytovatele pro server, který provádí úlohu zálohování, například IaaSVM, složka adresáře |
| OperationName |Text |Toto pole představuje název aktuální operace – úloha |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabízených do protokolů Azure Monitor. Je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| ProtectedServerUniqueId_s |Text |Jedinečný identifikátor přidruženého serveru k úloze |
| ProtectedContainerUniqueId_s |Text | Jedinečné ID pro identifikaci chráněného kontejneru, na kterém je úloha spuštěná |
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněného trezoru |
| JobOperation_s |Text |Operace, pro kterou je úloha spuštěná, například zálohování, obnovení, konfigurace zálohování |
| JobStatus_s |Text |Stav dokončené úlohy, například dokončeno, neúspěšné |
| JobFailureCode_s |Text |Řetězec kódu chyby, protože došlo k selhání úlohy |
| JobStartDateTime_s |Datum/Čas |Datum a čas spuštění úlohy |
| BackupStorageDestination_s |Text |Cíl úložiště zálohování, třeba Cloud, disk  |
| AdHocOrScheduledJob_s |Text | Pole, které určuje, jestli má být úloha ad hoc nebo plánovaná |
| JobDurationInSecs_s | Číslo |Celková doba trvání úlohy v sekundách |
| DataTransferredInMB_s | Číslo |Data přenesená v MB pro tuto úlohu|
| JobUniqueId_g |Text |Jedinečné ID pro identifikaci úlohy |
| RecoveryJobDestination_s |Text | Cíl úlohy obnovení, kde se data obnovují |
| RecoveryJobRPDateTime_s |DateTime | Datum a čas vytvoření obnoveného bodu obnovení |
| RecoveryJobRPLocation_s |Text | Umístění, kam se uložil bod obnovení, který se má obnovit|
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services|
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro který se shromažďují data Například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který se shromažďují data Například trezory |

### <a name="policy"></a>Zásady

Tato tabulka poskytuje podrobnosti o polích souvisejících s zásadami.

| Pole | Typ dat | Použitelné verze | Description |
| --- | --- | --- | --- |
| EventName_s |Text ||Toto pole představuje název této události. Vždycky se AzureBackupCentralReport |
| SchemaVersion_s |Text ||Toto pole označuje aktuální verzi schématu. Je **v2** |
| State_s |Text ||Aktuální stav objektu zásad, například aktivní, odstraněn |
| BackupManagementType_s |Text ||Typ poskytovatele pro server, který provádí úlohu zálohování, například IaaSVM, složka adresáře |
| OperationName |Text ||Toto pole představuje název aktuální operace – zásady |
| Kategorie |Text ||Toto pole představuje kategorii dat diagnostiky nabízených do protokolů Azure Monitor. Je AzureBackupReport |
| Prostředek |Text ||Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| PolicyUniqueId_g |Text ||Jedinečné ID pro identifikaci zásad |
| PolicyName_s |Text ||Název definované zásady |
| BackupFrequency_s |Text ||Frekvence, se kterou se spouštějí zálohy, například denně, týdně |
| BackupTimes_s |Text ||Datum a čas plánování zálohování |
| BackupDaysOfTheWeek_s |Text ||Dny v týdnu, kdy bylo naplánováno zálohování |
| RetentionDuration_s |Celé číslo ||Doba uchování pro nakonfigurované zálohy |
| DailyRetentionDuration_s |Celé číslo ||Celková doba uchování ve dnech pro nakonfigurované zálohy |
| DailyRetentionTimes_s |Text ||Datum a čas, kdy se nakonfigurovali denní uchovávání |
| WeeklyRetentionDuration_s |Desetinné číslo ||Celková doba uchování v týdnu pro nakonfigurované zálohy v týdnech |
| WeeklyRetentionTimes_s |Text ||Datum a čas, kdy je nastaveno týdenní uchovávání |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dny v týdnu vybrané pro týdenní uchování |
| MonthlyRetentionDuration_s |Desetinné číslo ||Celková doba uchování v měsících pro nakonfigurované zálohy |
| MonthlyRetentionTimes_s |Text ||Datum a čas, kdy se nakonfiguruje měsíční uchování |
| MonthlyRetentionFormat_s |Text ||Typ konfigurace pro měsíční uchování, například denně pro každý den, týdně na základě týdne |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dny v týdnu vybrané pro měsíční uchování |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Týdny v měsíci, kdy je nastaveno měsíční uchovávání, například jako první, poslední |
| YearlyRetentionDuration_s |Desetinné číslo ||Celková doba uchování v letech pro nakonfigurovaná zálohování |
| YearlyRetentionTimes_s |Text ||Datum a čas, kdy se nakonfiguruje roční uchování |
| YearlyRetentionMonthsOfTheYear_s |Text ||Měsíců v roce vybraných pro roční uchování |
| YearlyRetentionFormat_s |Text ||Typ konfigurace pro roční uchování, například denně pro každý den, týdně na základě týdne | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Data v měsíci vybraná pro roční uchování |
| SynchronisationFrequencyPerDay_s |Celé číslo |v2|Počet pokusů za den, kdy je záloha souborů synchronizovaná pro SC DPM a MABS |
| DiffBackupFormat_s |Text |v2|Formát pro rozdílové zálohování SQL v zálohování virtuálních počítačů Azure |
| DiffBackupTime_s |Čas |v2|Čas rozdílového zálohování SQL v zálohování virtuálních počítačů Azure|
| DiffBackupRetentionDuration_s |Desetinné číslo |v2|Doba uchování rozdílového zálohování pro SQL v zálohování virtuálních počítačů Azure|
| LogBackupFrequency_s |Desetinné číslo |v2|Frekvence zálohování protokolů pro SQL|
| LogBackupRetentionDuration_s |Desetinné číslo |v2|Doba uchování pro zálohování protokolů pro SQL v zálohování virtuálních počítačů Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Dny v týdnu pro rozdílové zálohy pro SQL v zálohování virtuálních počítačů Azure|
| SourceSystem |Text ||Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text ||Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text ||Identifikátor předplatného prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text ||Skupina prostředků prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text ||Poskytovatel prostředků, pro který se shromažďují data Například Microsoft. RecoveryServices |
| ResourceType |Text ||Typ prostředku, pro který se shromažďují data Například trezory |

### <a name="policyassociation"></a>PolicyAssociation

Tato tabulka poskytuje podrobnosti o přidružení zásad s různými entitami.

| Pole | Typ dat | Použitelné verze | Description |
| --- | --- | --- | --- |
| EventName_s |Text ||Toto pole představuje název této události. Vždycky se AzureBackupCentralReport |
| SchemaVersion_s |Text ||Toto pole označuje aktuální verzi schématu. Je **v2** |
| State_s |Text ||Aktuální stav objektu zásad, například aktivní, odstraněn |
| BackupManagementType_s |Text ||Typ poskytovatele pro server, který provádí úlohu zálohování, například IaaSVM, složka adresáře |
| OperationName |Text ||Toto pole představuje název aktuální operace – PolicyAssociation |
| Kategorie |Text ||Toto pole představuje kategorii dat diagnostiky nabízených do protokolů Azure Monitor. Je AzureBackupReport |
| Prostředek |Text ||Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| PolicyUniqueId_g |Text ||Jedinečné ID pro identifikaci zásad |
| VaultUniqueId_s |Text ||Jedinečné ID trezoru, ke kterému patří tato zásada |
| BackupManagementServerUniqueId_s |Text |v2 |Pole k jednoznačné identifikaci záložního serveru pro správu, ve kterém je chráněná položka, pokud je k dispozici        |
| SourceSystem |Text ||Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text ||Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text ||Identifikátor předplatného prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text ||Skupina prostředků prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text ||Poskytovatel prostředků, pro který se shromažďují data Například Microsoft. RecoveryServices |
| ResourceType |Text ||Typ prostředku, pro který se shromažďují data Například trezory |

### <a name="protected-container"></a>Chráněný kontejner

Tato tabulka poskytuje základní pole o chráněných kontejnerech. (Byl ProtectedServer v V1)

| Pole | Typ dat | Popis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Pole pro jednoznačnou identifikaci chráněného kontejneru |
| ProtectedContainerOSType_s |Text |Typ operačního systému chráněného kontejneru |
| ProtectedContainerOSVersion_s |Text |Verze operačního systému chráněného kontejneru |
| AgentVersion_s |Text |Číslo verze zálohy agenta nebo agenta ochrany (v případě SC DPM a MABS) |
| BackupManagementType_s |Text |Typ poskytovatele pro provádění zálohování. Například IaaSVM, složka. |
| EntityState_s |Text |Aktuální stav objektu chráněného serveru. Například aktivní, odstraněno |
| ProtectedContainerFriendlyName_s |Text |Popisný název chráněného serveru |
| ProtectedContainerName_s |Text |Název chráněného kontejneru |
| ProtectedContainerWorkloadType_s |Text |Typ zálohovaného chráněného kontejneru. Například IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Bez ohledu na to, jestli je chráněný kontejner umístěný místně nebo v Azure |
| ProtectedContainerType_s |Text |Zda chráněný kontejner je server nebo kontejner |
| ProtectedContainerProtectionState_s  |Text |Stav ochrany chráněného kontejneru |

### <a name="storage"></a>Storage

Tato tabulka poskytuje podrobnosti o polích souvisejících s úložištěm.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| CloudStorageInBytes_s |Desetinné číslo |Cloudové úložiště záloh používané v zálohách vypočítané na základě nejnovější hodnoty (Toto pole je určené jenom pro schéma V1)|
| ProtectedInstances_s |Desetinné číslo |Počet chráněných instancí použitých pro výpočet front-endu úložiště ve fakturaci vypočítané na základě nejnovější hodnoty |
| EventName_s |Text |Toto pole představuje název této události. Vždycky se AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu. Je **v2** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněn |
| BackupManagementType_s |Text |Typ poskytovatele pro server, který provádí úlohu zálohování, například IaaSVM, složka adresáře |
| OperationName |Text |Toto pole představuje název aktuální operace – úložiště. |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabízených do protokolů Azure Monitor. Je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| ProtectedServerUniqueId_s |Text |Jedinečné ID chráněného serveru, pro který se počítá úložiště |
| VaultUniqueId_s |Text |Počítá se jedinečné ID trezoru pro úložiště. |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro který se shromažďují data Například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který se shromažďují data Například trezory |
| StorageUniqueId_s |Text |Jedinečné ID použité k identifikaci entity úložiště |
| StorageType_s |Text |Typ úložiště, například Cloud, svazek, disk |
| StorageName_s |Text |Název entity úložiště, například jednotku e:\ |
| StorageTotalSizeInGBs_s |Text |Celková velikost úložiště v GB, spotřebovaná entitou úložiště|

### <a name="storageassociation"></a>StorageAssociation

Tato tabulka poskytuje základní pole související s úložištěm, která propojuje úložiště s jinými entitami.

| Pole | Typ dat | Popis |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Jedinečné ID použité k identifikaci entity úložiště |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu. Je **v2** |
| BackupItemUniqueId_s |Text |Jedinečné ID, které slouží k identifikaci zálohované položky související s entitou úložiště |
| BackupManagementServerUniqueId_s |Text |Jedinečné ID, které slouží k identifikaci management server zálohy související s entitou úložiště|
| VaultUniqueId_s |Text |Jedinečné ID, které slouží k identifikaci trezoru souvisejícího s entitou úložiště|
| StorageConsumedInMBs_s |Číslo|Velikost úložiště spotřebovaného odpovídající zálohovanou položkou v příslušném úložišti |
| StorageAllocatedInMBs_s |Číslo |Velikost úložiště přidělená odpovídající zálohovanou položkou v odpovídajícím úložišti typu disk|

### <a name="vault"></a>Trezor

Tato tabulka poskytuje podrobnosti o polích souvisejících s trezorem.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události. Vždycky se AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu. Je **v2** |
| State_s |Text |Aktuální stav objektu trezoru, například aktivní, odstraněn |
| OperationName |Text |Toto pole představuje název aktuální operace – trezor |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabízených do protokolů Azure Monitor. Je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro který se data shromažďují, zobrazuje Recovery Services název trezoru. |
| VaultUniqueId_s |Text |Jedinečné ID trezoru |
| VaultName_s |Text |Název trezoru |
| AzureDataCenter_s |Text |Datové centrum, kde se nachází trezor |
| StorageReplicationType_s |Text |Typ replikace úložiště pro trezor, například, Neredundantní |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data Například ID prostředku úložiště Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např. Recovery Services trezor), pro který se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro který se shromažďují data Například Microsoft. RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který se shromažďují data Například trezory |

### <a name="backup-management-server"></a>Server pro správu zálohování

Tato tabulka poskytuje základní pole o serverech pro správu zálohování.

|Pole  |Typ dat  | Popis  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Název serveru pro správu zálohování        |
|AzureBackupAgentVersion_s     |Text         |Verze agenta Azure Backup na serveru pro správu zálohování          |
|BackupManagementServerVersion_s     |Text         |Verze serveru pro správu zálohování|
|BackupManagementServerOSVersion_s     |Text            |Verze operačního systému serveru pro správu zálohování|
|BackupManagementServerType_s     |Text         |Typ záložního serveru pro správu, jako je MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Pole pro jedinečnou identifikaci serveru pro správu zálohování       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Tato tabulka určuje úlohy, ke kterým je přiřazený svazek.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| StorageUniqueId_s |Text |Jedinečné ID použité k identifikaci entity úložiště |
| BackupItemType_s |Text |Úlohy, pro které je tento svazek upřednostňovaným úložištěm|

### <a name="protectedinstance"></a>ProtectedInstance

Tato tabulka poskytuje základní pole související s chráněnými instancemi.

| Pole | Typ dat |Použitelné verze | Description |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Jedinečné ID, které slouží k identifikaci zálohované položky pro virtuální počítače, které se zálohují pomocí DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Jedinečné ID, které slouží k identifikaci chráněného kontejneru pro vše kromě virtuálních počítačů zálohovaných pomocí DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Počet chráněných instancí pro přidruženou zálohovanou položku nebo chráněný kontejner v dané datum a čas|

### <a name="recoverypoint"></a>RecoveryPoint

Tato tabulka poskytuje základní pole související s bodem obnovení.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Jedinečné ID, které slouží k identifikaci zálohované položky pro virtuální počítače, které se zálohují pomocí DPM, MABS|
| OldestRecoveryPointTime_s |Text |Datum a čas nejstaršího bodu obnovení pro zálohovanou položku|
| OldestRecoveryPointLocation_s |Text |Umístění nejstaršího bodu obnovení pro zálohovanou položku|
| LatestRecoveryPointTime_s |Text |Datum a čas posledního bodu obnovení pro zálohovanou položku|
| LatestRecoveryPointLocation_s |Text |Umístění posledního bodu obnovení pro zálohovanou položku|

## <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Níže najdete několik ukázek, které vám pomohou při psaní dotazů na Azure Backup dat, která se nacházejí v tabulce Azure Diagnostics:

- Všechny úspěšné úlohy zálohování

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Všechny neúspěšné úlohy zálohování

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Všechny úspěšné úlohy zálohování virtuálních počítačů Azure

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Všechny úspěšné úlohy zálohování protokolu SQL

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Všechny úspěšné Azure Backup úlohy agenta

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

## <a name="v1-schema-vs-v2-schema"></a>Schéma v1 schématu vs v2

Dříve byla diagnostická data pro Azure Backup agenta a zálohování virtuálních počítačů Azure odeslána do tabulky Azure Diagnostics ve schématu, které se říká jako ***v1 schéma** _. Následně byly přidány nové sloupce pro podporu jiných scénářů a úloh a data diagnostiky byla vložena do nového schématu, které je označováno jako "schéma _ *_v2_* *".  

Z důvodu zpětné kompatibility se v současné době odesílají diagnostická data pro Azure Backup agenta a zálohování virtuálních počítačů Azure do Azure Diagnostics tabulky ve schématu V1 a v2 (se schématem v1 nyní na cestě k vyřazení). Pomocí filtrování záznamů pro SchemaVersion_s = = v1 v dotazech protokolu můžete určit, které záznamy v Log Analytics mají schéma v1.

Pokud chcete zjistit, které sloupce patří pouze ke schématu V1, podívejte se do třetího sloupce "Description" v [datovém modelu](#using-azure-backup-data-model) popsaném výše.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>Úprava dotazů na použití schématu v2

Vzhledem k tomu, že je schéma V1 na cestě pro vyřazení, doporučuje se použít ve všech vašich vlastních dotazech na Azure Backup diagnostických dat pouze schéma v2. Níže je uveden příklad, jak aktualizovat dotazy pro odebrání závislostí na schématu V1:

1. Určete, jestli dotaz používá jakékoli pole, které platí jenom pro schéma v1. Předpokládejme, že máte dotaz na výpis všech zálohovaných položek a jejich přidružených chráněných serverů následujícím způsobem:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    Výše uvedený dotaz používá pole ProtectedServerUniqueId_s, které se vztahuje pouze na schéma v1. Ekvivalent schématu v2 tohoto pole je ProtectedContainerUniqueId_s (odkazuje na tabulky výše). Pole BackupItemUniqueId_s platí i pro schéma v2 a v tomto dotazu lze použít stejné pole.

2. Aktualizujte dotaz tak, aby používal názvy polí schématu v2. Doporučuje se použít filtr, **kde SchemaVersion_s = = "v2"** ve všech dotazech, takže dotaz bude analyzovat pouze záznamy odpovídající schématu v2:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Další kroky

Po kontrole datového modelu můžete začít [vytvářet vlastní dotazy](../azure-monitor/visualize/tutorial-logs-dashboards.md) v protokolech Azure monitor a vytvořit si tak vlastní řídicí panel.
