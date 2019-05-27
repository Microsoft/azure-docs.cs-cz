---
title: Vytvoření nástroje pro vyrovnávání zatížení přístupem k Internetu s protokolem IPv6 – PowerShell
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit internetový nástroj pro vyrovnávání zatížení s IPv6 pomocí prostředí PowerShell pro Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejné IP adresy, nativní protokol ipv6, mobilní zařízení, iot
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: e4bc889df008283f05be5f820b66415cd38c1595
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149274"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Začínáme vytvářet internetový nástroj pro vyrovnávání zatížení s IPv6 pomocí prostředí PowerShell pro Resource Manager

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Šablona](load-balancer-ipv6-internet-template.md)


Azure Load Balancer je nástroj pro vyrovnávání zatížení úrovně 4 (TCP, UDP). Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost díky distribuci příchozích přenosů mezi instance služeb, které jsou v pořádku, v cloudových službách nebo virtuálních počítačích v sadě nástroje pro vyrovnávání zatížení. Azure Load Balancer můžete také tyto služby prezentovat na více portech, více IP adresách nebo obojím.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="example-deployment-scenario"></a>Příklad scénáře nasazení

Následující diagram znázorňuje řešení se nasazuje v tomto článku pro vyrovnávání zatížení.

![Scénář nástroje pro vyrovnávání zatížení](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

V tomto scénáři vytvoříte následující prostředky Azure:

* Vyrovnávání zatížení přístupem k Internetu s IPv4 a IPv6 veřejnou IP adresu
* Dvě pravidla vyrovnávání přiřadit privátní koncových bodů veřejných virtuálních IP adres zatížení
* Skupinu dostupnosti, který obsahuje dva virtuální počítače
* Dva virtuální počítače (VM)
* Rozhraní virtuální sítě pro každý virtuální počítač s protokoly IPv4 a IPv6 adresy přiřazené

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Nasazení řešení pomocí Azure Powershellu

Následující kroky ukazují, jak vytvořit internetový nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru pomocí Powershellu. S Azure Resource Managerem se jednotlivé prostředky se vytvoří a nakonfigurují zvlášť, následně se spojí dohromady a vytvoří prostředek.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, vytvořte a nakonfigurujte následující objekty:

* Konfigurace front-endových IP adres – obsahuje veřejné IP adresy pro příchozí síťový provoz.
* Back-endový fond adres – obsahuje síťová rozhraní, pomocí kterých virtuální počítače přijímají síťový provoz z nástroje pro vyrovnávání zatížení.
* Pravidla vyrovnávání zatížení – obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port v back-endovém fondu adres.
* Pravidla příchozího překladu adres (NAT) – obsahuje pravidla mapující veřejný port v nástroji pro vyrovnávání zatížení na port konkrétního virtuálního počítače v back-endovém fondu adres.
* Testy – obsahuje testy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů v back-endovém fondu adres.

Další informace najdete v tématu [Podpora služby Load Balancer v Azure Resource Manageru](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Nastavení prostředí PowerShell pro použití Resource Manageru

Ujistěte se, že máte nejnovější produkční verzi modulu Azure Resource Manageru pro prostředí PowerShell.

1. Přihlaste se k Azure

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

4. Vytvořte skupinu prostředků (Pokud se používá existující skupina prostředků Tenhle krok přeskočit)

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Vytvoření virtuální sítě a veřejné IP adresy pro front-endový fond IP adres

1. Vytvoření virtuální sítě k podsíti.

    ```azurepowershell-interactive
    $backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    $vnet = New-AzvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Vytvořte Azure veřejná IP adresa (PIP) prostředky pro front-endový fond IP adres. Nezapomeňte změnit hodnotu pro `-DomainNameLabel` před spuštěním následujících příkazů. Hodnota musí být jedinečný v rámci oblasti Azure.

    ```azurepowershell-interactive
    $publicIPv4 = New-AzPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
    $publicIPv6 = New-AzPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6
    ```

    > [!IMPORTANT]
    > Nástroje pro vyrovnávání zatížení používá název domény veřejné IP adresy jako předponu jeho plně kvalifikovaného názvu domény. V tomto příkladu jsou plně kvalifikované názvy *lbnrpipv4.westus.cloudapp.azure.com* a *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Vytvoření konfigurace protokolu IP front-endový a Back-Endového fondu adres

1. Vytvořte konfiguraci front-endu adresy používá veřejné IP adresy, kterou jste vytvořili.

    ```azurepowershell-interactive
    $FEIPConfigv4 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
    $FEIPConfigv6 = New-AzLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6
    ```

2. Vytvořte fondy adres back endu.

    ```azurepowershell-interactive
    $backendpoolipv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
    $backendpoolipv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"
    ```

## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Vytvoření pravidel LB, pravidla NAT, testu a nástroje pro vyrovnávání zatížení

Tento příklad vytvoří následující položky:

* pravidlo NAT pro převod veškerý příchozí provoz na portu 443 na port 4443
* Pravidlo nástroje pro vyrovnávání zatížení, které vyrovnává zatížení veškerého příchozího provozu na portu 80 na port 80 na adresách v back-endovém fondu.
* pravidlo nástroje pro vyrovnávání zatížení, které povolit připojení RDP k virtuálním počítačům na port 3389.
* pravidlo testu, které kontroluje stav na stránce s názvem *HealthProbe.aspx* nebo službu na portu 8080
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

    nebo test protokolu TCP

    ```azurepowershell-interactive
    $healthProbe = New-AzLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
    $RDPprobe = New-AzLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2
    ```

    V tomto příkladu budeme používat sondy protokolu TCP.

3. Vytvořte pravidlo nástroje pro vyrovnávání zatížení.

    ```azurepowershell-interactive
    $lbrule1v4 = New-AzLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $lbrule1v6 = New-AzLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
    $RDPrule = New-AzLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389
    ```

4. Vytvoření nástroje pro vyrovnávání zatížení pomocí dříve vytvořeného objektů.

    ```azurepowershell-interactive
    $NRPLB = New-AzLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule
    ```

## <a name="create-nics-for-the-back-end-vms"></a>Vytvoření síťových rozhraní pro virtuální počítače back-end

1. Získejte virtuální síť a virtuální síť podsíť, ve kterém je potřeba vytvořit síťová rozhraní.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name VNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Vytvoření konfigurace IP adresy a síťové adaptéry pro virtuální počítače.

    ```azurepowershell-interactive
    $nic1IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
    $nic1IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
    $nic1 = New-AzNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

    $nic2IPv4 = New-AzNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
    $nic2IPv6 = New-AzNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
    $nic2 = New-AzNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'
    ```

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Vytvoření virtuálních počítačů a přiřaďte nově vytvořené síťové karty

Další informace o vytvoření virtuálního počítače najdete v tématu [vytvoření a předkonfigurace virtuálního počítače Windows pomocí Resource Manageru a Azure Powershellu](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Vytvoření skupiny dostupnosti a úložiště účtu

    ```azurepowershell-interactive
    New-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
    $availabilitySet = Get-AzAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
    New-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName "Standard_LRS"
    $CreatedStorageAccount = Get-AzStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'
    ```

2. Každý virtuální počítač vytvořit a přiřadit předchozí vytvořené síťové karty

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

## <a name="next-steps"></a>Další postup

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
