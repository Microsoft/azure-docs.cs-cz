---
title: Monitorování zálohování Azure pomocí Azure Monitoru
description: Sledujte úlohy Azure Backup a vytvářejte vlastní výstrahy pomocí Azure Monitoru.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459510"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorování ve velkém měřítku pomocí Azure Monitoru

Azure Backup poskytuje [integrované funkce monitorování a upozorňování](backup-azure-monitoring-built-in-monitor.md) v trezoru služby Recovery Services. Tyto funkce jsou k dispozici bez jakékoli další infrastruktury pro správu. Ale tato integrovaná služba je omezena v následujících scénářích:

- Pokud monitorujete data z více trezorů služby Recovery Services napříč předplatnými
- Pokud preferovaný kanál oznámení *není* e-mail
- Pokud uživatelé chtějí výstrahy pro více scénářů
- Pokud chcete zobrazit informace z místní součásti, jako je Správce ochrany dat System Center v Azure, který se na portálu nezobrazuje v [**zálohovacích úlohách**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) nebo [**výstrahách zálohování**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Použití pracovního prostoru Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Vytváření výstrah pomocí analýzy protokolů

Ve službě Azure Monitor můžete vytvářet vlastní výstrahy v pracovním prostoru Analýzy protokolů. V pracovním prostoru použijete *skupiny akcí Azure* k výběru upřednostňovaného mechanismu oznámení.

> [!IMPORTANT]
> Informace o nákladech na vytvoření tohoto dotazu najdete v [tématu Azure Monitor cen](https://azure.microsoft.com/pricing/details/monitor/).

Otevřete část **Protokoly** v pracovním prostoru Log Analytics a vytvořte dotaz pro vlastní protokoly. Když vyberete **nové pravidlo výstrahy**, otevře se stránka vytvoření výstrah azure monitoru, jak je znázorněno na následujícím obrázku.

![Vytvoření výstrahy v pracovním prostoru Analýzy protokolů](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Zde je prostředek již označen jako pracovní prostor Analýzy protokolů a je k dispozici integrace skupiny akcí.

![Stránka vytvoření výstrahy Analýzy protokolů](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Podmínka výstrahy

Definující charakteristikou výstrahy je její spouštěcí podmínka. Vyberte **Podmínka,** chcete-li automaticky načíst dotaz Kusto na stránku **Protokoly,** jak je znázorněno na následujícím obrázku. Zde můžete upravit podmínku tak, aby vyhovovala vašim potřebám. Další informace naleznete v [tématu Ukázka kusto dotazů](#sample-kusto-queries).

![Nastavení stavu výstrahy](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

V případě potřeby můžete upravit dotaz Kusto. Zvolte prahovou hodnotu, tečku a frekvenci. Prahová hodnota určuje, kdy bude výstraha vyvolána. Tečka je časové okno, ve kterém je dotaz spuštěn. Pokud je například prahová hodnota větší než 0, je doba 5 minut a frekvence je 5 minut, spustí pravidlo dotaz každých 5 minut a prověří předchozích 5 minut. Pokud je počet výsledků větší než 0, budete upozorněni prostřednictvím vybrané skupiny akcí.

#### <a name="alert-action-groups"></a>Skupiny akcí výstrah

Pomocí skupiny akcí určete kanál oznámení. Pokud chcete zobrazit dostupné mechanismy oznámení, vyberte v části **Skupiny akcí** **možnost Vytvořit nový**.

![Dostupné mechanismy oznámení v okně "Přidat skupinu akcí"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Můžete splnit všechny požadavky na výstrahy a monitorování z Log Analytics sám, nebo můžete použít Log Analytics doplnit integrované oznámení.

Další informace najdete [v tématu Vytváření, zobrazení a správa výstrah protokolu pomocí Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a Vytváření a správa skupin akcí na webu Azure [Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Výchozí grafy poskytují kusto dotazy pro základní scénáře, na kterých můžete vytvářet výstrahy. Můžete také upravit dotazy získat data, na která chcete být upozorněni. Na stránku **Protokoly** vložte následující ukázkové dotazy Kusto a pak vytvořte výstrahy na dotazy:

- Všechny úspěšné úlohy zálohování

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Všechny neúspěšné úlohy zálohování

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Všechny úspěšné úlohy zálohování virtuálních počítačových virtuálních počítačů Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Všechny úspěšné úlohy zálohování protokolu SQL

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Všechny úspěšné úlohy agenta Azure Backup

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Úložiště záloh spotřebované na položku zálohy

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Četnost aktualizací diagnostických dat

Diagnostická data z trezoru jsou čerpána do pracovního prostoru Log Analytics s určitým zpožděním. Každá událost dorazí do pracovního prostoru Log Analytics *20 až 30 minut* po jeho vytlačení z trezoru služby Recovery Services. Zde jsou další podrobnosti o zpoždění:

- Ve všech řešeních jsou integrované výstrahy služby zálohování nabízeny ihned po jejich vytvoření. Takže se obvykle zobrazí v pracovním prostoru Log Analytics po 20 až 30 minutách.
- Ve všech řešeních jsou úlohy zálohování na vyžádání a úlohy obnovení posunuty, jakmile budou *dokončeny*.
- Pro všechna řešení kromě zálohování SQL jsou naplánované úlohy zálohování posunuty, jakmile *skončí*.
- Pro zálohování SQL, protože zálohy protokolu může dojít každých 15 minut, informace pro všechny dokončené naplánované úlohy zálohování, včetně protokolů, je dávkově a nabízeny každých 6 hodin.
- Ve všech řešeních jsou další informace, jako je položka zálohování, zásady, body obnovení, úložiště a tak dále, posunuty alespoň *jednou denně.*
- Změna konfigurace zálohování (například změna zásad nebo zásad úprav) spustí nabízení všech souvisejících informací o zálohování.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Použití protokolů aktivit trezoru služby Recovery Services

> [!CAUTION]
> Následující kroky platí jenom pro *zálohy virtuálních počítačů Azure.* Tyto kroky nelze použít pro řešení, jako je agent Azure Backup, zálohování SQL v rámci Azure nebo Soubory Azure.

Protokoly aktivit můžete také použít k získání oznámení o událostech, jako je například úspěch zálohování. Chcete-li začít, postupujte takto:

1. Přihlaste se k portálu Azure Portal.
1. Otevřete příslušný trezor služby Recovery Services.
1. Ve vlastnostech úschovny otevřete oddíl **Protokol aktivit.**

Chcete-li identifikovat příslušný protokol a vytvořit výstrahu:

1. Pomocí filtrů zobrazených na následujícím obrázku ověřte, zda přijímáte protokoly aktivit pro úspěšné zálohování. Podle potřeby změňte hodnotu **Časový rozsah** pro zobrazení záznamů.

   ![Filtrování pro vyhledání protokolů aktivit pro zálohy virtuálních aplikací Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Vyberte název operace, chcete-li zobrazit příslušné podrobnosti.
1. Výběrem **nového pravidla výstrahy** otevřete stránku **Vytvořit pravidlo.**
1. Vytvořte výstrahu podle kroků v [části Vytvoření, zobrazení a správa výstrah protokolu aktivit pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nové pravidlo výstrahy](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Zde je zdrojem samotný trezor služby Recovery Services. Opakujte stejné kroky pro všechny úschovny, ve kterých chcete být upozorňováni prostřednictvím protokolů aktivit. Podmínka nebude mít prahovou hodnotu, tečku nebo frekvenci, protože tato výstraha je založena na událostech. Jakmile je vygenerován příslušný protokol aktivit, je vyvolána výstraha.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Použití analýzy protokolů ke sledování ve velkém měřítku

Všechny výstrahy vytvořené z protokolů aktivit a pracovních prostorů Analýzy protokolů můžete zobrazit ve službě Azure Monitor. Stačí otevřít podokno **Výstrahy** vlevo.

I když můžete dostávat oznámení prostřednictvím protokolů aktivit, důrazně doporučujeme používat Log Analytics spíše než protokoly aktivit pro monitorování ve velkém měřítku. Důvod:

- **Omezené scénáře**: Oznámení prostřednictvím protokolů aktivit platí jenom pro zálohy virtuálních počítačích Azure. Oznámení musí být nastavena pro každý trezor služby Recovery Services.
- **Přizpůsobení definice**: Naplánovaná aktivita zálohování neodpovídá nejnovější definici protokolů aktivit. Místo toho je zarovnán s [protokoly prostředků](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace). Toto zarovnání způsobí neočekávané účinky při změně dat, která procházejí kanálem protokolu aktivit.
- **Problémy s kanálem protokolu aktivit**: V trezorech služby Recovery Services následují protokoly aktivit čerpané z azure backupu podle nového modelu. Tato změna bohužel ovlivňuje generování protokolů aktivit v Azure Government, Azure Germany a Azure China 21Vianet. Pokud uživatelé těchto cloudových služeb vytvořit nebo nakonfigurovat žádné výstrahy z protokolů aktivit v Azure Monitor, výstrahy se neaktivují. Také ve všech veřejných oblastech Azure, pokud uživatel [shromažďuje protokoly aktivit služby recovery Services do pracovního prostoru Analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), tyto protokoly se nezobrazí.

Pracovní prostor Log Analytics slouží k monitorování a upozorňování ve velkém měřítku pro všechny vaše úlohy, které jsou chráněné službou Azure Backup.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit vlastní dotazy, přečtěte [si informace o datovém modelu Analýzy protokolů](backup-azure-reports-data-model.md).
