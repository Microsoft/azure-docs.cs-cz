---
title: Vytvoření imagí virtuálních počítačů z specializované image virtuálního pevného disku s Windows pro zařízení s grafickým procesorem Azure Stack Edge pro
description: Popisuje postup vytvoření imagí virtuálních počítačů z specializovaných imagí počínaje virtuálním pevným diskem systému Windows nebo diskem VHDX. Tato specializovaná image slouží k vytváření imagí virtuálních počítačů pro použití s virtuálními počítači nasazenými na zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575938"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Nasazení virtuálního počítače z specializované image na zařízení GPU Azure Stack Edge pro pomocí Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje kroky potřebné k nasazení virtuálního počítače na zařízení GPU Azure Stack Edge pro z specializované image. 

Pokud chcete připravit zobecněnou image pro nasazení virtuálních počítačů v grafickém procesoru Azure Stack Edge pro, přečtěte si téma [Příprava generalizované image z VHD s Windows](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) nebo [Příprava generalizované image z ISO](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md).

## <a name="about-vm-images"></a>O imagí virtuálních počítačů

K vytvoření *specializované* Image nebo *generalizované* image se dá použít soubor VHD nebo VHDX Windows. Následující tabulka shrnuje hlavní rozdíly mezi *specializovanými* a *zobecněnými* obrázky.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Pracovní postup

Pracovní postup vysoké úrovně pro nasazení virtuálního počítače z specializované Image je:

1. Zkopírujte virtuální pevný disk do místního účtu úložiště na zařízení GPU Azure Stack Edge pro.
1. Vytvořte nový spravovaný disk z virtuálního pevného disku.
1. Vytvořte nový virtuální počítač ze spravovaného disku a připojte spravovaný disk.

## <a name="prerequisites"></a>Požadavky

Než budete moct nasadit virtuální počítač do svého zařízení přes PowerShell, ujistěte se, že:

- Máte přístup k klientovi, který budete používat pro připojení k vašemu zařízení.
    - Váš klient běží na [podporovaném operačním systému](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Klient je nakonfigurován pro připojení k místnímu Azure Resource Manager zařízení podle pokynů v tématu [připojení k Azure Resource Manager pro vaše zařízení](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="verify-the-local-azure-resource-manager-connection"></a>Ověření místního Azure Resource Managerho připojení

Ověřte, že se váš klient může připojit k místnímu Azure Resource Manager. 

1. Volat rozhraní API místních zařízení k ověření:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Zadejte uživatelské jméno `EdgeArmUser` a heslo pro připojení prostřednictvím Azure Resource Manager. Pokud heslo nebudete volat, [resetujte heslo pro Azure Resource Manager](azure-stack-edge-gpu-set-azure-resource-manager-password.md) a použijte toto heslo pro přihlášení.

## <a name="deploy-vm-from-specialized-image"></a>Nasazení virtuálního počítače z specializované image

Následující oddíly obsahují podrobné pokyny k nasazení virtuálního počítače z specializované image.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Kopírovat VHD do místního účtu úložiště na zařízení

Pomocí těchto kroků zkopírujte virtuální pevný disk do místního účtu úložiště:

1. Zkopírujte zdrojový virtuální pevný disk do místního účtu služby Blob Storage na Azure Stack Edge.

1. Poznamenejte si výsledný identifikátor URI. Tento identifikátor URI použijete v pozdějším kroku.

    Informace o vytvoření a přístup k účtu místního úložiště najdete v částech [Vytvoření účtu úložiště](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) prostřednictvím [nahrání VHD](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) v článku [nasazení virtuálních počítačů na zařízení Azure Stack Edge přes Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Vytvoření spravovaného disku z VHD

Pomocí těchto kroků můžete vytvořit spravovaný disk z virtuálního pevného disku, který jste nahráli do účtu úložiště dříve:

1. Nastavte některé parametry.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Tady je příklad výstupu.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Vytvořte nový spravovaný disk.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Tady je příklad výstupu. Toto umístění je nastavené na umístění místního účtu úložiště a je vždycky `DBELocal` pro všechny účty místního úložiště na zařízení GPU Azure Stack Edge pro. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Vytvoření virtuálního počítače ze spravovaného disku

Pomocí těchto kroků vytvoříte virtuální počítač ze spravovaného disku:

1. Nastavte některé parametry.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP`Parametr je nepovinný. Pomocí tohoto parametru můžete přiřadit statickou IP adresu, jinak výchozí je dynamická IP adresa pomocí protokolu DHCP.

    Tady je příklad výstupu. V tomto příkladu je pro všechny prostředky virtuálních počítačů zadána stejná skupina prostředků, i když můžete v případě potřeby vytvořit a zadat samostatné skupiny prostředků pro prostředky.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Získejte informace o virtuální síti a vytvořte nové síťové rozhraní.

    Tato ukázka předpokládá, že vytváříte jedno síťové rozhraní ve výchozí virtuální síti `ASEVNET` , které je přidruženo k výchozí skupině prostředků `ASERG` . V případě potřeby můžete zadat alternativní virtuální síť nebo vytvořit více síťových rozhraní. Další informace najdete v tématu [Přidání síťového rozhraní k virtuálnímu počítači prostřednictvím Azure Portal](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Tady je příklad výstupu.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Vytvořte nový objekt konfigurace virtuálního počítače.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Přidejte síťové rozhraní k virtuálnímu počítači.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Nastavte vlastnosti disku operačního systému na virtuálním počítači.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    Poslední příznak v tomto příkazu bude `-Windows` nebo `-Linux` v závislosti na operačním systému, který používáte pro virtuální počítač.

1. Vytvořte virtuální počítač.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Tady je příklad výstupu. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Odstranění virtuálních počítačů a prostředků

Tento článek používá jenom jednu skupinu prostředků k vytvoření všech prostředků virtuálního počítače. Odstraněním této skupiny prostředků se odstraní virtuální počítač a všechny související prostředky. 

1. Nejprve zobrazte všechny prostředky vytvořené v rámci skupiny prostředků.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Tady je příklad výstupu.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Odstraňte skupinu prostředků a všechny související prostředky.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Tady je příklad výstupu.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Ověřte, že se skupina prostředků odstranila. Načte všechny skupiny prostředků, které na zařízení existují. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Tady je příklad výstupu.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Další kroky

- [Příprava generalizované image z VHD s Windows k nasazení virtuálních počítačů na procesorovém GPU Azure Stack Edge pro](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Příprava generalizované image z ISO na nasazení virtuálních počítačů v grafickém procesoru Azure Stack Edge pro](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)