---
title: Použití vlastních tras Azure k povolení aktivace prostřednictvím služby správy klíčů s vynuceným tunelovým propojením | Microsoft Docs
description: Ukazuje, jak pomocí vlastních tras Azure povolit aktivaci pomocí služby správy klíčů při vynuceném tunelování v Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 1c2050969e95b521554bba100b688add3a987a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526737"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Aktivace systému Windows ve scénáři vynuceného tunelování se nezdařila

Tento článek popisuje, jak vyřešit potíže s aktivací služby správy klíčů, ke kterým může dojít při povolování vynuceného tunelového propojení v rámci připojení VPN typu Site-to-site nebo v ExpressRoute scénářích.

## <a name="symptom"></a>Příznak

V podsítích Azure Virtual Network povolíte [vynucené tunelování](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) , které směruje veškerý internetový provoz zpátky do vaší místní sítě. V tomto scénáři se virtuální počítače Azure se systémem Windows nepodaří aktivovat systém Windows.

## <a name="cause"></a>Příčina

Virtuální počítače Azure s Windows se musí připojit k serveru služby správy klíčů Azure pro aktivaci Windows. Aktivace vyžaduje, aby žádost o aktivaci pocházela z veřejné IP adresy Azure. Ve scénáři vynucené tunelování se aktivace nezdařila, protože požadavek na aktivaci pochází z místní sítě, nikoli z veřejné IP adresy Azure.

## <a name="solution"></a>Řešení

Pokud chcete tento problém vyřešit, použijte vlastní trasu Azure pro směrování aktivačních dat do serveru služby Azure KMS.

IP adresa serveru služby správy klíčů pro globální cloud Azure je 23.102.135.246. Název DNS je kms.core.windows.net. Pokud používáte jiné platformy Azure, jako je Azure Německo, musíte použít IP adresu odpovídajícího serveru služby správy klíčů. Další informace najdete v následující tabulce:

|Platforma| DNS SLUŽBY SPRÁVY KLÍČŮ|IP ADRESA SLUŽBY SPRÁVY KLÍČŮ|
|------|-------|-------|
|Globální Azure|kms.core.windows.net|23.102.135.246|
|Azure (Německo)|kms.core.cloudapi.de|51.4.143.248|
|Azure pro vládu USA|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure (Čína) 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


K přidání vlastní trasy použijte následující postup:

### <a name="for-resource-manager-vms"></a>Pro Správce prostředků virtuální počítače

 

> [!NOTE] 
> Aktivace používá veřejné IP adresy a bude mít vliv na standardní SKU Load Balancer konfiguraci. Zkontrolujte pečlivě [odchozí připojení v Azure](../../load-balancer/load-balancer-outbound-connections.md) , abyste se dozvěděli o požadavcích.

1. Otevřete Azure PowerShell a [Přihlaste se ke svému předplatnému Azure](/powershell/azure/authenticate-azureps).
2. Spusťte následující příkazy:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Přejít na virtuální počítač, který obsahuje problémy s aktivací. Použijte [PsPing](/sysinternals/downloads/psping) k otestování, jestli se může připojit k serveru služby správy klíčů:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Zkuste aktivovat Windows a podívejte se, jestli se problém vyřeší.

### <a name="for-classic-vms"></a>Pro klasické virtuální počítače

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Otevřete Azure PowerShell a [Přihlaste se ke svému předplatnému Azure](/powershell/azure/authenticate-azureps).
2. Spusťte následující příkazy:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Přejít na virtuální počítač, který obsahuje problémy s aktivací. Použijte [PsPing](/sysinternals/downloads/psping) k otestování, jestli se může připojit k serveru služby správy klíčů:

    ```console
    psping kms.core.windows.net:1688
    ```

4. Zkuste aktivovat Windows a podívejte se, jestli se problém vyřeší.

## <a name="next-steps"></a>Další kroky

- [Instalační klíče klienta služby správy klíčů](/windows-server/get-started/kmsclientkeys)
- [Kontrola a výběr metod aktivace](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj134256(v=ws.11))
