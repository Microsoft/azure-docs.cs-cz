---
title: Vytvoření Azure internal Load Balancer pomocí Powershellu
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí modulu Azure PowerShellu a Azure Resource Manageru
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 521f8f29e2f8475ab7308f5646b94c6fc0f6a01f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60398768"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Vytvoření interního nástroje pro vyrovnávání zatížení pomocí modulu Azure PowerShellu

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Začínáme s konfigurací

Tento článek popisuje vytvoření interního nástroje pro vyrovnávání zatížení pomocí Azure Resource Manageru a modulu Azure PowerShellu. V modelu nasazení Resource Manager se objekty požadované k vytvoření interního nástroje pro vyrovnávání zatížení konfigurují jednotlivě. Po vytvoření a konfiguraci se objekty spojí a vytvoří nástroj pro vyrovnávání zatížení.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, musíte vytvořit následující objekty:

* Front-endový fond IP adres: Privátní IP adresa pro veškerý příchozí síťový provoz.
* Back endový fond adres: Síťová rozhraní pro přijímání provozu s vyrovnáváním zatížení z front-endovou IP adresu.
* Pravidla Vyrovnávání zatížení: Konfigurace portů (zdrojového a místního) pro nástroj pro vyrovnávání zatížení.
* Konfigurace testu: Sondy stavu pro virtuální počítače.
* Pravidla příchozího překladu adres: Pravidla portů pro přímý přístup k virtuálním počítačům.

Další informace součástech nástroje pro vyrovnávání zatížení najdete v tématu [Podpora nástroje pro vyrovnávání zatížení v Azure Resource Manageru](load-balancer-arm.md).

V následujícím postupu se dozvíte, jak nakonfigurovat nástroj pro vyrovnávání zatížení mezi dvěma virtuálními počítači.

## <a name="set-up-powershell-to-use-resource-manager"></a>Nastavení prostředí PowerShell pro použití Resource Manageru

Ujistěte se, že máte nejnovější produkční verzi modulu Azure PowerShellu. PowerShell musí být správně nakonfigurovaný pro přístup k vašemu předplatnému Azure.

### <a name="step-1-start-powershell"></a>Krok 1: Spuštění PowerShellu

Spusťte modul PowerShellu pro Azure Resource Manager.

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>Krok 2: Zobrazení vašich předplatných

Zkontrolujte dostupná předplatná Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Po zobrazení výzvy k ověření zadejte své přihlašovací údaje.

### <a name="step-3-select-the-subscription-to-use"></a>Krok 3: Vyberte předplatné, které chcete použít

Zvolte, které z vašich předplatných Azure se má použít k nasazení nástroje pro vyrovnávání zatížení.

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Krok 4: Zvolte skupinu prostředků, nástroje pro vyrovnávání zatížení

Vytvořte pro nástroj pro vyrovnávání zatížení novou skupinu prostředků. Tento krok přeskočte, pokud používáte některou ze stávajících skupin prostředků.

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. Toto umístění slouží jako výchozí umístění pro všechny prostředky v příslušné skupině prostředků. Pro všechny příkazy související s vytvořením nástroje pro vyrovnávání zatížení vždy používejte stejnou skupinu prostředků.

V tomto příkladu jsme vytvořili skupinu prostředků s názvem **NRP-RG** a umístěním Západní USA.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Vytvoření virtuální sítě a IP adresy pro front-endový fond IP adres

Vytvořte podsíť pro virtuální síť a přiřaďte ji do proměnné **$backendSubnet**.

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Vytvořte virtuální síť.

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Vytvořila se virtuální síť. Do virtuální sítě **NRPVNet** se přidala podsíť **LB-Subnet-BE**. Tyto hodnoty jsou přiřazené do proměnné **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Vytvoření front-endového fondu IP adres a back-endového fondu adres

Vytvořte front-endový fond IP adres pro příchozí provoz a back-endový fond adres pro příjem provozu s vyrovnáváním zatížení.

### <a name="step-1-create-a-front-end-ip-pool"></a>Krok 1: Vytvořte front-endový fond IP adres

Vytvořte front-endový fond IP adres s privátní IP adresou 10.0.2.5 pro podsíť 10.0.2.0/24. Tato adresa je koncovým bodem příchozího síťového provozu.

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Krok 2: Vytvoření fondu back-endových adres

Vytvořte back-endový fond adres pro příjem příchozího provozu z front-endového fondu IP adres:

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Vytvoření pravidel konfigurace, sondy a nástroje pro vyrovnávání zatížení

Po vytvoření front-endového fondu IP adres a back--endového fondu adres zadejte pravidla pro prostředek nástroje pro vyrovnávání zatížení.

### <a name="step-1-create-the-configuration-rules"></a>Krok 1: Vytvoření pravidel konfigurace

Tento příklad vytvoří následující čtyři objekty pravidel:

* Příchozí pravidlo NAT pro protokol RDP (Remote Desktop): Přesměruje veškerý příchozí provoz na portu 3441 překládá na port 3389.
* Druhé příchozí pravidlo NAT pro protokol RDP: Přesměruje veškerý příchozí provoz na portu 3442 na port 3389.
* Pravidlo testu stavu: Kontroluje stav HealthProbe.aspx cesty.
* Pravidlo nástroje pro vyrovnávání zatížení: Vyrovnává zatížení veškerý příchozí provoz na veřejném portu 80 na port 80 v back endových adres fondu.

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte nástroj pro vyrovnávání zatížení a zkombinujte objekty pravidel (příchozí NAT pro protokol RDP, nástroj pro vyrovnávání zatížení a sonda stavu):

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Vytvoření síťových rozhraní

Po vytvoření interního nástroje pro vyrovnávání zatížení definujte, která síťová rozhraní budou přijímat příchozí síťový provoz s vyrovnáváním zatížení, pravidla NAT a sondu. Každé síťové rozhraní se konfiguruje samostatně a později se přiřadí k virtuálnímu počítači.

### <a name="step-1-create-the-first-network-interface"></a>Krok 1: Vytvořte první síťové rozhraní

Získejte virtuální síť a podsíť prostředku. Tyto hodnoty se použijí k vytvoření síťových rozhraní:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Vytvořte první síťové rozhraní s názvem **lb-nic1-be**. Přiřaďte toto rozhraní k back-endovému fondu nástroje pro vyrovnávání zatížení. Přidružte k tomuto síťovému rozhraní první pravidlo NAT pro protokol RDP:

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Krok 2: Vytvořte druhé síťové rozhraní

Vytvořte druhé síťové rozhraní s názvem **lb-nic2-be**. Přiřaďte druhé síťové rozhraní ke stejnému back-endovému fondu nástroje pro vyrovnávání zatížení jako první rozhraní. Přidružte k druhému síťovému rozhraní druhé pravidlo NAT pro protokol RDP:

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Zkontrolujte konfiguraci:

    $backendnic1

Nastavení by měl vypadat takto:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>Krok 3: Přiřazení síťového rozhraní k virtuálnímu počítači

Přiřaďte druhé síťové rozhraní k virtuálnímu počítači pomocí příkazu `Add-AzVMNetworkInterface`.

Podrobné pokyny k vytvoření virtuálního počítače a přiřazení síťového rozhraní najdete v tématu [Vytvoření virtuálního počítače Azure pomocí PowerShellu](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Přidání síťového rozhraní

Po vytvoření virtuálního počítače přidejte síťové rozhraní.

### <a name="step-1-store-the-load-balancer-resource"></a>Krok 1: Store prostředek nástroje pro vyrovnávání zatížení

Uložte prostředek nástroje pro vyrovnávání zatížení do proměnné (pokud jste tak ještě neučinili). Používáme proměnnou s názvem **$lb**. Jako hodnoty atributů ve skriptu použijte názvy prostředků nástroje pro vyrovnávání zatížení vytvořené v předchozích krocích.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Krok 2: Store konfiguraci back endu

Uložte konfiguraci back-endu do proměnné **$backend**.

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Krok 3: Store síťové rozhraní

Do další proměnné uložte síťové rozhraní. Toto rozhraní jste vytvořili v kroku 1: Vytvoření síťových rozhraní. Používáme proměnnou s názvem **$nic1**. Použijte stejný název síťového rozhraní jako v předchozím příkladu.

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Krok 4: Změňte konfiguraci back endu

Změňte konfiguraci back-endu na síťovém rozhraní.

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Krok 5: Uložte objekt síťového rozhraní

Uložte objekt síťového rozhraní.

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

Po přidání rozhraní do back-endového fondu se zatížení síťového provozu vyrovnává podle nastavených pravidel. Tato pravidla jste nakonfigurovali v části Vytvoření pravidel konfigurace, sondy a nástroje pro vyrovnávání zatížení.

## <a name="update-an-existing-load-balancer"></a>Aktualizace stávajícího nástroje pro vyrovnávání zatížení

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Krok 1: Přiřazení objektu nástroje pro vyrovnávání zatížení do proměnné

Přiřaďte objekt nástroje pro vyrovnávání zatížení (z předchozího příkladu) do proměnné **$slb** pomocí příkazu `Get-AzLoadBalancer`:

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Krok 2: Přidání pravidla NAT

Přidejte do stávajícího nástroje pro vyrovnávání zatížení nové příchozí pravidlo NAT. Pro front-endový fond použijte port 81 a pro back-endový fond použijte port 8181:

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Krok 3: Uložit konfiguraci

Uložte novou konfiguraci pomocí příkazu `Set-AzureLoadBalancer`:

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Odebrání stávajícího nástroje pro vyrovnávání zatížení

Odstraňte nástroj pro vyrovnávání zatížení **NRP-LB** ve skupině prostředků **NRP-RG** pomocí příkazu `Remove-AzLoadBalancer`:

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Pokud chcete zabránit zobrazení výzvy k potvrzení odstranění, použijte volitelný přepínač **-Force**.

## <a name="next-steps"></a>Další postup

* [Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)