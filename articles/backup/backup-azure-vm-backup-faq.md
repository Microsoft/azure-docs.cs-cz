---
title: Nejčastější dotazy k zálohování virtuálních počítačů Azure
description: V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování virtuálních počítačů Azure pomocí služby Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 5d2f702b49e1e7aeb2ab33008556e91264b39427
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705407"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Nejčastější dotazy – zálohování virtuálních počítačů Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování virtuálních počítačů Azure pomocí služby [Azure Backup](backup-introduction-to-azure-backup.md) .

## <a name="backup"></a>Backup

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

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Aktivoval (a) jsem zámek pro skupinu prostředků vytvořenou službou Azure Backup (tj. `AzureBackupRG_<geo>_<number>`) budou moje zálohy nadále fungovat?

Pokud zamknete skupinu prostředků vytvořenou službou Azure Backup, zálohování začnou selhat, protože je k dispozici maximální limit 18 bodů obnovení.

Uživatel musí odebrat zámek a vymazat kolekci bodů obnovení z dané skupiny prostředků, aby bylo možné dokončit budoucí zálohování, [pomocí následujících kroků](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) odeberte kolekci bodů obnovení.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Podporuje Azure Backup standardní disky se správou SSD?

Ano, Azure Backup podporuje [Standard SSD spravované disky](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Můžeme zálohovat virtuální počítač s diskem s povoleným Akcelerátor zápisu (WA)?

Na disku s podporou WA se snímky nedají považovat. Služba Azure Backup však může ze zálohy vyloučit disk s podporou WA.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mám virtuální počítač s disky Akcelerátor zápisu (WA) a SAP HANA nainstalované. Návody zálohování?

Azure Backup nemůže zálohovat disk s podporou WA, ale může ho vyloučit ze zálohy. Zálohování ale neposkytne konzistenci databáze, protože informace na disku s podporou WA se nezálohují. Disky s touto konfigurací můžete zálohovat, pokud chcete zálohovat disk s operačním systémem a zálohovat disky, u kterých není povolený režim WA.

Provozujeme soukromou verzi Preview pro SAP HANA zálohování s RPO 15 minut. Je sestaven podobným způsobem jako zálohování databáze SQL a používá rozhraní backInt pro řešení třetích stran s certifikací SAP HANA. Pokud vás zajímá, pošlete nám e-mail na adresu `AskAzureBackupTeam@microsoft.com` s předmětem **registrace privátní Preview pro zálohování SAP HANA ve virtuálních počítačích Azure**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Jaká je maximální prodleva, kterou je možné očekávat v čase zahájení zálohování z naplánovaného času zálohování, který jsem nastavil v zásadách zálohování virtuálních počítačů?

Naplánované zálohování se spustí do 2 hodin plánovaného času zálohování. Pokud například virtuální počítače 100 mají čas zahájení zálohování naplánovaný na 2:00 dop. pak podle maximálního počtu 4:00 budou mít všechny virtuální počítače s 100 probíhající úlohu zálohování. Pokud bylo naplánované zálohování pozastaveno z důvodu výpadku a obnovení/opakování pokusu, pak může zálohování začít mimo toto naplánované dva hodiny.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Jaký je minimální povolený rozsah uchování pro denní bod zálohy?

Zásady zálohování virtuálních počítačů Azure podporují minimální dobu uchování na sedm dní až po 9999 dnů. Jakákoli změna existující zásady zálohování virtuálního počítače s méně než sedmi dny bude vyžadovat aktualizaci, aby splňovala minimální dobu uchování sedmi dnů.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>Můžu zálohovat nebo obnovit selektivní disky připojené k virtuálnímu počítači?

Azure Backup teď podporuje zálohování a obnovení selektivního disku pomocí řešení zálohování virtuálních počítačů Azure.

V současné době Azure Backup podporuje zálohování všech disků (operačního systému a dat) na virtuálním počítači společně s využitím řešení zálohování virtuálních počítačů. Díky funkci vyloučení disku získáte možnost zálohovat jeden nebo několik datových disků ve virtuálním počítači. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých v operaci zálohování, která dále umožňuje mít v průběhu operace obnovení podmnožinu disků obnovených z daného bodu obnovení. To platí pro obnovení ze snímku i z trezoru.

Pokud si chcete zaregistrovat verzi Preview, napište nám na AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Obnovení

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Návody rozhodnout, jestli chcete obnovit jenom disky nebo celý virtuální počítač?

Představte si obnovení virtuálního počítače jako možnost rychlého vytvoření pro virtuální počítač Azure. Tato možnost změní názvy disků, kontejnery používané disky, veřejné IP adresy a názvy síťových rozhraní. Tato změna udržuje při vytvoření virtuálního počítače jedinečné prostředky. Virtuální počítač není přidaný do skupiny dostupnosti.

Možnost obnovit disk můžete použít, pokud chcete:

- Přizpůsobte virtuální počítač, který se vytvoří. Změňte například velikost.
- Přidejte nastavení konfigurace, které nebylo v době zálohování k dispozici.
- Řízení zásad vytváření názvů pro prostředky, které jsou vytvořeny.
- Přidejte virtuální počítač do skupiny dostupnosti.
- Přidejte všechna další nastavení, která se musí konfigurovat pomocí PowerShellu nebo šablony.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Můžu po upgradu na spravované disky obnovit zálohy nespravovaných disků virtuálních počítačů?

Ano, můžete použít zálohy provedené před migrací disků z nespravovaného do spravovaného.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak obnovit virtuální počítač do bodu obnovení před migrací virtuálního počítače na spravované disky?

Proces obnovení zůstává stejný. Pokud je bod obnovení k určitému bodu v čase, kdy má virtuální počítač nespravované disky, můžete [disky obnovit jako nespravované](tutorial-restore-disk.md#unmanaged-disks-restore). Pokud má virtuální počítač spravované disky, můžete [disky obnovit jako spravované disky](tutorial-restore-disk.md#managed-disk-restore). Pak můžete [z těchto disků vytvořit virtuální počítač](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Přečtěte si další informace](backup-azure-vms-automation.md#restore-an-azure-vm) o tom, jak to provést v PowerShellu.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Můžu obnovit odstraněný virtuální počítač?

Ano. I když virtuální počítač odstraníte, můžete přejít na odpovídající zálohovanou položku v trezoru a obnovit ji z bodu obnovení.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Jak obnovit virtuální počítač do stejných skupin dostupnosti?

V případě spravovaného virtuálního počítače Azure je povolený obnovení do skupin dostupnosti tím, že při obnovení jako spravované disky poskytne možnost v šabloně. Tato šablona obsahuje vstupní parametr s názvem **skupiny dostupnosti**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak získám rychlejší obnovení?

Funkce [okamžitého obnovení](backup-instant-restore-capability.md) pomáhá urychlit zálohování a okamžité obnovení ze snímků.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Co se stane, když změníme nastavení trezoru klíčů pro šifrovaný virtuální počítač?

Po změně nastavení trezoru klíčů pro šifrovaný virtuální počítač budou zálohy nadále fungovat s novou sadou podrobností. Po obnovení z bodu obnovení před změnou ale budete muset tajné klíče v trezoru klíčů obnovit, aby bylo možné virtuální počítač vytvořit. Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret) .

Operace, jako je tajný klíč nebo převzetí klíče, nevyžadují tento krok a stejný Trezor klíčů je možné použít i po obnovení.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Můžu k virtuálnímu počítači získat přístup po obnovení z důvodu přerušeného vztahu mezi virtuálním počítačem a řadičem domény?

Ano, budete mít přístup k virtuálnímu počítači po obnovení, protože došlo k přerušení vztahu virtuálních počítačů s řadičem domény. Další informace najdete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) .

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
