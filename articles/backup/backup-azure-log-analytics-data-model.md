---
title: Datový model Log Analytics pro Azure Backup
description: Tento článek pojednává o podrobnosti modelu dat Log Analytics pro Azure zálohování – uložená data.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09f7d4c5e76d4f74d447f8e8760e1f348462c769
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54887015"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Datový model log Analytics pro Azure zálohování – uložená data
Datový model Log Analytics můžete použijte k vytvoření sestav. S datovým modelem můžete vytvořit vlastní dotazy a řídicí panely nebo přizpůsobení zálohování Azure – uložená data, ale chcete.

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
| EventName_s |Text |Název události Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky přiřazené k výstraze |
| SchemaVersion_s |Text |Aktuální verze schématu, například **V1** |
| State_s |Text |Aktuální stav výstrahy objektu, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, ke kterému patří tato výstraha |
| OperationName |Text |Název aktuální operace, například výstrahy |
| Kategorie |Text |Kategorie diagnostická data do Log Analytics. Vždy AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přiřazené k výstraze |
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
| EventName_s |Text |Název události Vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky |
| BackupItemId_s |Text |Identifikátor zálohovaná položka |
| BackupItemName_s |Text |Název zálohované položky |
| BackupItemFriendlyName_s |Text |Popisný název zálohované položky |
| BackupItemType_s |Text |Typ zálohování položky, například virtuální počítač, FileFolder |
| ProtectedServerName_s |Text |Název chráněného serveru na zálohovanou položku, která patří do |
| ProtectionState_s |Text |Aktuální stav ochrany zálohované položky, třeba chráněné, ProtectionStopped |
| SchemaVersion_s |Text |Verze schématu, například **V1** |
| State_s |Text |Stav objektu zálohované položky, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, ke kterému patří této zálohované položky |
| OperationName |Text |Název operace, například BackupItem |
| Kategorie |Text |Kategorie diagnostická data do Log Analytics. Vždy AzureBackupReport |
| Prostředek |Text |Prostředek pro jaká data se shromažďují, například název trezoru služby Recovery Services |
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
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav objektu přidružení zálohovaná položka, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - BackupItemAssociation |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| PolicyUniqueId_g |Text |Jedinečný identifikátor pro zásady přidružené k zálohované položky |
| ProtectedServerUniqueId_s |Text |Jedinečný identifikátor chráněného serveru přidružené k zálohované položky |
| VaultUniqueId_s |Text |Jedinečný identifikátor trezoru, který obsahuje zálohované položky |
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
| EventName_s |Text |Název události Vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečný identifikátor zálohované položky |
| SchemaVersion_s |Text |Verze schématu, například **V1** |
| State_s |Text |Aktuální stav objektu úlohy, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace – úloha |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečný identifikátor na chráněném serveru související úlohy |
| VaultUniqueId_s |Text |Jedinečný identifikátor chráněné trezoru |
| JobOperation_s |Text |Operace, pro který se úloha spustí například zálohování, obnovení, nakonfigurujte zálohování |
| JobStatus_s |Text |Stav dokončené úlohy, například Completed, Failed |
| JobFailureCode_s |Text |Řetězec kódu chyby kvůli které došlo k selhání úlohy |
| JobStartDateTime_s |Datum a čas |Datum a čas, kdy úlohy Začínáme spuštění |
| BackupStorageDestination_s |Text |Cílové úložiště záloh, například Cloud, disku  |
| JobDurationInSecs_s | Číslo |Celkový počet úloh doba trvání v sekundách |
| DataTransferredInMB_s | Číslo |Data přenesená v MB pro tuto úlohu|
| JobUniqueId_g |Text |Jedinečné Id pro identifikaci úlohy |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services|
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="policy"></a>Zásada
Tato tabulka obsahuje podrobnosti o týkající se zásad pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav objektu zásad, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální akce – zásady |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| PolicyUniqueId_g |Text |Jedinečný identifikátor zásad |
| PolicyName_s |Text |Název definice zásady |
| BackupFrequency_s |Text |Frekvence, se kterým se spouštět zálohování, třeba každý den, každý týden |
| BackupTimes_s |Text |Datum a čas, kdy jsou naplánované zálohy |
| BackupDaysOfTheWeek_s |Text |Dny v týdnu, kdy bylo naplánováno zálohy |
| RetentionDuration_s |Celé číslo |Doba uchování pro nastavená zálohování |
| DailyRetentionDuration_s |Celé číslo |Doba trvání celkový uchování ve dnech pro nastavená zálohování |
| DailyRetentionTimes_s |Text |Datum a čas, kdy byl nakonfigurován denní uchovávání |
| WeeklyRetentionDuration_s |Desetinné číslo |Celková doba uchování týdenní v týdnech nakonfigurované zálohování |
| WeeklyRetentionTimes_s |Text |Datum a čas, kdy nakonfigurovat týdenní uchovávání |
| WeeklyRetentionDaysOfTheWeek_s |Text |Vybrané dny v týdnu pro týdenní uchovávání |
| MonthlyRetentionDuration_s |Desetinné číslo |Doba uchování celkový počet měsíců pro nastavená zálohování |
| MonthlyRetentionTimes_s |Text |Datum a čas, kdy je nakonfigurován měsíční uchovávání |
| MonthlyRetentionFormat_s |Text |Typ konfigurace pro měsíční uchovávání informací, například každý den za den základě týdně podle týdnů |
| MonthlyRetentionDaysOfTheWeek_s |Text |Vybrané dny v týdnu pro měsíční uchovávání |
| MonthlyRetentionWeeksOfTheMonth_s |Text |Týdny v měsíci, když je nakonfigurovaná měsíční uchovávání informací, například první, poslední atd. |
| YearlyRetentionDuration_s |Desetinné číslo |Doba uchování celkový počet v letech nastavená zálohování |
| YearlyRetentionTimes_s |Text |Datum a čas, kdy je nakonfigurován roční uchovávání |
| YearlyRetentionMonthsOfTheYear_s |Text |Měsíce roku vybraných pro roční uchovávání |
| YearlyRetentionFormat_s |Text |Typ konfigurace pro roční uchovávání informací, například každý den za den základě týdně podle týdnů |
| YearlyRetentionDaysOfTheMonth_s |Text |Data v měsíci vybraných pro roční uchovávání |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="policyassociation"></a>PolicyAssociation
Tato tabulka obsahuje podrobné informace o přidružení zásady s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav objektu zásad, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - PolicyAssociation |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| PolicyUniqueId_g |Text |Jedinečný identifikátor zásad |
| VaultUniqueId_s |Text |Jedinečné Id trezoru, ke kterému patří tato zásada |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="protectedserver"></a>ProtectedServer
Tato tabulka obsahuje podrobnosti o chráněný server související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| ProtectedServerName_s |Text |Název chráněného serveru |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav objektu chráněného serveru, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - ProtectedServer |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru |
| RegisteredContainerId_s |Text |ID kontejneru zaregistrované pro zálohování |
| ProtectedServerType_s |Text |Typ chráněného serveru, například Windows |
| ProtectedServerFriendlyName_s |Text |Popisný název chráněného serveru |
| AzureBackupAgentVersion_s |Text |Číslo verze agenta zálohování |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Tato tabulka obsahuje podrobnosti o sdružení chráněným serverem s jinými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav přidružení objektu chráněného serveru, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - ProtectedServerAssociation |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru |
| VaultUniqueId_s |Text |Jedinečné Id trezoru, ke kterému patří tento chráněný server |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="storage"></a>Storage
Tato tabulka obsahuje podrobnosti o polích týkající se úložiště.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| CloudStorageInBytes_s |Desetinné číslo |Cloudové zálohování úložiště využitá službou zálohování, které se počítá na základě nejnovější hodnoty |
| ProtectedInstances_s |Desetinné číslo |Počet chráněných instancí sloužící k výpočtu front-endu úložiště fakturace, vypočítané hodnoty na základě nejnovější |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro server provádění úlohy zálohování, například IaaSVM, FileFolder |
| OperationName |Text |Toto pole představuje název aktuální operace - úložiště |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru, pro které se počítá úložiště |
| VaultUniqueId_s |Text |Jedinečné Id trezoru pro úložiště se počítá. |
| SourceSystem |Text |Zdrojový systém aktuálních dat – Azure |
| ResourceId |Text |Identifikátor URI pro shromažďovaných údajů. Například id prostředku trezoru služby Recovery Services |
| SubscriptionId |Text |Identifikátor předplatného prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceGroup |Text |Skupina prostředků prostředku (např.) Trezor služby Recovery Services) pro které se shromažďují data |
| ResourceProvider |Text |Poskytovatel prostředků, pro které se shromažďují data. Například Microsoft.recoveryservices. |
| ResourceType |Text |Typ prostředku, pro které se shromažďují data. Například trezory Recovery Services |

### <a name="vault"></a>Trezor
Tato tabulka obsahuje podrobnosti o trezoru související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, ale **V1** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněno |
| OperationName |Text |Toto pole představuje název aktuální operace - Vault |
| Kategorie |Text |Toto pole představuje kategorie diagnostická data do Log Analytics, je AzureBackupReport |
| Prostředek |Text |Toto je prostředek, pro kterou se data shromažďují, zobrazuje název trezoru služby Recovery Services |
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

## <a name="next-steps"></a>Další postup
Jakmile zkontrolujete datový model pro vytváření sestav Azure Backup, můžete začít [vytváření řídicího panelu](../azure-monitor/learn/tutorial-logs-dashboards.md) v Log Analytics.
