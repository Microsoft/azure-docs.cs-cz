---
title: Řešení potíží se službou Azure VM File Recovery
description: Řešení potíží při obnovování souborů a složek ze zálohy virtuálního počítače Azure
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605288"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Řešení potíží s obnovením souborů zálohování virtuálních počítačů Azure

Tento článek popisuje postup řešení potíží, který vám může při obnovování souborů a složek ze zálohy virtuálního počítače Azure pomáhat při řešení problémů s Azure Backup.

## <a name="common-error-messages"></a>Běžné chybové zprávy

### <a name="exception-caught-while-connecting-to-target"></a>Při připojování k cíli se zachytila výjimka.

**Možná příčina**: skript nemůže získat přístup k bodu obnovení.

**Doporučená akce**: Ověřte, zda počítač splňuje všechny požadavky na [přístup](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Cíl se už přihlásil přes relaci iSCSI.

**Možná příčina**: skript již byl spuštěn na stejném počítači a jednotky byly připojeny.

**Doporučená akce**: svazky bodu obnovení již byly připojeny. Nemusí být připojené se stejnými písmeny jednotky původního virtuálního počítače. Procházejte všemi dostupnými svazky v Průzkumníkovi souborů.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Tento skript je neplatný, protože disky byly odpojeny přes portál nebo překročily limit 12 hodin. Stažení nového skriptu z portálu

**Možná příčina**: disky byly odpojeny z portálu nebo byl překročen limit 12 hodin.

**Doporučená akce**: skript je po uplynutí 12 hodin od jeho stažení neplatný a nedá se spustit. Přejděte na portál a Stáhněte si nový skript, abyste mohli pokračovat v obnovování souborů.

## <a name="troubleshooting-common-scenarios"></a>Řešení potíží s běžnými scénáři

V této části najdete postup řešení problémů, ke kterým může docházet při stahování a spouštění skriptu pro obnovení souborů.

### <a name="cant-download-the-script"></a>Skript nejde stáhnout.

Doporučená akce:

1. Ujistěte se, že máte všechna [potřebná oprávnění ke stažení skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Ujistěte se, že existuje připojení k IP adresám cíle Azure.

Chcete-li ověřit připojení, spusťte jeden z následujících příkazů z příkazového řádku se zvýšenými oprávněními.

`nslookup download.microsoft.com`

nebo

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skript se úspěšně stáhne, ale nespustí se.

#### <a name="for-sles-12-sp4-os-linux"></a>Pro SLES 12 SP4 OS (Linux)

**Chyba**: iscsi_tcp_module Nenalezeno

**Možná příčina**: při spuštění skriptu Pythonu pro obnovení na úrovni položek (ilr) v operačním systému SUSE Linux verze 12sp4 dojde k chybě **_modul iscsi_tcp nelze načíst_* _.

Modul ILR používá _ *iscsi_tcp** k navázání připojení TCP ke službě zálohování. V rámci verze 12SP4 se SUSE **iscsi_tcp** odebral z balíčku Open-iSCSI, takže operace ilr se nezdařila.

**Doporučená akce**: spuštění skriptu obnovení souborů není podporované na virtuálních počítačích s SUSE 12SP4. Zkuste operaci obnovení provést na starší verzi SUSE 12SP4.

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Skript se spustí, ale připojení k cíli iSCSI selhalo.

**Chyba**: při připojování k cíli se zachytila výjimka.

1. Ujistěte se, že počítač, ve kterém je spuštěný skript, splňuje všechny [požadavky na přístup](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Ověřte připojení k cílovým IP adresám Azure.
Chcete-li ověřit připojení, spusťte jeden z následujících příkazů z příkazového řádku se zvýšenými oprávněními.

   `nslookup download.microsoft.com`

   nebo

   `ping download.microsoft.com`
1. Ujistěte se, že máte přístup k odchozímu portu iSCSI 3260.
1. Ujistěte se, že neexistuje žádná brána firewall nebo NSG, která blokuje provoz na IP adresy cíle Azure nebo na adresy URL služby Recovery Services.
1. Zkontroluje, jestli antivirová ochrana blokuje spuštění skriptu.

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>Připojeno k bodu obnovení, ale disky se nepřipojily

Ujistěte se, že máte [správný počítač pro spuštění skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) .

#### <a name="on-a-windows-vm"></a>Na virtuálním počítači s Windows

**Fond úložiště na virtuálním počítači se připojí v režimu jen pro čtení**: ve Windows 2012 R2 a Windows 2016 (s fondy úložiště) při prvním spuštění skriptu může přejít do stavu jen pro čtení.

Aby bylo možné tento problém vyřešit, je nutné ručně nastavit Read-Write přístup k fondu úložiště poprvé a připojit virtuální disky. Postupujte následovně:

1. Nastavte přístup Read-Write.

   Přejděte na **Správce serveru**  >  **souborové fondy úložiště a souborové služby úložiště**  >    >  .

   ![Úložiště Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. V okně **fond úložiště** klikněte pravým tlačítkem na dostupný fond úložiště a vyberte možnost **nastavit Read-Write přístup** .

   ![Zápis pro čtení do úložiště Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Po přiřazení fondu úložiště s přístupem pro čtení a zápis Připojte virtuální disk.

   Přejděte na **Správce serveru uživatelské rozhraní**. V části **virtuální disky** > kliknutím pravým tlačítkem myši vyberte možnost **připojit virtuální disk** .

   ![Virtuální disk správce serveru](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>Na virtuálním počítači se systémem Linux

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>Obnovení souboru se nepodaří automaticky připojit, protože disk neobsahuje svazky.

Při provádění obnovení souborů služba zálohování detekuje svazky a automounts. Pokud však jsou v zálohovaných discích nezpracované oddíly, pak tyto disky nejsou automaticky připojeny a datový disk nelze pro obnovení zobrazit.

Chcete-li tento problém vyřešit, postupujte podle kroků popsaných v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>OPERAČNÍ systém nemohl identifikovat systém souborů, který způsobil selhání obnovení souboru systému Linux během připojování disků.

Při spouštění skriptu pro obnovení souborů se datový disk nepodařilo připojit s následující chybou:

"Následující oddíly se nepovedlo připojit, protože operační systém nemohl identifikovat systém souborů."

Pokud chcete tento problém vyřešit, ověřte, jestli je svazek zašifrovaný pomocí aplikace třetí strany. Pokud je šifrovaný, disk nebo virtuální počítač se na portálu nezobrazí jako zašifrovaný.

1. Přihlaste se k zálohovanému virtuálnímu počítači a spusťte příkaz:

   `*lsblk -f*`

   ![Disk bez svazku](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Ověřte systém souborů a šifrování.
3. Pokud je svazek zašifrovaný, obnovení souborů se nepodporuje. [Přečtěte si další informace](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Disky jsou připojené, ale nemůžou připojit svazky.

- Ujistěte se, že máte [správný počítač pro spuštění skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

#### <a name="on-windows-vms"></a>Na virtuálních počítačích s Windows

Při spuštění skriptu pro obnovení souborů pro Windows se zobrazí zpráva, že **je připojený tento svazek**: * 0. Disky jsou ale zjištěné v konzole pro správu disků. Při připojování svazků přes iSCSI se některé zjištěné svazky přejdou do stavu offline. Když kanál iSCSI komunikuje mezi virtuálním počítačem a službou, detekuje tyto svazky a převede je do režimu online, ale nejsou připojené.

   ![Disk není připojený.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Chcete-li tento problém identifikovat a vyřešit, proveďte následující kroky:

1. V okně příkazového řádku pomocí příkazu **diskmgmt** * spusťte příkaz *Správa disků**.
1. Ověřte, zda jsou zobrazeny žádné další disky. V následujícím příkladu je disk 2 další disk.

   ![Disk management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Pravým tlačítkem myši klikněte na **nový svazek** a vyberte **změnit písmeno jednotky a cestu**.

   ![Disk management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. V okně **Přidat písmeno jednotky nebo cestu** vyberte **přiřadit následující písmeno jednotky** a přiřaďte dostupnou jednotku a vyberte **OK**.

   ![Disk management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. V Průzkumníku souborů si prohlédněte písmeno jednotky, které jste zvolili, a prozkoumejte soubory.

#### <a name="on-linux-vms"></a>Na virtuálních počítačích se systémem Linux

- Ujistěte se, že máte [správný počítač pro spuštění skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Pokud chráněný virtuální počítač se systémem Linux používá LVM nebo pole RAID, postupujte podle kroků uvedených v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

### <a name="cant-copy-the-files-from-mounted-volumes"></a>Nejde zkopírovat soubory z připojených svazků.

Kopírování může selhat s chybou **0x80070780: k souboru nelze přistupovat systém.** V takovém případě Ověřte, jestli je na zdrojovém serveru povolená funkce odstranění duplicitních dat na disku. Pak zajistěte, aby měl server pro obnovení duplicity na jednotkách povolený. Roli odstranění duplicitních dat můžete nechat nenakonfigurovanou, takže nebudete jednotky odstraňovat na serveru pro obnovení.

## <a name="next-steps"></a>Další kroky

- [Obnovení souborů a složek ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md)
