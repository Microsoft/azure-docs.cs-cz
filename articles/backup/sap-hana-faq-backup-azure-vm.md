---
title: Nejčastější dotazy – zálohování databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování SAP HANA databází pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: d9d10e38885ba814045d8476b83671153feb7b8c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919681"
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

Přečtěte si o [požadavcích](tutorial-backup-sap-hana-db.md#prerequisites) a o [tom, co skript předregistrací zahrnuje](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) oddíly.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Jaká oprávnění by měla být nastavená pro Azure, aby bylo možné zálohovat SAP HANA databáze?

Spuštění předregistračního skriptu nastaví požadovaná oprávnění, která umožní službě Azure zálohovat SAP HANA databáze. Další informace o tom, co skript před registrací dělá, najdete [tady](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

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
