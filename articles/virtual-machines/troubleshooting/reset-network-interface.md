---
title: Resetování síťové rozhraní pro virtuální počítač Windows Azure | Dokumentace Microsoftu
description: Ukazuje, jak resetování síťové rozhraní virtuálního počítače Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982875"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Resetování síťové rozhraní pro virtuální počítač Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Tento článek ukazuje, jak resetování síťové rozhraní pro virtuální počítač Windows Azure pro řešení potíží, pokud se nemůžete připojit k Microsoft Azure Windows virtuální počítač (VM) po:

* Jste zakázali výchozí síťové rozhraní (NIC). 
* Ručně nastavit statickou IP adresu pro síťové rozhraní 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Resetování síťové rozhraní

### <a name="for-vms-deployed-in-resource-group-model"></a>Pro virtuální počítače nasazené v modelu skupiny prostředků

1.  Přejděte na [Azure Portal](https://ms.portal.azure.com).
2.  Vyberte ovlivněné virtuální počítač.
3.  Vyberte **sítě** a pak vyberte síťové rozhraní virtuálního počítače.

    ![Síťové rozhraní umístění](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Vyberte **konfigurací protokolu IP**.
5.  Vyberte IP adresu. 
6.  Pokud **přiřazení privátní IP adresy** není **statické**, změňte ho na **statické**.
7.  Změnit **IP adresu** na jinou IP adresu, která je dostupná v podsíti.
8. Virtuální počítač se restartuje za účelem inicializace nového síťového rozhraní v systému.
9.  Zkuste RDP k vašemu počítači. V případě úspěchu, můžete změnit privátní IP adresu zpět na původní Pokud byste o ni. V opačném případě ho nechat. 

#### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ujistěte se, že máte [nejnovější Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nainstalovaná
2. Spusťte relaci Azure Powershellu se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Zkuste RDP k vašemu počítači.  V případě úspěchu, můžete změnit privátní IP adresu zpět na původní Pokud byste o ni. V opačném případě ho nechat.

### <a name="for-classic-vms"></a>Pro klasické virtuální počítače

Resetování síťové rozhraní, postupujte podle těchto kroků:

#### <a name="use-azure-portal"></a>Použití webu Azure Portal

1.  Přejděte na [Azure Portal]( https://ms.portal.azure.com).
2.  Vyberte **virtuální počítače (Classic)**.
3.  Vyberte ovlivněné virtuální počítač.
4.  Vyberte **IP adresy**.
5.  Pokud **přiřazení privátní IP adresy** není **statické**, změňte ho na **statické**.
6.  Změnit **IP adresu** na jinou IP adresu, která je dostupná v podsíti.
7.  Vyberte **Uložit**.
8.  Virtuální počítač se restartuje za účelem inicializace nového síťového rozhraní v systému.
9.  Zkuste RDP k vašemu počítači. V případě úspěchu se můžete vrátit privátní IP adresu zpět na původní.  

#### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ujistěte se, že máte [nejnovější Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nainstalované.
2. Spusťte relaci Azure Powershellu se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Zkuste RDP k vašemu počítači. V případě úspěchu, můžete změnit privátní IP adresu zpět na původní Pokud byste o ni. V opačném případě ho nechat. 

## <a name="delete-the-unavailable-nics"></a>Odstranění není k dispozici síťové karty
Poté, co je to možné k počítači pomocí vzdálené plochy, je potřeba odstranit staré síťové karty, aby se zabránilo případným problémům:

1.  Spustit nástroj Správce zařízení.
2.  Vyberte **zobrazení** > **zobrazit skrytá zařízení**.
3.  Vyberte **síťové adaptéry**. 
4.  Zkontrolujte adaptéry s názvem "Síťový adaptér Microsoft Hyper-V".
5.  Možná se šedě zobrazí nedostupný adaptér. Klikněte pravým tlačítkem na adaptéru a vyberte odinstalovat.

    ![Obrázek karty síťového rozhraní](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Není k dispozici adaptéry, které mají název "Síťový adaptér Microsoft Hyper-V" pouze odinstalujte. Pokud provádíte odinstalaci všech skryté adaptérů, mohlo by dojít ke další problémy.
    >
    >

6.  Teď by měla zůstat nezaškrtnutá všechny nedostupné adaptéry vašeho systému.
