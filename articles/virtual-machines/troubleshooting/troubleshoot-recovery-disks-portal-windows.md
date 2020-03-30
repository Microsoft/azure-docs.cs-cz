---
title: Použití virtuálního počítače pro řešení potíží s Windows na webu Azure Portal | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit problémy s virtuálním počítačem windows v Azure připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí portálu Azure
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
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249994"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Poradce při potížích s virtuálním počítačem s Windows připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí portálu Azure
Pokud váš virtuální počítač (VM) windows v Azure narazí na chybu při spuštění nebo disku, budete muset provést kroky řešení potíží na virtuálním pevném disku sám. Běžným příkladem by mohla být neúspěšná aktualizace aplikace, která brání úspěšnému spuštění virtuálního virtuálního movitého virtuálního movitého virtuálního softwaru. Tento článek podrobně popisuje, jak pomocí portálu Azure připojit virtuální pevný disk k jinému virtuálnímu počítači s Windows opravit všechny chyby a pak znovu vytvořit původní virtuální počítač. 

## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Zastavte ovlivněný virtuální virtuální ms.
1. Vytvořte snímek pro disk operačního systému virtuálního počítače.
1. Vytvořte virtuální pevný disk ze snímku.
1. Připojte a připojte virtuální pevný disk k jinému virtuálnímu počítači se systémem Windows pro účely řešení potíží.
1. Připojení k virtuálnímu počítači pro řešení potíží. Upravte soubory nebo spusťte jakékoli nástroje k opravě problémů na původním virtuálním pevném disku.
1. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
1. Zaměnujte disk operačního systému za virtuální počítače.

> [!NOTE]
> Tento článek se nevztahuje na virtuální počítače s nespravovaný disk.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pořízení snímku disku operačního systému
Snímek je úplná kopie virtuálního pevného disku (VHD) jen pro čtení. Doporučujeme, abyste před pořízením snímku virtuální ho zavřeli čistě, abyste vyčistili všechny probíhající procesy. Chcete-li pořídit snímek disku operačního systému, postupujte takto:

1. Přejděte na [portál Azure](https://portal.azure.com). Vyberte **Virtuální počítače** z postranního panelu a vyberte virtuální počítač, který má problém.
1. V levém podokně vyberte **Disky**a pak vyberte název disku operačního systému.
    ![Obrázek o názvu disku operačního systému](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na stránce **Přehled** disku operačního systému a potom vyberte **Vytvořit snímek**.
1. Vytvořte snímek ve stejném umístění jako disk operačního systému.

## <a name="create-a-disk-from-the-snapshot"></a>Vytvoření disku ze snímku
Chcete-li vytvořit disk ze snímku, postupujte takto:

1. Na webu Azure Portal vyberte **Cloud Shell.**

    ![Obrázek o prostředí Open Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Spusťte následující příkazy prostředí PowerShell a vytvořte ze snímku spravovaný disk. Tyto názvy vzorků byste měli nahradit příslušnými názvy.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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
3. Pokud příkazy úspěšně spustit, zobrazí se nový disk ve skupině prostředků, které jste zadali.

## <a name="attach-the-disk-to-another-vm"></a>Připojení disku k jinému virtuálnímu virtuálnímu počítače
V následujících několika krocích použijete jiný virtuální virtuální ms pro účely řešení potíží. Po připojení disku k virtuálnímu počítače pro řešení potíží můžete procházet a upravovat obsah disku. Tento proces umožňuje opravit všechny chyby konfigurace nebo zkontrolovat další soubory protokolu aplikace nebo systému. Pokud chcete disk připojit k jinému virtuálnímu počítače, postupujte takto:

1. Vyberte skupinu prostředků z portálu a vyberte virtuální počítač pro řešení potíží. Vyberte **Disky**, vyberte **Upravit**a pak klepněte na **Přidat datový disk**:

    ![Připojení existujícího disku na portálu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. V seznamu **Datové disky** vyberte disk operačního systému virtuálního počítače, který jste identifikovali. Pokud disk operačního systému nevidíte, ujistěte se, že řešení potíží s virtuálním počítačem a disk operačního systému je ve stejné oblasti (umístění). 
3. Chcete-li změny použít, vyberte **Uložit.**

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Připojení připojeného datového disku k virtuálnímu počítače

1. Otevřete připojení ke vzdálené ploše k virtuálnímu počítači pro řešení potíží. 
2. V řešení potíží s virtuálním montovantem vyberte Open **Server Manager**a vyberte Soubor a **úložiště služby**. 

    ![Vyberte služby souborů a úložišť ve Správci serveru.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Datový disk je automaticky rozpoznán a připojen. Chcete-li zobrazit seznam připojených disků, vyberte **možnost Disky**. Chcete-li zobrazit informace o svazku, včetně písmene jednotky, můžete vybrat datový disk. Následující příklad ukazuje datový disk připojený a používající **F:**:

    ![Připojené disky a informace o svazku ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Oprava problémů na původním virtuálním pevném disku
Se stávajícím virtuálním pevným diskem můžete nyní podle potřeby provádět jakékoli kroky údržby a odstraňování potíží. Jakmile vyřešíte problémy, pokračujte následujícími kroky.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojit a odpojit původní virtuální pevný disk
Jakmile jsou chyby vyřešeny, odpojte existující virtuální pevný disk od virtuálního počítače pro řešení potíží. Virtuální pevný disk nelze použít s žádným jiným virtuálním počítačem, dokud nebude uvolněna zapůjčení připojené virtuální pevný disk k virtuálnímu počítači řešící potíže.

1. Z relace RDP do virtuálního počítače otevřete **Správce serveru**a pak vyberte Soubor a **úložiště :**

    ![Výběr služby Souborů a úložišť ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Vyberte **Disky** a pak vyberte datový disk. Klikněte pravým tlačítkem myši na datový disk a vyberte **převzít offline**:

    ![Nastavení datového disku jako offline ve Správci serveru](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Nyní odpojte virtuální pevný disk od virtuálního počítače. Vyberte virtuální počítač na webu Azure Portal a klikněte na **Disky**. 
4. Vyberte **Upravit**, vyberte připojený disk operačního systému a klepněte na **odpojit**:

    ![Odpojení existujícího virtuálního pevného disku](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Počkejte, dokud virtuální počítače úspěšně odpojen datový disk před pokračováním.

## <a name="swap-the-os-disk-for-the-vm"></a>Výměna disku operačního systému za virtuální počítače

Portál Azure teď podporuje změnu disku operačního systému virtuálního počítače. Postupujte přitom takto:

1. Přejděte na [portál Azure](https://portal.azure.com). Vyberte **Virtuální počítače** z postranního panelu a vyberte virtuální počítač, který má problém.
1. V levém podokně vyberte **Disky**a pak vyberte **Zaměnit disk operačního systému**.
        ![Obrázek o disku swapového operačního systému na webu Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Vyberte nový disk, který jste opravili, a zadejte název virtuálního počítače a potvrďte změnu. Pokud disk v seznamu nevidíte, počkejte 10 ~ 15 minut po odpojení disku z virtuálního počítače pro řešení potíží. Také se ujistěte, že disk je ve stejném umístění jako virtuální počítače.
1. Vyberte OK.

## <a name="next-steps"></a>Další kroky
Pokud máte problémy s připojením k virtuálnímu počítači, [přečtěte si článek Poradce při potížích s připojením RDP k virtuálnímu počítači Azure](troubleshoot-rdp-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači najdete [v tématu Řešení problémů s připojením aplikací na virtuálním počítači se systémem Windows](troubleshoot-app-connection.md).

Další informace o používání Správce prostředků najdete v [tématu Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md).


