---
title: Vyrovnávání zatížení na více konfiguracích IP – Azure CLI
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte o vyrovnávání zatížení napříč primární a sekundární konfigurace IP pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075966"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Vyrovnávání zatížení ve více konfiguracích IP pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [Cli](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Tento článek popisuje, jak používat Azure Load Balancer s více IP adresami na sekundární síťové rozhraní (NIC). V tomto scénáři máme dva virtuální počítače se systémem Windows, každý s primární a sekundární nic. Každá ze sekundárních nic má dvě konfigurace IP. Každý virtuální virtuální virtuální mše hostuje weby contoso.com i fabrikam.com. Každý web je vázán na jednu z konfigurací IP na sekundární nic. Nástroj Azure Load Balancer používáme k vystavení dvou front-endových IP adres, jedné pro každý web, k distribuci provozu do příslušné konfigurace IP webu. Tento scénář používá stejné číslo portu v obou front-endů, stejně jako oba back-endového fondu IP adresy.

![Obrázek scénáře LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Kroky pro vyrovnávání zatížení ve více konfiguracích IP

Postupujte podle následujících kroků k dosažení scénáře popsaného v tomto článku:

1. Nainstalujte Azure PowerShell. Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.
2. Vytvořte skupinu prostředků pomocí následujících nastavení:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Další informace naleznete v tématu Krok 2 vytvoření [skupiny prostředků](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Vytvořte sadu dostupnosti,](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) která bude obsahovat vaše virtuální počítače. V tomto scénáři použijte následující příkaz:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Postupujte podle pokynů kroky 3 až 5 v vytvoření článku [virtuálního počítače systému Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) připravit vytvoření virtuálního počítače s jednou nic. Spusťte krok 6.1 a místo kroku 6.2 použijte následující:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Pak [dokončete vytvoření](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) kroků virtuálního aplikace pro Windows 6.3 až 6.8.

5. Přidejte druhou konfiguraci IP do každého z virtuálních počítačů. Postupujte podle pokynů v [článku Přiřazení více IP adres virtuálním počítačům.](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) Použijte následující nastavení konfigurace:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Pro účely tohoto kurzu není nutné přidružit sekundární konfigurace IP adres k veřejným IP adresám. Chcete-li odebrat veřejnou část přidružení IP adresy, upravte příkaz.

6. Znovu proveďte kroky 4 až 6 tohoto článku pro VM2. Nezapomeňte při tom nahradit název virtuálního virtuálního montovana na VM2. Všimněte si, že není nutné vytvořit virtuální síť pro druhý virtuální počítač. Můžete nebo nemusíte vytvořit novou podsíť na základě případu použití.

7. Vytvořte dvě veřejné IP adresy a uložte je do příslušných proměnných, jak je znázorněno na obrázku:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Vytvořte dvě front-endové konfigurace IP adres:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Vytvořte fondy back-endových adres, sondu a pravidla vyrovnávání zatížení:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Po vytvoření těchto prostředků vytvořte vyvažovači zatížení:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Přidejte do nově vytvořeného nástroje pro vyrovnávání zatížení druhý fond adres back-end a konfiguraci ip adres front-endu:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Níže uvedené příkazy získají síťové karty a pak přidají obě konfigurace IP jednotlivých sekundárních nic do fondu back-endových adres nástroje pro vyrovnávání zatížení:

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

13. Nakonec je nutné nakonfigurovat záznamy o prostředcích DNS tak, aby ukazovaly na příslušnou front-endovou IP adresu vykladače zatížení. Můžete hostovat své domény v Azure DNS. Další informace o používání Azure DNS s Balancer, najdete [v tématu použití Azure DNS s jinými službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak kombinovat služby vyrovnávání zatížení v Azure v [použití služeb vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Zjistěte, jak můžete použít různé typy protokolů v Azure ke správě a odstraňování problémů s vyrovnávání zatížení v [protokolech Azure Monitor pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md).
