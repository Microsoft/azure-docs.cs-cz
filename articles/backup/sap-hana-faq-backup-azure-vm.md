---
title: Nejčastější dotazy k zálohování SAP HANA databází na virtuálních počítačích Azure
description: V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e5684024668103ccbe13be4af3d7a9336651df77
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287837"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Nejčastější dotazy – zálohování SAP HANA databází na virtuálních počítačích Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Kolik úplných záloh se podporuje za den?

Podporujeme jenom jednu úplnou zálohu za den. Nemůžete mít ke stejnému dni spuštěno rozdílové zálohování a úplné zálohování.

### <a name="do-successful-backup-jobs-create-alerts"></a>Vytvářejí úspěšné úlohy zálohování výstrahy?

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Výstrahy se odesílají jenom pro úlohy zálohování, které selžou. Podrobné chování výstrah na portálu [najdete tady](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Pokud ale máte zájem o výstrahy i pro úspěšné úlohy, můžete použít [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Můžu v nabídce úlohy zálohování Zobrazit naplánované úlohy zálohování?

V nabídce Úloha zálohování se zobrazí pouze úlohy zálohování ad-hoc. V případě naplánovaných úloh použijte [Azure monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Jsou budoucí databáze automaticky přidány pro zálohování?

Ne, toto není aktuálně podporováno.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Když odstraním databázi z instance, co se stane s zálohami?

Pokud je databáze vyřazena z instance SAP HANA, zálohování databáze se stále pokouší. To znamená, že Odstraněná databáze začne v rámci **zálohované položky** zobrazovat stav není v pořádku a je pořád chráněná.
Správný způsob, jak zastavit ochranu této databáze, je provést **zastavení zálohování s odstraňováním dat** v této databázi.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Když změním název databáze po jejím ochraně, jaká chování budou?

Přejmenovaná databáze je považována za novou databázi. Proto se služba bude považovat za tuto situaci, jako kdyby nebyla nalezena databáze a selhala zálohování. Přejmenovaná databáze se zobrazí jako nová databáze a je nutné ji nakonfigurovat pro ochranu.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jaké jsou požadavky pro zálohování SAP HANA databází na virtuálním počítači Azure?

Přečtěte si část [požadavky](tutorial-backup-sap-hana-db.md#prerequisites) a [Nastavení oprávnění](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Jaká oprávnění by měla být nastavená pro Azure, aby bylo možné zálohovat SAP HANA databáze?

Spuštění předregistračního skriptu nastaví požadovaná oprávnění, která umožní službě Azure zálohovat SAP HANA databáze. [Tady](tutorial-backup-sap-hana-db.md#setting-up-permissions)najdete další informace o oprávněních.

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Budou zálohy fungovat po migraci SAP HANA od 1,0 do 2,0?

Informace najdete v [této části](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) Průvodce odstraňováním potíží.

## <a name="restore"></a>Obnovení

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Proč se mi nedá zobrazit systém HANA, na který chci obnovit moji databázi?

Ověřte, zda jsou splněny všechny požadavky pro cíl obnovení na instanci SAP HANA. Další informace najdete v tématu [požadavky – obnovení SAP HANA databází ve virtuálním počítači Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Proč se pro moji databázi nezdaří obnovení přepsání databáze?

Zajistěte, aby při obnovení byla vybrána možnost **Vynutit přepsání** .

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Proč se zobrazuje chyba "zdrojový a cílový systém pro obnovení jsou nekompatibilní"?

Informace o tom, jaké typy obnovení se aktuálně podporují, najdete v SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="next-steps"></a>Další kroky

Naučte se, jak [zálohovat SAP HANA databáze](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) běžící na virtuálních počítačích Azure.
