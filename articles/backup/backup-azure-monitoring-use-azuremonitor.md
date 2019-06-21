---
title: 'Azure Backup: Monitorování Azure Backup pomocí Azure monitoru'
description: Monitorování úloh Azure Backup a vytvářet vlastní výstrahy pomocí Azure monitoru
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; Výstrahy; Nastavení diagnostiky; Skupiny akcí
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 7c53d8fe0ee5bbfdbe180aa4d18d8c7b7fab29c2
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295297"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Monitorování ve velkém měřítku pomocí Azure monitoru

[Integrované monitorování a upozorňování článku](backup-azure-monitoring-built-in-monitor.md) uvedené monitorování a upozorňování v jeden trezor služby Recovery services a která nabízí se bez jakékoli další správu infrastruktury. Integrovaná služba je ale omezený v následujících scénářích.

- Monitorování dat z více trezorů RS napříč předplatnými
- Pokud e-mailu není upřednostňovaný oznámení kanálu
- Pokud uživatelé chtějí upozorněni a pro další scénáře
- Pokud chcete zobrazit informace z místní komponenty, jako je System Center DPM (SC-DPM) v Azure, který není uveden v [úlohy zálohování](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) nebo [výstrahy zálohování](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) portálu.

## <a name="using-log-analytics-workspace"></a>Použití pracovního prostoru Log Analytics

> [!NOTE]
> Do pracovního prostoru Log Analytics přes nastavení diagnostiky se právě přijímat data ze záloh virtuálních počítačů Azure, MAB Agent, System Center DPM (SC-DPM), zálohování SQL na virtuálních počítačích Azure a zálohování sdílených složek Azure. Podpora pro Microsoft Azure Backup Server (MABS) je již brzy.

Můžeme se využívá k tomu možnosti ze dvou služeb Azure – **nastavení diagnostiky** (k odesílání dat z několika prostředků Azure Resource Manageru k jinému zdroji) a **Log Analytics** (LA - ke generování vlastní upozornění, ve kterém můžete definovat další kanály oznámení pomocí skupiny akcí) pro monitorování ve velkém měřítku. Následující části podrobně popisují, jak používat LA pro monitorování Azure Backup ve velkém měřítku.

### <a name="configuring-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Prostředek Azure Resource Manageru, jako je Azure Recovery services vault zaznamenává všechny možné informace o naplánované operace a uživatel aktivuje operace jako diagnostická data. Klikněte na "nastavení diagnostiky" v části monitorování a určete cíl pro diagnostická data z trezoru RS.

![Trezor RS nastavení diagnostiky s LA jako cíl](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Pracovní prostor služby LA můžete vybrat z jiného předplatného jako cíl. *Výběrem stejného pracovního prostoru LA pro více trezorů RS můžete monitorovat trezory napříč předplatnými na jednom místě.* Vyberte "AzureBackupReport" jako protokolu kanálu všechny informace do pracovního prostoru LA související s Azure Backup.

> [!IMPORTANT]
> Po dokončení konfigurace čekat na dokončení počáteční datová oznámení po dobu 24 hodin. Po tomto datu všechny události se nasdílejí, jak je uvedeno v [části četnost](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Nasazení řešení do pracovního prostoru Log Analytics

Jakmile jsou data v prostoru LA [nasazení šablony Githubu](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) na LA k vizualizaci dat. Ujistěte se, že vám poskytnou stejné skupiny prostředků, název pracovního prostoru a umístění pracovního prostoru správně identifikovat pracovní prostor a pak na něj nainstalovat tuto šablonu.

> [!NOTE]
> Uživatelé, kteří nemají upozornění nebo úlohy zálohování a obnovení v jejich pracovním prostoru LA může zobrazit chyba s kódem "BadArgumentError" na portálu. Uživatelé mohou tuto chybu ignorovat a pokračovat v používání řešení. Po spuštění odpovídající typ dat odesílaných do pracovního prostoru vizualizace, bude odrážet že stejné a uživatelé neuvidí tato chyba už.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Zobrazení dat Azure Backup pomocí Log Analytics (LA)

Po nasazení šablony řešení pro monitorování Azure Backup se zobrazí v oblasti souhrnu pracovního prostoru. Můžete procházet přes

- Azure Monitor -> "Informace" v části "Přehled" a vyberte odpovídající pracovní prostor nebo
- Pracovní prostory log Analytics -> vyberte prostoru souhrnné části Obecné -> relevantní pracovní prostor.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Po kliknutí na dlaždici na návrháře šablona se otevře několik grafů týkající se základního monitorování dat z Azure Backup, jako

#### <a name="all-backup-jobs"></a>Všechny úlohy zálohování

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Úlohy obnovení

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Integrované upozornění Azure Backup pro prostředky Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Integrované upozornění Azure Backup k místním prostředkům

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Aktivní zdroje dat

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS Vault Cloud Storage

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Výše uvedené grafy jsou poskytovány se šablonou a zákazník je můžete upravit nebo přidat další grafy.

> [!IMPORTANT]
> Při nasazování šablony v podstatě vytvoří zámek jen pro čtení a je třeba odebrat ji upravit šablonu a uložit. Odebrat zámky, podívejte se v podokně "Zámky" pracovního prostoru Log Analytics v části "Nastavení".

### <a name="create-alerts-using-log-analytics"></a>Vytvoření výstrah pomocí Log Analytics

Azure Monitor umožňuje uživatelům vytvářet své vlastní oznámení z pracovního prostoru LA, kde můžete *využití skupin akcí Azure a vyberte váš mechanismus upřednostňované oznámení*. Kliknutím na některé z výše uvedené části "Protokoly" pracovního prostoru LA otevřít grafy ***ve kterém můžete upravit dotazy a vytvářet výstrahy nad nich***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Kliknutím na "Nové pravidlo výstrah" jak je znázorněno výše se otevře obrazovka vytvoření výstrahy Azure Monitor.

Jak si můžete níže, prostředek je již označen jako pracovního prostoru LA a integraci skupin akce je k dispozici.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Mějte prosím na paměti, že je k dispozici odpovídající dopad vytvoření tohoto dotazu [tady](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Podmínku upozornění

Klíčovým aspektem je spouštěcí podmínka výstrahy. Kliknutím na "Podmínku" se automaticky načtou dotaz Kusto na obrazovce 'Logs' jak je znázorněno níže, a můžete upravit tak, aby odpovídal vašemu scénáři. Několik ukázkových dotazů Kusto jsou součástí [níže v části](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

V případě potřeby upravit dotaz Kusto, vyberte správné prahovou hodnotu (která se rozhodnout, kdy bude vyvoláno upozornění), správné období (časový interval pro niž je proveden dotaz) a četnost. Příklad: Pokud je prahová hodnota větší než 0, doba je 5 minut a četnost je 5 minut, pak toto pravidlo je přeložen jako "Spustit dotaz pro posledních 5 minut každých 5 minut a pokud počet výsledků, které je větší než 0, upozornit mě zprávou skupině vybranou akci"

#### <a name="action-group-integration"></a>Integrace skupiny akcí

Skupiny akcí zadejte kanály oznámení, která je k dispozici pro uživatele. Kliknutím na "Vytvořit nový" v části "Skupiny akcí" část zobrazuje seznam dostupných mechanismy oznámení.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Další informace o [oznámení z pracovního prostoru LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a asi [skupiny akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) dokumentaci ke službě Azure Monitor.

Proto můžete splňovat všechny výstrahy a monitorování požadavky z LA samostatně nebo ho použít jako doplňkové techniku, která umožňuje oznámení integrované mechanismy.

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Výchozí grafy by vám poskytlo Kusto dotazy pro základní scénáře, ve které můžete vytvářet výstrahy. Můžete také upravit, abyste získali data, která chcete na problém upozorněni. Tady vám nabízíme několik ukázkových dotazů Kusto, které můžete vložit v okně "Protokoly" a pak vytvořte výstrahu na dotazu.

#### <a name="all-successful-backup-jobs"></a>Všechny úspěšné úlohy zálohování

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Všechny neúspěšné úlohy zálohování

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Všechny úspěšné úlohy zálohování virtuálních počítačů Azure

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

#### <a name="all-successful-sql-log-backup-jobs"></a>Všechny úspěšné úlohy zálohování protokolu SQL

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

#### <a name="all-successful-mab-agent-backup-jobs"></a>Všechny úspěšné úlohy zálohování agenta MAB

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

### <a name="diagnostic-data-update-frequency"></a>Četnost aktualizace diagnostických dat

Diagnostická data z trezoru je přijímat do pracovního prostoru LA s některých zpoždění. Každé události dorazí do pracovního prostoru LA ***s prodlení 20 – 30 minut po odesílána z trezoru RS.***

- Předdefinované výstrahy služby backup (v rámci všech řešení) se nasdílejí ihned po jejich vytvoření. Což znamená, že jsou obvykle zobrazena v pracovním prostoru LA po prodlení 20 – 30 minut.
- Úlohy zálohování ad hoc a obnovení úloh (v rámci všech řešení) se nasdílejí co nejdříve, jakmile ***jsou dokončeny***.
- Naplánované úlohy zálohování z všechna řešení (s výjimkou SQL backup), jakmile brzy vložení ***jsou dokončeny***.
- Pro zálohování SQL protože budeme mít zálohy protokolu každých 15 minut, pro všechny dokončené naplánovaných úloh zálohování, včetně protokolů, informace se dávce a vloženo každých 6 hodin.
- Všechny informace, jako je například zálohovaná položka, zásada, body obnovení, úložiště atd. ve všech řešení se nasdílejí **alespoň jednou za den.**
- Změna v konfiguraci zálohování, jako je například změna úpravy zásad atd. zásad spustí zablokovaly všechny související informace o zálohování.

## <a name="using-rs-vaults-activity-logs"></a>Pomocí trezoru RS protokoly aktivit

Protokoly aktivit můžete také pro události, například úspěšnosti zálohování, které dostanete oznámení.

> [!CAUTION]
> **Upozorňujeme, že to platí pouze pro zálohy virtuálních počítačů Azure.** Toto nelze použít pro ostatní řešení, jako je Azure Backup Agent záloh SQL v Azure, Azure souborů atd.

### <a name="sign-in-into-azure-portal"></a>Přihlásit k webu Azure portal

Přihlaste se na web Azure Portal a přejít k příslušné trezor služeb zotavení Azure a klikněte na část "Protokol aktivit" ve vlastnostech.

### <a name="identify-appropriate-log-and-create-alert"></a>Určit příslušný protokol a vytvořit upozornění

Použijte filtry, které je znázorněno na následujícím obrázku ověřit, jestli jste obdrželi, protokoly aktivit pro úspěšné zálohování. Chcete-li zobrazit záznamy časový interval odpovídajícím způsobem měnit.

![Protokoly aktivit pro zálohy virtuálních počítačů Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Klikněte na název operace se zobrazí, operace a související podrobnosti o.

![Nové pravidlo upozornění](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Klikněte na tlačítko **nové pravidlo upozornění** otevřít **vytvořit pravidlo** obrazovky, můžete tady vytvořit upozornění, pomocí kroků popsaných v tomto [článku](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

Zde je prostředek samotný trezor služby Recovery Services a proto je nutné opakovat stejnou akci pro všechny tyto trezory, ve kterých chcete oznámení prostřednictvím protokolů aktivit. Podmínka nebude mít žádné prahové hodnoty, období, frekvence vzhledem k tomu, že toto je upozornění založeného na událostech. Poté, co se vygeneruje protokol aktivit relevantní, je vyvolána výstraha.

## <a name="recommendation"></a>Doporučení

***Všechny výstrahy vytvořené na základě protokolů aktivit a pracovní prostory LA lze zobrazit ve službě Azure Monitor v podokně "Upozornění" vlevo.***

Oznámení přes protokoly aktivit můžete využít, ***služby Azure Backup se důrazně doporučuje použít LA pro monitorování na škálování a ne protokolů aktivit z následujících důvodů***.

- **Omezeném počtu scénářů:** Vztahuje se jenom zálohy virtuálních počítačů Azure a je potřeba zopakovat pro každý trezor RS.
- **Podle definice:** Naplánované zálohování aktivity nevejde se nejnovější definice protokolů aktivit a v souladu s [diagnostické protokoly](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Tato vést k neočekávaným dopad při změně dat – čerpání přes kanál protokolu aktivit, jak níže.
- **Problémy s kanálem protokolu činnosti:** Jsme přesunuli na nový model čerpání protokoly aktivit z Azure Backup na trezory služby Recovery Services. Přesunutí bohužel má vliv na generování protokolů aktivit v suverénních Cloudech Azure. Pokud uživatelé suverénní Cloud Azure vytvořili/nakonfigurované všechny výstrahy na základě protokolů aktivit prostřednictvím služby Azure Monitor, nebylo aktivované. Ve všech veřejných oblastech Azure, pokud uživatel je shromažďování protokolů aktivit služby obnovení do pracovního prostoru analýzy protokolů jak už bylo zmíněno [tady](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), neobjevila by se také tyto protokoly.

Proto se důrazně doporučujeme používat pracovní prostor analýzy protokolů pro monitorování a upozorňování v různém rozsahu pro všechny Azure Backup chráněných pracovních vytížení.

## <a name="next-steps"></a>Další postup

- Odkazovat na [datový model Log analytics](backup-azure-log-analytics-data-model.md) k vytvoření vlastních dotazů.
