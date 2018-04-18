---
title: Vytvoření veřejného nástroje pro vyrovnávání zatížení – PowerShell | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit veřejný nástroj pro vyrovnávání zatížení v Resource Manageru pomocí PowerShellu
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4ce11b0b06e1feaf55d17e25500c43a7eb1bf3d5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="get-started"></a>Vytvoření veřejného nástroje pro vyrovnávání zatížení v Resource Manageru pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-internet-arm-template.md)



[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Nasazení řešení pomocí Azure PowerShellu

Následující postupy vysvětlují, jak vytvořit veřejný nástroj pro vyrovnávání zatížení pomocí Azure Resource Manageru s prostředím PowerShell. S Azure Resource Managerem se jednotlivé prostředky vytvoří a nakonfigurují zvlášť, následně se spojí dohromady a vytvoří nástroj pro vyrovnávání zatížení.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, je nutné vytvořit a nakonfigurovat následující objekty:

* Konfigurace front-endových IP adres: obsahuje veřejné IP adresy pro příchozí síťový provoz.
* Back-endový fond adres: obsahuje síťová rozhraní, pomocí kterých virtuální počítače přijímají síťový provoz z nástroje pro vyrovnávání zatížení.
* Pravidla vyrovnávání zatížení: obsahuje pravidla, která mapují veřejný port v nástroji pro vyrovnávání zatížení na port v back-endovém fondu adres.
* Pravidla příchozího překladu adres (NAT): obsahuje pravidla, která mapují veřejný port v nástroji pro vyrovnávání zatížení na port konkrétního virtuálního počítače v back-endovém fondu adres.
* Testy: obsahuje testy stavu sloužící ke kontrole dostupnosti instancí virtuálních počítačů v back-endovém fondu adres.

Další informace najdete v tématu [Podpora služby Load Balancer v Azure Resource Manageru](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Nastavení prostředí PowerShell pro použití Resource Manageru

Ujistěte se, že máte nejnovější produkční verzi modulu Azure Resource Manageru pro prostředí PowerShell:

1. Přihlaste se k Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Po zobrazení výzvy zadejte své přihlašovací údaje.

2. Zkontrolujte předplatná pro příslušný účet.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Zvolte předplatné Azure, které chcete použít.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. Vytvořte skupinu prostředků. (Tento krok přeskočte, pokud používáte některou ze stávajících skupin prostředků.)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Vytvoření virtuální sítě a veřejné IP adresy pro front-endový fond IP adres

1. Vytvořte podsíť a virtuální síť.

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. Vytvořte prostředek veřejné IP adresy Azure s názvem **PublicIP**, který bude používat front-endový fond IP adres s názvem DNS **loadbalancernrp.westus.cloudapp.azure.com**. Následující příkaz používá statický typ přidělování.

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > Nástroj pro vyrovnávání zatížení používá název domény veřejné IP adresy jako předponu svého plně kvalifikovaného názvu domény. Tím se liší od modelu nasazení Classic, který používá cloudovou službu jako plně kvalifikovaný název domény nástroje pro vyrovnávání zatížení.
   > V tomto příkladu je plně kvalifikovaný název domény **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Vytvoření front-endového fondu IP adres a back-endového fondu adres

1. Vytvořte front-endový fond IP adres s názvem **LB-Frontend**, který používá prostředek **PublicIp**.

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. Vytvořte back-endový fond adres s názvem **LB-backend**.

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Vytvoření pravidel překladu adres (NAT), pravidla nástroje pro vyrovnávání zatížení, testu a nástroje pro vyrovnávání zatížení

Tento příklad vytvoří následující položky:

* Pravidlo překladu adres (NAT), které veškerý příchozí provoz na portu 3441 překládá na port 3389.
* Pravidlo překladu adres (NAT), které veškerý příchozí provoz na portu 3442 překládá na port 3389.
* Pravidlo testu, které kontroluje stav na stránce **HealthProbe.aspx**.
* Pravidlo nástroje pro vyrovnávání zatížení, které veškerý příchozí provoz na portu 80 vyvažuje na port 80 na adresách v back-endovém fondu.
* Nástroj pro vyrovnávání zatížení, který používá všechny tyto objekty.

Proveďte následující kroky:

1. Vytvořte pravidla překladu adres (NAT).

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. Vytvořte test stavu. Test můžete nakonfigurovat dvěma způsoby:

    Test protokolu HTTP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    Test protokolu TCP

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. Vytvořte pravidlo nástroje pro vyrovnávání zatížení.

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. Vytvořte nástroj pro vyrovnávání zatížení pomocí objektů, které jste vytvořili v předchozích krocích.

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořte síťová rozhraní (nebo upravte stávající) a potom je přidružte k pravidlům překladu adres (NAT), pravidlům nástroje pro vyrovnávání zatížení a testům:

1. Získejte virtuální síť a některou z podsítí virtuální sítě, ve které je třeba vytvořit síťová rozhraní.

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. Vytvořte síťové rozhraní s názvem **lb-nic1-be** a přidružte jej k prvnímu pravidlu překladu adres (NAT) a prvnímu (zároveň jedinému) back-endovému fondu adres.

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. Vytvořte síťové rozhraní s názvem **lb-nic2-be** a přidružte jej k druhému pravidlu překladu adres (NAT) a prvnímu (zároveň jedinému) back-endovému fondu adres.

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. Zkontrolujte síťová rozhraní.

        $backendnic1

    Očekávaný výstup:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
                            "PublicIpAddress": {
                                "Id": null
                            },
                            "LoadBalancerBackendAddressPools": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                                }
                            ],
                            "LoadBalancerInboundNatRules": [
                                {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                                }
                            ],
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Pomocí rutiny `Add-AzureRmVMNetworkInterface` přiřaďte síťová rozhraní k různým virtuálním počítačům.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Pokyny k vytvoření virtuálního počítače a přiřazení síťového rozhraní najdete v tématu [Vytvoření virtuálního počítače Azure pomocí prostředí PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Přidání síťového rozhraní do nástroje pro vyrovnávání zatížení

1. Načtěte nástroj pro vyrovnávání zatížení z Azure.

    Načtěte prostředek nástroje pro vyrovnávání zatížení do proměnné (pokud jste tak ještě neučinili). Název této proměnné je **$lb**. Použijte stejné názvy z prostředku nástroje pro vyrovnávání zatížení, který jste vytvořili v předchozích krocích.

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. Načtěte konfiguraci back-endu do proměnné.

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. Načtěte již vytvořené síťové rozhraní do proměnné. Název této proměnné je **$nic**. Název síťového rozhraní je stejný, jako v předchozím příkladu.

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. Změňte konfiguraci back-endu na síťovém rozhraní.

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. Uložte objekt síťového rozhraní.

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    Síťového rozhraní po přidání do back-endového fondu nástroje pro vyrovnávání zatížení začne přijímat síťový provoz na základě pravidel vyrovnávání zatížení pro daný prostředek nástroje pro vyrovnávání zatížení.

## <a name="update-an-existing-load-balancer"></a>Aktualizace stávajícího nástroje pro vyrovnávání zatížení

1. Použijte nástroj pro vyrovnávání zatížení z předchozího příkladu k přiřazení objektu nástroje pro vyrovnávání zatížení do proměnné **$slb** pomocí příkazu `Get-AzureLoadBalancer`.

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. V následujícím příkladu do stávajícího nástroje pro vyrovnávání zatížení přidáte pravidlo příchozího překladu adres (NAT) pomocí portu 81 ve front-endovém fondu a portu 8181 pro back-endový fond.

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. Uložte novou konfiguraci pomocí příkazu `Set-AzureLoadBalancer`.

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>Odebrání nástroje pro vyrovnávání zatížení

Použijte příkaz `Remove-AzureLoadBalancer` k odstranění dříve vytvořeného nástroje pro vyrovnávání zatížení s názvem **NRP-LB** ve skupině prostředků s názvem **NRP-RG**.

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Můžete použít volitelný přepínač **-Force**, abyste se vyhnuli zobrazení výzvy k odstranění.

## <a name="next-steps"></a>Další kroky

[Začínáme s konfigurací interního nástroje pro vyrovnávání zatížení](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)

[Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
