---
title: Monitorování Azure Backup s využitím Azure Monitor
description: Monitorujte Azure Backup úlohy a vytvářejte vlastní výstrahy pomocí Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580743"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorování ve velkém měřítku pomocí Azure Monitor

Azure Backup poskytuje [integrované funkce monitorování a upozorňování](backup-azure-monitoring-built-in-monitor.md) v trezoru Recovery Services. Tyto možnosti jsou k dispozici bez jakékoli další infrastruktury pro správu. Tato integrovaná služba je však omezená v následujících situacích:

- Pokud budete monitorovat data z několika trezorů Recovery Services napříč předplatnými
- Pokud preferovaný kanál oznámení není *e-* mailem
- Pokud uživatelé chtějí výstrahy pro více scénářů
- Pokud chcete zobrazit informace z místní komponenty, jako je například System Center Data Protection Manager v Azure, který portál není zobrazený v [**úlohách zálohování**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) nebo [**výstrahách zálohování**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Použití Log Analyticsho pracovního prostoru

### <a name="create-alerts-by-using-log-analytics"></a>Vytváření výstrah pomocí Log Analytics

V Azure Monitor můžete vytvořit vlastní výstrahy v pracovním prostoru Log Analytics. V pracovním prostoru použijete *skupiny akcí Azure* k výběru upřednostňovaného mechanismu oznámení.

> [!IMPORTANT]
> Informace o nákladech na vytvoření tohoto dotazu najdete v tématu [Azure monitor Price](https://azure.microsoft.com/pricing/details/monitor/).

Otevřete část **protokoly** v pracovním prostoru Log Analytics a vytvořte dotaz pro vlastní protokoly. Když vyberete **nové pravidlo výstrahy**, otevře se stránka Azure monitor pro vytvoření výstrahy, jak je znázorněno na následujícím obrázku.

![Vytvoření výstrahy v pracovním prostoru Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Tento prostředek je už označený jako pracovní prostor Log Analytics a poskytuje se integrace skupiny akcí.

![Stránka pro vytvoření výstrahy Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Podmínka upozornění

Charakteristickou charakteristikou výstrahy je stav triggeru. Vyberte **podmínku** , která automaticky načte dotaz Kusto na stránce **protokoly** , jak je znázorněno na následujícím obrázku. Tady můžete podmínku upravit tak, aby vyhovovala vašim potřebám. Další informace najdete v tématu [Ukázky dotazů Kusto](#sample-kusto-queries).

![Nastavení podmínky upozornění](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

V případě potřeby můžete upravit dotaz Kusto. Výběr prahové hodnoty, tečky a frekvence. Prahová hodnota určuje, kdy bude vyvolána výstraha. Období je okno času, ve kterém se dotaz spustí. Pokud je například prahová hodnota větší než 0, období je 5 minut a frekvence je 5 minut, potom pravidlo spustí dotaz každých 5 minut a zkontroluje předchozí 5 minut. Pokud je počet výsledků větší než 0, budete upozorněni na vybranou skupinu akcí.

> [!NOTE]
> Pokud chcete pravidlo výstrahy spustit jednou denně, ve všech událostech nebo protokolech, které byly vytvořené v daném dni, změňte hodnotu Perioda a počet_plateb na 1440, tj. 24 hodin.

#### <a name="alert-action-groups"></a>Skupiny akcí výstrah

Pro zadání kanálu oznámení použijte skupinu akcí. Chcete-li zobrazit dostupné mechanismy oznámení, vyberte v části **skupiny akcí** možnost **vytvořit nový**.

![Dostupné mechanismy oznámení v okně Přidat skupinu akcí](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Požadavky na výstrahy a monitorování můžete vyhovět pouze Log Analytics, nebo můžete pomocí Log Analytics doplnit Vestavěná oznámení.

Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](../azure-monitor/alerts/alerts-log.md) a [vytváření a Správa skupin akcí v Azure Portal](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Výchozí grafy vám umožní Kusto dotazy na základní scénáře, ve kterých můžete vytvářet výstrahy. Můžete také upravit dotazy a získat tak data, která chcete upozornit. Na stránce **protokoly** vložte následující vzorové dotazy Kusto a pak vytvořte výstrahy na dotazech:

- Všechny úspěšné úlohy zálohování

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Všechny neúspěšné úlohy zálohování

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Všechny úspěšné úlohy zálohování virtuálních počítačů Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- Všechny úspěšné Azure Backup úlohy agenta

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- Využité úložiště záloh za zálohovanou položku

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

### <a name="diagnostic-data-update-frequency"></a>Frekvence aktualizace diagnostických dat

Diagnostická data z trezoru se do Log Analyticsho pracovního prostoru napumpa s určitou prodlevou. Každá událost se doručí na Log Analytics pracovní prostor od *20 do 30 minut* od jeho vložení z trezoru Recovery Services. Tady jsou další podrobnosti o prodlevě:

- V rámci všech řešení se integrované výstrahy služby Backup odešlou hned po jejich vytvoření. Proto se obvykle zobrazují v pracovním prostoru Log Analytics po 20 až 30 minutách.
- V rámci všech řešení se úlohy zálohování na vyžádání a úlohy obnovení odešlou hned po *dokončení*.
- Pro všechna řešení kromě zálohování SQL se naplánované úlohy zálohování odešlou hned po *dokončení*.
- U služby SQL Backup, protože k zálohování protokolů může docházet každých 15 minut, informace o všech dokončených úlohách plánovaného zálohování, včetně protokolů, jsou dávkové a vložené každých 6 hodin.
- V rámci všech řešení se pro všechny ostatní informace, jako je například zálohovaná položka, zásada, body obnovení, úložiště a tak dále, je alespoň *jednou denně* nabízena.
- Změna v konfiguraci zálohování (například změna zásady nebo zásady úprav) aktivuje vložení všech souvisejících informací o zálohování.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Použití protokolů aktivit trezoru Recovery Services

> [!CAUTION]
> Následující postup platí jenom pro *zálohy virtuálních počítačů Azure.* Tyto kroky nemůžete použít pro řešení, jako je agent Azure Backup, zálohy SQL v Azure nebo soubory Azure.

Protokoly aktivit můžete použít také k získání oznámení o událostech, jako je například úspěch zálohování. Začněte tím, že prodržíte tyto kroky:

1. Přihlaste se k portálu Azure Portal.
2. Otevřete příslušný trezor Recovery Services.
3. Ve vlastnostech trezoru otevřete část **Protokol aktivit** .

Chcete-li identifikovat příslušný protokol a vytvořit výstrahu:

1. Pomocí filtrů zobrazených na následujícím obrázku ověřte, zda jsou pro úspěšné zálohy přijímány protokoly aktivit. Změňte hodnotu **TimeSpan** podle potřeby pro zobrazení záznamů.

   ![Filtrování pro vyhledání protokolů aktivit pro zálohování virtuálních počítačů Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Chcete-li zobrazit relevantní podrobnosti, vyberte název operace.
3. Výběrem **nového pravidla výstrahy** otevřete stránku **vytvořit pravidlo** .
4. Pomocí Azure Monitor postupujte podle kroků uvedených v části [Vytvoření, zobrazení a správa výstrah protokolu aktivit](../azure-monitor/alerts/alerts-activity-log.md).

   ![Nové pravidlo výstrahy](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Zde je prostředek Recovery Services samotným trezorem. Opakujte stejný postup u všech trezorů, ve kterých chcete být upozorňováni prostřednictvím protokolů aktivit. Podmínka nebude mít prahovou hodnotu, tečku nebo frekvenci, protože tato výstraha je založena na událostech. Jakmile se vygeneruje relevantní protokol aktivit, vyvolá se výstraha.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Monitorování ve velkém měřítku pomocí Log Analytics

Můžete zobrazit všechny výstrahy vytvořené z protokolů aktivit a Log Analytics pracovní prostory v Azure Monitor. Stačí otevřít podokno **výstrahy** na levé straně.

I když můžete dostávat oznámení prostřednictvím protokolů aktivit, důrazně doporučujeme použít Log Analytics místo protokolů aktivit pro monitorování ve velkém měřítku. Důvod:

- **Omezené scénáře**: oznámení prostřednictvím protokolů aktivit se vztahují jenom na zálohy virtuálních počítačů Azure. Oznámení musí být nastavená pro každý trezor Recovery Services.
- **Přizpůsobení definice**: naplánovaná aktivita zálohování se nevejde do poslední definice protokolů aktivit. Místo toho se zarovnává s [protokoly prostředků](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Při tomto zarovnání dojde k neočekávaným důsledkům při změně dat, která se přetékají přes kanál protokolu aktivit.
- **Problémy s kanálem protokolu aktivit**: v úložištích Recovery Services se protokoly aktivit, které jsou z Azure Backup vycházejí z nového modelu. Tato změna má však vliv na generování protokolů aktivit v Azure Government, Azure Německo a Azure Čína 21Vianet. Pokud uživatelé těchto cloudových služeb vytvoří nebo nakonfigurují výstrahy z protokolů aktivit v Azure Monitor, výstrahy se neaktivují. Pokud uživatel ve všech veřejných oblastech Azure [shromažďuje Recovery Services protokoly aktivit do pracovního prostoru Log Analytics](../azure-monitor/essentials/activity-log.md), tyto protokoly se nezobrazují.

Použijte Log Analytics pracovní prostor pro monitorování a upozorňování ve velkém měřítku pro všechny vaše úlohy, které jsou chráněné Azure Backup.

## <a name="next-steps"></a>Další kroky

Informace o vytváření vlastních dotazů naleznete v tématu [Log Analytics data model](backup-azure-reports-data-model.md).
