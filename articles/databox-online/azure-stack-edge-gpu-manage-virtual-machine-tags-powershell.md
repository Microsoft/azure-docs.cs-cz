---
title: Správa značek virtuálních počítačů na zařízeních s grafickým procesorem Azure Stack Edge pro prostřednictvím Azure PowerShell
description: Popisuje, jak vytvořit a spravovat značky virtuálních počítačů pro virtuální počítače běžící v Azure Stack Edge pomocí Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305547"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Správa značek virtuálních počítačů na Azure Stack Edge přes Azure PowerShell

Tento článek popisuje, jak označit virtuální počítače běžící na zařízeních s grafickým procesorem Azure Stack Edge pro pomocí Azure PowerShell.

## <a name="about-tags"></a>O značkách

Značky jsou uživatelsky definované páry klíč-hodnota, které se dají přiřadit prostředku nebo skupině prostředků. Pomocí značek můžete na virtuálních počítačích, které běží na vašem zařízení, a logicky je uspořádat do taxonomie. Značky můžete umístit na prostředek v okamžiku vytvoření nebo ho přidat do existujícího prostředku. Můžete například použít název `Organization` a hodnotu `Engineering` na všechny virtuální počítače používané technickým oddělením ve vaší organizaci.

Další informace o značkách najdete v tématu [Správa značek prostřednictvím prostředí PowerShell AzureRM](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Požadavky

Než budete moct nasadit virtuální počítač do svého zařízení přes PowerShell, ujistěte se, že:

- Máte přístup k klientovi, který budete používat pro připojení k vašemu zařízení.
    - Váš klient běží na [podporovaném operačním systému](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - Klient je nakonfigurován pro připojení k místnímu Azure Resource Manager zařízení podle pokynů v tématu [připojení k Azure Resource Manager pro vaše zařízení](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Ověřit připojení k místnímu Azure Resource Manager

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Přidání značky k virtuálnímu počítači

1. Nastavte některé parametry.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Získat objekt virtuálního počítače a jeho značky.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Přidejte značku a aktualizujte virtuální počítač. Aktualizace virtuálního počítače může trvat několik minut.

    Pomocí volitelného příznaku **-Force** můžete spustit příkaz bez potvrzení uživatele.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Další informace najdete v tématu [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Zobrazení značek virtuálního počítače

Můžete zobrazit značky použité pro konkrétní virtuální počítač běžící na vašem zařízení. 

1. Definujte parametry přidružené k virtuálnímu počítači, jehož značky chcete zobrazit.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Získejte objekt virtuálního počítače a zobrazte jeho značky.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Zobrazit značky pro všechny prostředky

Pokud chcete zobrazit aktuální seznam značek pro všechny prostředky v předplatném místní Azure Resource Manager (jiné než vaše předplatné Azure) vašeho zařízení, použijte `Get-AzureRMTag` příkaz.


Tady je příklad výstupu, když je na zařízení spuštěno více virtuálních počítačů a chcete zobrazit všechny značky na všech virtuálních počítačích.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

Předchozí výstup ukazuje, že na virtuálních počítačích, které jsou `Organization` spuštěny na vašem zařízení, existují tři značky.

Chcete-li zobrazit další podrobnosti, použijte `-Detailed` parametr.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

Předchozí výstup ukazuje, že se nacházejí tři značky, 2 virtuální počítače jsou označeny jako `Engineering` a jedna je označena jako patřící do `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Odebrání značky z virtuálního počítače

1. Nastavte některé parametry. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Získejte objekt virtuálního počítače.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Odeberte značku a aktualizujte virtuální počítač. Pomocí volitelného `-Force` příznaku spusťte příkaz bez potvrzení uživatele.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Tady je příklad výstupu:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat značky prostřednictvím PowerShellu AzureRM](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
