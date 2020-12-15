---
title: Řešení potíží se službou Azure VM File Recovery
description: Řešení potíží specifických pro obnovování souborů a složek ze zálohy virtuálního počítače Azure
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513305"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Řešení potíží s obnovením souborů zálohování virtuálních počítačů Azure

Tento článek popisuje postup řešení potíží, který vám může pomáhat vyřešit chyby Azure Backup související s problémy specifickými pro obnovování souborů a složek ze zálohy virtuálního počítače Azure. 

## <a name="exception-caught-while-connecting-to-target"></a>Při připojování k cíli se zachytila výjimka.

Možná příčina: skript nemůže získat přístup k doporučené akci bodu obnovení: Ověřte, zda počítač splňuje všechny [požadavky na přístup](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>Cíl se už přihlásil přes relaci iSCSI.

Možná příčina: skript již byl spuštěn na stejném počítači a jednotky byly připojeny.
Doporučená akce: svazky bodu obnovení již byly připojeny. Nemusí být připojené se stejnými písmeny jednotky původního virtuálního počítače. Procházejte všemi dostupnými svazky v Průzkumníkovi souborů.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Tento skript je neplatný, protože disky byly odpojeny přes portál nebo překročily limit 12 hodin. Stažení nového skriptu z portálu

Možná příčina: disky byly odpojeny z portálu nebo byl překročen limit 12 hodin.
Doporučená akce: skript je po uplynutí 12 hodin od jeho stažení neplatný a nedá se spustit. Přejděte na portál a Stáhněte si nový skript, abyste mohli pokračovat v obnovování souborů.

## <a name="troubleshooting-common-issues"></a>Řešení běžných potíží

V této části najdete postup řešení potíží zjištěných při stahování a spouštění skriptu pro obnovení souboru.

## <a name="cannot-download-the-script"></a>Skript nejde stáhnout.

Doporučená akce:

1. Ujistěte se, že máte všechna [potřebná oprávnění ke stažení skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Zajistěte, aby bylo připojení k cílovým IP adresám Azure.
Chcete-li ověřit připojení, spusťte z příkazového řádku se zvýšenými oprávněními následující příkaz. 
    - *nslookup download.Microsoft.com* nebo
    - *download.microsoft.com příkazů testu*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>Skript se úspěšně stáhne, ale nespustí se.

### <a name="for-sles-12-sp4-os-linux"></a>Pro SLES 12 SP4 OS (Linux)

Chyba: iscsi_tcp_module Nenalezeno

Možná příčina: když spustíte skript Pythonu pro obnovení na úrovni položek v operačním systému SUSE Linux verze 12sp4, dojde k selhání s chybou ***iscsi_tcp modul nejde načíst** _. Modul ILR k navázání připojení TCP ke službě zálohování používá iscsi_tcp. SUSE jako součást vydání 12SP4 se odebrala iscsi_tcp z balíčku Open-iSCSI, takže operace ILR se nezdařila.

Doporučená akce: spuštění skriptu obnovení souborů není u virtuálních počítačů s SUSE 12SP4 podporováno, zkuste operaci obnovení provést ve starší verzi SUSE 12SP4.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>Skript se spustí, ale připojení k cíli iSCSI selhalo.

Chyba: při připojování k cíli se zachytila výjimka.

1. Ujistěte se, že počítač, ve kterém je spuštěný skript, splňuje všechny [požadavky na přístup](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Ověřte připojení k cílovým IP adresám Azure.
Chcete-li ověřit připojení, spusťte z příkazového řádku se zvýšenými oprávněními následující příkaz. 
    - _nslookup download.microsoft.com * nebo<br>
    - *download.microsoft.com příkazů testu*
3. Ujistěte se, že máte přístup k odchozímu portu iSCSI 3260.
4. Zajistěte, aby nedocházelo k bráně firewall/NSG, která blokuje provoz do cílových IP adres Azure nebo na adresy URL služby Recovery Services.
5. Ověřte, jestli antivirová ochrana blokuje provádění skriptu.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Připojeno k bodu obnovení, ale disky se nepřipojily.

Ujistěte se, že máte [správný počítač pro spuštění skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) .

### <a name="on-windows-vm"></a>Na virtuálním počítači s Windows

**Fond úložiště na virtuálním počítači se připojí v režimu jen pro čtení** . V případě systémů Windows 2012 R2 a Windows 2016 (s fondem úložiště) při prvním spuštění skriptu může fond úložiště připojený k virtuálnímu počítači přejít do stavu jen pro čtení.

K vyřešení tohoto problému musíme ručně nastavit Read-Write přístup k fondu úložiště poprvé a připojit virtuální disky, a to podle následujících kroků:

1. Nastavte přístup Read-Write.
Přejděte na Správce serveru > Souborová služba a služba úložiště > svazky > fondy úložiště.

   ![Úložiště Windows](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. V okně **fond úložiště** klikněte pravým tlačítkem myši na dostupný fond úložiště a vyberte možnost **nastavit Read-Write přístup** .

   ![Zápis pro čtení do úložiště Windows](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. Po přiřazení fondu úložiště s přístupem pro čtení i zápis musíme připojit virtuální disk.
V části virtuální disky přejděte na **Správce serveru uživatelské rozhraní**> klikněte pravým tlačítkem myši a vyberte možnost **připojit virtuální disk** .

   ![Virtuální disk správce serveru](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Na virtuálním počítači se systémem Linux

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>Obnovení souboru se nepodařilo automaticky připojit, protože disk neobsahuje svazky.

Při provádění obnovení souborů služba zálohování detekuje svazky a automatické připojení. Pokud však jsou v zálohovaných discích nezpracované oddíly, tyto disky nejsou automaticky připojeny a nedokážeme pro obnovení zobrazit datový disk.

Chcete-li tento problém vyřešit, postupujte podle kroků popsaných v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>OPERAČNÍ systém nemohl identifikovat systém souborů, který způsobil selhání obnovení souboru systému Linux během připojování disků.

Při spuštění datového disku skriptu pro obnovení souborů se nezdařilo připojit k následující chybě: *nepodařilo se připojit následující oddíly, protože operační systém nemohl identifikovat systém souborů* .

- Pokud chcete tento problém vyřešit, ověřte, jestli je svazek zašifrovaný pomocí aplikace třetí strany. Pokud se disk nebo virtuální počítač zašifrují, na portálu se nezobrazuje jako zašifrovaný.
1. Přihlaste se k zálohovanému virtuálnímu počítači a spusťte příkaz: *lsblk-f* .<br>

   ![Disk bez svazku](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Ověřte systém souborů a šifrování.
3. Pokud je svazek zašifrovaný, obnovení souborů se nepodporuje. [Přečtěte si další informace](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>Disky jsou připojené, ale nemůžou připojit svazky.

- Ujistěte se, že máte [správný počítač pro spuštění skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Na virtuálním počítači s Windows

Při spuštění skriptu pro obnovení souborů pro systém Windows se zobrazí zpráva, že **jsou připojeny svazky * 0**, ale disky se objeví v konzole pro správu disků. Při připojování svazků přes iSCSI některé svazky, které se zjistily, přešly do stavu offline. Když kanál iSCSI komunikuje mezi virtuálním počítačem a službou, detekuje tyto svazky a přenese je do režimu online, ale nejsou připojené.

   ![Disk není připojený.](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

Chcete-li tento problém identifikovat a vyřešit, proveďte následující kroky:

1. V prostředí příkazového řádku v příkazovém řádku spusťte příkaz **diskmgmt** –*Správa disků**.
2. Ověřte, zda jsou zobrazeny žádné další disky. V následujícím příkladu je disk 2 další disk.

   ![Disk management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Klikněte pravým tlačítkem na **svazek** a vyberte **změnit písmeno jednotky a cestu**.

   ![Disk management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. V okně **Přidat písmeno jednotky nebo cestu** vyberte **přiřadit následující písmeno jednotky** a přiřaďte dostupnou jednotku a klikněte na **OK**. 

   ![Disk management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. V Průzkumníku souborů můžete zobrazit jednotku.

   ![Disk management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. Nové svazky by měly být připojené.  

   ![Disk není připojuje](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. V Průzkumníkovi souborů budou nové svazky po novém spuštění skriptu viditelné.

   ![Disk není mounting1](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Disk není mounting2](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Na virtuálních počítačích se systémem Linux 

- Ujistěte se, že máte [správný počítač pro spuštění skriptu](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Pokud chráněný virtuální počítač se systémem Linux používá LVM a/nebo pole RAID, postupujte podle kroků uvedených v tomto [článku](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms).

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>Nejde zkopírovat soubory z připojených svazků.

Kopírování může selhat s chybou 0x80070780: k souboru nelze přistupovat systém. V takovém případě Ověřte, jestli je na zdrojovém serveru povolená funkce odstranění duplicitních dat na disku. Pak zajistěte, aby měl server pro obnovení duplicity na jednotkách povolený. Roli odstranění duplicitních dat můžete nechat nenakonfigurovanou, takže nebudete jednotky na serveru pro obnovení duplikovat.
