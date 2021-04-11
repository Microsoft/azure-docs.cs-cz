---
title: Vytvoření nového virtuálního přepínače v Azure Stack Edge přes PowerShell
description: Popisuje, jak vytvořit virtuální přepínač na zařízení Azure Stack Edge pomocí prostředí PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556120"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>Vytvoření nového virtuálního přepínače v grafickém procesoru Azure Stack Edge pro pomocí PowerShellu

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak vytvořit nový virtuální přepínač na zařízení GPU Azure Stack Edge pro. Můžete například vytvořit nový virtuální přepínač, pokud chcete, aby se virtuální počítače připojovaly prostřednictvím jiného fyzického síťového portu.

## <a name="vm-deployment-workflow"></a>Pracovní postup nasazení virtuálních počítačů

1. Připojte se k rozhraní PowerShell na svém zařízení.
2. Dotaz k dostupným fyzickým síťovým rozhraním
3. Vytvořte virtuální přepínač.
4. Ověřte, zda je virtuální síť a podsíť automaticky vytvořena.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Máte přístup ke klientskému počítači, který má přístup k rozhraní PowerShell vašeho zařízení. Viz [připojení k rozhraní PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

    Na klientském počítači by měl být spuštěn [podporovaný operační systém](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).

- Pomocí místního uživatelského rozhraní můžete na svém zařízení povolit výpočetní výkon na jednom z fyzických síťových rozhraní, podle pokynů v tématu [Povolení výpočetní sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) v zařízení. 


## <a name="connect-to-the-powershell-interface"></a>Připojte se k rozhraní PowerShellu.

[Připojte se k rozhraní PowerShell vašeho zařízení](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="query-available-network-interfaces"></a>Dotázat se na dostupná síťová rozhraní

1. Pomocí následujícího příkazu zobrazte seznam fyzických síťových rozhraní, na kterých můžete vytvořit nový virtuální přepínač. Vyberete jedno z těchto síťových rozhraní.

    ```powershell
    Get-NetAdapter -Physical
    ```
    Tady je příklad výstupu:
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. Vyberte síťové rozhraní, které je:

    - Ve stavu **nahoru** . 
    - Nepoužívá se u žádných stávajících virtuálních přepínačů. V současné době je možné nakonfigurovat pouze jeden virtuální přepínač pro každé síťové rozhraní. 
    
    Pokud chcete ověřit existující virtuální přepínač a přidružení síťového rozhraní, spusťte `Get-HcsExternalVirtualSwitch` příkaz.
 
    Tady je příklad výstupu.

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    V této instanci je port 2 přidružený k existujícímu virtuálnímu přepínači a neměl by se používat.

## <a name="create-a-virtual-switch"></a>Vytvoření virtuálního přepínače

Pomocí následující rutiny vytvořte nový virtuální přepínač na zadaném síťovém rozhraní. Po dokončení této operace můžou vaše výpočetní instance používat novou virtuální síť.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

Pomocí `Get-HcsExternalVirtualSwitch` příkazu Identifikujte nově vytvořený přepínač. Nový přepínač, který je vytvořen, se nazývá `vswitch-<InterfaceAlias>` . 

Tady je příklad výstupu:

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>Ověření sítě, podsítě 

Po vytvoření nového virtuálního přepínače Azure Stack grafický procesor Edge pro automaticky vytvoří virtuální síť a podsíť, která to odpovídá. Tuto virtuální síť můžete použít při vytváření virtuálních počítačů.

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>Další kroky

- [Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro pomocí Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro pomocí Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
