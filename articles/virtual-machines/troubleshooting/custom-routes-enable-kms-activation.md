---
title: Pomocí vlastních tras Azure povolte aktivaci KMS s vynuceným tunelovým propojením | Dokumenty společnosti Microsoft
description: Ukazuje, jak používat vlastní trasy Azure k povolení aktivace KMS při použití vynuceného tunelového propojení v Azure.
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
ms.openlocfilehash: 90034a56fcf5211059d37270e12391249f7a16b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920157"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Aktivace systému Windows se nezdaří v případě vynuceného tunelového propojení

Tento článek popisuje, jak vyřešit problém aktivace služby KMS, ke kterému může dojít při povolení vynuceného tunelování v připojení VPN mezi lokalitami nebo ve scénářích ExpressRoute.

## <a name="symptom"></a>Příznak

Vynucené [tunelové propojení](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) v podsítích virtuálnísítě Azure umožníte tak, že přesměrujete veškerý internetový provoz zpět do místní sítě. V tomto scénáři virtuální počítače Azure (VM), které běží windows nezdaří aktivovat Windows.

## <a name="cause"></a>Příčina

Virtuální počítače Azure pro Windows se musí připojit k serveru Azure KMS pro aktivaci Windows. Aktivace vyžaduje, aby žádost o aktivaci pocházet z veřejné IP adresy Azure. V případě vynuceného tunelového propojení se aktivace nezdaří, protože požadavek na aktivaci pochází z místní sítě namísto z veřejné IP adresy Azure.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte vlastní trasu Azure k směrování aktivační provoz na server Azure KMS.

IP adresa serveru KMS pro globální cloud Azure je 23.102.135.246. Jeho název DNS je kms.core.windows.net. Pokud používáte jiné platformy Azure, jako je Azure Germany, musíte použít IP adresu odpovídajícího serveru KMS. Další informace naleznete v následující tabulce:

|Platforma| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Globální|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Chcete-li přidat vlastní trasu, postupujte takto:

### <a name="for-resource-manager-vms"></a>Pro virtuální aplikace Správce prostředků

 

> [!NOTE] 
> Aktivace používá veřejné IP adresy a bude ovlivněna konfigurací standardního nástroje pro vyrovnávání zatížení skladových položk. Pečlivě zkontrolujte [odchozí připojení v Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) abyste se dozvěděli o požadavcích.

1. Otevřete Azure PowerShell a pak [se přihlaste k předplatnému Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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
3. Přejděte na virtuální počítače, který má problémy s aktivací. Pomocí [příkazu PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) otestujte, zda se může dostat na server SLUŽBY SPRÁVY KLÍČŮ:

        psping kms.core.windows.net:1688

4. Pokuste se aktivovat systém Windows a zjistěte, zda je problém vyřešen.

### <a name="for-classic-vms"></a>Pro klasické virtuální měsíčové

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

1. Otevřete Azure PowerShell a pak [se přihlaste k předplatnému Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Přejděte na virtuální počítače, který má problémy s aktivací. Pomocí [příkazu PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) otestujte, zda se může dostat na server SLUŽBY SPRÁVY KLÍČŮ:

        psping kms.core.windows.net:1688

4. Pokuste se aktivovat systém Windows a zjistěte, zda je problém vyřešen.

## <a name="next-steps"></a>Další kroky

- [Instalační klíče klienta služby správy klíčů](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Zkontrolovat a vybrat metody aktivace](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
