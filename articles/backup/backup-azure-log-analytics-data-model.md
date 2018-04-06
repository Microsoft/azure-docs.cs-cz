---
title: Datový model Log Analytics pro Azure Backup
description: V tomto článku bude zmíněn podrobnosti modelu dat analýzy protokolů pro data Azure Backup.
services: backup
documentationcenter: ''
author: JPallavi
manager: vijayts
editor: ''
ms.assetid: dfd5c73d-0d34-4d48-959e-1936986f9fc0
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d55ec8ac4416fe0a082812584552462292b6dbb7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Model dat analýzy protokolů pro zálohování Azure data
Tento článek popisuje model dat použitý pro vkládání data pro generování sestav k analýze protokolů. Tento datový model můžete vytvářet vlastní dotazy, řídicí panely a využívat v analýzy protokolů. 

## <a name="using-azure-backup-data-model"></a>Pomocí Azure Backup datový model
Následující pole zadaný jako součást modelu dat můžete použít k vytváření vizuálních prvků, vlastních dotazů a řídicí panel podle vašich požadavků.

### <a name="alert"></a>Výstrahy
Tato tabulka obsahuje podrobnosti o výstrahy související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| AlertUniqueId_s |Text |Jedinečné Id generované výstrahy |
| AlertType_s |Text |Typu generované výstrahy, například zálohování |
| AlertStatus_s |Text |Stav výstrahy, například aktivní |
| AlertOccurenceDateTime_s |Datum/čas |Datum a čas vytvoření výstrahy |
| AlertSeverity_s |Text |Závažnost výstrahy, například kritický |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečné Id položky zálohování, do které patří tato výstraha |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav výstrahy objektu, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří tato výstraha |
| OperationName |Text |Toto pole představuje název aktuální operace – výstraha |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| ProtectedServerUniqueId_s |Text |Jedinečné Id systému chráněného, do které patří tato výstraha |
| VaultUniqueId_s |Text |Jedinečné Id systému chráněného, do které patří tato výstraha |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="backupitem"></a>BackupItem
Tato tabulka obsahuje podrobné informace o zálohování související s položkou pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečné Id položky zálohování |
| BackupItemId_s |Text |ID položky zálohování |
| BackupItemName_s |Text |Název položky zálohování |
| BackupItemFriendlyName_s |Text |Popisný název položky zálohování |
| BackupItemType_s |Text |Typ zálohování položky, například virtuální počítač, FileFolder |
| ProtectedServerName_s |Text |Název chráněného serveru, na která zálohování položka patří do |
| ProtectionState_s |Text |Aktuální stav ochrany zálohování položky, například chráněné, ProtectionStopped |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu zálohování položek, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří tato položka zálohování |
| OperationName |Text |Toto pole představuje název aktuální operace - BackupItem |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="backupitemassociation"></a>BackupItemAssociation
Tato tabulka obsahuje podrobné informace o přidružení položky zálohování s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Jedinečné Id položky zálohování |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu přidružení zálohování položek, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří tato položka zálohování |
| OperationName |Text |Toto pole představuje název aktuální operace - BackupItemAssociation |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| PolicyUniqueId_g |Text |Jedinečné Id pro identifikaci zásad, která zálohování položka je přidružena k |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru, do které patří tato položka zálohování |
| VaultUniqueId_s |Text |Jedinečné Id trezoru, do které patří tato položka zálohování |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="job"></a>Úloha
Tato tabulka obsahuje podrobnosti o pole související úlohy.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Jedinečné Id položky zálohování, do které patří tato úloha |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu úlohy, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří tato úloha |
| OperationName |Text |Toto pole představuje název aktuální operace – úloha |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| ProtectedServerUniqueId_s |Text |Jedinečné Id systému chráněného, do které patří tato úloha |
| VaultUniqueId_s |Text |Jedinečné Id systému chráněného, do které patří tato úloha |
| JobOperation_s |Text |Operaci, pro kterou úlohy je třeba spustit zálohování, obnovení, nakonfigurujte zálohování |
| JobStatus_s |Text |Stav dokončení úlohy, například dokončeno, se nezdařilo |
| JobFailureCode_s |Text |Selhání kódu řetězec kvůli které došlo k selhání úlohy |
| JobStartDateTime_s |Datum/čas |Datum a čas, kdy úloha spustila spuštěná |
| BackupStorageDestination_s |Text |Cílové úložiště záloh, například cloudu, disku  |
| JobDurationInSecs_s | Číslo |Celkový počet úloh doby v sekundách |
| DataTransferredInMB_s | Číslo |Data přenesená v MB pro tuto úlohu|
| JobUniqueId_g |Text |Jedinečné Id pro identifikaci úlohy |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="policy"></a>Zásada
Tato tabulka obsahuje podrobnosti o pole související se zásadami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu zásad, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří tato zásada |
| OperationName |Text |Toto pole představuje název aktuální operace - zásad |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| PolicyUniqueId_g |Text |Jedinečné Id pro identifikaci zásady |
| PolicyName_s |Text |Název zásady definované |
| BackupFrequency_s |Text |Frekvence, se kterým se spustit zálohování, například denně, týdně |
| BackupTimes_s |Text |Datum a čas, kdy jsou naplánované zálohy |
| BackupDaysOfTheWeek_s |Text |Dny v týdnu, kdy bylo naplánováno zálohování |
| RetentionDuration_s |Celé číslo |Doba uchování pro nastavená zálohování |
| DailyRetentionDuration_s |Celé číslo |Doba celkový uchování ve dnech pro nastavená zálohování |
| DailyRetentionTimes_s |Text |Datum a čas, kdy byl nakonfigurován denní uchování |
| WeeklyRetentionDuration_s |Desetinné číslo |Celková doba uchování týdenní týdnů pro nastavená zálohování |
| WeeklyRetentionTimes_s |Text |Datum a čas, když je nakonfigurovaný týdenního uchovávání |
| WeeklyRetentionDaysOfTheWeek_s |Text |Vybrané dny v týdnu pro týdenní uchování |
| MonthlyRetentionDuration_s |Desetinné číslo |Doba uchování celkový počet měsíců pro nastavená zálohování |
| MonthlyRetentionTimes_s |Text |Datum a čas, když je nakonfigurovaný měsíční uchování |
| MonthlyRetentionFormat_s |Text |Typ konfigurace pro měsíční uchování, například denní, den, každý týden základě za týden na základě |
| MonthlyRetentionDaysOfTheWeek_s |Text |Vybrané dny v týdnu pro měsíční uchování |
| MonthlyRetentionWeeksOfTheMonth_s |Text |Týdny v měsíci, když je nakonfigurovaná měsíční uchovávání informací, například první, poslední atd. |
| YearlyRetentionDuration_s |Desetinné číslo |Doba uchování celkový počet roků pro nastavená zálohování |
| YearlyRetentionTimes_s |Text |Datum a čas, když je nakonfigurovaný roční uchování |
| YearlyRetentionMonthsOfTheYear_s |Text |Měsíce v roce vybrané pro roční uchování |
| YearlyRetentionFormat_s |Text |Typ konfigurace pro roční uchování, například denní, den, každý týden základě za týden na základě |
| YearlyRetentionDaysOfTheMonth_s |Text |Kalendářních dat v měsíci vybrané pro roční uchování |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="policyassociation"></a>PolicyAssociation
Tato tabulka obsahuje podrobné informace o přidružení zásady s různými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu zásad, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování například IaaSVM FileFolder, do které patří tato zásada |
| OperationName |Text |Toto pole představuje název aktuální operace - PolicyAssociation |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| PolicyUniqueId_g |Text |Jedinečné Id pro identifikaci zásady |
| VaultUniqueId_s |Text |Jedinečné Id trezoru, do které patří tato zásada |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="protectedserver"></a>ProtectedServer
Tato tabulka obsahuje podrobnosti o chráněný server související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| ProtectedServerName_s |Text |Název chráněného serveru |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu chráněného serveru, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování například IaaSVM FileFolder, do které patří tohoto chráněného serveru |
| OperationName |Text |Toto pole představuje název aktuální operace - ProtectedServer |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru |
| RegisteredContainerId_s |Text |ID kontejneru zaregistrované pro zálohování |
| ProtectedServerType_s |Text |Typ chráněného serveru zálohovat například systému Windows |
| ProtectedServerFriendlyName_s |Text |Popisný název chráněného serveru |
| AzureBackupAgentVersion_s |Text |Číslo verze agenta zálohování |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Tato tabulka obsahuje podrobné informace o přidružení chráněném serveru s jinými entitami.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu přidružení chráněného serveru, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří tohoto chráněného serveru |
| OperationName |Text |Toto pole představuje název aktuální operace - ProtectedServerAssociation |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru |
| VaultUniqueId_s |Text |Jedinečné Id trezoru, do které patří tohoto chráněného serveru |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

### <a name="storage"></a>Úložiště
Tato tabulka obsahuje podrobnosti o pole související s úložištěm.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| CloudStorageInBytes_s |Desetinné číslo |Cloudové zálohování úložiště používané zálohování, počítá na základě nejnovější hodnoty |
| ProtectedInstances_s |Desetinné číslo |Počet instancí chráněné sloužící k výpočtu front-endu úložiště v fakturace, počítaný na základě nejnovější hodnotu. |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněno |
| BackupManagementType_s |Text |Typ zprostředkovatele pro provádění zálohování, například IaaSVM FileFolder, do které patří toto úložiště |
| OperationName |Text |Toto pole představuje název aktuální operace – úložiště |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| ProtectedServerUniqueId_s |Text |Jedinečné Id chráněného serveru, pro které je vypočtena úložiště |
| VaultUniqueId_s |Text |Jedinečné Id trezoru pro úložiště se počítá. |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole representse typ prostředku, pro které se shromažďují data - trezory |

### <a name="vault"></a>Trezor
Tato tabulka obsahuje podrobné informace o trezoru související pole.

| Pole | Typ dat | Popis |
| --- | --- | --- |
| EventName_s |Text |Toto pole představuje název této události, je vždy AzureBackupCentralReport |
| SchemaVersion_s |Text |Toto pole označuje aktuální verzi schématu, je **V1** |
| State_s |Text |Aktuální stav objektu úložiště, například aktivní, odstraněno |
| OperationName |Text |Toto pole představuje název aktuální operace - trezoru |
| Kategorie |Text |Toto pole představuje kategorii dat diagnostiky nabídnutých k analýze protokolů, je AzureBackupReport |
| Prostředek |Text |Toto je prostředků, pro které se shromažďují data, se zobrazí název trezoru služeb zotavení |
| VaultUniqueId_s |Text |Jedinečné Id trezoru |
| VaultName_s |Text |Název trezoru |
| AzureDataCenter_s |Text |Datové centrum, kde se nachází úložiště |
| StorageReplicationType_s |Text |Typ replikace úložiště pro úložiště, například GeoRedundant |
| SourceSystem |Text |Systém zdrojového aktuální dat – Azure |
| ID prostředku |Text |Toto pole představuje id prostředku pro kterou se data shromažďují, zobrazuje id prostředku trezoru služeb zotavení |
| SubscriptionId |Text |Toto pole představuje id předplatného prostředku (RS trezoru), pro které se shromažďují data |
| ResourceGroup |Text |Toto pole představuje skupinu prostředků prostředku (RS trezoru), pro které se shromažďují data |
| ResourceProvider |Text |Toto pole představuje poskytovatele prostředků pro které se shromažďují data - Microsoft.RecoveryServices |
| ResourceType |Text |Toto pole představuje typ prostředku, pro které se shromažďují data - trezory |

## <a name="next-steps"></a>Další postup
Po prostudování datový model pro vytváření sestav Azure Backup, můžete spustit [vytváření řídicího panelu](../log-analytics/log-analytics-dashboards.md) v analýzy protokolů.
