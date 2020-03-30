---
title: Jak obnovit síťové rozhraní pro virtuální počítač Azure Windows| Dokumenty společnosti Microsoft
description: Ukazuje, jak obnovit síťové rozhraní pro virtuální počítač Azure Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: a8bd12d98b76d5848753987c4f7bcb76d4e2266d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250072"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Jak resetovat síťové rozhraní pro virtuální počítač Azure s Windows 

Tento článek ukazuje, jak obnovit síťové rozhraní pro virtuální počítač Azure windows k vyřešení problémů, když se nemůžete připojit k virtuálnímu počítači Microsoft Azure Windows (VM) po:

* Zakážete výchozí síťové rozhraní (NIC). 
* Ručně nastavit statickou IP pro nic. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Obnovit síťové rozhraní

### <a name="for-vms-deployed-in-resource-group-model"></a>Pro virtuální chody nasazené v modelu skupiny prostředků

1.  Přejděte na [portál Azure](https://ms.portal.azure.com).
2.  Vyberte ovlivněný virtuální počítač.
3.  Vyberte **Síť a** pak vyberte síťové rozhraní virtuálního soudu.

    ![Umístění síťového rozhraní](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Vyberte **konfigurace protokolu IP**.
5.  Vyberte IP adresu. 
6.  Pokud **přiřazení Privátní IP** není **statické**, změňte jej na **Statický**.
7.  Změňte **adresu IP** na jinou adresu IP, která je k dispozici v podsíti.
8. Virtuální počítač se restartuje a inicializuje novou nic do systému.
9.  Pokuste se RDP do vašeho počítače. V případě úspěchu můžete změnit privátní IP adresu zpět na původní, pokud chcete. V opačném případě si ho můžete nechat. 

#### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ujistěte se, že máte [nainstalovanou nejnovější Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
2. Otevřete relaci Azure PowerShellu se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy:

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
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Pokuste se RDP do vašeho počítače.  V případě úspěchu můžete změnit privátní IP adresu zpět na původní, pokud chcete. V opačném případě si ho můžete nechat.

### <a name="for-classic-vms"></a>Pro klasické virtuální měsíčové

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Chcete-li obnovit síťové rozhraní, postupujte takto:

#### <a name="use-azure-portal"></a>Použití webu Azure Portal

1.  Přejděte na [portál Azure]( https://ms.portal.azure.com).
2.  Vyberte **virtuální počítače (klasické)**.
3.  Vyberte ovlivněný virtuální počítač.
4.  Vyberte **adresy IP**.
5.  Pokud **přiřazení Privátní IP** není **statické**, změňte jej na **Statický**.
6.  Změňte **adresu IP** na jinou adresu IP, která je k dispozici v podsíti.
7.  Vyberte **Uložit**.
8.  Virtuální počítač se restartuje a inicializuje novou nic do systému.
9.  Pokuste se RDP do vašeho počítače. V případě úspěchu můžete vrátit privátní IP adresu zpět na původní adresu.  

#### <a name="use-azure-powershell"></a>Použití Azure Powershell

1. Ujistěte se, že máte [nainstalovaný nejnovější Azure PowerShell.](https://docs.microsoft.com/powershell/azure/overview)
2. Otevřete relaci Azure PowerShellu se zvýšenými oprávněními (Spustit jako správce). Spusťte následující příkazy:

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
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Pokuste se RDP do vašeho počítače. V případě úspěchu můžete změnit privátní IP adresu zpět na původní, pokud chcete. V opačném případě si ho můžete nechat. 

## <a name="delete-the-unavailable-nics"></a>Odstranění nedostupných nicotnic
Po vzdálené ploše k počítači je nutné odstranit staré síťové karty, aby se zabránilo možnému problému:

1.  Spusťte Správce zařízení.
2.  Vyberte **Zobrazit** > **skrytá zařízení**.
3.  Vyberte **možnost Síťové adaptéry**. 
4.  Zkontrolujte adaptéry s názvem "Síťový adaptér Microsoft Hyper-V".
5.  Může se zobrazit nedostupný adaptér, který je zašedlý. Klepněte pravým tlačítkem myši na adaptér a vyberte odinstalovat.

    ![obrázek nic](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Odinstalujte pouze nedostupné adaptéry s názvem "Síťový adaptér Microsoft Hyper-V". Pokud odinstalujete některý z ostatních skrytých adaptérů, může to způsobit další problémy.
    >
    >

6.  Nyní by měly být všechny nedostupné adaptéry vymazány ze systému.
