---
title: Nejčastější dotazy týkající se zálohování virtuálních počítačů Azure pomocí Azure Backup
description: Odpovědi na běžné dotazy týkající se zálohování virtuálních počítačů Azure pomocí Azure Backup.
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: sogup
ms.openlocfilehash: a26cc2fcdc381361912085b0980a736f040c5b0a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465291"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Nejčastější dotazy – zálohování virtuálních počítačů Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování virtuálních počítačů Azure pomocí služby [Azure Backup](backup-introduction-to-azure-backup.md) .


## <a name="backup"></a>Zálohovat

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Které image virtuálních počítačů se můžou při vytváření záloh povolit?
Když vytváříte virtuální počítač, můžete povolit zálohování pro virtuální počítače s [podporovanými operačními systémy](backup-support-matrix-iaas.md#supported-backup-actions) .

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Jsou náklady na zálohování zahrnuty do nákladů na virtuální počítače?

Ne. Náklady na zálohování jsou oddělené od nákladů na virtuální počítače. Přečtěte si další informace o [cenách Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Která oprávnění jsou nutná k povolení zálohování pro virtuální počítač?

Pokud jste přispěvatelem virtuálních počítačů, můžete na virtuálním počítači povolit zálohování. Pokud používáte vlastní roli, budete potřebovat následující oprávnění, abyste mohli na virtuálním počítači povolit zálohování:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft. RecoveryServices/umístění/*
- Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/*/Read
- Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft. RecoveryServices/trezory/backupPolicies/Read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Pokud má váš Recovery Services trezor a virtuální počítač jiné skupiny prostředků, ujistěte se, že máte oprávnění k zápisu do skupiny prostředků pro trezor Recovery Services.  


### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Používá úloha zálohování na vyžádání stejný plán uchovávání dat jako plánované zálohy?
Ne. Zadejte rozsah uchování pro úlohu zálohování na vyžádání. Ve výchozím nastavení se po aktivaci z portálu uchovávají po dobu 30 dnů.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Na některých virtuálních počítačích byla nedávno povolena služba Azure Disk Encryption. Budou moje zálohy stále fungovat?
Poskytněte oprávnění pro přístup k Key Vault Azure Backup. V prostředí PowerShell zadejte oprávnění, jak je popsáno v části **Povolení zálohování** v dokumentaci k [prostředí Azure Backup PowerShell](backup-azure-vms-automation.md) .

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migrovali jsem disky virtuálních počítačů na Managed disks. Budou moje zálohy stále fungovat?
Ano, zálohování funguje bez problémů. Není nutné nic znovu konfigurovat.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Proč se můj virtuální počítač nezobrazuje v průvodci konfigurací zálohování?
Průvodce zobrazí jenom virtuální počítače ve stejné oblasti jako trezor, které ještě nejsou zálohované.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Virtuální počítač je vypnutý. Bude aplikace na vyžádání nebo plánované zálohování fungovat?
Ano. Zálohování se spustí při vypnutí počítače. Bod obnovení je označen jako konzistentní se selháním.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Můžu zrušit probíhající úlohu zálohování?
Ano. Úlohu zálohování můžete zrušit ve stavu **pořizování snímků** . Pokud probíhá přenos dat z snímku, nemůžete úlohu zrušit.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Aktivoval (a) jsem zámek pro skupinu prostředků vytvořenou službou Azure Backup (tj. `AzureBackupRG_<geo>_<number>`) budou moje zálohy i nadále fungovat?
Pokud zamknete skupinu prostředků vytvořenou službou Azure Backup, zálohování začnou selhat, protože je k dispozici maximální limit 18 bodů obnovení.

Uživatel musí odebrat zámek a vymazat kolekci bodů obnovení z dané skupiny prostředků, aby bylo možné dokončit budoucí zálohování, [pomocí následujících kroků](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) odeberte kolekci bodů obnovení.


### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Podporuje Azure Backup standardní disk SSD, který je spravovaný?
Azure Backup podporuje [Standard SSD spravované disky](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Disky spravované SSD poskytují nový typ trvalého úložiště pro virtuální počítače Azure. Při [okamžitém obnovení](backup-instant-restore-capability.md)je k dispozici podpora pro spravované disky SSD.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Můžeme zálohovat virtuální počítač s diskem s povoleným Akcelerátor zápisu (WA)?
Na disku s podporou WA se snímky nedají považovat. Služba Azure Backup však může ze zálohy vyloučit disk s podporou WA.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mám virtuální počítač s disky Akcelerátor zápisu (WA) a SAP HANA nainstalované. Návody zálohování?
Azure Backup nemůže zálohovat disk s podporou WA, ale může ho vyloučit ze zálohy. Zálohování ale neposkytne konzistenci databáze, protože informace na disku s podporou WA se nezálohují. Disky s touto konfigurací můžete zálohovat, pokud chcete zálohovat disk s operačním systémem a zálohovat disky, u kterých není povolený režim WA.

Provozujeme soukromou verzi Preview pro SAP HANA zálohování s RPO 15 minut. Je sestaven podobným způsobem jako zálohování databáze SQL a používá rozhraní backInt pro řešení třetích stran s certifikací SAP HANA. Pokud vás zajímá, pošlete nám `AskAzureBackupTeam@microsoft.com` e-mail s předmětem **registrace privátní verze Preview pro zálohování SAP HANA ve virtuálních počítačích Azure**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Jaká je maximální prodleva, kterou je možné očekávat v čase zahájení zálohování z naplánovaného času zálohování, který jsem nastavil v zásadách zálohování virtuálních počítačů?
Naplánované zálohování se spustí do 2 hodin plánovaného času zálohování. Pro ex. Pokud mají virtuální počítače 100 čas zahájení zálohování naplánovaný na 2:00 am, pak podle maximální 4:00 dostanou všechny 100VMs úlohy zálohování. Pokud bylo naplánované zálohování pozastaveno z důvodu výpadku a obnovení nebo opakování, může zálohování začít mimo toto naplánované 2hr okno.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Jaký je minimální povolený rozsah uchování pro denní bod zálohy?
Zásady zálohování virtuálních počítačů Azure podporují minimální dobu uchování, která je 7 dní až 9999 dní. Jakákoli změna existující zásady zálohování virtuálního počítače s méně než 7 dny bude vyžadovat aktualizaci, aby splňovala minimální dobu uchovávání 7 dní.

## <a name="restore"></a>Obnovit

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Návody rozhodnout, jestli chcete obnovit jenom disky nebo celý virtuální počítač?
Představte si obnovení virtuálního počítače jako možnost rychlého vytvoření pro virtuální počítač Azure. Tato možnost změní názvy disků, kontejnery používané disky, veřejné IP adresy a názvy síťových rozhraní. Tato změna udržuje při vytvoření virtuálního počítače jedinečné prostředky. Virtuální počítač není přidaný do skupiny dostupnosti.

Možnost obnovit disk můžete použít, pokud chcete:
  * Přizpůsobte virtuální počítač, který se vytvoří. Změňte například velikost.
  * Přidejte nastavení konfigurace, která v době zálohování neexistovala.
  * Řízení zásad vytváření názvů pro prostředky, které jsou vytvořeny.
  * Přidejte virtuální počítač do skupiny dostupnosti.
  * Přidejte všechna další nastavení, která se musí konfigurovat pomocí PowerShellu nebo šablony.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Můžu po upgradu na spravované disky obnovit zálohy nespravovaných disků virtuálních počítačů?
Ano, můžete použít zálohy provedené před migrací disků z nespravovaného do spravovaného.
- Ve výchozím nastavení úloha obnovení virtuálního počítače vytvoří nespravovaný virtuální počítač.
- Můžete ale obnovit disky a použít je k vytvoření spravovaného virtuálního počítače.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak obnovit virtuální počítač do bodu obnovení před migrací virtuálního počítače na spravované disky?
Ve výchozím nastavení úloha obnovení virtuálního počítače vytvoří virtuální počítač s nespravovanými disky. Vytvoření virtuálního počítače se službou Managed disks:
1. [Obnovte na nespravované disky](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Převeďte obnovené disky na Managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Vytvořte virtuální počítač se spravovanými disky](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Přečtěte si další informace](backup-azure-vms-automation.md#restore-an-azure-vm) o tom, jak to provést v PowerShellu.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Můžu obnovit odstraněný virtuální počítač?
Ano. I když virtuální počítač odstraníte, můžete přejít na odpovídající zálohovanou položku v trezoru a obnovit ji z bodu obnovení.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Jak obnovit virtuální počítač do stejných skupin dostupnosti?
V případě spravovaného virtuálního počítače Azure je povolený obnovení do skupin dostupnosti tím, že při obnovení jako spravované disky poskytne možnost v šabloně. Tato šablona obsahuje vstupní parametr s názvem **skupiny dostupnosti**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak získám rychlejší obnovení?
Pro rychlejší obnovení se přesouváme do možnosti [okamžitého obnovení](backup-instant-restore-capability.md) .

## <a name="manage-vm-backups"></a>Správa záloh virtuálních počítačů

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co se stane, když upravím zásady zálohování?
Virtuální počítač se zálohuje pomocí nastavení plánu a uchování v upravené nebo nové zásadě.

- Pokud je uchovávání dat rozšířené, existující body obnovení jsou označeny a udržovány v souladu s novou zásadou.
- Pokud je uchovávání dat sníženo, body obnovení jsou označeny pro vyřazení v další úloze čištění a následně odstraněny.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Návody přesunout virtuální počítač zálohovaný pomocí Azure Backup do jiné skupiny prostředků?

1. Dočasné zastavení zálohování a uchování zálohovaných dat.
2. Přesuňte virtuální počítač do cílové skupiny prostředků.
3. Opětovné povolení zálohy ve stejném nebo novém trezoru.

Virtuální počítač můžete obnovit z dostupných bodů obnovení, které byly vytvořeny před operací přesunutí.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Existuje omezení počtu virtuálních počítačů, které mohou být přidruženy ke stejné zásadě zálohování?
Ano, počet 100 virtuálních počítačů, které mohou být přidruženy ke stejné zásadě zálohování z portálu. Doporučujeme, abyste pro více než 100 virtuálních počítačů vytvořili více zásad zálohování se stejným plánem nebo jiným plánem.
