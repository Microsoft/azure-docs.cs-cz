---
title: Nejčastější dotazy – zálohování databází SAP HANA na virtuálních počítačích Azure
description: V tomto článku se dozvíte odpovědi na běžné otázky týkající se zálohování databází SAP HANA pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155388"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Nejčastější dotazy – Zálohování databází SAP HANA na virtuálních počítačích Azure

Tento článek odpovídá na běžné otázky týkající se zálohování databází SAP HANA pomocí služby Azure Backup.

## <a name="backup"></a>Zálohování

### <a name="how-many-full-backups-are-supported-per-day"></a>Kolik úplných záloh je podporováno za den?

Podporujeme pouze jednu plnou zálohu denně. Nelze mít rozdílové zálohování a úplné zálohování spuštěno ve stejný den.

### <a name="do-successful-backup-jobs-create-alerts"></a>Vytvářejí úspěšné úlohy zálohování upozornění?

Ne. Úspěšné úlohy zálohování negenerují výstrahy. Výstrahy jsou odesílány pouze pro úlohy zálohování, které se nezdaří. Podrobné chování pro portálové výstrahy je dokumentováno [zde](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Pokud však máte zájem o výstrahy i pro úspěšné úlohy, můžete použít [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Lze zobrazit naplánované úlohy zálohování v nabídce Úlohy zálohování?

V nabídce Úloha zálohování se zobrazí pouze úlohy zálohování ad hoc. Pro naplánované úlohy použijte [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Budou se automaticky zálohovat i budoucí databáze?

Ne, toto není aktuálně podporováno.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Pokud odstraním databázi z instance, co se stane se zálohami?

Pokud je databáze vynechána z instance SAP HANA, jsou stále pokusy o zálohování databáze. To znamená, že odstraněná databáze se začne zobrazovat jako není v pořádku v části **Položky zálohování** a je stále chráněna.
Správný způsob, jak zastavit ochranu této databáze je provést **stop zálohování s odstranit data** v této databázi.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Pokud změním název databáze poté, co byla chráněna, jaké bude chování?

Přejmenovaná databáze je považována za novou databázi. Proto služba bude považovat tuto situaci, jako kdyby databáze nebyla nalezena a selhání zálohy. Přejmenovaná databáze se zobrazí jako nová databáze a musí být nakonfigurována pro ochranu.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jaké jsou předpoklady pro zálohování databází SAP HANA na virtuálním počítači Azure?

Podívejte se na [požadavky](tutorial-backup-sap-hana-db.md#prerequisites) a [co dělá skript předběžné registrace.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Jaká oprávnění by měla být nastavena pro Azure, aby bylo možné zálohovat databáze SAP HANA?

Spuštěním skriptu předběžné registrace nastavíte požadovaná oprávnění, která azure umožní zálohovat databáze SAP HANA. Více toho, co dělá skript pro předběžnou [registraci, naleznete zde](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Budou zálohy fungovat po migraci SAP HANA z 1.0 na 2.0?

V [této části](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) příručky pro řešení potíží naleznete.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Může být azure hana backup nastavena proti virtuální IP (nástroj pro vyrovnávání zatížení) a ne virtuální stroj?

V současné době nemáme možnost nastavit řešení proti virtuální IP sám. Potřebujeme virtuální stroj pro spuštění řešení.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Mám SAP HANA systémová replikace (HSR), jak mám nakonfigurovat zálohování pro toto nastavení?

Primární a sekundární uzly HSR budou považovány za dva samostatné virtuální chody, které nejsou příbuzné. Je třeba nakonfigurovat zálohování na primárním uzlu a když dojde k převzetí služeb při selhání, je třeba nakonfigurovat zálohování na sekundárním uzlu (který se nyní stane primárním uzem). Neexistuje žádný automatický 'převzetí služeb při selhání' zálohování do druhého uzlu.

## <a name="restore"></a>Obnovení

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Proč nevidím systém HANA, do kterého chcete databázi obnovit?

Zkontrolujte, zda jsou splněny všechny předpoklady pro obnovení cílové instance SAP HANA. Další informace najdete [v tématu Požadavky – obnovení databází SAP HANA ve virtuálním počítači Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Proč se obnovení přepsání db nedaří pro databázi?

Ujistěte se, že je při obnovení vybraná možnost **Vynutit přepsání.**

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Proč se zobrazuje chyba "Zdrojové a cílové systémy pro obnovení jsou nekompatibilní"?

Informace o tom, jaké typy obnovení jsou aktuálně podporovány, naleznete v poznámce SAP HANA [1642148.](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [zálohovat databáze SAP HANA spuštěné](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) na virtuálních počítačích Azure.
