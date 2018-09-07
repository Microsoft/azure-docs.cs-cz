---
title: Správa disků virtuálních počítačů ve službě Azure Stack | Dokumentace Microsoftu
description: Zřízení disky pro virtuální počítače ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: bdf31c72fbcd8941161e6b9df0a490df7f6a16e0
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44026514"
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Zřízení disku úložiště virtuálního počítače ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje, jak zřídit disku úložiště virtuálního počítače pomocí portálu Azure Stack nebo pomocí prostředí PowerShell.

## <a name="overview"></a>Přehled

Počínaje verzí. 1808, podporuje Azure Stack použít spravované disky a nespravované disky na virtuální počítače, jako operační systém (OS) a datového disku. Starší než verze. 1808 jsou podporovány pouze nespravované disky. 

**[Spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)**  zjednodušit správu disků virtuálních počítačů Azure IaaS tím, že spravují účty úložiště přidružené k diskům virtuálních počítačů. Budete muset určit velikost disku je nutné, a Azure Stack vytváří a spravuje disk za vás.

**[Nespravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)**, vyžaduje vytvořené [účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) k uložení disku. Disky, které vytvoříte, se označují jako disky virtuálních počítačů a ukládají v kontejnery v účtu úložiště.

 

### <a name="best-practice-guidelines"></a>Pravidla osvědčených postupů

Chcete-li zvýšit výkon a snížit celkové náklady na, doporučujeme, abyste že jednotlivých disků virtuálního počítače umístíte do samostatného kontejneru. Kontejner uchovávat disk s operačním systémem nebo datový disk, ale ne obojí současně. (Ale nic a tím vám znemožnit uvedení oba typy disků ve stejném kontejneru.)

Pokud chcete přidat jednu nebo několik datových disků k virtuálnímu počítači, použijte další kontejnery jako umístění pro ukládání těchto disků. Disk s operačním systémem pro další virtuální počítače musí být také ve své vlastní kontejnery.

Když vytvoříte několik virtuálních počítačů, můžete znovu použít stejný účet úložiště pro každý nový virtuální počítač. Kontejnery, které vytvoříte, musí být jedinečné.

### <a name="adding-new-disks"></a>Přidávání nových disků

Následující tabulka shrnuje, jak přidat disky pomocí portálu a pomocí prostředí PowerShell.

| Metoda | Možnosti
|-|-|
|[Portál User portal](#use-the-portal-to-add-additional-disks-to-a-vm)|-Přidáte nové datové disky existujícího virtuálního počítače. Nové disky, které jsou vytvořeny ve službě Azure Stack. </br> </br>-Přidáte existující soubor disku (VHD) k virtuálnímu počítači s dříve zřízené. Provedete to tak, musíte připravit VHD a pak nahrajte soubor do služby Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Vytvořit nový virtuální počítač s diskem operačního systému a ve stejnou dobu jeden nebo více datových disků k tomuto virtuálnímu počítači. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Přidejte disky do virtuálního počítače pomocí portálu

Ve výchozím nastavení se při použití portálu k vytvoření virtuálního počítače většiny položek na webu marketplace, vytvoří jenom disk s operačním systémem.

Po vytvoření virtuálního počítače, můžete na portálu:
* Vytvořit nový datový disk a připojit k virtuálnímu počítači.
* Nahrát stávající datový disk a připojit ho k virtuálnímu počítači.

Každý nespravovaný disk, který přidáte měly být umístěny v samostatných kontejneru.

>[!NOTE]
>Disky, vytvářet a spravovat Azure, se nazývají [spravované disky](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Použití portálu k vytvoření a připojení nového datového disku

1.  Na portálu, vyberte **virtuálních počítačů**.    
    ![Příklad: Řídicího panelu virtuálního počítače](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Vyberte virtuální počítač, která dříve byla zřízena.   
    ![Příklad: Vyberte virtuální počítač na řídicím panelu](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Pro virtuální počítač vyberte **disky** > **připojit nový**.       
    ![Příklad: Připojte nový disk k virtuálnímu počítači](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  V **připojit nový disk** vyberte **umístění**. Ve výchozím umístění se nastaví do kontejneru, který obsahuje disk s operačním systémem.      
    ![Příklad: Nastavte na místo na disku](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Vyberte **účtu úložiště** používat. V dalším kroku vyberte **kontejneru** kam chcete umístit datový disk. Z **kontejnery** stránky, můžete vytvořit nový kontejner. Pokud chcete. Pak můžete změnit umístění nového disku do vlastního kontejneru. Pokud použijete samostatný kontejner pro každý disk, je distribuovat umístění datový disk, který může zlepšit výkon. Zvolte **vyberte** a uložte výběr.     
    ![Příklad: Vyberete kontejner](media/azure-stack-manage-vm-disks/select-container.png)

6.  V **připojit nový disk** stránce, aktualizujte **název**, **typ**, **velikost**, a **hostovat ukládání do mezipaměti** nastavení disku. Potom vyberte **OK** uložte novou konfiguraci disku pro virtuální počítač.  
    ![Příklad: Dokončení disku přílohy](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Poté, co Azure Stack disk vytvoří a připojí ho k virtuálnímu počítači, nový disk je uvedený v nastavení disku virtuálního počítače v rámci **datové disky**.   
    ![Příklad: Zobrazení disk](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Připojit stávající datový disk k virtuálnímu počítači

1.  [Připravte soubor VHD](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) pro použití jako datového disku virtuálního počítače. Nahrání souboru VHD do účtu úložiště, který používáte s virtuálním Počítačem, který chcete připojit soubor .vhd.

  Plán, který chcete použít jiný kontejner pro uložení souboru .vhd než kontejner, který obsahuje disk s operačním systémem.   
  ![Příklad: Odeslání souboru VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Po nahrání souboru VHD, budete chtít připojit virtuální pevný disk k virtuálnímu počítači. V nabídce na levé straně vyberte **virtuálních počítačů**.  
 ![Příklad: Vyberte virtuální počítač na řídicím panelu](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Zvolte virtuální počítač ze seznamu.    
  ![Příklad: Vyberte virtuální počítač na řídicím panelu](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Na stránce pro virtuální počítač vyberte **disky** > **připojit existující**.   
  ![Příklad: Připojit stávající disk](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  V **připojit stávající disk** stránce **souboru virtuálního pevného disku**. **Účty úložiště** otevře se stránka.    
  ![Příklad: Vyberte soubor virtuálního pevného disku](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  V části **účty úložiště**, vyberte účet, který chcete použít a pak zvolte kontejner, který obsahuje soubor .vhd, který byl dříve odeslán. Vyberte soubor VHD a klikněte na tlačítko **vyberte** a uložte výběr.    
  ![Příklad: Vyberete kontejner](media/azure-stack-manage-vm-disks/select-container2.png)

7.  V části **připojit stávající disk**, vybraný soubor je uvedený v části **souboru virtuálního pevného disku**. Aktualizace **hostovat ukládání do mezipaměti** nastavení disku a pak vyberte **OK** uložte novou konfiguraci disku pro virtuální počítač.    
  ![Příklad: Připojit soubor virtuálního pevného disku](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Poté, co Azure Stack disk vytvoří a připojí ho k virtuálnímu počítači, nový disk je uvedený v nastavení disku virtuálního počítače v rámci **datové disky**.   
  ![Příklad: Dokončení disk attach](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Chcete-li přidat více nespravované disky virtuálního počítače pomocí Powershellu
Prostředí PowerShell můžete použít pro zřízení virtuálního počítače a přidat nový datový disk nebo připojit existující **VHD** souboru jako datového disku.

**Add-AzureRmVMDataDisk** rutina přidá datový disk k virtuálnímu počítači. Datový disk můžete přidat při vytváření virtuálního počítače nebo datový disk můžete přidat do existujícího virtuálního počítače. Zadejte **VhdUri** parametr k distribuci discích, které mají různé kontejnery.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Použití datových disků k vytvoření nového virtuálního počítače
Následující příklady používají příkazy prostředí PowerShell k vytvoření virtuálního počítače pomocí tří datových disků, každý umístěny do jiného kontejneru.

První příkaz vytvoří objekt virtuálního počítače a uloží jej v *$VirtualMachine* proměnné. Příkaz přiřadí název a velikost virtuálního počítače.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

Následující tři příkazy přiřadit cesty tří datových disků, aby *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, a *$DataDiskVhdUri03* proměnné. Definujte název jiné cestě v adrese URL pro distribuci discích, které mají různé kontejnery.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

Poslední tři příkazy přidat datové disky virtuálního počítače uloženého v *$VirtualMachine*. Každý příkaz určuje název, umístění a další vlastnosti disku. Identifikátor URI každý disk je uložený v *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, a *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Pomocí následujících příkazů Powershellu k přidání konfigurace operačního systému disku a sítě k virtuálnímu počítači a pak spusťte nový virtuální počítač.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Přidat datové disky existujícího virtuálního počítače
Následující příklady používají příkazy Powershellu přidáte tři datové disky existujícího virtuálního počítače.
První příkaz načte virtuální počítač s názvem virtuální počítač s použitím **Get-AzureRmVM** rutiny. Příkaz uloží virtuální počítač *$VirtualMachine* proměnné.
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
Následující tři příkazy přiřadit $DataDiskVhdUri01 $DataDiskVhdUri02 a $DataDiskVhdUri03 proměnné cest ke třem datovým diskům.  Názvy jinou cestu v vhduri ukazují různé kontejnery pro umístění disku.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  Následující tři příkazy přidat datové disky virtuálního počítače uloženého v *$VirtualMachine* proměnné. Každý příkaz určuje název, umístění a další vlastnosti disku. Identifikátor URI každý disk je uložený v *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, a *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  Poslední příkaz aktualizuje stav virtuálního počítače uloženého v *$VirtualMachine* v -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Další kroky
Další informace o virtuálních počítačích Azure Stack, najdete v článku [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
