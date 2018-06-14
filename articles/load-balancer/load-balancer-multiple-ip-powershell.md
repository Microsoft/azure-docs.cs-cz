---
title: Víc konfigurací IP adres v Azure pro vyrovnávání zatížení | Microsoft Docs
description: Vyrovnávání zatížení napříč primární a sekundární konfigurace protokolu IP.
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: annahar
ms.openlocfilehash: 12a978fc85d9502ce484859b436575b67364c9c4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261717"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Víc konfigurací IP adres pomocí prostředí PowerShell pro vyrovnávání zatížení

> [!div class="op_single_selector"]
> * [Azure Portal](load-balancer-multiple-ip.md)
> * [Rozhraní příkazového řádku](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)


Tento článek popisuje, jak používat nástroj pro vyrovnávání zatížení Azure s více IP adres v sekundárním síťovém rozhraní (NIC). V tomto scénáři máme dva virtuální počítače se systémem Windows, každý s primární a sekundární síťový adaptér. Každý sekundární síťové karty má dvě konfigurace protokolu IP. Každý virtuální počítač hostuje weby contoso.com a fabrikam.com. Každý web je vázána na jednu z konfigurace protokolu IP v sekundárním síťovém adaptéru. Nástroj pro vyrovnávání zatížení Azure používáme ke zveřejnění dvě front-end IP adresy, jednu pro každý web, distribuovat provoz do příslušných konfiguraci protokolu IP pro web. Tento scénář používá stejné číslo portu mezi frontends, jak oba back-end fondu IP adres.

![Scénář LB bitové kopie](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Postup na víc konfigurací IP adres Vyrovnávání zatížení

Použijte následující postup k dosažení scénáři uvedeném v tomto článku:

1. Nainstalujte Azure PowerShell. Projděte si článek [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview), kde najdete informace o instalaci nejnovější verze prostředí Azure PowerShell, výběru předplatného a přihlášení k účtu.
2. Vytvořte skupinu prostředků s následujícím nastavením:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Další informace najdete v části Krok 2 [vytvořte skupinu prostředků](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Vytvoření skupiny dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) tak, aby obsahovala vaše virtuální počítače. V tomto scénáři použijte následující příkaz:

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Postupujte podle pokynů kroky 3 až 5 v [vytvoření virtuálního počítače s Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) článku Příprava vytvoření virtuálního počítače s jeden síťový adaptér. Spustit krok 6.1 a místo krok 6.2 použít následující:

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Dokončete [vytvoření virtuálního počítače s Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) kroky 6.3 prostřednictvím 6.8.

5. Přidejte druhý konfigurace protokolu IP do všech virtuálních počítačích. Postupujte podle pokynů v [přiřadit více IP adres k virtuálním počítačům](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) článku. Použijte následující nastavení:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    Není nutné přidružit sekundární konfigurace IP veřejné IP adresy pro účely tohoto kurzu. Upravte příkaz odebrat část přidružení veřejné IP.

6. Proveďte kroky 4 až 6 tohoto článku znovu pro virtuálního počítače 2. Nezapomeňte nahradit název virtuálního počítače do virtuálního počítače 2 při této činnosti. Všimněte si, že není potřeba vytvořit virtuální síť pro druhý virtuální počítač. Může nebo nemusí vytvořit novou podsíť, na základě vaší případu použití.

7. Vytvořte dvě veřejné IP adresy a uložit je do příslušné proměnné, jak je znázorněno:

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Vytvořte dvě konfigurace IP front-endu:

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Vytvoření vašeho back-endové fondy adres, sondu a pravidel Vyrovnávání zatížení:

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Až budete mít tyto prostředky vytvořit, vytvořte nástroj pro vyrovnávání zatížení:

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Nástroj pro vyrovnávání zatížení nově vytvořený přidejte druhý back-end adresy fondu a front-endové konfiguraci protokolu IP:

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Níže uvedených příkazů získat síťovými adaptéry a poté přidejte obě konfigurace IP každý sekundární síťové karty na back-endových adres nástroje pro vyrovnávání zatížení:

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Nakonec je nutné nakonfigurovat záznamy prostředků DNS tak, aby odkazoval na příslušné front-endovou IP adresu služby Vyrovnávání zatížení. Může hostovat vaše domény v Azure DNS. Další informace o používání s nástrojem pro vyrovnávání zatížení Azure DNS najdete v tématu [pomocí Azure DNS s jinými službami Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak kombinací v Azure v rámci služby Vyrovnávání zatížení [pomocí služby Vyrovnávání zatížení v Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Zjistěte, jak můžete použít různé typy protokolů v Azure ke správě a odstraňování potíží pro vyrovnávání zatížení v [protokolu pro vyrovnávání zatížení Azure analytics](../load-balancer/load-balancer-monitor-log.md).
