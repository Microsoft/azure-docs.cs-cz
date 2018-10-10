---
title: Zálohování virtuálních počítačů Azure – nejčastější dotazy
description: Odpovědi na běžné dotazy týkající se fungování zálohování virtuálních počítačů Azure, omezení a toho, co se stane při změnách zásad.
services: backup
author: trinadhk
manager: shreeshd
keywords: azure vm backup, azure vm restore, backup policy
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: 910225e74fcd4655a9eff711d3ac1316d948c2b3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886190"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Dotazy týkající se služby Azure Backup pro virtuální počítače
Tento článek obsahuje odpovědi na běžné dotazy, které vám pomůžou rychle porozumět komponentám služby Azure Backup pro virtuální počítače. Některé odpovědi zahrnují odkazy na články obsahující komplexní informace. Otázky týkající se služby Azure Backup můžete také publikovat na [diskusním fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Konfigurace zálohování
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Podporují trezory Recovery Services klasické virtuální počítače nebo virtuální počítače využívající Resource Manager? <br/>
Trezory Recovery Services podporují oba modely.  Můžete zálohovat klasický virtuální počítač nebo virtuální počítač Resource Manageru do trezoru služby Recovery Services.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Jaký konfigurace zálohování virtuálních počítačů Azure nepodporuje?
Projděte si [podporované operační systémy](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) a [zálohování omezení virtuálních počítačů](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Proč se můj virtuální počítač nezobrazuje v průvodci konfigurací zálohování?
V Průvodci konfigurací zálohování Azure Backup uvádí pouze virtuální počítače, které jsou:
  * Ještě nejsou chráněné můžete ověřit stav zálohování virtuálního počítače tak, že přejdete do okna virtuálního počítače a kontrola stavu zálohování z nabídky nastavení. Další informace o [kontrole stavu zálohování virtuálního počítače](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu).
  * Patří do stejné oblasti jako virtuální počítač.

## <a name="backup"></a>Zálohování
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Bude úloha zálohování na vyžádání používat stejný plán uchovávání jako plánovaní zálohování?
Ne. Měli byste určit rozsah uchování pro úlohu zálohování na vyžádání. Ve výchozím nastavení se uchovávají po dobu 30 dnů po aktivaci z portálu.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Na některých virtuálních počítačích byla nedávno povolena služba Azure Disk Encryption. Budou moje zálohy stále fungovat?
Musíte službě Azure Backup udělit oprávnění pro přístup ke službě Key Vault. Tato oprávnění můžete zadat v PowerShellu pomocí kroků popsaných v části *Povolení zálohování* v dokumentaci k [PowerShellu](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Disky virtuálního počítače se migrovaly na spravované disky. Budou moje zálohy stále fungovat?
Ano, zálohy budou fungovat bez problémů a není potřeba znovu nakonfigurovat zálohování.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Můj virtuální počítač je vypnutý. Bude na vyžádání nebo naplánované zálohování práce?
Ano. I v případě, že počítač je vypnutý zálohy budou fungovat a bod obnovení je označen jako selhání konzistentní vzhledem k aplikacím. Další podrobnosti najdete v části konzistence dat v [v tomto článku](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Můžete zrušit úlohu zálohování probíhá?
Ano. Úloha zálohování můžete zrušit, pokud je ve fázi "Pořízení snímku". **Úlohu nelze zrušit, pokud probíhá přenos dat ze snímku**.

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Můžu povoleno uzamčení skupinu prostředků na spravovaný disk Moje zálohy virtuálních počítačů. Budou moje zálohy stále fungovat?
Pokud se uživatel uzamkne skupinu prostředků, služby Backup není možné odstranit starší body obnovení. Z toho důvodu začne nový zálohování selhávat, jako je stanovený limit maximální 18 bodů obnovení uložených z back-endu. Pokud je zálohování neúspěšné kvůli vnitřní chybě po uzamčení RG, postupujte podle těchto [kolekci bodů kroky k odebrání obnovení](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>Zásady zálohování trvá, než Time(DST) letního času ukládá do účtu?
Ne. Mějte na paměti, že datum a čas v místním počítači se zobrazí v místním čase a s vaší aktuální posun na letní čas. Proto může být nakonfigurovaný čas plánovaného zálohování liší od místního času z důvodu letního času.

### <a name="maximum-of-how-many-data-disks-can-i-attach-to-a-vm-to-be-backed-up-by-azure-backup"></a>Maximální počet datových disků můžete můžu připojit k virtuálnímu počítači zálohovat službou Azure Backup?
Azure Backup nyní podporuje zálohování virtuálních počítačů s disky až 32. Chcete-li získat 32 podporu pro disky, [upgrade na zásobník záloh virtuálních počítačů Azure V2](backup-upgrade-to-vm-backup-stack-v2.md). Všechny virtuální počítače povolit ochranu od 24. září 2018 bude získat podporované.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure podporuje zálohování SSD na úrovni Standard se spravovaný disk?
Azure Backup podporuje [Standard Managed SSD Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), nový typ trvalého úložiště pro Microsoft Azure Virtual machines. Je podporované u spravovaných disků na [zásobníku zálohování virtuálních počítačů Azure V2](backup-upgrade-to-vm-backup-stack-v2.md).

## <a name="restore"></a>Obnovení
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Jak se rozhodnout mezi obnovením disků a úplným obnovením virtuálního počítače?
Azure úplné obnovení virtuálního počítače můžete představit jako možnost pro rychlé vytvoření. Obnovení virtuálních počítačů možnost změny názvy disků, kontejnery, které používají tyto disky, veřejné IP adresy a názvy síťových rozhraní. Tato změna je nutné k údržbě jedinečnosti prostředků vytvořených během vytváření virtuálních počítačů. Ale virtuální počítač nebude přidána do skupiny dostupnosti.

Obnovení disků použijte k:
  * Přizpůsobení virtuálního počítače vytvářeného z bodu v čase konfiguraci jako změny velikosti
  * Přidání konfigurací, které nejsou k dispozici v době zálohování
  * Řízení zásad vytváření názvů pro vytvářené prostředky.
  * Přidání virtuálního počítače do skupiny dostupnosti.
  * Pro ostatní konfigurace, které lze dosáhnout jenom pomocí Powershellu nebo definice deklarativní šablony

### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Můžete použít zálohování nespravovaných disků virtuálních počítačů k obnovení po upgradu Moje disky na managed disks?
Ano, můžete použít zálohy pořízené před migraci disků z nespravovaných do spravovaných. Ve výchozím nastavení bude úloha obnovení virtuálního počítače s nespravovanými disky vytvoření virtuálního počítače. Obnovit disky a jejich používání při vytváření virtuálního počítače na spravovaných discích můžete použít funkci obnovení disků.

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Jaký je postup obnovení virtuálního počítače do bodu obnovení provést před provedením převod z nespravovaných do spravovaných disků pro virtuální počítač?
V tomto scénáři ve výchozím nastavení, úloha obnovení virtuálního počítače bude vytvoření virtuálního počítače s nespravovanými disky. Vytvoření virtuálního počítače se spravovanými disky:
1. [Obnovit do nespravované disky](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Převod obnovených disků na managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Vytvoření virtuálního počítače se spravovanými disky](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
Rutiny Powershellu, přečtěte si [tady](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Můžete obnovit virtuální počítač, pokud se odstraní virtuální počítač?
Ano. Životního cyklu virtuálního počítače a jeho odpovídající zálohovaná položka se liší. Takže i v případě, že odstraníte virtuální počítač, můžete přejít na odpovídající položku v trezoru služby Recovery Services zálohování a spustit obnovení pomocí jednoho z bodů obnovení.

## <a name="manage-vm-backups"></a>Správa záloh virtuálních počítačů
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Co se stane, když že se na virtuálních počítačích změní zásady zálohování?
Pokud na virtuálních počítačích změní se použije nová zásada, plán a uchovávání nové zásady následuje. Pokud se doba uchovávání prodlouží, existující body obnovení se označí k zachování pro novou zásadu. Pokud se doba uchovávání zkrátí, označí se k vyřazení v rámci další úlohy čištění a následně se odstraní.

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Jak si můžu přesunout že virtuální počítač zaregistrovat ve službě Azure backup mezi skupinami prostředků?
Postupujte podle níže uvedený postup úspěšně přesunout virtuální počítač zálohovaných do cílová skupina prostředků
1. Dočasně zastavit zálohování a uchování zálohovaných dat
2. Přesuňte virtuální počítač na cílovou skupinu prostředků
3. Znovunastavení ochrany pomocí stejné/nový trezor

Uživatelé mohou obnovit z bodů obnovení k dispozici vytvořené před přesunutím.
