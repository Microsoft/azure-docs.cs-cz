---
title: Povolit aktivaci prostřednictvím služby správy KLÍČŮ u vynuceného tunelování pomocí Azure vlastní trasy | Dokumentace Microsoftu
description: Ukazuje, jak povolit aktivaci prostřednictvím služby správy KLÍČŮ vynucené tunelování v Azure pomocí Azure vlastní trasy.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797934"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Aktivace Windows selže v případě vynuceného tunelování

Tento článek popisuje, jak vyřešit problém aktivace služby správy KLÍČŮ, který může dojít, pokud je povolené vynucené tunelování v připojení VPN typu site-to-site nebo ExpressRoute scénáře.

## <a name="symptom"></a>Příznak

Povolíte [vynucené tunelování](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) na Azure podsítí virtuální sítě směrovat všechen provoz směřující na Internet zpět do místní sítě. V tomto scénáři můžete virtuální počítače Azure (VM), na kterých běží Windows Server 2012 R2 nebo novější verze úspěšně aktivovat Windows. Však virtuální počítače, na kterých běží starší verze Windows nepodaří aktivovat Windows. 

## <a name="cause"></a>Příčina

Virtuální počítače s Windows Azure potřebujete připojit k serveru Azure prostřednictvím služby správy KLÍČŮ pro aktivaci Windows. Aktivace vyžaduje, že žádost o aktivaci musí pocházet z Azure veřejnou IP adresu. Vynucené tunelování scénář aktivace selže, protože požadavek na aktivaci se z místní sítě, místo ze Azure veřejnou IP adresu. 

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte Azure aktivace vlastní trasy pro směrování provozu na server služby správy KLÍČŮ Azure (23.102.135.246). 

IP adresa 23.102.135.246 je IP adresa serveru služby správy KLÍČŮ pro Azure globální cloud. Názvu DNS je kms.core.windows.net. Pokud používáte jiné platformy Azure jako Azure Germany, musíte použít IP adresu serveru služby správy KLÍČŮ odpovídá. Další informace najdete v tématu v následující tabulce:

|Platforma| DNS SLUŽBY SPRÁVY KLÍČŮ|SLUŽBY SPRÁVY KLÍČŮ IP|
|------|-------|-------|
|Globální Azure|KMS.Core.Windows.NET|23.102.135.246|
|Azure Germany|KMS.Core.cloudapi.de|51.4.143.248|
|Azure US Government|KMS.Core.usgovcloudapi.NET|23.97.0.13|
|Azure China 21Vianet|KMS.Core.chinacloudapi.CN|42.159.7.249|


Chcete-li přidat vlastní trasy, postupujte takto:

### <a name="for-resource-manager-vms"></a>Pro virtuální počítače Resource Manageru

1. Otevřete prostředí Azure PowerShell a potom [přihlásit ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Spusťte následující příkazy:

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. Přejděte na virtuální počítač, který má problém s aktivací, použijte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) chcete otestovat, jestli ho můžete získat přístup k serveru služby správy KLÍČŮ:

        psping kms.core.windows.net:1688

4. Pokusu o aktivaci Windows a zobrazit, pokud se problém vyřeší.

### <a name="for-classic-vms"></a>Pro klasické virtuální počítače

1. Otevřete prostředí Azure PowerShell a potom [přihlásit ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Spusťte následující příkazy:

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Přejděte na virtuální počítač, který má problém s aktivací, použijte [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) chcete otestovat, jestli ho můžete získat přístup k serveru služby správy KLÍČŮ:

        psping kms.core.windows.net:1688

4. Pokusu o aktivaci Windows a zobrazit, pokud se problém vyřeší.

## <a name="next-steps"></a>Další postup

- [Instalační klíče klienta služby správy KLÍČŮ klíčů](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Kontrola a vyberte možnost aktivace metody](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)