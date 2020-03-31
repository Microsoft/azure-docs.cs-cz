---
title: Datový model pro události diagnostiky zálohování Azure
description: Tento datový model odkazuje na režim odesílání diagnostických událostí do analýzy protokolů (LA) specifický pro daný prostředek.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583380"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Datový model pro události diagnostiky zálohování Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Tato tabulka obsahuje informace o základních entitách zálohování, jako jsou úschovny a položky zálohování.

| **Pole**                         | **Typ dat** | **Popis**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services. |
| OperationName                     | Text          | Toto pole představuje název aktuální operace - BackupItem, BackupItemAssociation nebo ProtectedContainer. |
| Kategorie                          | Text          | Toto pole představuje kategorii diagnostických dat zaslaných do protokolů Azure Monitor. Například CoreAzureBackup. |
| Verze agenta                      | Text          | Číslo verze aplikace Agent Backup nebo agenta ochrany (v případě sc dpm a mabs) |
| AzureBackupAgentVersion           | Text          | Verze agenta zálohování Azure na serveru pro správu zálohování |
| AzureDataCenter                   | Text          | Datové centrum, kde se nachází trezor                       |
| BackupItemAppVersion              | Text          | Verze zálohovací položky aplikace                       |
| Název BackupItemFriendlyName            | Text          | Popisný název položky zálohy                             |
| Název_zálohy                    | Text          | Název zálohovací položky                                      |
| Systém BackupItemProtectionState         | Text          | Stav ochrany položky zálohování                          |
| BackupItemfrontendSize            | Text          | Velikost zálohovací položky front-endu                            |
| Typ položky BackupItem                    | Text          | Typ položky zálohování. Například: Virtuální měna, Složka souborů             |
| BackupItemUniqueId                | Text          | Jedinečný identifikátor položky zálohy                         |
| Typ BackupManagementServerType        | Text          | Typ serveru pro správu zálohování, jako v MABS, SC DPM     |
| BackupManagementServerUniqueId    | Text          | Pole pro jednoznačnou identifikaci serveru pro správu zálohování      |
| BackupManagementType              | Text          | Typ zprostředkovatele pro server, který provádí úlohu zálohování. Například IaaSVM, FileFolder |
| Název_serveru BackupManagementServerName        | Text          | Název serveru pro správu zálohování                         |
| BackupManagementServerOSversion   | Text          | Verze systému zálohování os                   |
| BackupManagementServerVersion     | Text          | Verze serveru pro správu zálohování                      |
| Nejnovější RecoveryPointLocation       | Text          | Umístění nejnovějšího bodu obnovení pro položku zálohování    |
| Nejnovější RecoveryPointTime           | DateTime      | Datum data posledního bodu obnovení pro položku zálohování   |
| Nejstarší recoverypointlocation       | Text          | Umístění nejstaršího bodu obnovení pro položku zálohování    |
| Nejstarší RecoveryPointTime           | DateTime      | Datum data posledního bodu obnovení pro položku zálohování   |
| PolicyUniqueId                    | Text          | Jedinečné ID pro identifikaci zásady                             |
| ProtectedContainerFriendlyName    | Text          | Popisný název chráněného serveru                        |
| Umístění protectedkontejneru        | Text          | Jestli se chráněný kontejner nachází místně nebo v Azure |
| ProtectedContainerName            | Text          | Název chráněného kontejneru                            |
| ProtectedContainerOSType          | Text          | Typ chráněného kontejneru operačního režimu                          |
| Chráněnácontainerosversion       | Text          | Verze chráněného kontejneru pro operační systém                        |
| ProtectedContainerProtectionState | Text          | Stav ochrany chráněného kontejneru                  |
| ProtectedContainerType            | Text          | Zda je chráněný kontejner server nebo kontejner  |
| ProtectedContainerUniqueId        | Text          | Jedinečné ID používané k identifikaci chráněného kontejneru pro všechno kromě virtuálních počítačů zálohovaných pomocí DPM, MABS |
| ProtectedContainerWorkloadType    | Text          | Typ zálohovaného chráněného kontejneru. Například IaASVMContainer |
| ProtectionGroupName               | Text          | Název skupiny ochrany, ve které je zálohovací položka chráněna, pro sc dpm a mabs, pokud je to možné |
| ResourceGroupName                 | Text          | Skupina prostředků prostředku (například trezor služby Recovery Services) pro shromažďovaná data |
| Verze schématu                     | Text          | Toto pole označuje aktuální verzi schématu, je **to V2** |
| SecondaryBackupProtectionState    | Text          | Zda je pro položku zálohování povolena sekundární ochrana  |
| Stav                             | Text          | Stav objektu zálohovací položky. Například Aktivní, Odstraněno |
| StorageReplicationType            | Text          | Typ replikace úložiště pro trezor. Například Georedundantní |
| SubscriptionId                    | Text          | Identifikátor předplatného prostředku (například trezor služby Recovery Services), pro který jsou shromažďována data |
| Název úložiště                         | Text          | Název trezoru                                            |
| VaultTagy                         | Text          | Značky přidružené k prostředku trezoru                    |
| VaultUniqueId                     | Text          | Jedinečný identifikátor trezoru                             |
| SourceSystem                      | Text          | Zdrojový systém aktuálních dat – Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Tato tabulka obsahuje podrobnosti o polích souvisejících s výstrahami.

| **Pole**                      | **Typ dat** | **Popis**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Jedinečný identifikátor prostředku, o kterém zdroji jsou shromažďována. Například ID prostředku trezoru služby Recovery Services |
| OperationName                  | Text          | Název aktuální operace. Například výstraha            |
| Kategorie                       | Text          | Kategorie diagnostických dat zasunutých do protokolů azure monitoru – AddonAzureBackupAlerts |
| Výstražný kód                      | Text          | Kód pro jednoznačnou identifikaci typu výstrahy                     |
| Stav alertconsolidationu       | Text          | Zjištění, zda se jedná o konsolidovanou výstrahu či nikoli         |
| AlertOccurrenceDateTime        | DateTime      | Datum a čas, kdy byla výstraha vytvořena                     |
| Výstraha                  | Text          | Typ entity, na kterou je výstraha vyvolána                        |
| Závažnost výstrahy                  | Text          | Závažnost výstrahy. Například kritické                 |
| Stav výstrahy                    | Text          | Stav výstrahy. Například aktivní                     |
| AlertTimeToResolveInMinutes    | Číslo        | Čas na vyřešení výstrahy. Prázdné pro aktivní výstrahy.     |
| Typ výstrahy                      | Text          | Typ výstrahy. Například zálohování                           |
| AlertUniqueId                  | Text          | Jedinečný identifikátor generované výstrahy                    |
| BackupItemUniqueId             | Text          | Jedinečný identifikátor zálohovací položky přidružené k výstraze |
| BackupManagementServerUniqueId | Text          | Pole pro jednoznačnou identifikaci serveru pro správu zálohování, kterým je položka zálohování chráněna, pokud je to možné |
| BackupManagementType           | Text          | Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| CountOfAlertsKonsolidované      | Číslo        | Počet konsolidovaných záznamů, pokud se jedná o konsolidovaný záznam  |
| ProtectedContainerUniqueId     | Text          | Jedinečný identifikátor chráněného serveru přidruženého k výstraze |
| Doporučená akce              | Text          | Akce doporučená k vyřešení výstrahy                      |
| Verze schématu                  | Text          | Aktuální verze schématu, například **V2**            |
| Stav                          | Text          | Aktuální stav objektu výstrahy, například Aktivní, Odstraněno |
| StorageUniqueId                | Text          | Jedinečné ID použité k identifikaci entity úložiště                |
| VaultUniqueId                  | Text          | Jedinečné ID použité k identifikaci trezoru souvisejícího s výstrahou    |
| SourceSystem                   | Text          | Zdrojový systém aktuálních dat – Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>Instance AddonAzureBackupProtectedInstance

Tato tabulka obsahuje základní pole související s chráněnými instancemi.

| **Pole**                      | **Typ dat** | **Popis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Jedinečný identifikátor prostředku, o kterém zdroji jsou shromažďována. Například ID prostředku trezoru služby Recovery Services |
| OperationName                  | Text          | Název operace, například ProtectedInstance         |
| Kategorie                       | Text          | Kategorie diagnostických dat zasunutých do protokolů programu Azure Monitor – AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | Jedinečné ID položky zálohy                                 |
| BackupManagementServerUniqueId | Text          | Pole pro jednoznačnou identifikaci serveru pro správu zálohování, kterým je položka zálohování chráněna, pokud je to možné |
| BackupManagementType           | Text          | Typ zprostředkovatele pro server, který provádí úlohu zálohování, například IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Text          | Jedinečné ID k identifikaci chráněného kontejneru, na který je úloha spuštěna |
| Počet chráněných instancí         | Text          | Počet chráněných instancí pro přidruženou položku zálohování nebo chráněný kontejner k tomuto datu a času |
| Verze schématu                  | Text          | Aktuální verze schématu, například **V2**            |
| Stav                          | Text          | Stav objektu zálohovací položky, například Aktivní, Odstraněno |
| VaultUniqueId                  | Text          | Jedinečný identifikátor chráněného úložiště přidruženého k chráněné instanci |
| SourceSystem                   | Text          | Zdrojový systém aktuálních dat – Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureZálohy

Tato tabulka obsahuje podrobnosti o polích souvisejících s úlohou.

| **Pole**                      | **Typ dat** | **Popis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| OperationName                  | Text          | Toto pole představuje název aktuální operace - Úloha    |
| Kategorie                       | Text          | Toto pole představuje kategorii diagnostických dat zaslaných do protokolů programu Azure Monitor – AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | Pole, které určuje, zda je úloha Ad Hoc nebo Scheduled           |
| BackupItemUniqueId             | Text          | Jedinečné ID použité k identifikaci položky zálohování související s entitou úložiště |
| BackupManagementServerUniqueId | Text          | Jedinečné ID používané k identifikaci serveru pro správu zálohování souvisejícího s entitou úložiště |
| BackupManagementType           | Text          | Typ zprostředkovatele pro zálohování, například IaaSVM, Folder FileFolder, do kterého tato výstraha patří |
| DataTransferredInMB            | Číslo        | Data přenesená v MB pro tuto úlohu                          |
| JobDurationInSecs              | Číslo        | Celková doba trvání úlohy v sekundách                                |
| JobFailureCode                 | Text          | Řetězec kódu selhání, kvůli kterému došlo k selhání úlohy    |
| Operace úlohy                   | Text          | Operace, pro kterou je úloha spuštěna, například Zálohování, Obnovení, Konfigurace zálohování |
| JobOperationSubType            | Text          | Dílčí typ operace úlohy. Například "Log", v případě úlohy zálohování protokolu |
| Doba spuštění úlohy               | DateTime      | Datum a čas spuštění úlohy                       |
| JobStatus                      | Text          | Stav dokončené úlohy, například Dokončeno, Nezdařilo se   |
| JobUniqueId                    | Text          | Jedinečné ID pro identifikaci úlohy                                |
| ProtectedContainerUniqueId     | Text          | Jedinečný identifikátor chráněného serveru přidruženého k výstraze |
| RecoveryJobDestination         | Text          | Cíl úlohy obnovení, kde jsou data obnovena   |
| RecoveryJobRPDateTime          | DateTime      | Datum, čas, kdy byl vytvořen bod obnovení, který je obnovován |
| RecoveryJobLocation            | Text          | Místo, kde byl uložen bod obnovení, který je obnovován |
| RecoveryLocationType           | Text          | Typ umístění pro obnovení                                |
| Verze schématu                  | Text          | Aktuální verze schématu, například **V2**            |
| Stav                          | Text          | Aktuální stav objektu výstrahy, například Aktivní, Odstraněno |
| VaultUniqueId                  | Text          | Jedinečný identifikátor chráněného trezoru přidruženého k výstraze |
| SourceSystem                   | Text          | Zdrojový systém aktuálních dat – Azure                    |

## <a name="addonazurebackuppolicy"></a>Zásady zálohování AddonAzure

Tato tabulka obsahuje podrobnosti o polích souvisejících se zásadami.

| **Pole**                       | **Typ dat**  | **Popis**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | Jedinečný identifikátor prostředku, o kterém zdroji jsou shromažďována. Například ID prostředku trezoru služby Recovery Services |
| OperationName                   | Text           | Název operace, například Policy nebo PolicyAssociation |
| Kategorie                        | Text           | Kategorie diagnostických dat zasunutých do protokolů Azure Monitoru – AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | Dny v týdnu, kdy byly naplánovány zálohy            |
| Frekvence zálohování                 | Text           | Frekvence, s jakou jsou spuštěny zálohy. Například denní, týdenní |
| BackupManagementType            | Text           | Typ zprostředkovatele pro server, který provádí úlohu zálohování. Například IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Text           | Pole pro jednoznačnou identifikaci serveru pro správu zálohování, kterým je položka zálohování chráněna, pokud je to možné |
| Záložníčasy                     | Text           | Datum a čas, kdy jsou naplánovány zálohy                     |
| Denní doba trvání          | Celé číslo   | Celková doba uchovávání ve dnech pro nakonfigurované zálohy      |
| DailyRetentionTimes             | Text           | Datum a čas, kdy bylo nakonfigurováno denní uchovávání informací            |
| DiffBackupDaysOfTheWeek         | Text           | Dny v týdnu pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure |
| Formát diffBackupFormat                | Text           | Formát pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure   |
| Doba trvání diferenciace_programu     | Desetinné číslo | Doba uchovávání informací pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure |
| Doba zálohování                  | Time           | Čas pro rozdílové zálohy pro SQL v zálohování virtuálních počítačích Azure     |
| Frekvence zálohování protokolu              | Desetinné číslo | Frekvence pro zálohy protokolů pro SQL                            |
| Doba trvání_uchovávání_programu      | Desetinné číslo | Doba uchovávání informací pro zálohy protokolů pro SQL v zálohování virtuálních počítačích Azure |
| MěsíčníRetentionDaysOfTheMonth  | Text           | Týdny v měsíci, kdy je nakonfigurováno měsíční uchovávání informací.  Například First, Last, atd. |
| MěsíčníRetentionDaysOfTheWeek   | Text           | Dny v týdnu vybrané pro měsíční uchovávání              |
| Měsíční doba trvání        | Text           | Celková doba uchovávání v měsících pro nakonfigurované zálohy    |
| Měsíční formát retentionu          | Text           | Typ konfigurace pro měsíční uchovávání. Například denně pro den na základě, týdně pro týden na |
| Měsíčnírekonreionční časy           | Text           | Datum a čas, kdy je nakonfigurováno měsíční uchovávání informací           |
| MěsíčníRetenční týdnyměsíce | Text           | Týdny v měsíci, kdy je nakonfigurováno měsíční uchovávání informací.   Například First, Last, atd. |
| PolicyName                      | Text           | Název definované zásady                                   |
| PolicyUniqueId                  | Text           | Jedinečné ID pro identifikaci zásady                             |
| PolicyTimeZone                  | Text           | Časové pásmo, ve kterém jsou v protokolech zadána časová pole zásad |
| Doba uchovávání               | Text           | Doba uchovávání pro nakonfigurované zálohy                    |
| Typ retence                   | Text           | Druh zadržení                                            |
| Verze schématu                   | Text           | Toto pole označuje aktuální verzi schématu, je **to V2** |
| Stav                           | Text           | Aktuální stav objektu zásady. Například Aktivní, Odstraněno |
| SynchronizaceFrequencyPerDay  | Celé číslo   | Počet synchronizací zálohování souborů pro sazí SC DPM a MABS za den |
| VaultUniqueId                   | Text           | Jedinečné ID trezoru, do kterého tato zásada patří          |
| Týdenníretenzivací dnív týdnu    | Text           | Dny v týdnu vybrané pro týdenní uchovávání               |
| Týdenní doba trvání udržení         | Desetinné číslo | Celková týdenní doba uchovávání v týdnech pro nakonfigurované zálohy |
| TýdenníRetenční časy            | Text           | Datum a čas, kdy je nakonfigurováno týdenní uchovávání informací            |
| Roční retenční dnyMěsíce   | Text           | Data měsíce vybraného pro roční uchovávání             |
| Roční retentionDaysOfTheWeek    | Text           | Dny v týdnu vybrané pro roční uchovávání               |
| Roční doba uchovávání         | Desetinné číslo | Celková doba uchovávání v letech pro nakonfigurované zálohy     |
| Roční formát retentionu           | Text           | Typ konfigurace pro roční uchovávání, například denně pro den, týdně pro týden |
| Roční retenční měsíceoftheyear  | Text           | Měsíce v roce vybrané pro roční uchovávání             |
| Roční retenční časy            | Text           | Datum a čas, kdy je nakonfigurováno roční uchovávání informací            |
| Ročníretenční týdnyMěsíce  | Text           | Týdny v měsíci vybrané pro roční uchovávání             |
| SourceSystem                    | Text           | Zdrojový systém aktuálních dat – Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Tato tabulka obsahuje podrobnosti o polích souvisejících s úložištěm.

| **Pole**                      | **Typ dat** | **Popis**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | Identifikátor prostředku pro shromažďovaná data. Například ID prostředku trezoru služby Recovery Services |
| OperationName                  | Text          | Toto pole představuje název aktuální operace - Storage nebo StorageAssociation |
| Kategorie                       | Text          | Toto pole představuje kategorii diagnostických dat zasunutých do protokolů Programu Azure – AddonAzureBackupStorage. |
| BackupItemUniqueId             | Text          | Jedinečné ID použité k identifikaci zálohovací položky pro virtuální počítače zálohované pomocí dpm, MABS |
| BackupManagementServerUniqueId | Text          | Pole pro jednoznačnou identifikaci serveru pro správu zálohování, kterým je položka zálohování chráněna, pokud je to možné |
| BackupManagementType           | Text          | Typ zprostředkovatele pro server, který provádí úlohu zálohování. Například IaaSVM, FileFolder |
| Upřednostňovaný pracovní vytíženíonový svazek      | Text          | Pracovní vytížení, pro které je tento svazek upřednostňovaným úložištěm      |
| ProtectedContainerUniqueId     | Text          | Jedinečný identifikátor chráněného serveru přidruženého k výstraze |
| Verze schématu                  | Text          | Verze schématu. Například **V2**                   |
| Stav                          | Text          | Stav objektu zálohovací položky. Například Aktivní, Odstraněno |
| StorageAllocatedInMBs          | Číslo        | Velikost úložiště přidělená odpovídající položkou zálohy v odpovídajícím úložišti typu Disk |
| StorageConsumedInMbs           | Číslo        | Velikost úložiště spotřebovaného odpovídající položkou zálohy v odpovídajícím úložišti |
| Název_úložiště                    | Text          | Název entity úložiště. Například E:\                      |
| StorageTotalSizeInGBs          | Text          | Celková velikost úložiště v GB spotřebované entitou úložiště     |
| StorageType                    | Text          | Typ úložiště, například Cloud, Svazek, Disk             |
| StorageUniqueId                | Text          | Jedinečné ID použité k identifikaci entity úložiště                |
| VaultUniqueId                  | Text          | Jedinečné ID použité k identifikaci trezoru souvisejícího s entitou úložiště |
| Název_velikosti_svazku             | Text          | Popisný název svazku úložiště                          |
| SourceSystem                   | Text          | Zdrojový systém aktuálních dat – Azure                    |

## <a name="next-steps"></a>Další kroky

- [Přečtěte si, jak odesílat diagnostická data do služby Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Naučte se psát dotazy na tabulky specifické pro prostředky](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)