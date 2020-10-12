---
title: Použití virtuálního počítače pro řešení potíží se systémem Linux v Azure Portal | Microsoft Docs
description: Naučte se řešit potíže s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: f9907be0e7cd14876964b820d9b267f279fc50d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331450"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Řešení potíží s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal
Pokud váš virtuální počítač se systémem Linux zaznamená chybu spuštění nebo disku, možná budete muset provést kroky pro řešení potíží na samotném virtuálním pevném disku. Běžným příkladem může být neplatná položka v `/etc/fstab` systému, která zabraňuje úspěšnému spuštění virtuálního počítače. Tento článek podrobně popisuje, jak pomocí Azure Portal připojit virtuální pevný disk k jinému virtuálnímu počítači se systémem Linux a opravit případné chyby a pak znovu vytvořit původní virtuální počítač.

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální počítač.
1. Pořídit snímek disku s operačním systémem virtuálního počítače.
1. Z snímku vytvořte virtuální pevný disk.
1. Připojte virtuální pevný disk k jinému virtuálnímu počítači se systémem Linux pro účely řešení potíží.
1. Připojení k virtuálnímu počítači pro řešení potíží. Úpravou souborů nebo spuštěním libovolných nástrojů opravte potíže s původním virtuálním pevným diskem.
1. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
1. Proměňte disk s operačním systémem pro virtuální počítač.

> [!NOTE]
> Tento článek se nevztahuje na virtuální počítač s nespravovaným diskem.

## <a name="determine-boot-issues"></a>Určení problémů se spouštěním
Zkontrolujte diagnostiku spouštění a snímek obrazovky virtuálního počítače, abyste zjistili, proč se váš virtuální počítač nemůže správně spustit. Běžným příkladem může být neplatná položka v nástroji `/etc/fstab` , nebo odstranění nebo přesunutí základního virtuálního pevného disku.

Na portálu vyberte svůj virtuální počítač a pak přejděte dolů k části **Podpora a řešení potíží** . Kliknutím na **Diagnostika spouštění** zobrazíte zprávy konzoly streamované z virtuálního počítače. Projděte si protokoly konzoly a zjistěte, jestli můžete určit, proč má virtuální počítač narazit na problém. Následující příklad ukazuje, že se virtuální počítač zablokuje v režimu údržby, který vyžaduje ruční interakci:

![Zobrazení protokolů konzoly pro diagnostiku spouštění virtuálního počítače](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Můžete také kliknout na **snímek obrazovky** v horní části protokolu diagnostiky spouštění a stáhnout si snímek obrazovky virtuálního počítače.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pořídit snímek disku s operačním systémem
Snímek je plná kopie virtuálního pevného disku jen pro čtení (VHD). Doporučujeme, abyste virtuální počítač před vytvořením snímku čistě vypnuli, aby se vymazaly všechny procesy, které probíhají. Pokud chcete pořídit snímek disku s operačním systémem, postupujte podle těchto kroků:

1. Přejít na [Azure Portal](https://portal.azure.com). Z bočního panelu vyberte **virtuální počítače** a potom vyberte virtuální počítač, který má problém.
1. V levém podokně vyberte **disky**a potom vyberte název disku s operačním systémem.
    ![Obrázek s názvem disku s operačním systémem](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na stránce **Přehled** na disku s operačním systémem a pak vyberte **vytvořit snímek**.
1. Vytvořte snímek ve stejném umístění jako disk s operačním systémem.

## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku
K vytvoření disku ze snímku použijte následující postup:

1. Z Azure Portal vyberte **Cloud Shell** .

    ![Obrázek o otevřené Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Spuštěním následujících příkazů PowerShellu vytvořte ze snímku spravovaný disk. Tyto vzorové názvy byste měli nahradit odpovídajícími názvy.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Pokud se příkazy úspěšně spustí, zobrazí se nový disk ve skupině prostředků, kterou jste zadali.

## <a name="attach-disk-to-another-vm"></a>Připojit disk k jinému virtuálnímu počítači
V následujících několika krocích použijete pro účely odstraňování potíží jiný virtuální počítač. Po připojení disku k virtuálnímu počítači pro řešení potíží můžete procházet a upravovat obsah disku. Tento proces umožňuje opravit chyby konfigurace nebo zkontrolovat další soubory protokolu aplikace nebo systému. K připojení disku k jinému virtuálnímu počítači použijte následující postup:

1. Z portálu vyberte skupinu prostředků a potom vyberte virtuální počítač pro řešení potíží. Vyberte **disky**, vyberte **Upravit**a pak klikněte na **přidat datový disk**:

    ![Připojit existující disk na portálu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. V seznamu **datové disky** vyberte disk s operačním systémem virtuálního počítače, který jste identifikovali. Pokud se disk s operačním systémem nezobrazuje, ujistěte se, že je virtuální počítač pro řešení potíží a disk s operačním systémem ve stejné oblasti (umístění). 
3. Kliknutím na **Uložit** změny aplikujte.

## <a name="mount-the-attached-data-disk"></a>Připojit připojený datový disk

> [!NOTE]
> Následující příklady podrobně popisují kroky vyžadované na virtuálním počítači s Ubuntu. Pokud používáte jiný distribuce pro Linux, například Red Hat Enterprise Linux nebo SUSE, umístění souborů protokolu a `mount` příkazy se můžou trochu lišit. Příslušné změny v příkazech najdete v dokumentaci pro konkrétní distribuce.

1. SSH k vašemu VIRTUÁLNÍmu počítači pro řešení potíží pomocí příslušných přihlašovacích údajů. Pokud je tento disk prvním datovým diskem připojeným k vašemu VIRTUÁLNÍmu počítači pro řešení potíží, je nejspíš připojený k `/dev/sdc` . Použijte `dmseg` k vypsání připojených disků:

    ```bash
    dmesg | grep SCSI
    ```
    Výstup se podobá následujícímu příkladu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    V předchozím příkladu je disk s operačním systémem `/dev/sda` a dočasný disk poskytnutý pro každý virtuální počítač je v `/dev/sdb` . Pokud jste měli více datových disků, měly by být v `/dev/sdd` , `/dev/sde` a tak dále.

2. Vytvořte adresář pro připojení stávajícího virtuálního pevného disku. Následující příklad vytvoří adresář s názvem `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Pokud máte na svém stávajícím virtuálním pevném disku více oddílů, připojte požadovaný oddíl. Následující příklad připojí první primární oddíl na `/dev/sdc1` :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Osvědčeným postupem je připojení datových disků na virtuálních počítačích v Azure s použitím univerzálně jedinečného identifikátoru (UUID) virtuálního pevného disku. Pro tento scénář krátkého řešení potíží není nutné připojení virtuálního pevného disku pomocí identifikátoru UUID. V případě normálního použití ale úpravy `/etc/fstab` pro připojení virtuálních pevných disků pomocí názvu zařízení místo identifikátoru UUID můžou způsobit selhání spuštění virtuálního počítače.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Opravit problémy s původním virtuálním pevným diskem
S připojeným virtuálním pevným diskem teď můžete podle potřeby provádět libovolné kroky údržby a řešení potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojte a odpojte původní virtuální pevný disk
Po vyřešení chyb odpojte stávající virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nemůžete použít s žádným jiným virtuálním počítačem, dokud se neuvolní zapůjčení virtuálního pevného disku k virtuálnímu počítači pro řešení potíží.

1. Z relace SSH k vašemu VIRTUÁLNÍmu počítači pro řešení potíží odpojte stávající virtuální pevný disk. Nejdřív změňte nadřazený adresář pro přípojný bod:

    ```bash
    cd /
    ```

    Nyní odpojte stávající virtuální pevný disk. Následující příklad odpojí zařízení v `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teď odpojte virtuální pevný disk od virtuálního počítače. Na portálu vyberte svůj virtuální počítač a klikněte na **disky**. Vyberte existující virtuální pevný disk a potom klikněte na **Odpojit**:

    ![Odpojit stávající virtuální pevný disk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Než budete pokračovat, počkejte, než virtuální počítač úspěšně odpojí datový disk.

## <a name="swap-the-os-disk-for-the-vm"></a>Prohození disku s operačním systémem pro virtuální počítač

Azure Portal teď podporuje změnu disku s operačním systémem virtuálního počítače. Postupujte takto:

1. Přejít na [Azure Portal](https://portal.azure.com). Z bočního panelu vyberte **virtuální počítače** a potom vyberte virtuální počítač, který má problém.
1. V levém podokně vyberte **disky**a pak vyberte **swap disk s operačním systémem**.
        ![Obrázek odkládacího disku s operačním systémem v Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Zvolte nový disk, který jste opravili, a potom zadejte název virtuálního počítače pro potvrzení změny. Pokud se disk v seznamu nezobrazí, počkejte 10. po odpojení disku od virtuálního počítače pro řešení potíží počkejte 10 minut. Také se ujistěte, že je disk ve stejném umístění jako virtuální počítač.
1. Vyberte OK.

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači se systémem Linux](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).

Další informace o použití Správce prostředků naleznete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
