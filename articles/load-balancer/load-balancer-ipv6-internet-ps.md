---
title: Vytvoření internetového nástroje pro vyrovnávání zatížení s protokolem IPv6-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení s protokolem IPv6 pomocí prostředí PowerShell pro Správce prostředků
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, duální zásobník, veřejná IP adresa, nativní IPv6, mobilní zařízení, IoT
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 43203a756bcb42c7d00de9c11e9223f1d8b9e2a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87001582"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Začínáme vytvářet internetový nástroj pro vyrovnávání zatížení s protokolem IPv6 pomocí prostředí PowerShell pro Správce prostředků

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Šablona](load-balancer-ipv6-internet-template.md)

>[!NOTE] 
>Tento článek popisuje úvodní funkci IPv6, která umožňuje základním nástrojům pro vyrovnávání zatížení poskytovat připojení protokolem IPv4 i IPv6. K dispozici je teď komplexní připojení pomocí protokolu [IPv6 pro Azure virtuální sítě](../virtual-network/ipv6-overview.md) , které integruje připojení IPv6 k vašim virtuálním sítím a obsahuje klíčové funkce, jako jsou pravidla skupiny zabezpečení sítě IPv6, uživatelem definované směrování IPv6, protokol IPv6 Basic a standardní vyrovnávání zatížení a další.  Protokol IPv6 pro Azure virtuální sítě je doporučený standard pro aplikace IPv6 v Azure. Viz [protokol IPv6 pro nasazení PowerShellu pro virtuální síť Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Ukázkový scénář nasazení

Následující diagram znázorňuje řešení vyrovnávání zatížení, které je nasazené v tomto článku.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Internetový Load Balancer s IPv4 a veřejnou IP adresou IPv6
* dvě pravidla vyrovnávání zatížení pro mapování veřejných virtuálních IP adres na privátní koncové body
* Skupina dostupnosti, která obsahuje dva virtuální počítače
* dva virtuální počítače
* virtuální síťové rozhraní pro každý virtuální počítač s přiřazenými adresami IPv4 i IPv6

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Nasazení řešení pomocí Azure PowerShell

Následující kroky ukazují, jak vytvořit internetový nástroj pro vyrovnávání zatížení pomocí Azure Resource Manager pomocí prostředí PowerShell. Při Azure Resource Manager se každý prostředek vytvoří a nakonfiguruje jednotlivě a pak se spojí dohromady a vytvoří prostředek.

Pro nasazení nástroje pro vyrovnávání zatížení vytvoříte a nakonfigurujete následující objekty:

* Konfigurace protokolu IP front-endu obsahuje veřejné IP adresy pro příchozí síťový provoz.
* Fond back-end adres – obsahuje síťová rozhraní (nic), ve kterých virtuální počítače přijímají síťový provoz z nástroje pro vyrovnávání zatížení.
* Pravidla vyrovnávání zatížení – obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port v back-endovém fondu adres.
* Pravidla příchozího překladu adres (NAT) – obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port konkrétního virtuálního počítače v back-endovém fondu adres.
* Testy – obsahuje testy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů v back-endovém fondu adres.

Další informace najdete v tématu [Azure Load Balancer Components](./components.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Nastavení prostředí PowerShell pro použití Resource Manageru

Ujistěte se, že máte nejnovější produkční verzi modulu Azure Resource Manager pro prostředí PowerShell.

1. Přihlášení do Azure

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

    Po zobrazení výzvy zadejte své přihlašovací údaje.

2. Zkontrolujte předplatná pro příslušný účet.

   ```azurepowershell-interactive
    Get-AzSubscription
    ```

3. Zvolte předplatné Azure, které chcete použít.

    ```azurepowershell-interactive
    Select-AzSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Vytvořit skupinu prostředků (Tento krok přeskočte, pokud se používá existující skupina prostředků)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Vytvoření virtuální sítě a veřejné IP adresy pro front-endový fond IP adres

1. Vytvořte virtuální síť s podsítí.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Vytvořte prostředky služby Azure Public IP adresa (PIP) pro fond front-end IP adres. `-DomainNameLabel`Před spuštěním následujících příkazů nezapomeňte změnit hodnotu. Hodnota musí být jedinečná v rámci oblasti Azure.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Nástroj pro vyrovnávání zatížení používá označení domény veřejné IP adresy jako předponu svého plně kvalifikovaného názvu domény. V tomto příkladu jsou plně kvalifikované názvy domény *lbnrpipv4.westus.cloudapp.Azure.com* a *lbnrpipv6.westus.cloudapp.Azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Vytvoření Front-End konfigurace protokolu IP a fondu adres Back-End

1. Vytvořte konfiguraci front-endu adres, která používá veřejné IP adresy, které jste vytvořili.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Vytvořte fondy back-endové adresy.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Vytvoření pravidel pro uvolnění, pravidel NAT, sondy a nástroje pro vyrovnávání zatížení

Tento příklad vytvoří následující položky:

* pravidlo překladu adres (NAT) pro překlad veškerého příchozího provozu na portu 443 na port 4443
* Pravidlo nástroje pro vyrovnávání zatížení, které vyrovnává zatížení veškerého příchozího provozu na portu 80 na port 80 na adresách v back-endovém fondu.
* pravidlo nástroje pro vyrovnávání zatížení, které umožňuje připojení RDP k virtuálním počítačům na portu 3389.
* pravidlo testu pro kontrolu stavu na stránce s názvem *HealthProbe. aspx* nebo ve službě na portu 8080
* Nástroj pro vyrovnávání zatížení, který používá všechny tyto objekty

1. Vytvořte pravidla překladu adres (NAT).

    ```azurepowershell-interactive
    $inboundNATRule1v4 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    $inboundNATRule1v6 = New-AzLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443
    ```

2. Vytvořte test stavu. Test můžete nakonfigurovat dvěma způsoby:

    Test protokolu HTTP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    nebo test TCP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    V tomto příkladu budeme používat sondy TCP.

3. Vytvořte pravidlo nástroje pro vyrovnávání zatížení.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Vytvořte Nástroj pro vyrovnávání zatížení pomocí dříve vytvořených objektů.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Vytvoření síťových karet pro back-endové virtuální počítače

1. Získejte Virtual Network a Virtual Network podsíť, kde je potřeba vytvořit síťové karty.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Vytvořte konfigurace protokolu IP a síťové adaptéry pro virtuální počítače.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Vytvoření virtuálních počítačů a přiřazení nově vytvořených síťových karet

Další informace o vytvoření virtuálního počítače najdete v tématu [Vytvoření a Předkonfigurace virtuálního počítače s Windows pomocí Správce prostředků a Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Vytvoření skupiny dostupnosti a účtu úložiště

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Vytvořte každý virtuální počítač a přiřaďte předchozí vytvořené síťové karty.

    ```azurepowershell-interactive
    $mySecureCredentials= Get-Credential -Message "Type the username and password of the local administrator account."

    $vm1 = New-AzVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm1 = Set-AzVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm1 = Set-AzVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm1 = Add-AzVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
    $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
    $vm1 = Set-AzVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

    $vm2 = New-AzVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
    $vm2 = Set-AzVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
    $vm2 = Set-AzVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm2 = Add-AzVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
    $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
    $vm2 = Set-AzVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
    New-AzVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2
    ```


