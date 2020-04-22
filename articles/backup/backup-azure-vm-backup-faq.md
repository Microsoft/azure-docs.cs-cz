---
title: Nejčastější dotazy – zálohování virtuálních počítačů Azure
description: V tomto článku se dozvíte odpovědi na běžné otázky týkající se zálohování virtuálních počítačích Azure se službou Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: accfc57055f70254814c889de875f5360878bcd9
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757464"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Nejčastější dotazy – zálohování virtuálních počítačů Azure

Tento článek odpovídá na běžné otázky týkající se zálohování virtuálních počítačích Azure se službou [Azure Backup.](backup-introduction-to-azure-backup.md)

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Které image virtuálních aplikací lze povolit pro zálohování, když je vytvořím?

Když vytvoříte virtuální počítače, můžete povolit zálohování pro virtuální počítače s [podporovanými operačními systémy](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Jsou náklady na zálohování zahrnuty v ceně virtuálního počítače?

Ne. Náklady na zálohování jsou oddělené od nákladů na virtuální počítače. Přečtěte si další informace o [cenách azure backupu](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Která oprávnění jsou vyžadována k povolení zálohování pro virtuální počítače?

Pokud jste přispěvatelem virtuálního počítače, můžete povolit zálohování na virtuálním počítači. Pokud používáte vlastní roli, potřebujete následující oprávnění k povolení zálohování na virtuálním počítači:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Pokud váš trezor služby Recovery Services a virtuální počítač mají různé skupiny prostředků, ujistěte se, že máte oprávnění k zápisu ve skupině prostředků pro trezor služby Recovery Services.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Používá úloha zálohování na vyžádání stejný plán uchovávání informací jako naplánované zálohování?

Ne. Zadejte rozsah uchování pro úlohu zálohování na vyžádání. Ve výchozím nastavení je zachována po dobu 30 dnů při aktivaci z portálu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Na některých virtuálních počítačích byla nedávno povolena služba Azure Disk Encryption. Budou moje zálohy stále fungovat?

Poskytněte oprávnění pro azure backup pro přístup k trezoru klíčů. Zadejte oprávnění v PowerShellu, jak je popsáno v části **Povolit zálohování** v dokumentaci [k Prostředí Azure Backup PowerShell.](backup-azure-vms-automation.md)

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migroval jsem disky virtuálních počítačů na spravované disky. Budou moje zálohy stále fungovat?

Ano, zálohy fungují bez problémů. Není třeba nic překonfigurovat.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Proč se můj virtuální počítač nezobrazuje v průvodci konfigurací zálohování?

Průvodce uvádí pouze virtuální chody ve stejné oblasti jako trezor, které ještě nejsou zálohovány.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Můj virtuální virtuální měnič je vypnutý. Bude práce na vyžádání nebo naplánované zálohování?

Ano. Zálohy spustit při vypnutí počítače. Bod obnovení je označen jako konzistentní selhání.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Mohu zrušit probíhající úlohu zálohování?

Ano. Úlohu zálohování můžete zrušit ve stavu **Pořizování snímků.** Úlohu nelze zrušit, pokud probíhá přenos dat ze snímku.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Povolil jsem zámek skupiny prostředků vytvořené službou Azure `AzureBackupRG_<geo>_<number>`Backup Service (například). Budou moje zálohy stále fungovat?

Pokud uzamknete skupinu prostředků vytvořenou službou Azure Backup Service, zálohování se spustí, protože je maximální limit 18 bodů obnovení.

Odeberte zámek a zrušte kolekci bodů obnovení z této skupiny prostředků, aby byly budoucí zálohy úspěšné. Chcete-li odebrat kolekci bodů obnovení, [odeberte takto.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal)

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Podporuje zálohování Azure standardní disky spravované SSD?

Ano, Azure Backup podporuje [standardní spravované disky SSD](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Můžeme zálohovat virtuální hovirtuální ho s diskem s podporou akcelerátoru zápisu (WA)?

Snímky nelze pořizovat na disku s podporou WA. Služba Azure Backup však může vyloučit disk s podporou WA ze zálohy.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mám nainstalovaný virtuální virtuální modul s disky akcelerátoru zápisu (WA) a SAP HANA. Jak mohu zálohovat?

Azure Backup nemůže zálohovat disk s podporou WA, ale může ho vyloučit ze zálohy. Záloha však neposkytuje konzistenci databáze, protože informace na disku s podporou WA nejsou zálohovány. Pokud chcete zálohovat disky operačního systému, můžete je zálohovat pomocí této konfigurace a zálohovat disky, které nejsou povoleny službou WA.

Azure Backup poskytuje řešení pro zálohování datových proudů pro databáze SAP HANA s rpo 15 minut. Je backint certifikován společností SAP poskytovat nativní podporu zálohování s využitím nativních rozhraní API SAP HANA. Další informace [o zálohování databází SAP HANA ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-about).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Jaké je maximální zpoždění, které mohu očekávat v době zahájení zálohování z naplánovaného času zálohování, který jsem nastavil v zásadách zálohování virtuálních počítače?

Naplánované zálohování se spustí do 2 hodin od plánovaného času zálohování. Například Pokud 100 virtuálních počítače mají své spuštění zálohování čas naplánováno na 2:00 AM, pak nejpozději do 4:00 am nejpozději všech 100 virtuálních počítače bude mít své zálohovací úlohy v průběhu. Pokud byly naplánované zálohy pozastaveny z důvodu výpadku a obnoveny nebo opakovány, může být záloha zahájena mimo toto naplánované dvouhodinové okno.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Jaký je minimální povolený rozsah uchovávání pro denní bod zálohování?

Zásady zálohování virtuálního počítače Azure podporuje minimální rozsah uchovávání od sedmi dnů do 9999 dnů. Jakékoli změny existující zásady zálohování virtuálních počítače s méně než sedm dní bude vyžadovat aktualizaci ke splnění minimální rozsah uchovávání sedm dní.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Co se stane, když změním velikost písmen názvu virtuálního virtuálního montovna nebo skupiny prostředků virtuálního virtuálního montovna?

Pokud změníte velikost písmen (na horní nebo dolní) skupiny prostředků virtuálního počítače nebo virtuálního počítače, velikost písmen názvu položky zálohy se nezmění. To se však očekává, že azure backup chování. Změna případu se nezobrazí v položce zálohování, ale je aktualizován v back-endu.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Můžu zálohovat nebo obnovovat výběrové disky připojené k virtuálnímu počítače?

Azure Backup teď podporuje selektivní zálohování a obnovení disku pomocí řešení zálohování virtuálního počítače Azure.

Azure Backup dnes podporuje zálohování všech disků (operačního systému a dat) ve virtuálním počítači společně pomocí řešení zálohování virtuálního počítače. S funkcí vyloučit disk, získáte možnost zálohovat jeden nebo několik z mnoha datových disků ve virtuálním počítače. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých do operace zálohování, což dále umožňuje obnovit podmnožinu disků z daného bodu obnovení během operace obnovení. To platí pro obnovení ze snímku a úložiště.

Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Obnovení

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Jak se rozhodnu, jestli mám obnovit jenom disky nebo celý virtuální virtuální počítače?

Obnova virtuálního počítače si můžete myslet jako možnost rychlého vytvoření pro virtuální počítač Azure. Tato možnost změní názvy disků, kontejnery používané disky, veřejné adresy IP a názvy síťových rozhraní. Změna udržuje jedinečné prostředky při vytvoření virtuálního virtuálního soudu. Virtuální virtuální byl přidán do skupiny dostupnosti.

Možnost obnovení disku můžete použít, pokud chcete:

- Přizpůsobte si virtuální hod, který se vytvoří. Změňte například velikost.
- Přidejte nastavení konfigurace, která v době zálohování neexistovala.
- Řídit konvence pojmenování pro prostředky, které jsou vytvořeny.
- Přidejte virtuální ho do skupiny dostupnosti.
- Přidejte další nastavení, které musí být nakonfigurováno pomocí Prostředí PowerShell nebo šablony.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Můžu po upgradu na spravované disky obnovit zálohy nespravovaných disků virtuálního počítače?

Ano, můžete použít zálohy převzaté před migrací disků z nespravovaného na spravované.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak obnovit virtuální počítač do bodu obnovení před migrací virtuálního počítače na spravované disky?

Proces obnovení zůstává stejný. Pokud bod obnovení je bod v čase, kdy virtuální počítače měly nespravované disky, můžete [obnovit disky jako nespravované](tutorial-restore-disk.md#unmanaged-disks-restore). Pokud měl virtuální modul spravované disky, můžete [disky obnovit jako spravované disky](tutorial-restore-disk.md#managed-disk-restore). Pak můžete [vytvořit virtuální hod z těchto disků](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Další informace](backup-azure-vms-automation.md#restore-an-azure-vm) o tom v PowerShellu.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Můžu obnovit virtuální hod, který byl odstraněn?

Ano. I když virtuální ho odstraníte, můžete přejít na odpovídající položku zálohy v úložišti a obnovit z bodu obnovení.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Jak obnovím virtuální hod do stejných sad dostupnosti?

Pro spravované disky virtuálních počítačů Azure je obnovení na sady dostupnosti povoleno poskytnutím možnosti v šabloně při obnovení jako spravované disky. Tato šablona má vstupní parametr s názvem **Dostupnost sady**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak získáme rychlejší obnovení výkonů?

[Funkce okamžitého obnovení](backup-instant-restore-capability.md) pomáhá s rychlejším zálohováním a okamžitými obnoveními ze snímků.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Co se stane, když změníme nastavení trezoru klíčů pro šifrovaný virtuální virtuální počítače?

Po změně nastavení trezoru klíčů pro šifrovaný virtuální počítače budou zálohy nadále pracovat s novou sadou podrobností. Však po obnovení z bodu obnovení před změnou, budete muset obnovit tajné klíče v trezoru klíčů před vytvořením virtuálního virtuálního mísy z něj. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret).

Operace, jako je převrácení tajných a klíčne, tento krok nevyžadují a stejný keyvault lze použít po obnovení.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Můžu mít přístup k virtuálnímu virtuálnímu mněmu po obnovení z důvodu, že virtuální ms přerušil vztah s řadičem domény?

Ano, přístup k virtuálnímu virtuálnímu mněmu po obnovení z důvodu, že virtuální hod má přerušený vztah s řadičem domény. Další informace naleznete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Správa záloh virtuálních počítačů

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co se stane, když změním zásady zálohování?

Virtuální počítače se zálohuje pomocí nastavení plánu a uchovávání informací v upravené nebo nové zásady.

- Pokud je uchovávání prodlouženo, jsou stávající body obnovení označeny a uchovávány v souladu s novou zásadou.
- Pokud je retence snížena, body obnovení jsou označeny pro prořezávání v další úloze vyčištění a následně odstraněny.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Jak můžu přesunout virtuální počítač zálohovaný službou Azure Backup do jiné skupiny prostředků?

1. Dočasně zastavit zálohování a zachovat záložní data.
2. Přesuňte virtuální ho do cílové skupiny prostředků.
3. Znovu povolte zálohování ve stejném nebo novém trezoru.

Virtuální ho můžete obnovit z dostupných bodů obnovení, které byly vytvořeny před přesunutím operace.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Existuje omezení počtu virtuálních počítačů, které mohou být přidruženy ke stejným zásadám zálohování?

Ano, existuje limit 100 virtuálních počítače, které lze přidružit ke stejným zásadám zálohování z portálu. Doporučujeme, aby pro více než 100 virtuálních počítačů, vytvořit více zásad zálohování se stejným plánem nebo jiný plán.
