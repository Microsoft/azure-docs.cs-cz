---
title: 'Azure Backup: Monitorování Azure Backup pomocí Azure monitoru'
description: Monitorování úloh Azure Backup a vytvářet vlastní výstrahy pomocí Azure monitoru.
services: backup
author: pvrk
manager: shivamg
keywords: Log Analytics; Azure Backup; Výstrahy; Nastavení diagnostiky; Skupiny akcí
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786314"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorování ve velkém měřítku pomocí Azure monitoru

Azure Backup poskytuje [integrované monitorování a upozorňování](backup-azure-monitoring-built-in-monitor.md) v trezoru služby Recovery Services. Tyto možnosti jsou k dispozici bez jakékoli další správu infrastruktury. Ale této integrované služby je omezený v následujících scénářích:

- Pokud monitorování dat z více trezorů služby Recovery Services napříč předplatnými
- Pokud je kanál oznámení upřednostňované *není* e-mailu
- Pokud uživatelé chtějí upozornění pro další scénáře
- Pokud chcete zobrazit informace z komponentu v místním, jako je System Center Data Protection Manager v Azure, které na portálu se nezobrazí v [ **úlohy zálohování** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) nebo [  **Výstrahy záloh**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Používání pracovního prostoru Log Analytics

> [!NOTE]
> Do pracovního prostoru Log Analytics přes nastavení diagnostiky se přijímat data ze záloh virtuálních počítačů Azure, Azure Backup agent, System Center Data Protection Manager, zálohování SQL na virtuálních počítačích Azure a zálohování sdílené složky Azure Files. 

Pokud chcete sledovat ve velkém měřítku, potřebujete možnosti ze dvou služeb Azure. *Nastavení diagnostiky* Neodesílat data z několika prostředků Azure Resource Manageru do jiného prostředku. *Log Analytics* vygeneruje vlastní upozornění, kde můžete použít skupiny akcí k definování dalších kanálů oznámení. 

Následující části podrobně popisují, jak používat službu Log Analytics pro monitorování Azure Backup ve velkém měřítku.

### <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Prostředky Azure Resource Manageru, jako je například trezor služby Recovery Services zaznamenávejte informace o tom uživatel aktivuje operace a naplánované operace jako diagnostická data. 

V části monitorování zvolte **nastavení diagnostiky** a určete cíl pro trezor služby Recovery Services diagnostická data.

![Nastavení diagnostiky trezoru služby Recovery Services, cílení na Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Můžete cílit pracovnímu prostoru Log Analytics z jiného předplatného. K monitorování trezory napříč předplatnými na jednom místě, vyberte stejný pracovní prostor Log Analytics pro více trezorů služby Recovery Services. Na webu channel všechny informace, které souvisí s Azure Backup do pracovního prostoru Log Analytics, vyberte **AzureBackupReport** jako protokolu.

> [!IMPORTANT]
> Po dokončení konfigurace byste měli počkat po dobu 24 hodin počáteční datová oznámení na dokončení. Po počáteční, který datová oznámení, všechny události se nasdílejí, jak je popsáno dále v tomto článku v [části četnost](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Nasazení řešení do pracovního prostoru Log Analytics

Po data jsou v pracovním prostoru Log Analytics [nasazení šablony Githubu](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) ke službě Log Analytics, která bude vizualizovat data. Správně identifikovat pracovní prostor, ujistěte se, že jí přiřadit stejnou skupinu prostředků, název pracovního prostoru a umístění pracovního prostoru. Nainstalujte tuto šablonu v pracovním prostoru.

> [!NOTE]
> Pokud nemáte upozornění, úlohy zálohování nebo obnovení úlohy ve vašem pracovním prostoru Log Analytics, může se zobrazit "BadArgumentError" kód chyby: na portálu. Tuto chybu ignorovat a pokračovat v používání řešení. Po odpovídající datový typ spuštění toku do pracovního prostoru, vizualizace bude odrážet stejné a už se nebude zobrazí chyba.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Zobrazit Azure zálohování – uložená data pomocí Log Analytics

Po nasazení šablony řešení pro monitorování Azure Backup se zobrazí v oblasti souhrnu pracovního prostoru. Přejít na souhrn, proveďte jeden z těchto cest:

- **Azure Monitor**: V **Insights** vyberte **Další** a potom vyberte příslušné pracovní prostor.
- **Pracovní prostory log Analytics**: Vyberte relevantní pracovní prostor a potom v části **Obecné**vyberte **shrnutí pracovního prostoru**.

![Dlaždice monitorování Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Při výběru dlaždice monitorování návrháře šablony otevře řady grafů týkající se základního monitorování dat z Azure Backup. Tady jsou některé z grafů, které se zobrazí:

* Všechny úlohy zálohování

   ![Log Analytics grafů pro úlohy zálohování](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Úlohy obnovení

   ![Graf analýzy protokolů pro úlohy obnovení](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Předdefinovaná upozornění Azure Backup pro prostředky Azure

   ![Log Analytics grafu pro předdefinované výstrahy Azure Backup pro prostředky Azure](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* Předdefinované výstrahy zálohování Azure pro místní prostředky

   ![Log Analytics grafu pro předdefinované výstrahy zálohování Azure pro místní prostředky](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Aktivní zdroje dat.

   ![Graf analýzy protokolů pro aktivní zálohovanou entity](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Cloudové úložiště pro trezor služby Recovery Services

   ![Graf analýzy protokolů pro cloudové úložiště trezor služby Recovery Services](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Tyto grafy jsou poskytovány s šablonou. Můžete upravit v grafech nebo pokud potřebujete přidat další grafy.

> [!IMPORTANT]
> Při nasazení šablony, kterou vytváříte v podstatě zámek jen pro čtení. Upravit a uložit šablonu, budete muset odebrat zámek. Odebrat zámek v **nastavení** části pracovní prostor Log Analytics na **uzamkne** podokně.

### <a name="create-alerts-by-using-log-analytics"></a>Vytvoření výstrah pomocí Log Analytics

Ve službě Azure Monitor můžete vytvořit vlastní výstrahy v pracovním prostoru Log Analytics. V pracovním prostoru, použijte *skupiny akcí Azure* vyberte váš mechanismus upřednostňované oznámení. 

> [!IMPORTANT]
> Informace na náklady na vytvoření tohoto dotazu naleznete v tématu [ceny Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Vyberte libovolné grafy, otevřete **protokoly** části pracovní prostor Log Analytics. V **protokoly** části, upravte dotazy a vytvářet upozornění na ně.

![Vytvořit upozornění v pracovním prostoru Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Když vyberete **nové pravidlo upozornění**, otevře stránka vytvoření výstrahy Azure Monitor, jak je znázorněno na následujícím obrázku. Zde je prostředek již označen jako pracovní prostor Log Analytics a je k dispozici integrace skupiny akcí.

![Na stránce vytváření upozornění Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Podmínku upozornění

Charakteristickým výstrahy je jeho spouštěcí podmínkou. Vyberte **podmínku** automaticky načíst dotaz Kusto na **protokoly** stránce, jak je znázorněno na následujícím obrázku. Tady můžete upravit podmínku tak, aby odpovídala vašim potřebám. Další informace najdete v tématu [Kusto ukázkové dotazy](#sample-kusto-queries).

![Nastavení podmínku upozornění](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

V případě potřeby můžete upravit dotaz Kusto. Zvolte prahovou hodnotu, tečka a četnost. Prahová hodnota určuje, kdy bude vyvolána výstraha. Období je okno čas spuštění dotazu. Například pokud je prahová hodnota větší než 0, období je 5 minut a četnost je 5 minut, pak toto pravidlo spustí dotaz každých 5 minut, kontrola předchozí 5 minut. Pokud počet výsledků, které je větší než 0, zobrazí se oznámení prostřednictvím skupiny vybrané akce.

#### <a name="alert-action-groups"></a>Akce upozornění skupiny

Pomocí skupiny akcí můžete určit kanál oznámení. V části zobrazíte mechanismy oznámení o dostupnosti **skupiny akcí**vyberte **vytvořit nový**.

![Oznámení o dostupnosti mechanismů v okně "Přidat skupinu akcí"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Splňujete všechny výstrahy a monitorování požadavky od Log Analytics, samostatně nebo Log Analytics můžete použít k doplnění integrovaná oznámení.

Další informace najdete v tématu [vytvoření, zobrazení a Správa upozornění protokolů pomocí Azure monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a [vytvořit a spravovat skupiny akcí na webu Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Výchozích grafů získáte Kusto dotazy pro základní scénáře, ve které můžete vytvářet výstrahy. Můžete také upravit dotazy k získání dat, že který chcete být upozorněni na. Vložte následující ukázkové dotazy Kusto v **protokoly** stránce a pak vytvořte upozornění na dotazy:

* Všechny úspěšné úlohy zálohování

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Všechny neúspěšné úlohy zálohování

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Všechny úspěšné úlohy zálohování virtuálních počítačů Azure

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

* Všechny úspěšné protokolu zálohování úloh SQL

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

* Všechny úspěšné úlohy agenta Azure Backup

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

Diagnostická data z trezoru je přijímat do pracovního prostoru Log Analytics s některých zpoždění. Každé události dorazí na pracovní prostor Log Analytics *20 až 30 minut* po odesílána z trezoru služby Recovery Services. Tady jsou další podrobnosti o je zpoždění:

- Ve všech řešení jsou vloženy předdefinované výstrahy služby zálohování ihned po jejich vytvoření. Proto se obvykle zobrazují v pracovním prostoru Log Analytics po 20 až 30 minut.
- Ve všech řešení, ad hoc úlohy zálohování a obnovení úlohy se nasdílejí co nejdříve, jakmile *Dokončit*.
- U všech řešení s výjimkou zálohování SQL, naplánované úlohy zálohování se nasdílejí co nejdříve, jakmile *Dokončit*.
- Pro zálohování SQL protože zálohy protokolu každých 15 minut, může dojít, informace o všech dokončených naplánovaných úloh zálohování, včetně protokolů, je dávce a vloženo každých 6 hodin.
- Ve všech řešení, další informace, jako je zálohovaná položka, zásada, body obnovení, úložiště a tak dále se vloží alespoň *jednou za den.*
- Změna v konfiguraci zálohování (jako je například změna zásad nebo úprava zásad) spustí zablokovaly všechny související informace o zálohování.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Pomocí trezoru služby Recovery Services protokolů aktivit

> [!CAUTION]
> Následující postup se vztahuje pouze na *záloh virtuálních počítačů Azure.* Nelze použít tyto kroky pro řešení, jako je Azure Backup agent, záloh SQL v Azure nebo Azure Files.

Protokoly aktivit můžete také pro události, například úspěšnosti zálohování, které dostanete oznámení. Pokud chcete začít, postupujte podle těchto kroků:

1. Přihlaste se na web Azure Portal.
1. Otevřete příslušné trezoru služby Recovery Services. 
1. Ve vlastnostech v trezoru, otevřete **protokolu aktivit** oddílu.

K identifikaci příslušný protokol a vytvořte výstrahu:

1. Ověřte, že jste dostali protokolů aktivit pro zálohování bude probíhat úspěšně, použití filtrů je znázorněno na následujícím obrázku. Změnit **Timespan** hodnoty podle potřeby zobrazit záznamy.

   ![Filtrování protokolů aktivit pro zálohy virtuálních počítačů Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Vyberte název operace zobrazíte relevantní podrobnosti.
1. Vyberte **nové pravidlo upozornění** otevřít **vytvořit pravidlo** stránky. 
1. Vytvořit upozornění podle postupu v [vytvoření, zobrazení a Správa upozornění protokolu aktivit s využitím Azure monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nové pravidlo upozornění](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Zde je prostředek samotný trezor služby Recovery Services. U všech trezorů, ve kterých chcete být upozorněni v protokolech aktivit musí opakujte stejný postup. Podmínka nebudou mít prahovou hodnotu, tečkou ani frekvence, protože tato výstraha se zakládá na událostech. Poté, co se vygeneruje protokol aktivit relevantní, je vyvolána výstraha.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Používat službu Log Analytics k monitorování ve velkém měřítku

Můžete zobrazit všechny výstrahy vytvořené z protokolů aktivit a pracovní prostory Log Analytics ve službě Azure Monitor. Stačí otevřít **výstrahy** podokna na levé straně.

I když můžete dostávat oznámení v protokolech aktivit, důrazně doporučujeme použití Log Analytics, spíše než protokoly aktivit monitorování ve velkém měřítku. Tady jsou důvody:

- **Omezené scénáře**: Oznámení v protokolech aktivit se vztahují pouze na záloh virtuálních počítačů Azure. Oznámení musíte nastavit pro každý trezor služby Recovery Services.
- **Podle definice**: Naplánované zálohování aktivity nevejde se nejnovější definice protokolů aktivit. Místo toho souladu s [diagnostické protokoly](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Toto uspořádání způsobí, že neočekávané účinky data, která prochází přes aktivity přihlášení kanálu změn.
- **Problémy s kanálem protokolu aktivit**: Protokoly aktivit, které budou přijímat z Azure Backup v trezorech služby Recovery Services, postupujte podle nového modelu. Tato změna ovlivní bohužel generování protokoly aktivit v Azure Government, Azure Germany a Azure China 21Vianet. Pokud uživatelé z těchto cloudových služeb vytvořit nebo konfigurovat žádné výstrahy na základě protokolů aktivit ve službě Azure Monitor, nejsou aktivované výstrahy. Ve všech veřejných oblastech Azure, pokud uživatel [do pracovního prostoru Log Analytics shromažďuje protokoly aktivit služby Recovery Services](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), tyto protokoly se nezobrazí.

Použití pracovního prostoru Log Analytics pro monitorování a upozorňování v různém rozsahu pro všechny úlohy, které jsou chráněné službou Azure Backup.

## <a name="next-steps"></a>Další postup

Vytvoření vlastních dotazů najdete v tématu [datový model Log Analytics](backup-azure-log-analytics-data-model.md).
