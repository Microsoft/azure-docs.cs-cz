---
title: Zálohování virtuálních počítačů Azure – nejčastější dotazy
description: Odpovědi na běžné dotazy týkající se fungování zálohování virtuálních počítačů Azure, omezení a toho, co se stane při změnách zásad.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 6ec178a8cb457973f39ea2dd929a3486a7696c55
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972188"
---
# <a name="frequently-asked-questions-azure-backup"></a>Nejčastější dotazy – Azure Backup

Tento článek odpovědi na běžné dotazy týkající [Azure Backup](backup-introduction-to-azure-backup.md) služby.

## <a name="general-questions"></a>Obecné otázky


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Jaké virtuální počítače Azure můžete zálohovat pomocí služby Azure Backup?
[Kontrola](backup-azure-arm-vms-prepare.md#before-you-start) podporované operační systémy a omezení.



## <a name="backup"></a>Backup

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Úlohu zálohování na vyžádání používat stejný plán uchovávání jako plánovaní zálohování?
Ne. Měli byste určit rozsah uchování pro úlohu zálohování na vyžádání. Ve výchozím nastavení se uchovávají po dobu 30 dnů po aktivaci z portálu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Na některých virtuálních počítačích byla nedávno povolena služba Azure Disk Encryption. Budou moje zálohy stále fungovat?
Je třeba zadat oprávnění pro službu Azure Backup pro přístup k trezoru klíčů. Zadejte oprávnění v prostředí PowerShell, jak je popsáno v **povolit zálohování** tématu [zálohování prostředí Azure PowerShell](backup-azure-vms-automation.md) dokumentaci.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Můžu migrovat disky virtuálních počítačů na managed disks. Budou moje zálohy stále fungovat?
Ano, zálohy fungují. Není nutné nic překonfigurovat.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Proč nelze zobrazit v Průvodci nakonfigurujte zálohování virtuálního počítače?
Průvodce uvádí pouze virtuální počítače ve stejné oblasti jako trezor, a, který už se nezálohují.


### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Můj virtuální počítač je vypnutý. Bude na vyžádání nebo naplánované zálohování práce?
Ano. Zálohování se spustí, když se vypne počítač. Bod obnovení se označí jako selhání konzistentní vzhledem k aplikacím.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Můžete zrušit úlohu zálohování probíhá?
Ano. Můžete je zrušit úlohu zálohování v **pořizuje se snímek** stavu. Úlohu nelze zrušit, pokud probíhá přenos dat ze snímku.

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Můžu povoleno uzamčení skupiny prostředků na spravovaný disk Moje zálohy virtuálních počítačů. Budou moje zálohy stále fungovat?
Pokud skupinu prostředků, služby Azure Backup nelze odstranit starší body obnovení.
- Nových záloh se začnou nezdaří, protože je maximální limit 18 bodů obnovení.
- Pokud zálohování se nezdaří s interní chybou po uzamčení, [postupujte podle těchto kroků](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) odebrat kolekci bodů obnovení.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>Zásady zálohování za letního času (DST)?
Ne. Datum a čas v místním počítači je místní s aktuální letní použít. Času nastavenému pro naplánované zálohování se mohou lišit od místního času z důvodu letního času.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Kolik datových disků můžete připojit k virtuálnímu počítači zálohovat službou Azure Backup?
Azure Backup můžete zálohovat virtuální počítače s až 16 disků. Podpora pro 16 disků najdete v [nejnovější verzi](backup-upgrade-to-vm-backup-stack-v2.md) V2 v zásobníku zálohování virtuálních počítačů Azure.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Fakturuje se u Azure podporuje zálohování SSD spravovaných disků úrovně standard?
Azure Backup podporuje [SSD na úrovni standard managed disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). SSD spravované disky pro virtuální počítače Azure poskytují nový typ trvalého úložiště. Podporu pro disky SSD spravované je součástí [nejnovější verzi](backup-upgrade-to-vm-backup-stack-v2.md) V2 v zásobníku zálohování virtuálních počítačů Azure.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Můžete nám zálohování virtuálního počítače s diskem WA akcelerátor zápisu povolené?
Na disku povoleno WA nelze vytvářet snímky. Služba Azure Backup však můžete vyloučit disk WA povolené ze zálohy. Vyloučení disku pro virtuální počítače s podporou WA disky se podporuje jenom pro předplatná upgradovat na zásobník záloh virtuálních počítačů Azure V2. Upgrade na zásobník záloh virtuálních počítačů Azure V2, najdete v tomto [článku](backup-upgrade-to-vm-backup-stack-v2.md). Tato funkce je aktuálně dostupná v oblasti Japonsko – východ, Severní Evropa, jihovýchodní Asie, východní USA, západní USA 2, západní Evropa a východní USA 2.


### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Mám virtuálního počítače s disky zápisu akcelerátorů (WA) a nainstalovat SAP HANA. Jak můžu zálohovat?
Azure Backup nejde zálohovat disk WA povolené, ale můžete vyloučit ze zálohy. Zálohování však nebude poskytovat konzistence databáze, protože na disku povoleno WA informace se nezálohuje. Pokud chcete disk s operačním systémem, zálohování a zálohování disků, které nejsou povolené WA, můžete zálohování disků s touto konfigurací.

Máme verzi private preview pro zálohování SAP HANA s plánovaný bod obnovení do 15 minut. Je součástí podobným způsobem jako záloha databáze SQL a používá rozhraní backInt pro řešení třetích stran s certifikací SAP Hana. Pokud vás zajímá ve verzi private preview, pošlete nám e-mail na ` AskAzureBackupTeam@microsoft.com ` s předmětem **zaregistrovat k privátní verzi preview pro zálohování SAP HANA ve virtuálních počítačích Azure**.


## <a name="restore"></a>Obnovení

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Jak se rozhodnout, jestli se má obnovit jenom disky nebo úplné virtuálního počítače?
Představte si obnovení virtuálního počítače jako možnost pro rychlé vytvoření virtuálního počítače Azure. Tato možnost změní názvy disků, kontejnery, které používají disky, veřejné IP adresy a názvy síťových rozhraní. Změna udržuje jedinečný prostředky při vytvoření virtuálního počítače. Virtuální počítač není přidán do skupiny dostupnosti.

Možnost obnovení disku, pokud chcete:
  * Přizpůsobení virtuálního počítače, který se vytvoří. Například změna velikosti.
  * Přidat nastavení konfigurace, které nebyly existuje v době zálohování
  * Ovládací prvek zásady vytváření názvů pro prostředky, které jsou vytvořeny.
  * Přidáte virtuální počítač do skupiny dostupnosti.
  * Přidejte další nastavení, které musí být nakonfigurovaný pomocí Powershellu nebo šablony.  w

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Můžu provést obnovení zálohování nespravovaných disků virtuálních počítačů po upgradu na spravované disky?
Ano, můžete použít zálohy pořízené před disky se migrovaly z nespravovaných do spravovaných.
- Ve výchozím nastavení vytvoří úlohu obnovení virtuálního počítače nespravovaného virtuálního počítače.
- Však můžete obnovit disky a jejich používání při vytváření spravovaných virtuálních počítačů.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Jak obnovit virtuální počítač s bodem obnovení před migrací virtuálního počítače na managed disks?
Ve výchozím nastavení vytvoří úlohu obnovení virtuálního počítače s nespravovanými disky virtuálního počítače. Vytvoření virtuálního počítače se spravovanými disky:
1. [Obnovit do nespravované disky](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Převod obnovených disků na managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Vytvoření virtuálního počítače se spravovanými disky](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Další informace](backup-azure-vms-automation.md#restore-an-azure-vm) o to v prostředí PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Obnovit virtuální počítač, který je odstraněný
Ano. I v případě, že odstraníte virtuální počítač, můžete přejít na odpovídající zálohy v trezoru klikněte na ni a obnovení z bodu obnovení.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Jak obnovit virtuální počítač stejné skupiny dostupnosti?
Pro spravovaného disku virtuální počítač Azure obnovení do skupin dostupnosti je povoleno tím, že poskytuje možnost v šabloně při obnovování jako spravované disky. Tato šablona obsahuje vstupní parametr s názvem **dostupnosti**.

### <a name="how-do-we-get-faster-restore-performances"></a>Jak jsme získat rychlejší možnosti obnovení?
Pro rychlejší obnovení výkon, doporučujeme přesunout na zásobník záloh virtuálních počítačů V2 a používat [funkce rychlých RP](backup-upgrade-to-vm-backup-stack-v2.md).

## <a name="manage-vm-backups"></a>Správa záloh virtuálních počítačů

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Co se stane, když upravím zásady zálohování?
Virtuální počítač je zálohovány pomocí plán a uchovávání nastavení zásad nové nebo změněné.

- Pokud doba uchovávání prodlouží, existující body obnovení jsou označeny a uchovávat v souladu s novou zásadu.
- Pokud se doba uchovávání zkrátí, body obnovení označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Jak můžu přesunout zálohovat službou Azure Backup na jinou skupinu prostředků virtuálního počítače?

1. Dočasně zastavte zálohování a zachovat zálohovaná data.
2. Přesuňte virtuální počítač na cílovou skupinu prostředků.
3. Opětovně povolena zálohy v trezoru stejného nebo nové.

Virtuální počítač můžete obnovit z body obnovení k dispozici, které byly vytvořeny před přesunutím.
