---
title: Řešení potíží s virtuálním počítačem s Windows v Azure Portal | Microsoft Docs
description: Naučte se řešit problémy s virtuálními počítači s Windows v Azure připojením disku s operačním systémem k virtuálnímu počítači pro obnovení prostřednictvím Azure Portal.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735224"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení prostřednictvím Azure Portal
Pokud ve vašem virtuálním počítači s Windows v Azure dojde k chybě při spuštění nebo disku, možná budete muset provést kroky pro řešení potíží na virtuálním pevném disku (VHD). Běžným příkladem je neúspěšná aktualizace aplikace, která zabraňuje úspěšnému spuštění virtuálního počítače. Tento článek podrobně popisuje, jak pomocí Azure Portal připojit virtuální pevný disk k jinému virtuálnímu počítači s Windows a opravit případné chyby a pak znovu vytvořit původní virtuální počítač. 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální počítač.
1. Vytvořte snímek pro disk s operačním systémem virtuálního počítače.
1. Z snímku vytvořte virtuální pevný disk.
1. Připojte virtuální pevný disk k jinému virtuálnímu počítači s Windows pro účely odstraňování potíží.
1. Připojení k virtuálnímu počítači pro řešení potíží. Úpravou souborů nebo spuštěním libovolných nástrojů Opravte problémy na původním virtuálním pevném disku.
1. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
1. Proměňte disk s operačním systémem pro virtuální počítač.

> [!NOTE]
> Tento článek se nevztahuje na virtuální počítače s nespravovanými disky.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pořídit snímek disku s operačním systémem
Snímek je plná kopie virtuálního pevného disku jen pro čtení. Doporučujeme, abyste virtuální počítač před vytvořením snímku čistě vypnuli, aby se vymazaly všechny procesy, které probíhají. Pokud chcete pořídit snímek disku s operačním systémem, postupujte podle těchto kroků:

1. Přejděte na web [Azure Portal](https://portal.azure.com). Z bočního panelu vyberte **virtuální počítače** a potom vyberte virtuální počítač, který má problém.
1. V levém podokně vyberte **disky** a potom vyberte název disku s operačním systémem.
    ![Snímek obrazovky, který zobrazuje název disku operačního systému v nastavení disku.](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na stránce **Přehled** na disku s operačním systémem vyberte **vytvořit snímek**.
1. Vytvořte snímek ve stejném umístění jako disk s operačním systémem.

## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku
K vytvoření disku ze snímku použijte následující postup:

1. Z Azure Portal vyberte **Cloud Shell** .

    ![Snímek obrazovky, který zobrazuje tlačítko pro otevření Azure Cloud Shell.](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Spuštěním následujících příkazů PowerShellu vytvořte ze snímku spravovaný disk. Nahraďte příklady názvů odpovídajícími názvy.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Pokud se příkazy úspěšně spustí, zobrazí se nový disk ve skupině prostředků, kterou jste zadali.

## <a name="attach-the-disk-to-another-vm"></a>Připojte disk k jinému virtuálnímu počítači.
V následujících několika krocích použijete pro účely odstraňování potíží jiný virtuální počítač. Po připojení disku k virtuálnímu počítači pro řešení potíží můžete procházet a upravovat obsah disku. Tento proces umožňuje opravit chyby konfigurace nebo zkontrolovat další soubory protokolu aplikace nebo systému. K připojení disku k jinému virtuálnímu počítači použijte následující postup:

1. Z portálu vyberte skupinu prostředků a potom vyberte virtuální počítač pro řešení potíží. Vyberte **disky**  >  **Upravit**  >  **přidat datový disk**.

    ![Snímek obrazovky zobrazující výběry pro připojení stávajícího disku na portálu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. V seznamu **datové disky** vyberte disk s operačním systémem virtuálního počítače, který jste identifikovali. Pokud se disk s operačním systémem nezobrazuje, ujistěte se, že virtuální počítač pro řešení potíží a disk s operačním systémem jsou ve stejné oblasti (umístění). 
3. Kliknutím na **Uložit** změny aplikujte.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Připojte k virtuálnímu počítači připojený datový disk

1. Otevřete připojení ke vzdálené ploše na virtuálním počítači pro řešení potíží. 
2. Na virtuálním počítači pro řešení potíží otevřete **Správce serveru** a pak vyberte **Souborová služba a služba úložiště**. 

    ![Snímek obrazovky, který zobrazuje výběr souborové služby a služby úložiště v rámci Správce serveru.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Datový disk se automaticky detekuje a připojí. Pokud chcete zobrazit seznam připojených disků, vyberte **disky**. Můžete vybrat datový disk pro zobrazení informací o svazku, včetně písmene jednotky. Následující příklad ukazuje, že datový disk je připojený a používá jednotku **F**.

    ![Snímek obrazovky, který zobrazuje informace o připojeném disku a svazku v Správce serveru.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Opravit problémy s původním virtuálním pevným diskem
S připojeným virtuálním pevným diskem teď můžete podle potřeby provádět libovolné kroky údržby a řešení potíží. Po vyřešení všech chyb pokračujte následujícími kroky.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Odpojení původního virtuálního pevného disku
Odpojte stávající virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nemůžete použít s žádným jiným virtuálním počítačem, dokud se neuvolní zapůjčení, které připojení virtuálního pevného disku k virtuálnímu počítači pro řešení potíží.

1. Z relace vzdálené plochy k VIRTUÁLNÍmu počítači otevřete **Správce serveru** a pak vyberte **Souborová služba a služba úložiště**.

    ![Snímek obrazovky, který zobrazuje výběr souborové služby a služby úložiště v Správce serveru.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Vyberte **disky**, klikněte pravým tlačítkem na datový disk a pak vyberte **převést do režimu offline**.

    ![Snímek obrazovky, který ukazuje nastavení datového disku jako offline v Správce serveru.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Odpojte virtuální pevný disk od virtuálního počítače. Vyberte virtuální počítač v Azure Portal a pak vyberte **disky**. 
4. Vyberte **Upravit**, vyberte disk s operačním systémem, který jste připojili, a pak vyberte **Odstranit**.

    ![Snímek obrazovky zobrazující výběry pro odpojení stávajícího virtuálního pevného disku.](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Než budete pokračovat, počkejte, než se datový disk úspěšně odstraní na virtuálním počítači.

## <a name="swap-the-os-disk-for-the-vm"></a>Prohození disku s operačním systémem pro virtuální počítač

Azure Portal teď podporuje změnu disku s operačním systémem virtuálního počítače. Postupujte takto:

1. Přejděte na web [Azure Portal](https://portal.azure.com). Z bočního panelu vyberte **virtuální počítače** a potom vyberte virtuální počítač, který má problém.
1. V levém podokně vyberte **disky** a pak vyberte **swap disk s operačním systémem**.
   
    ![Snímek obrazovky, který zobrazuje tlačítko pro záměnu disku s operačním systémem v Azure Portal.](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Zvolte nový disk, který jste opravili, a potom zadejte název virtuálního počítače pro potvrzení změny. Pokud se disk v seznamu nezobrazuje, počkejte 10 až 15 minut, než se disk odpojíte od virtuálního počítače pro řešení potíží. Také se ujistěte, že je disk ve stejném umístění jako virtuální počítač.
1. Vyberte **OK**.

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k VIRTUÁLNÍmu počítači, přečtěte si téma [řešení potíží s připojením vzdálené plochy k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím běžícím na vašem VIRTUÁLNÍm počítači najdete v tématu [řešení potíží s připojením aplikací na virtuálním počítači s Windows](troubleshoot-app-connection.md).

Další informace o použití Azure Resource Manager naleznete v tématu [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md).


