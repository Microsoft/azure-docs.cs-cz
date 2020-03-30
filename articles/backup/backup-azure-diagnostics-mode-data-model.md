---
title: Azure Monitor protokoluje datový model
description: V tomto článku se dozvíte o podrobnostech datového modelu Azure Monitor Log Analytics pro data Azure Backup.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586372"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Datový model Log Analytics pro data zálohování Azure

Pomocí datového modelu Log Analytics můžete vytvářet vlastní výstrahy z Analýzy protokolů.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Tento datový model odkazuje na režim diagnostiky Azure odesílání diagnostických událostí do Analýzy protokolů (LA). Chcete-li se dozvědět datový model pro nový režim specifický pro prostředky, můžete se obrátit na následující článek: [Datového modelu pro diagnostické události zálohování Azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Použití datového modelu Azure Backup

Následující pole, která jsou součástí datového modelu, můžete použít k vytvoření vizuálů, vlastních dotazů a řídicího panelu podle vašich požadavků.

### <a name="alert"></a>Výstrahy

Tato tabulka obsahuje podrobnosti o polích souvisejících s výstrahami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| AlertUniqueId_s |Text |Jedinečný identifikátor generované výstrahy |
| AlertType_s |Text |Typ výstrahy, například Zálohování |
| AlertStatus_s |Text |Stav výstrahy, například Aktivní |
| AlertOccurrenceDateTime_s |Datum/Čas |Datum a čas, kdy byla výstraha vytvořena |
| AlertSeverity_s |Text |Závažnost výstrahy, například Kritická |
|AlertTimeToResolveInMinutes_s    | Číslo        |Čas na vyřešení výstrahy. Prázdné pro aktivní výstrahy.         |
|AlertConsolidationStatus_s   |Text         |Zjištění, zda se jedná o konsolidovanou výstrahu či nikoli         |
|CountOfAlertsConsolidated_s     |Číslo         |Počet konsolidovaných záznamů, pokud se jedná o konsolidovaný záznam          |
|AlertRaisedOn_s     |Text         |Typ entity, na kterou je výstraha vyvolána         |
|AlertCode_s     |Text         |Kód pro jednoznačnou identifikaci typu výstrahy         |
|RecommendedAction_s   |Text         |Akce doporučená k vyřešení výstrahy         |
| EventName_s |Text |Název události. Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohovací položky přidružené k výstraze |
| SchemaVersion_s |Text |Aktuální verze schématu, například **V2** |
| State_s |Text |Aktuální stav objektu výstrahy, například Aktivní, Odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro zálohování, například IaaSVM, Folder FileFolder, do kterého tato výstraha patří |
| OperationName |Text |Název aktuální operace, například Výstraha |
| Kategorie |Text |Kategorie diagnostických dat zasunutých do protokolů Azure Monitoru. Vždy AzureBackupReport |
| Prostředek |Text |Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| ProtectedContainerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidruženého k výstraze (byl ProtectedServerUniqueId_s ve V1)|
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněného trezoru přidruženého k výstraze |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Jedinečný identifikátor prostředku, o kterém zdroji jsou shromažďována. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text |Skupina prostředků prostředku (ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text |Zprostředkovatel prostředků, pro kterého jsou shromažďována data. Například Microsoft.RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který jsou shromažďována data. Například trezory |

### <a name="backupitem"></a>Položka zálohování

Tato tabulka obsahuje podrobnosti o polích souvisejících se zálohami položek.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Název události. Vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečný identifikátor položky zálohy |
| BackupItemId_s |Text |Identifikátor položky zálohy (Toto pole je pouze pro schéma v1) |
| BackupItemName_s |Text |Název položky zálohy |
| BackupItemFriendlyName_s |Text |Popisný název položky zálohy |
| BackupItemType_s |Text |Typ položky zálohování, například Virtuální počítače, Složka souborů |
| BackupItemProtectionState_s |Text |Stav ochrany položky zálohování |
| BackupItemAppVersion_s |Text |Verze zálohovací položky aplikace |
| ProtectionState_s |Text |Aktuální stav ochrany zálohovací položky, například Protected, ProtectionStopped |
| ProtectionGroupName_s |Text | Název skupiny ochrany, ve které je zálohovací položka chráněna, pro sc dpm a mabs, pokud je to možné|
| SecondaryBackupProtectionState_s |Text |Zda je pro položku zálohování povolena sekundární ochrana|
| SchemaVersion_s |Text |Verze schématu, například **V2** |
| State_s |Text |Stav objektu zálohovací položky, například Aktivní, Odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro zálohování, například IaaSVM, Folder FileFolder, do kterého tato položka zálohování patří |
| OperationName |Text |Název operace, například BackupItem |
| Kategorie |Text |Kategorie diagnostických dat zasunutých do protokolů Azure Monitoru. Vždy AzureBackupReport |
| Prostředek |Text |Prostředek, pro který jsou shromažďována data, například název trezoru služby Recovery Services |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |ID prostředku pro shromažďovaná data, například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro ex. úložiště služby Recovery Services) pro shromažďovaná data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro ex. úložiště služby Recovery Services) pro shromažďovaná data |
| ResourceProvider |Text |Zprostředkovatel prostředků pro shromažďovaná data, například Microsoft.RecoveryServices |
| ResourceType |Text |Typ zdroje pro shromažďovaná data, například Trezory |

### <a name="backupitemassociation"></a>BackupItemAssociation

Tato tabulka obsahuje podrobnosti o přidružení položek zálohování s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečné ID položky zálohy |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **to V2** |
| State_s |Text |Aktuální stav objektu přidružení položky zálohy, například Aktivní, Odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Velikost zálohovací položky front-endu |
| BackupManagementServerUniqueId_s |Text | Pole pro jednoznačnou identifikaci serveru pro správu zálohování, kterým je položka zálohování chráněna, pokud je to možné |
| Kategorie |Text |Toto pole představuje kategorii diagnostických dat zasunutých do log analytics, je to AzureBackupReport |
| OperationName |Text |Toto pole představuje název aktuální operace - BackupItemAssociation |
| Prostředek |Text |Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| ProtectedContainerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidruženého k položce zálohování (byl ProtectedServerUniqueId_s ve V1) |
| VaultUniqueId_s |Text |Jedinečný identifikátor trezoru obsahujícího položku zálohy |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text |Zprostředkovatel prostředků pro shromažďovaná data, například Microsoft.RecoveryServices |
| ResourceType |Text |Shromažďuje se typ prostředku pro data, například trezory. |

### <a name="backupmanagementserver"></a>BackupManagementServer

Tato tabulka obsahuje podrobnosti o přidružení položek zálohování s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Název serveru pro správu zálohování        |
|AzureBackupAgentVersion_s     |Text         |Verze agenta zálohování Azure na serveru pro správu zálohování          |
|BackupManagementServerVersion_s     |Text         |Verze serveru pro správu zálohování|
|BackupManagementServerOSVersion_s    |Text            |Verze systému zálohování os|
|BackupManagementServerType_s     |Text         |Typ serveru pro správu zálohování, jako mabs, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Pole pro jednoznačnou identifikaci serveru pro správu zálohování       |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (pro ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text |Skupina prostředků prostředku (pro ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text |Zprostředkovatel prostředků pro shromažďovaná data, například Microsoft.RecoveryServices |
| ResourceType |Text |Shromažďuje se typ prostředku pro data, například trezory. |

### <a name="job"></a>Úloha

Tato tabulka obsahuje podrobnosti o polích souvisejících s úlohou.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Název události. Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor položky zálohy |
| SchemaVersion_s |Text |Verze schématu, například **V2** |
| State_s |Text |Aktuální stav objektu úlohy, například Aktivní, Odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - Úloha |
| Kategorie |Text |Toto pole představuje kategorii diagnostických dat zasunutých do protokolů Azure Monitoru, je to AzureBackupReport |
| Prostředek |Text |Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| ProtectedServerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidruženého k úloze |
| ProtectedContainerUniqueId_s |Text | Jedinečné ID k identifikaci chráněného kontejneru, na který je úloha spuštěna |
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněného trezoru |
| JobOperation_s |Text |Operace, pro kterou je úloha spuštěna, například Zálohování, Obnovení, Konfigurace zálohování |
| JobStatus_s |Text |Stav dokončené úlohy, například Dokončeno, Nezdařilo se |
| JobFailureCode_s |Text |Řetězec kódu selhání, kvůli kterému došlo k selhání úlohy |
| JobStartDateTime_s |Datum/Čas |Datum a čas spuštění úlohy |
| BackupStorageDestination_s |Text |Cíl úložiště záloh, například Cloud, Disk  |
| AdHocOrScheduledJob_s |Text | Pole, které určuje, zda je úloha Ad Hoc nebo Scheduled |
| JobDurationInSecs_s | Číslo |Celková doba trvání úlohy v sekundách |
| DataTransferredInMB_s | Číslo |Data přenesená v MB pro tuto úlohu|
| JobUniqueId_g |Text |Jedinečné ID pro identifikaci úlohy |
| RecoveryJobDestination_s |Text | Cíl úlohy obnovení, kde jsou data obnovena |
| RecoveryJobRPDateTime_s |DateTime | Datum, čas, kdy byl vytvořen bod obnovení, který je obnovován |
| RecoveryJobRPLocation_s |Text | Místo, kde byl uložen bod obnovení, který je obnovován|
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services|
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text |Skupina prostředků prostředku (ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text |Zprostředkovatel prostředků, pro kterého jsou shromažďována data. Například Microsoft.RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který jsou shromažďována data. Například trezory |

### <a name="policy"></a>Zásada

Tato tabulka obsahuje podrobnosti o polích souvisejících se zásadami.

| Pole | Typ dat | Použitelné verze | Popis |
| --- | --- | --- | --- |
| EventName_s |Text ||Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text ||Toto pole označuje aktuální verzi schématu, je **to V2** |
| State_s |Text ||Aktuální stav objektu zásad, například Aktivní, Odstraněno |
| BackupManagementType_s |Text ||Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| OperationName |Text ||Toto pole představuje název aktuální operace - Zásady |
| Kategorie |Text ||Toto pole představuje kategorii diagnostických dat zasunutých do protokolů Azure Monitoru, je to AzureBackupReport |
| Prostředek |Text ||Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| PolicyUniqueId_g |Text ||Jedinečné ID pro identifikaci zásady |
| PolicyName_s |Text ||Název definované zásady |
| BackupFrequency_s |Text ||Frekvence, s jakou jsou zálohy spouštěny, například denně, týdně |
| BackupTimes_s |Text ||Datum a čas, kdy jsou naplánovány zálohy |
| BackupDaysOfTheWeek_s |Text ||Dny v týdnu, kdy byly naplánovány zálohy |
| RetentionDuration_s |Celé číslo ||Doba uchovávání pro nakonfigurované zálohy |
| DailyRetentionDuration_s |Celé číslo ||Celková doba uchovávání ve dnech pro nakonfigurované zálohy |
| DailyRetentionTimes_s |Text ||Datum a čas, kdy bylo nakonfigurováno denní uchovávání informací |
| WeeklyRetentionDuration_s |Desetinné číslo ||Celková týdenní doba uchovávání v týdnech pro nakonfigurované zálohy |
| WeeklyRetentionTimes_s |Text ||Datum a čas, kdy je nakonfigurováno týdenní uchovávání informací |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dny v týdnu vybrané pro týdenní uchovávání |
| MonthlyRetentionDuration_s |Desetinné číslo ||Celková doba uchovávání v měsících pro nakonfigurované zálohy |
| MonthlyRetentionTimes_s |Text ||Datum a čas, kdy je nakonfigurováno měsíční uchovávání informací |
| MonthlyRetentionFormat_s |Text ||Typ konfigurace pro měsíční uchovávání, například denně pro den, týdně pro týden |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dny v týdnu vybrané pro měsíční uchovávání |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Týdny v měsíci, kdy je nakonfigurováno měsíční uchovávání, například První, Poslední atd. |
| YearlyRetentionDuration_s |Desetinné číslo ||Celková doba uchovávání v letech pro nakonfigurované zálohy |
| YearlyRetentionTimes_s |Text ||Datum a čas, kdy je nakonfigurováno roční uchovávání informací |
| YearlyRetentionMonthsOfTheYear_s |Text ||Měsíce v roce vybrané pro roční uchovávání |
| YearlyRetentionFormat_s |Text ||Typ konfigurace pro roční uchovávání, například denně pro den, týdně pro týden | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Data měsíce vybraného pro roční uchovávání |
| SynchronisationFrequencyPerDay_s |Celé číslo |v2|Počet synchronizací zálohování souborů pro sazí SC DPM a MABS za den |
| DiffBackupFormat_s |Text |v2|Formát pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure |
| DiffBackupTime_s |Time |v2|Čas pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure|
| DiffBackupRetentionDuration_s |Desetinné číslo |v2|Doba uchovávání informací pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure|
| LogBackupFrequency_s |Desetinné číslo |v2|Frekvence pro zálohy protokolů pro SQL|
| LogBackupRetentionDuration_s |Desetinné číslo |v2|Doba uchovávání informací pro zálohy protokolů pro SQL v zálohování virtuálních počítačích Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Dny v týdnu pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure|
| SourceSystem |Text ||Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text ||Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text ||Identifikátor předplatného prostředku (např. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text ||Skupina prostředků prostředku (ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text ||Zprostředkovatel prostředků, pro kterého jsou shromažďována data. Například Microsoft.RecoveryServices |
| ResourceType |Text ||Typ prostředku, pro který jsou shromažďována data. Například trezory |

### <a name="policyassociation"></a>Přidružení zásad

Tato tabulka obsahuje podrobnosti o přidružení zásad s různými entitami.

| Pole | Typ dat | Použitelné verze | Popis |
| --- | --- | --- | --- |
| EventName_s |Text ||Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text ||Toto pole označuje aktuální verzi schématu, je **to V2** |
| State_s |Text ||Aktuální stav objektu zásad, například Aktivní, Odstraněno |
| BackupManagementType_s |Text ||Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| OperationName |Text ||Toto pole představuje název aktuální operace - PolicyAssociation |
| Kategorie |Text ||Toto pole představuje kategorii diagnostických dat zasunutých do protokolů Azure Monitoru, je to AzureBackupReport |
| Prostředek |Text ||Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| PolicyUniqueId_g |Text ||Jedinečné ID pro identifikaci zásady |
| VaultUniqueId_s |Text ||Jedinečné ID trezoru, do kterého tato zásada patří |
| BackupManagementServerUniqueId_s |Text |v2 |Pole pro jednoznačnou identifikaci serveru pro správu zálohování, kterým je položka zálohování chráněna, pokud je to možné        |
| SourceSystem |Text ||Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text ||Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text ||Identifikátor předplatného prostředku (např. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text ||Skupina prostředků prostředku (ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text ||Zprostředkovatel prostředků, pro kterého jsou shromažďována data. Například Microsoft.RecoveryServices |
| ResourceType |Text ||Typ prostředku, pro který jsou shromažďována data. Například trezory |

### <a name="protected-container"></a>Chráněný kontejner

Tato tabulka obsahuje základní pole o chráněných kontejnerech. (Byl ProtectedServer v v1)

| Pole | Typ dat | Popis |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Pole pro jednoznačnou identifikaci chráněného kontejneru |
| ProtectedContainerOSType_s |Text |Typ chráněného kontejneru operačního režimu |
| ProtectedContainerOSVersion_s |Text |Verze chráněného kontejneru pro operační systém |
| AgentVersion_s |Text |Číslo verze aplikace Agent Backup nebo agenta ochrany (v případě sc dpm a mabs) |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování. Například IaaSVM, FileFolder |
| EntityState_s |Text |Aktuální stav objektu chráněného serveru. Například Aktivní, Odstraněno |
| ProtectedContainerFriendlyName_s |Text |Popisný název chráněného serveru |
| ProtectedContainerName_s |Text |Název chráněného kontejneru |
| ProtectedContainerWorkloadType_s |Text |Typ zálohovaného chráněného kontejneru. Například IaASVMContainer |
| ProtectedContainerLocation_s |Text |Jestli se chráněný kontejner nachází místně nebo v Azure |
| ProtectedContainerType_s |Text |Zda je chráněný kontejner server nebo kontejner |
| ProtectedContainerProtectionState_s"  |Text |Stav ochrany chráněného kontejneru |

### <a name="storage"></a>Úložiště

Tato tabulka obsahuje podrobnosti o polích souvisejících s úložištěm.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| CloudStorageInBytes_s |Desetinné číslo |Úložiště zálohování v cloudu používané zálohami, vypočtené na základě nejnovější hodnoty (Toto pole je pouze pro schéma v1)|
| ProtectedInstances_s |Desetinné číslo |Počet chráněných instancí použitých pro výpočet úložiště front-end ve fakturaci, vypočítaný na základě nejnovější hodnoty |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **to V2** |
| State_s |Text |Aktuální stav objektu úložiště, například Aktivní, Odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - úložiště |
| Kategorie |Text |Toto pole představuje kategorii diagnostických dat zasunutých do protokolů Azure Monitoru, je to AzureBackupReport |
| Prostředek |Text |Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| ProtectedServerUniqueId_s |Text |Jedinečné ID chráněného serveru, pro který se počítá úložiště |
| VaultUniqueId_s |Text |Jedinečné ID trezoru pro úložiště se vypočítá |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text |Skupina prostředků prostředku (ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text |Zprostředkovatel prostředků, pro kterého jsou shromažďována data. Například Microsoft.RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který jsou shromažďována data. Například trezory |
| StorageUniqueId_s |Text |Jedinečné ID použité k identifikaci entity úložiště |
| StorageType_s |Text |Typ úložiště, například Cloud, Svazek, Disk |
| StorageName_s |Text |Název entity úložiště, například E:\ |
| StorageTotalSizeInGBs_s |Text |Celková velikost úložiště v GB spotřebované entitou úložiště|

### <a name="storageassociation"></a>Přidružení úložiště

Tato tabulka obsahuje základní pole související s úložištěm, která připojují úložiště k jiným entitám.

| Pole | Typ dat | Popis |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Jedinečné ID použité k identifikaci entity úložiště |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **to V2** |
| BackupItemUniqueId_s |Text |Jedinečné ID použité k identifikaci položky zálohování související s entitou úložiště |
| BackupManagementServerUniqueId_s |Text |Jedinečné ID používané k identifikaci serveru pro správu zálohování souvisejícího s entitou úložiště|
| VaultUniqueId_s |Text |Jedinečné ID použité k identifikaci trezoru souvisejícího s entitou úložiště|
| StorageConsumedInMBs_s |Číslo|Velikost úložiště spotřebovaného odpovídající položkou zálohy v odpovídajícím úložišti |
| StorageAllocatedInMBs_s |Číslo |Velikost úložiště přidělená odpovídající položkou zálohy v odpovídajícím úložišti typu Disk|

### <a name="vault"></a>Trezor

Tato tabulka obsahuje podrobnosti o polích souvisejících s úschovnou.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **to V2** |
| State_s |Text |Aktuální stav objektu úschovny, například Aktivní, Odstraněno |
| OperationName |Text |Toto pole představuje název aktuální operace - trezor |
| Kategorie |Text |Toto pole představuje kategorii diagnostických dat zasunutých do protokolů Azure Monitoru, je to AzureBackupReport |
| Prostředek |Text |Jedná se o prostředek, pro který jsou shromažďována data, zobrazuje název trezoru služby Recovery Services. |
| VaultUniqueId_s |Text |Jedinečné ID trezoru |
| VaultName_s |Text |Název trezoru |
| AzureDataCenter_s |Text |Datové centrum, kde se nachází trezor |
| StorageReplicationType_s |Text |Typ replikace úložiště pro úložiště, například GeoRedundant |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceGroup |Text |Skupina prostředků prostředku (ex. úložiště služby Recovery Services), pro které jsou shromažďována data |
| ResourceProvider |Text |Zprostředkovatel prostředků, pro kterého jsou shromažďována data. Například Microsoft.RecoveryServices |
| ResourceType |Text |Typ prostředku, pro který jsou shromažďována data. Například trezory |

### <a name="backup-management-server"></a>Server pro správu zálohování

Tato tabulka obsahuje základní pole o serverech pro správu zálohování.

|Pole  |Typ dat  | Popis  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |Název serveru pro správu zálohování        |
|AzureBackupAgentVersion_s     |Text         |Verze agenta zálohování Azure na serveru pro správu zálohování          |
|BackupManagementServerVersion_s     |Text         |Verze serveru pro správu zálohování|
|BackupManagementServerOSVersion_s     |Text            |Verze systému zálohování os|
|BackupManagementServerType_s     |Text         |Typ serveru pro správu zálohování, jako mabs, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Pole pro jednoznačnou identifikaci serveru pro správu zálohování       |

### <a name="preferredworkloadonvolume"></a>Upřednostňovaný pracovní vytíženíonový svazek

Tato tabulka určuje zatížení, ke kterým je svazek přidružen.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| StorageUniqueId_s |Text |Jedinečné ID použité k identifikaci entity úložiště |
| BackupItemType_s |Text |Úlohy, pro které je tento svazek upřednostňovaným úložištěm|

### <a name="protectedinstance"></a>Chráněná instance

Tato tabulka obsahuje základní pole související s chráněnými instancemi.

| Pole | Typ dat |Použitelné verze | Popis |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Jedinečné ID použité k identifikaci zálohovací položky pro virtuální počítače zálohované pomocí dpm, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Jedinečné ID používané k identifikaci chráněného kontejneru pro všechno kromě virtuálních počítačů zálohovaných pomocí DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Počet chráněných instancí pro přidruženou položku zálohování nebo chráněný kontejner k tomuto datu a času|

### <a name="recoverypoint"></a>Bod obnovení

Tato tabulka obsahuje základní pole související s bodem obnovení.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Jedinečné ID použité k identifikaci zálohovací položky pro virtuální počítače zálohované pomocí dpm, MABS|
| OldestRecoveryPointTime_s |Text |Datum data nejstaršího bodu obnovení pro položku zálohování|
| OldestRecoveryPointLocation_s |Text |Umístění nejstaršího bodu obnovení pro položku zálohování|
| LatestRecoveryPointTime_s |Text |Datum data posledního bodu obnovení pro položku zálohování|
| LatestRecoveryPointLocation_s |Text |Umístění nejnovějšího bodu obnovení pro položku zálohování|

## <a name="sample-kusto-queries"></a>Ukázka dotazů Kusto

Níže je několik ukázek, které vám pomohou psát dotazy na data Azure Backup, která se nacházejí v tabulce Diagnostika Azure:

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

- Všechny úspěšné úlohy zálohování virtuálních počítačových virtuálních počítačů Azure

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

- Všechny úspěšné úlohy agenta Azure Backup

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
    
## <a name="next-steps"></a>Další kroky

Po kontrole datového modelu můžete začít [vytvářet vlastní dotazy](../azure-monitor/learn/tutorial-logs-dashboards.md) v protokolech Azure Monitor u vytvoření vlastního řídicího panelu.
