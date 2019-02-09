---
title: Povolit aktivaci prostřednictvím služby správy KLÍČŮ u vynuceného tunelování pomocí Azure vlastní trasy | Dokumentace Microsoftu
description: Ukazuje, jak použít Azure vlastní trasy k povolení aktivace prostřednictvím služby správy KLÍČŮ při použití vynuceného tunelování v Azure.
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
ms.openlocfilehash: b121996530ea0618fc757f1ae12dfafde10ed7bb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979373"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Aktivace Windows selže v případě vynuceného tunelování

Tento článek popisuje, jak vyřešit problém aktivace služby správy KLÍČŮ, se kterým může docházet při povolení vynuceného tunelování v připojení VPN typu site-to-site nebo ExpressRoute scénáře.

## <a name="symptom"></a>Příznak

Povolíte [vynucené tunelování](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) v Azure podsítí virtuální sítě směrovat všechen provoz směřující na Internet zpět do místní sítě. V tomto scénáři Azure virtuální počítače (VM), na kterých běží Windows Server 2012 R2 (nebo novější verze systému Windows) úspěšně aktivace Windows. Virtuální počítače, na kterých běží starší verze Windows ale selhala aktivace Windows.

## <a name="cause"></a>Příčina

Virtuální počítače Windows Azure je nutné se připojit k serveru Azure prostřednictvím služby správy KLÍČŮ pro aktivaci Windows. Aktivace vyžaduje, že žádost o aktivaci pocházejí z Azure veřejné IP adresy. Vynucené tunelování scénář tato aktivace selže, protože požadavek na aktivaci přichází z místní sítě, místo z Azure veřejné IP adresy.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, použijte Azure aktivace vlastní trasy pro směrování provozu na server služby správy KLÍČŮ Azure.

IP adresa serveru služby správy KLÍČŮ pro Azure globální cloud je 23.102.135.246. Názvu DNS je kms.core.windows.net. Pokud používáte jiné platformy Azure jako Azure Germany, je nutné použít IP adresu odpovídající serveru služby správy KLÍČŮ. Další informace najdete v tématu v následující tabulce:

|Platforma| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Chcete-li přidat vlastní trasy, postupujte takto:

### <a name="for-resource-manager-vms"></a>Pro virtuální počítače Resource Manageru

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. Otevřete prostředí Azure PowerShell a potom [přihlásit ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Spusťte následující příkazy:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable
    ```
3. Přejděte k virtuálnímu počítači, který má problémy s aktivací. Použití [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) chcete otestovat, jestli se můžete připojit k serveru služby správy KLÍČŮ:

        psping kms.core.windows.net:1688

4. Pokusu o aktivaci Windows a zobrazit, pokud se problém vyřeší.

### <a name="for-classic-vms"></a>Pro klasické virtuální počítače

1. Otevřete prostředí Azure PowerShell a potom [přihlásit ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
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

3. Přejděte k virtuálnímu počítači, který má problémy s aktivací. Použití [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) chcete otestovat, jestli se můžete připojit k serveru služby správy KLÍČŮ:

        psping kms.core.windows.net:1688

4. Pokusu o aktivaci Windows a zobrazit, pokud se problém vyřeší.

## <a name="next-steps"></a>Další postup

- [Instalační klíče klienta služby správy KLÍČŮ klíčů](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Kontrola a vyberte možnost aktivace metody](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
