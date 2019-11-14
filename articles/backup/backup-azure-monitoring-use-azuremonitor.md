---
title: 'Azure Backup: monitorování Azure Backup pomocí Azure Monitor'
description: Monitorujte Azure Backup úlohy a vytvářejte vlastní výstrahy pomocí Azure Monitor.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Log Analytics; Azure Backup; Generoval Nastavení diagnostiky; Skupiny akcí
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: dacurwin
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e8f162b9dc84b863c54d92313307ef4abca76ebb
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074237"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorování ve velkém měřítku pomocí Azure Monitor

Azure Backup poskytuje [integrované funkce monitorování a upozorňování](backup-azure-monitoring-built-in-monitor.md) v trezoru Recovery Services. Tyto možnosti jsou k dispozici bez další infrastruktury pro správu. Tato integrovaná služba je však omezená v následujících situacích:

- Pokud budete monitorovat data z několika trezorů Recovery Services napříč předplatnými
- Pokud preferovaný kanál oznámení není *e-* mailem
- Pokud uživatelé chtějí výstrahy pro více scénářů
- Pokud chcete zobrazit informace z místní komponenty, jako je například System Center Data Protection Manager v Azure, který portál není zobrazený v [**úlohách zálohování**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) nebo [**výstrahách zálohování**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Použití Log Analyticsho pracovního prostoru

> [!NOTE]
> Data ze záloh virtuálních počítačů Azure, Azure Backup Agent, System Center Data Protection Manager, zálohy SQL na virtuálních počítačích Azure a sdílené složky Azure se v rámci nastavení diagnostiky naplní do pracovního prostoru Log Analytics. Bude brzy přidána podpora pro Microsoft Azure Backup Server (MABS).

Abyste mohli monitorovat a sestavovat škálování, potřebujete možnosti dvou služeb Azure. *Nastavení diagnostiky* odesílají data z několika prostředků Azure Resource Manager do jiného prostředku. *Log Analytics* generuje vlastní výstrahy, kde můžete použít skupiny akcí k definování jiných kanálů oznámení.

Následující části podrobně popisují, jak pomocí Log Analytics monitorovat Azure Backup škálované.

### <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky

Azure Resource Manager prostředky, jako je například trezor Recovery Services, zaznamená informace o plánovaných operacích a uživatelem aktivované operace jako diagnostická data.

V části monitorování vyberte **nastavení diagnostiky** a zadejte cíl pro diagnostická data trezoru Recovery Services.

![Nastavení diagnostiky Recovery Services trezoru, které cílí na Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Log Analytics pracovní prostor můžete cílit z jiného předplatného. Pokud chcete monitorovat trezory v rámci předplatných na jednom místě, vyberte stejný pracovní prostor Log Analytics pro několik trezorů Recovery Services. Pokud chcete všechny informace, které souvisejí s Azure Backup, přeAzureBackupReport do pracovního prostoru Log Analytics, jako protokol vyberte .

> [!IMPORTANT]
> Po dokončení konfigurace byste měli počkat 24 hodin na dokončení počátečního vkládání dat. Po této počáteční datové stránce jsou všechny události vloženy, jak je popsáno dále v tomto článku v [části frekvence](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Nasazení řešení do pracovního prostoru Log Analytics

> [!IMPORTANT]
> V Azure Backup jsme vydali aktualizovanou [šablonu](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) s více zobrazeními pro monitorování a vytváření sestav na bázi La. Počítejte s tím, že uživatelé, kteří používali [předchozí řešení](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) , se budou dál zobrazovat ve svých pracovních prostorech i po nasazení nového řešení. Staré řešení ale může poskytovat nepřesné výsledky kvůli nějakým malým změnám schématu. Uživatelé proto musí nasadit novou šablonu.

Až budou data v pracovním prostoru Log Analytics, [Nasaďte šablonu GitHubu](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) , která Log Analytics k vizualizaci dat. Pokud chcete pracovní prostor správně identifikovat, ujistěte se, že mu udělíte stejnou skupinu prostředků, název pracovního prostoru a umístění pracovního prostoru. Pak nainstalujte tuto šablonu do pracovního prostoru.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Zobrazení Azure Backup dat pomocí Log Analytics

Po nasazení šablony se řešení pro monitorování a vytváření sestav v Azure Backup zobrazí v oblasti souhrnu pracovního prostoru. Pokud chcete přejít na souhrn, postupujte podle jedné z následujících cest:

- **Azure monitor**: v části **přehledy** vyberte **Další** a pak zvolte příslušný pracovní prostor.
- **Log Analytics pracovní prostory**: vyberte příslušný pracovní prostor a potom v části **Obecné**vyberte možnost **Souhrn pracovního prostoru**.

![Dlaždice monitorování a vytváření sestav Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Když vyberete některou z dlaždic přehledu, můžete zobrazit další informace. Tady jsou některé ze sestav, které vidíte:

- Úlohy zálohování bez protokolu

   ![Diagramy Log Analytics pro úlohy zálohování](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Výstrahy ze zálohy prostředků Azure

   ![Log Analytics Graph pro úlohy obnovení](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

Podobně můžete kliknutím na další dlaždice zobrazit sestavy pro úlohy obnovení, cloudové úložiště, zálohované položky, výstrahy z místních prostředků zálohování a úlohy zálohování protokolů.

Tyto grafy jsou k dispozici se šablonou. Pokud potřebujete, můžete grafy upravit nebo přidat další grafy.

### <a name="create-alerts-by-using-log-analytics"></a>Vytváření výstrah pomocí Log Analytics

V Azure Monitor můžete vytvořit vlastní výstrahy v pracovním prostoru Log Analytics. V pracovním prostoru použijete *skupiny akcí Azure* k výběru upřednostňovaného mechanismu oznámení.

> [!IMPORTANT]
> Informace o nákladech na vytvoření tohoto dotazu najdete v tématu [Azure monitor Price](https://azure.microsoft.com/pricing/details/monitor/).

Vyberte libovolné grafy a otevřete tak část **protokoly** v pracovním prostoru Log Analytics. V části **protokoly** upravte dotazy a vytvořte výstrahy.

![Vytvoření výstrahy v pracovním prostoru Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Když vyberete **nové pravidlo výstrahy**, otevře se stránka Azure monitor pro vytvoření výstrahy, jak je znázorněno na následujícím obrázku. Tento prostředek je už označený jako pracovní prostor Log Analytics a poskytuje se integrace skupiny akcí.

![Stránka pro vytvoření výstrahy Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Podmínka upozornění

Charakteristickou charakteristikou výstrahy je stav triggeru. Vyberte **podmínku** , která automaticky načte dotaz Kusto na stránce **protokoly** , jak je znázorněno na následujícím obrázku. Tady můžete podmínku upravit tak, aby vyhovovala vašim potřebám. Další informace najdete v tématu [Ukázky dotazů Kusto](#sample-kusto-queries).

![Nastavení podmínky upozornění](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

V případě potřeby můžete upravit dotaz Kusto. Výběr prahové hodnoty, tečky a frekvence. Prahová hodnota určuje, kdy bude vyvolána výstraha. Období je okno času, ve kterém se dotaz spustí. Pokud je například prahová hodnota větší než 0, období je 5 minut a frekvence je 5 minut, potom pravidlo spustí dotaz každých 5 minut a zkontroluje předchozí 5 minut. Pokud je počet výsledků větší než 0, budete upozorněni na vybranou skupinu akcí.

#### <a name="alert-action-groups"></a>Skupiny akcí výstrah

Pro zadání kanálu oznámení použijte skupinu akcí. Chcete-li zobrazit dostupné mechanismy oznámení, vyberte v části **skupiny akcí**možnost **vytvořit nový**.

![Dostupné mechanismy oznámení v okně Přidat skupinu akcí](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Požadavky na výstrahy a monitorování můžete vyhovět pouze Log Analytics, nebo můžete pomocí Log Analytics doplnit Vestavěná oznámení.

Další informace najdete v tématu [Vytvoření, zobrazení a správa výstrah protokolu pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) a [vytváření a Správa skupin akcí v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

Výchozí grafy vám umožní Kusto dotazy na základní scénáře, ve kterých můžete vytvářet výstrahy. Můžete také upravit dotazy a získat tak data, která chcete upozornit. Na stránce **protokoly** vložte následující vzorové dotazy Kusto a pak vytvořte výstrahy na dotazech:

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

1. Přihlaste se k Azure Portal.
1. Otevřete příslušný trezor Recovery Services.
1. Ve vlastnostech trezoru otevřete část **Protokol aktivit** .

Chcete-li identifikovat příslušný protokol a vytvořit výstrahu:

1. Pomocí filtrů zobrazených na následujícím obrázku ověřte, zda jsou pro úspěšné zálohy přijímány protokoly aktivit. Změňte hodnotu **TimeSpan** podle potřeby pro zobrazení záznamů.

   ![Filtrování pro vyhledání protokolů aktivit pro zálohování virtuálních počítačů Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Chcete-li zobrazit relevantní podrobnosti, vyberte název operace.
1. Výběrem **nového pravidla výstrahy** otevřete stránku **vytvořit pravidlo** .
1. Pomocí Azure Monitor postupujte podle kroků uvedených v části [Vytvoření, zobrazení a správa výstrah protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nové pravidlo výstrahy](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Zde je prostředek Recovery Services samotným trezorem. Opakujte stejný postup u všech trezorů, ve kterých chcete být upozorňováni prostřednictvím protokolů aktivit. Podmínka nebude mít prahovou hodnotu, tečku nebo frekvenci, protože tato výstraha je založena na událostech. Jakmile se vygeneruje relevantní protokol aktivit, vyvolá se výstraha.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Monitorování ve velkém měřítku pomocí Log Analytics

Můžete zobrazit všechny výstrahy vytvořené z protokolů aktivit a Log Analytics pracovní prostory v Azure Monitor. Stačí otevřít podokno **výstrahy** na levé straně.

I když můžete dostávat oznámení prostřednictvím protokolů aktivit, důrazně doporučujeme použít Log Analytics místo protokolů aktivit pro monitorování ve velkém měřítku. Důvody:

- **Omezené scénáře**: oznámení prostřednictvím protokolů aktivit se vztahují jenom na zálohy virtuálních počítačů Azure. Oznámení musí být nastavená pro každý trezor Recovery Services.
- **Přizpůsobení definice**: naplánovaná aktivita zálohování se nevejde do poslední definice protokolů aktivit. Místo toho se zarovnává s [protokoly prostředků](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace). Při tomto zarovnání dojde k neočekávaným důsledkům při změně dat, která se přetékají přes kanál protokolu aktivit.
- **Problémy s kanálem protokolu aktivit**: v úložištích Recovery Services se protokoly aktivit, které jsou z Azure Backup vycházejí z nového modelu. Tato změna má však vliv na generování protokolů aktivit v Azure Government, Azure Německo a Azure Čína 21Vianet. Pokud uživatelé těchto cloudových služeb vytvoří nebo nakonfigurují výstrahy z protokolů aktivit v Azure Monitor, výstrahy se neaktivují. Pokud uživatel ve všech veřejných oblastech Azure [shromažďuje Recovery Services protokoly aktivit do pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), tyto protokoly se nezobrazují.

Použijte Log Analytics pracovní prostor pro monitorování a upozorňování ve velkém měřítku pro všechny vaše úlohy, které jsou chráněné Azure Backup.

## <a name="next-steps"></a>Další kroky

Informace o vytváření vlastních dotazů naleznete v tématu [Log Analytics data model](backup-azure-log-analytics-data-model.md).
