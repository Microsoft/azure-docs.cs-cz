---
title: Vyrovnávání zatížení u více konfigurací IP – Azure CLI
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte víc o vyrovnávání zatížení napříč primárními a sekundárními konfiguracemi IP adres pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: a2916f28be0b45eec6e9c1a85c0b8db3fb611381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417854"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Vyrovnávání zatížení u více konfigurací IP adres pomocí prostředí PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](load-balancer-multiple-ip.md)
> * [Rozhraní příkazového řádku](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

Tento článek popisuje, jak použít Azure Load Balancer s více IP adresami na sekundárním síťovém rozhraní (NIC). V tomto scénáři máme dva virtuální počítače s Windows, každý s primárním a sekundárním síťovým ADAPTÉRem. Každá ze sekundárních síťových adaptérů má dvě konfigurace protokolu IP. Každý virtuální počítač je hostitelem webů contoso.com a fabrikam.com. Každý web je vázán na jednu z konfigurací protokolu IP v sekundárním síťovém adaptéru. K vystavení přenosu dat do příslušné konfigurace protokolu IP pro web používáme Azure Load Balancer k vystavení dvou IP adres front-endu, jeden pro každý web. V tomto scénáři se používá stejné číslo portu v obou front-endu i v obou IP adresách back-end fondu.

![Obrázek scénáře vyrovnávání zatížení](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Postup vyrovnávání zatížení u více konfigurací IP adres

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Podle následujícího postupu můžete dosáhnout scénáře popsaného v tomto článku:

1. Nainstalujte Azure PowerShell. Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.
2. Vytvořte skupinu prostředků pomocí následujících nastavení:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Další informace najdete v kroku 2 tématu [Vytvoření skupiny prostředků](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Vytvořte skupinu dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) , která bude obsahovat vaše virtuální počítače. V tomto scénáři použijte následující příkaz:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Postupujte podle pokynů 3 až 5 v článku [Vytvoření virtuálního počítače s Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) a připravte vytváření virtuálního počítače s jedním síťovým adaptérem. Proveďte krok 6,1 a místo kroku 6,2 použijte následující:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Potom dokončete kroky [Vytvoření virtuálního počítače s Windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) 6,3 až 6,8.

5. Přidejte ke každému virtuálnímu počítači druhou konfiguraci protokolu IP. Postupujte podle pokynů v tématu [přiřazení více IP adres k virtuálním počítačům](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) . Použijte následující nastavení konfigurace:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Pro účely tohoto kurzu nemusíte přidružit sekundární konfigurace IP s veřejnými IP adresami. Úpravou příkazu odeberte část přidružení veřejné IP adresy.

6. Kroky 4 až 6 tohoto článku proveďte znovu pro VM2. Pokud to uděláte, nezapomeňte název virtuálního počítače nahradit na VM2. Všimněte si, že nemusíte vytvářet virtuální síť pro druhý virtuální počítač. V závislosti na vašem případu použití můžete nebo nemůžete vytvořit novou podsíť.

7. Vytvořte dvě veřejné IP adresy a uložte je do příslušných proměnných, jak je znázorněno níže:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Vytvořte dvě konfigurace IP adresy front-endu:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Vytvořte své fondy back-end adres, sondu a pravidla vyrovnávání zatížení:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Po vytvoření těchto prostředků vytvořte Nástroj pro vyrovnávání zatížení:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Do nově vytvořeného nástroje pro vyrovnávání zatížení přidejte druhý fond back-end adres a konfiguraci IP adresy front-endu:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Níže uvedené příkazy získají síťové karty a potom do fondu back-end adres nástroje pro vyrovnávání zatížení přidají obě konfigurace protokolu IP obou sekundárních síťových adaptérů:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Nakonec je třeba nakonfigurovat záznamy prostředků DNS tak, aby odkazovaly na příslušné IP adresy front-endu Load Balancer. Domény můžete hostovat v Azure DNS. Další informace o použití Azure DNS s Load Balancer najdete v tématu [použití Azure DNS s dalšími službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o tom, jak kombinovat služby Vyrovnávání zatížení v Azure [pomocí služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Přečtěte si, jak můžete pomocí různých typů protokolů v Azure spravovat a řešit potíže s vyrovnáváním zatížení v [protokolech Azure Monitor Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).