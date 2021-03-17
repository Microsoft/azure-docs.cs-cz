---
title: Řešení potíží se službou Azure VM File Recovery
description: Řešení potíží při obnovování souborů a složek ze zálohy virtuálního počítače Azure
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700299"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>Řešení potíží s obnovením souborů zálohování virtuálních počítačů Azure

Tento článek popisuje kroky pro řešení potíží, které vám pomůžou vyřešit problémy obnovování souborů a složek ze zálohy virtuálních počítačů Azure.

## <a name="common-error-messages"></a>Běžné chybové zprávy

V této části najdete postup řešení potíží s chybovými zprávami, které se mohou zobrazit.

### <a name="exception-caught-while-connecting-to-target"></a>"Při připojování k cíli byla zachycena výjimka"

**Možná příčina**: skript nemůže získat přístup k bodu obnovení.

**Doporučená akce**: Pokud chcete tento problém vyřešit, postupujte podle kroků uvedených ve [skriptu, ale připojení selhalo](#the-script-runs-but-the-connection-to-the-iscsi-target-failed).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"Cíl se už přihlásil prostřednictvím relace iSCSI."

**Možná příčina**: skript již byl spuštěn na stejném počítači a jednotky byly připojeny.

**Doporučená akce**: svazky bodu obnovení již byly připojeny. Nejde je připojit se stejnými písmeny jednotky původního virtuálního počítače. Procházejte dostupné svazky v Průzkumníkovi souborů.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Tento skript je neplatný, protože disky byly odpojeny přes portál nebo překročily limit 12 hodin. Stažení nového skriptu z portálu "

**Možná příčina**: disky byly odpojeny z portálu nebo byl překročen časový limit 12 hodin.

**Doporučená akce**: 12 hodin po stažení skriptu je neplatných a nedá se spustit. Přejděte na portál a pak stáhněte nový skript, abyste mohli pokračovat v obnovování souborů.

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>Modul iscsi_tcp nejde načíst (nebo) Modul iscsi_tcp_module se nenašel

**Doporučená akce**: Chcete-li tento problém vyřešit, postupujte podle kroků v [části stažení skriptu úspěšně, ale spuštění se nezdaří](#the-script-downloads-successfully-but-fails-to-run).

## <a name="common-problems"></a>Běžné problémy

V této části najdete postup řešení běžných problémů, ke kterým může dojít při stahování a spouštění skriptu pro obnovení souboru.

### <a name="you-cant-download-the-script"></a>Nemůžete stáhnout skript.

1. Ujistěte se, že máte [potřebná oprávnění ke stažení skriptu](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script).
1. Ověřte připojení k IP adresám cíle Azure. Z příkazového řádku se zvýšenými oprávněními spusťte jeden z následujících příkazů:

   `nslookup download.microsoft.com`

    nebo

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skript se úspěšně stáhne, ale nespustí se.

Když spustíte skript Pythonu pro obnovení na úrovni položek (ILR) v SUSE Linux Enterprise Server 12 SP4, dojde k chybě s chybou "iscsi_tcp modul nejde načíst" nebo "iscsi_tcp_module nebyl nalezen".

**Možná příčina**: modul ILR používá **iscsi_tcp** k navázání připojení TCP ke službě zálohování. V rámci verze SLES 12 SP4 odstranil SUSE z balíčku Open-iSCSI **iscsi_tcp** , takže operace ilr se nezdařila.

**Doporučená akce**: spuštění skriptu obnovení souborů není podporované na virtuálních počítačích s SUSE 12 SP4. Zkuste operaci obnovení provést na starší verzi SUSE 12 SP4.

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>Skript se spustí, ale připojení k cíli iSCSI selhalo.

Při připojování k cíli se může zobrazit chybová zpráva "výjimka byla zachycena.

1. Ujistěte se, že počítač, ve kterém je spuštěný skript, splňuje [požadavky na přístup](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script).
1. Ověřte připojení k IP adresám cíle Azure. Z příkazového řádku se zvýšenými oprávněními spusťte jeden z následujících příkazů:

   `nslookup download.microsoft.com`

   nebo

   `ping download.microsoft.com`
1. Ujistěte se, že máte přístup k odchozímu portu iSCSI 3260.
1. Ověřte, jestli brána firewall nebo NSG blokuje provoz na IP adresy cíle Azure nebo na adresy URL služby Recovery Services.
1. Ujistěte se, že váš antivirový software neblokoval spuštění skriptu.

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>Jste připojení k bodu obnovení, ale nepřipojili jsme disky.

Vyřešte tento problém podle kroků pro váš operační systém.

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Obnovení souborů systému Windows na serveru s fondy úložiště se nezdařilo.

Při prvním spuštění skriptu na Windows Serveru 2012 R2 a Windows serveru 2016 (s fondy úložiště) se může fond úložiště připojit k virtuálnímu počítači jen pro čtení.

>[!Tip]
> Ujistěte se, že máte [správný počítač pro spuštění skriptu](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Tento problém vyřešíte tak, že ručně přiřadíte ke fondu úložiště přístup pro čtení i zápis a připojíte virtuální disky:

1. Přejít na **Správce serveru**  >  **souborové fondy úložiště a souborové služby úložiště**  >    >  .

   ![Snímek obrazovky s možnostmi fondů úložiště](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. V okně **fond úložiště** klikněte pravým tlačítkem na dostupný fond úložiště a vyberte **nastavit přístup Read-Write**.

   ![Snímek obrazovky s možnostmi kliknutí pravým tlačítkem myši pro zařazování úložiště](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Až bude fond úložiště přiřazený k přístupu pro čtení i zápis, klikněte pravým tlačítkem myši v části **virtuální disky** a vyberte **připojit virtuální disk**.

   ![Snímek obrazovky zobrazující možnosti pro virtuální disk pravým tlačítkem myši](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Obnovení souborů systému Linux se nepodařilo automaticky připojit, protože disk neobsahuje svazky.

Při provádění obnovení souborů služba zálohování detekuje svazky a automaticky se připojí. Pokud však jsou v zálohovaných discích nezpracované oddíly, tyto disky nejsou automaticky připojeny a datový disk nelze pro obnovení zobrazit.

Tento problém vyřešíte tak, že přejdete na [obnovit soubory ze zálohy virtuálního počítače Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Obnovení souboru pro Linux se nepovede, protože operační systém nemohl identifikovat systém souborů.

Když spustíte skript pro obnovení souborů, datový disk se nemůže připojit. Zobrazí se chybová zpráva: následující oddíly se nepodařilo připojit, protože došlo k chybě operačního systému při pokusu o identifikaci systému souborů.

Pokud chcete tento problém vyřešit, ověřte, jestli je svazek zašifrovaný pomocí aplikace třetí strany. Pokud je šifrovaný, disk nebo virtuální počítač se na portálu nezobrazí jako zašifrovaný.

1. Přihlaste se k zálohovanému virtuálnímu počítači a spusťte tento příkaz:

   `lsblk -f`

   ![Snímek obrazovky zobrazující výsledky příkazu pro výpis blokovaných zařízení](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. Ověřte systém souborů a šifrování. Pokud je svazek zašifrovaný, obnovení souborů se nepodporuje. Další informace najdete v [matrici podpory pro zálohování virtuálních počítačů Azure](./backup-support-matrix-iaas.md#support-for-file-level-restore).

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>Disky jsou připojené, ale svazky nejsou připojené.

Vyřešte tento problém podle kroků pro váš operační systém.

#### <a name="windows"></a>Windows

Když spustíte skript pro obnovení souborů pro Windows, zobrazí se zpráva "0 připojené svazky obnovení". Disky jsou ale zjištěné v konzole pro správu disků.

**Možná příčina**: když jste připojili svazky přes iSCSI, některé svazky, které se zjistily, přešly do offline režimu. Když kanál iSCSI komunikuje mezi virtuálním počítačem a službou, detekuje tyto svazky a převede je do režimu online, ale nejsou připojené.

   ![Snímek obrazovky znázorňující připojené svazky pro obnovení 0](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Chcete-li tento problém identifikovat a vyřešit, proveďte následující kroky:

>[!Tip]
>Ujistěte se, že máte [správný počítač pro spuštění skriptu](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

1. V okně **příkazového** řádku spusťte příkaz **diskmgmt** a spusťte **správu disků**.
1. Vyhledejte všechny další disky. V následujícím příkladu je **disk 2** další disk.

   ![Snímek obrazovky s oknem správy disků s dalším diskem](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Klikněte pravým tlačítkem na **nový svazek** a pak vyberte **změnit písmeno jednotky a cestu**.

   ![Snímek obrazovky s možnostmi kliknutí pravým tlačítkem myši na další disk](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. V okně **změnit písmeno jednotky nebo cestu** vyberte **přiřadit následující písmeno jednotky**, přiřaďte dostupnou jednotku a pak vyberte **OK**.

   ![Snímek obrazovky okna změnit písmeno jednotky nebo cestu](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. Otevřete Průzkumníka souborů, kde můžete zobrazit jednotku, kterou jste zvolili, a prozkoumat soubory.

#### <a name="linux"></a>Linux

>[!Tip]
>Ujistěte se, že máte [správný počítač pro spuštění skriptu](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

Pokud chráněný virtuální počítač se systémem Linux používá LVM nebo pole RAID, postupujte podle kroků v části [obnovení souborů ze zálohy virtuálního počítače Azure](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms).

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>Soubory nemůžete kopírovat z připojených svazků.

Kopírování může selhat s chybou "0x80070780: soubor nemůže být v systému k dispozici." 

Ověřte, jestli je na zdrojovém serveru povolená funkce odstranění duplicitních dat na disku. Pokud tomu tak je, zajistěte, aby na jednotkách pro obnovení bylo povoleno odstraňování duplicitních dat. Odstranění duplicitních dat můžete nechat nenakonfigurované, abyste jednotky na serveru pro obnovení nemuseli odstraňovat.

## <a name="next-steps"></a>Další kroky

- [Obnovení souborů a složek ze zálohy virtuálního počítače Azure](backup-azure-restore-files-from-vm.md)