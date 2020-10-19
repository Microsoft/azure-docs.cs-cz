---
title: Nejčastější dotazy k zálohování virtuálních počítačů Azure
description: V tomto článku najdete odpovědi na běžné dotazy týkající se zálohování virtuálních počítačů Azure pomocí služby Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: f318d785fdfa5b72050bdd805ecfe801d307b9a7
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172839"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Nejčastější dotazy – zálohování virtuálních počítačů Azure

Tento článek obsahuje odpovědi na běžné dotazy týkající se zálohování virtuálních počítačů Azure pomocí služby [Azure Backup](./backup-overview.md) .

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Které image virtuálních počítačů se můžou při vytváření záloh povolit?

Když vytváříte virtuální počítač, můžete povolit zálohování pro virtuální počítače s [podporovanými operačními systémy](backup-support-matrix-iaas.md#supported-backup-actions).

### <a name="why-initial-backup-is-taking-lot-of-time-to-complete"></a>Proč dokončení počátečního zálohování trvá dlouho?

Prvotní zálohování je vždy úplné zálohování a bude záviset na velikosti dat a při zpracování zálohy. <br>
Pokud chcete zlepšit výkon zálohování, Projděte si [osvědčené postupy pro zálohování](./backup-azure-vms-introduction.md#best-practices). [Předpoklady zálohování](./backup-azure-vms-introduction.md#backup-and-restore-considerations) a [výkon zálohování](./backup-azure-vms-introduction.md#backup-performance)<br>
Přestože je celková doba zálohování v případě přírůstkového zálohování kratší než 24 hodin, u prvního zálohování to platit nemusí.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Jsou náklady na zálohování zahrnuty do nákladů na virtuální počítače?

Ne. Náklady na zálohování jsou oddělené od nákladů na virtuální počítače. Přečtěte si další informace o [cenách Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Která oprávnění jsou nutná k povolení zálohování pro virtuální počítač?

Pokud jste přispěvatelem virtuálních počítačů, můžete na virtuálním počítači povolit zálohování. Pokud používáte vlastní roli, budete potřebovat následující oprávnění, abyste mohli na virtuálním počítači povolit zálohování:

- Microsoft. RecoveryServices/trezory/Write
- Microsoft. RecoveryServices/trezory/číst
- Microsoft. RecoveryServices/umístění/*
- Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/*/Read
- Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Read
- Microsoft. RecoveryServices/trezory/backupFabrics/protectionContainers/protectedItems/Write
- Microsoft. RecoveryServices/trezory/backupFabrics/backupProtectionIntent/Write
- Microsoft. RecoveryServices/trezory/backupPolicies/Read
- Microsoft. RecoveryServices/trezory/backupPolicies/Write

Pokud má váš Recovery Services trezor a virtuální počítač jiné skupiny prostředků, ujistěte se, že máte oprávnění k zápisu do skupiny prostředků pro trezor Recovery Services.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Používá úloha zálohování na vyžádání stejný plán uchovávání dat jako plánované zálohy?

Ne. Zadejte rozsah uchování pro úlohu zálohování na vyžádání. Ve výchozím nastavení se po aktivaci z portálu uchovávají po dobu 30 dnů.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Na některých virtuálních počítačích byla nedávno povolena služba Azure Disk Encryption. Budou moje zálohy stále fungovat?

Poskytněte oprávnění Azure Backup pro přístup k Key Vault. V prostředí PowerShell zadejte oprávnění, jak je popsáno v části **Povolení zálohování** v dokumentaci k [prostředí Azure Backup PowerShell](backup-azure-vms-automation.md) .

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Migrovali jsem disky virtuálních počítačů na Managed disks. Budou moje zálohy stále fungovat?

Ano, zálohování funguje bez problémů. Není nutné nic znovu konfigurovat.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Proč se můj virtuální počítač nezobrazuje v průvodci konfigurací zálohování?

Průvodce zobrazí jenom virtuální počítače ve stejné oblasti jako trezor, které ještě nejsou zálohované.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Virtuální počítač je vypnutý. Bude aplikace na vyžádání nebo plánované zálohování fungovat?

Yes. Zálohování se spustí při vypnutí počítače. Bod obnovení je označen jako konzistentní se selháním.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Můžu zrušit probíhající úlohu zálohování?

Yes. Úlohu zálohování můžete zrušit ve stavu **pořizování snímků** . Pokud probíhá přenos dat z snímku, nemůžete úlohu zrušit.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Aktivoval (a) jsem zámek pro skupinu prostředků vytvořenou službou Azure Backup (například `AzureBackupRG_<geo>_<number>` ). Budou moje zálohy stále fungovat?

Pokud zamknete skupinu prostředků vytvořenou službou Azure Backup, zálohování začnou selhat, protože je k dispozici maximální limit 18 bodů obnovení.

Odeberte zámek a vymažte kolekci bodů obnovení z dané skupiny prostředků, aby bylo možné obnovit budoucí zálohy. [Pomocí těchto kroků](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) odeberte kolekci bodů obnovení.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Podporuje Azure Backup standardní disky se správou SSD?

Ano, Azure Backup podporuje [Standard SSD spravované disky](https://docs.microsoft.com/azure/virtual-machines/disks-types#standard-ssd).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Můžeme zálohovat virtuální počítač s diskem s povoleným Akcelerátor zápisu (WA)?

Na disku s podporou WA se snímky nedají považovat. Služba Azure Backup však může ze zálohy vyloučit disk s podporou WA.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mám virtuální počítač s disky Akcelerátor zápisu (WA) a SAP HANA nainstalované. Návody zálohování?

Azure Backup nemůže zálohovat disk s podporou WA, ale může ho vyloučit ze zálohy. Zálohování ale neposkytne konzistenci databáze, protože informace na disku s podporou WA se nezálohují. Disky s touto konfigurací můžete zálohovat, pokud chcete zálohovat disk s operačním systémem a zálohovat disky, u kterých není povolený režim WA.

Azure Backup poskytuje řešení zálohování pro služby streamování pro databáze SAP HANA s PLÁNOVANÝm zápisem na 15 minut. Backint je certifikováno pro SAP, aby poskytoval nativní podporu zálohování s využitím nativních rozhraní API SAP HANA. Přečtěte si další informace [o zálohování SAP HANA databází na virtuálních počítačích Azure](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Jaká je maximální prodleva, kterou je možné očekávat v čase zahájení zálohování z naplánovaného času zálohování, který jsem nastavil v zásadách zálohování virtuálních počítačů?

Naplánované zálohování se spustí do 2 hodin plánovaného času zálohování. Pokud má například 100 virtuálních počítačů čas zahájení zálohování naplánovaný na 2:00 AM, pak podle 4:00 na nejnovějším virtuálním počítači s 100 bude úloha zálohování probíhat. Pokud bylo naplánované zálohování pozastaveno z důvodu výpadku a obnovení nebo opakování, pak může zálohování začít mimo toto plánované dva hodiny.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Jaký je minimální povolený rozsah uchování pro denní bod zálohy?

Zásady zálohování virtuálních počítačů Azure podporují minimální rozsah uchování od sedmi dnů až po 9999 dnů. Jakákoli změna existující zásady zálohování virtuálního počítače s méně než sedmi dny bude vyžadovat aktualizaci, aby splňovala minimální dobu uchování sedmi dnů.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Co se stane, když změníte případ názvu svého virtuálního počítače nebo skupiny prostředků virtuálních počítačů?

Pokud změníte velikost písmen (na vyšší nebo nižší) vašeho virtuálního počítače nebo skupiny prostředků virtuálních počítačů, nebudete moct změnit velikost názvu zálohované položky. Očekává se ale Azure Backup chování. Změna velikosti písmen se nezobrazí v zálohovaných položkách, ale aktualizuje se v back-endu.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Můžu zálohovat nebo obnovit selektivní disky připojené k virtuálnímu počítači?

Azure Backup teď podporuje zálohování a obnovení selektivního disku pomocí řešení zálohování virtuálních počítačů Azure. Další informace najdete v tématu [selektivní zálohování disku a obnovení pro virtuální počítače Azure](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Jsou spravované identity zachované, pokud během zálohování dojde ke změně tenanta?

Pokud dojde ke [změnám tenanta](/azure/devops/organizations/accounts/change-azure-ad-connection) , budete muset zakázat a znovu povolit [spravované identity](../active-directory/managed-identities-azure-resources/overview.md) , aby bylo zálohování znovu fungovat.

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

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Pokud se při obnovení nepovede vytvořit virtuální počítač, co se stane s disky zahrnutými do obnovení?

V případě obnovení spravovaného virtuálního počítače i v případě, že se virtuální počítač nepodaří vytvořit, budou disky obnoveny.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Můžu obnovit odstraněný virtuální počítač?

Yes. I když virtuální počítač odstraníte, můžete přejít na odpovídající zálohovanou položku v trezoru a obnovit z bodu obnovení.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Návody obnovit virtuální počítač do stejných skupin dostupnosti?

U virtuálních počítačů Azure se spravovanými disky je obnovení do skupin dostupnosti umožněno tím, že v šabloně při obnovení jako spravované disky poskytnete možnost. Tato šablona obsahuje vstupní parametr s názvem **skupiny dostupnosti**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak získám rychlejší obnovení?

Možnost [okamžitého obnovení](backup-instant-restore-capability.md) pomáhá při rychlejším zálohování a okamžitém obnovení ze snímků.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Co se stane, když změníme nastavení trezoru klíčů pro šifrovaný virtuální počítač?

Po změně nastavení trezoru klíčů pro šifrovaný virtuální počítač budou zálohy nadále fungovat s novou sadou podrobností. Po obnovení z bodu obnovení před změnou budete muset tajné klíče obnovit v trezoru klíčů předtím, než budete moct vytvořit virtuální počítač z něj. Další informace najdete v tomto [článku](./backup-azure-restore-key-secret.md).

Operace, jako je tajný klíč nebo klíčová implementace, nevyžadují tento krok a stejný Trezor klíčů je možné použít i po obnovení.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Můžu k virtuálnímu počítači získat přístup po obnovení z důvodu přerušeného vztahu mezi virtuálním počítačem a řadičem domény?

Ano, budete mít přístup k virtuálnímu počítači po obnovení, protože došlo k přerušení vztahu virtuálních počítačů s řadičem domény. Další informace najdete v tomto [článku](./backup-azure-arm-restore-vms.md#post-restore-steps) .

### <a name="why-restore-operation-is-taking-long-time-to-complete"></a>Proč dokončení operace obnovení trvá dlouho?

Celková doba obnovení závisí na vstupně-výstupních operacích za sekundu (IOPS) a propustnosti účtu úložiště. Celková doba obnovení může být ovlivněna v případě, že je cílový účet úložiště načten s jinými operacemi čtení a zápisu aplikace. Pokud chcete zlepšit operaci obnovení, vyberte účet úložiště, který není načtený s ostatními aplikačními daty.

## <a name="manage-vm-backups"></a>Správa záloh virtuálních počítačů

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co se stane, když upravím zásady zálohování?

Virtuální počítač se zálohuje pomocí nastavení plánu a uchování v upravené nebo nové zásadě.

- Pokud je uchovávání dat rozšířené, existující body obnovení jsou označeny a udržovány v souladu s novou zásadou.
- Pokud je uchovávání dat sníženo, body obnovení jsou označeny pro vyřazení v další úloze čištění a následně odstraněny.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Návody přesunout virtuální počítač zálohovaný pomocí Azure Backup do jiné skupiny prostředků?

1. Dočasné zastavení zálohování a uchování zálohovaných dat.
2. Pokud chcete přesunout virtuální počítače nakonfigurované s Azure Backup, proveďte následující kroky:

   1. Najděte umístění virtuálního počítače.
   2. Vyhledejte skupinu prostředků s následujícím vzorem pojmenování: `AzureBackupRG_<location of your VM>_1` . Například *AzureBackupRG_westus2_1*
   3. V Azure Portal zaškrtnout **Zobrazit skryté typy**.
   4. Vyhledejte prostředek typu **Microsoft. COMPUTE/restorePointCollections** , který má vzor pojmenování `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Odstranit tento prostředek. Tato operace odstraní pouze rychlé body obnovení, nikoli zálohovaná data v trezoru.
   6. Po dokončení operace odstranění můžete virtuální počítač přesunout.

3. Přesuňte virtuální počítač do cílové skupiny prostředků.
4. Obnovte zálohu.

Virtuální počítač můžete obnovit z dostupných bodů obnovení, které byly vytvořeny před operací přesunutí.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Co se stane po přesunu virtuálního počítače do jiné skupiny prostředků?

Po přesunutí virtuálního počítače do jiné skupiny prostředků se jedná o nový virtuální počítač, který je v Azure Backup.

Po přesunutí virtuálního počítače do nové skupiny prostředků můžete virtuální počítač znovu chránit buď ve stejném trezoru, nebo v jiném trezoru. Vzhledem k tomu, že se jedná o nový virtuální počítač pro Azure Backup, bude se vám účtovat samostatně.

V případě potřeby budou obnoveny body obnovení starého virtuálního počítače. Pokud tato zálohovaná data nepotřebujete, můžete zastavit ochranu původního virtuálního počítače pomocí odstranit data.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Existuje omezení počtu virtuálních počítačů, které mohou být přidruženy ke stejné zásadě zálohování?

Ano, existuje limit 100 virtuálních počítačů, které se dají přidružit ke stejné zásadě zálohování z portálu. Doporučujeme, abyste pro více než 100 virtuálních počítačů vytvořili více zásad zálohování se stejným plánem nebo jiným plánem.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Jak si můžu zobrazit nastavení uchovávání záloh?

V současné době můžete nastavení uchování zobrazit na úrovni zálohované položky na základě zásady zálohování, která je přiřazená k virtuálnímu počítači.

Jedním ze způsobů, jak zobrazit nastavení uchovávání záloh, je přejít na [řídicí panel](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) zálohovaných položek pro váš virtuální počítač v Azure Portal. Když vyberete odkaz na zásady zálohování, můžete si prohlédnout dobu uchovávání všech denních, týdenních, měsíčních a ročních bodů uchování přidružených k virtuálnímu počítači.

Pomocí [Průzkumníka služby Backup](./monitor-azure-backup-with-backup-explorer.md) můžete také zobrazit nastavení uchovávání všech virtuálních počítačů v jednom podokně skla. Přejděte do Průzkumníka zálohování z libovolného trezoru Recovery Services, přejděte na kartu **zálohované položky** a kliknutím na rozšířené zobrazení zobrazte podrobné informace o uchovávání dat pro jednotlivé virtuální počítače.