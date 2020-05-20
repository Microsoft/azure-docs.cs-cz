---
title: Nejčastější dotazy – zálohování databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: f9e0d96439a79c2c3d2cb2caa00ff09be3ff790d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660110"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Nejčastější dotazy – zálohování SAP HANA databází na virtuálních počítačích Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Kolik úplných záloh se podporuje za den?

Podporujeme jenom jednu úplnou zálohu za den. Nemůžete mít ke stejnému dni spuštěno rozdílové zálohování a úplné zálohování.

### <a name="do-successful-backup-jobs-create-alerts"></a>Vytvářejí úspěšné úlohy zálohování upozornění?

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Výstrahy se odesílají jenom pro úlohy zálohování, které selžou. Podrobné chování výstrah na portálu [najdete tady](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Pokud ale máte zájem o výstrahy i pro úspěšné úlohy, můžete použít [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Můžu v nabídce úlohy zálohování Zobrazit naplánované úlohy zálohování?

V nabídce Úloha zálohování se zobrazí pouze úlohy zálohování ad-hoc. V případě naplánovaných úloh použijte [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Budou se automaticky zálohovat i budoucí databáze?

Ne, toto není aktuálně podporováno.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Když odstraním databázi z instance, co se stane s zálohami?

Pokud je databáze vyřazena z instance SAP HANA, zálohování databáze se stále pokouší. To znamená, že Odstraněná databáze začne v rámci **zálohované položky** zobrazovat stav není v pořádku a je pořád chráněná.
Správný způsob, jak zastavit ochranu této databáze, je provést **zastavení zálohování s odstraňováním dat** v této databázi.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Když změním název databáze po jejím ochraně, jaká chování budou?

Přejmenovaná databáze je považována za novou databázi. Proto se služba bude považovat za tuto situaci, jako kdyby nebyla nalezena databáze a selhala zálohování. Přejmenovaná databáze se zobrazí jako nová databáze a je nutné ji nakonfigurovat pro ochranu.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jaké jsou požadavky pro zálohování SAP HANA databází na virtuálním počítači Azure?

Přečtěte si o [požadavcích](tutorial-backup-sap-hana-db.md#prerequisites) a o [tom, co skript předregistrací zahrnuje](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) oddíly.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Jaká oprávnění by měla být nastavená pro Azure, aby bylo možné zálohovat SAP HANA databáze?

Spuštění předregistračního skriptu nastaví požadovaná oprávnění, která umožní službě Azure zálohovat SAP HANA databáze. Další informace o tom, co skript před registrací dělá, najdete [tady](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Budou zálohy fungovat po migraci SAP HANA od 1,0 do 2,0?

Informace najdete v [této části](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) Průvodce odstraňováním potíží.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Je možné vytvořit zálohu v Azure HANA proti virtuální IP adrese (Nástroj pro vyrovnávání zatížení) a ne virtuálnímu počítači?

V současné době nemáme možnost nastavit řešení pouze na virtuální IP adresu. K provedení řešení potřebujeme virtuální počítač.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Mám replikaci systému SAP HANA (HSR), jak mám nakonfigurovat zálohování pro tuto instalaci?

Primární a sekundární uzel HSR se budou považovat za dva jednotlivé virtuální počítače, které se netýkají. Musíte nakonfigurovat zálohování na primárním uzlu a když dojde k převzetí služeb při selhání, musíte nakonfigurovat zálohování na sekundárním uzlu (který se teď stane primárním uzlem). Neexistuje žádné automatické zálohování po převzetí služeb při selhání do druhého uzlu.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Jak můžu přesunout zálohu na vyžádání do místního systému souborů místo do trezoru Azure?

1. Počkat na dokončení aktuálně běžícího zálohování v požadované databázi (kontrolu dokončíte od studia)
1. Zakažte zálohy protokolů a nastavte zálohu katalogu na **systém souborů** pro požadovanou databázi pomocí následujících kroků:
1. Dvakrát klikněte na **SYSTEMDB**  ->  **Konfigurace**  ->  **vybrat databázový**  ->  **filtr (protokol)** .
    1. Nastavit enable_auto_log_backup na **ne**
    1. Nastavit log_backup_using_backint na **hodnotu false**
1. Proveďte zálohování na vyžádání v požadované databázi a počkejte, než se dokončí zálohování a zálohování katalogu.
1. Vraťte se k předchozímu nastavení, aby bylo možné zálohy do trezoru Azure přesměrovat:
    1. Nastavit enable_auto_log_backup na **Ano**
    1. Nastavit log_backup_using_backint na **hodnotu true**

## <a name="restore"></a>Obnovení

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Proč se mi nedá zobrazit systém HANA, na který chci obnovit moji databázi?

Ověřte, zda jsou splněny všechny požadavky pro cíl obnovení na instanci SAP HANA. Další informace najdete v tématu [požadavky – obnovení SAP HANA databází ve virtuálním počítači Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Proč se pro moji databázi nezdaří obnovení přepsání databáze?

Zajistěte, aby při obnovení byla vybrána možnost **Vynutit přepsání** .

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Proč se zobrazuje chyba "zdrojový a cílový systém pro obnovení jsou nekompatibilní"?

Informace o tom, jaké typy obnovení se aktuálně podporují, najdete v SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="next-steps"></a>Další kroky

Naučte se, jak [zálohovat SAP HANA databáze](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) běžící na virtuálních počítačích Azure.
