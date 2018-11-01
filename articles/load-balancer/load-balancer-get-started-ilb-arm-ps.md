---
title: Vytvoření interního nástroje pro vyrovnávání zatížení v Azure pomocí PowerShellu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí modulu Azure PowerShellu a Azure Resource Manageru
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4e99411ec56f25e249429e4e65bae4a8e7071cc1
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412682"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Vytvoření interního nástroje pro vyrovnávání zatížení pomocí modulu Azure PowerShellu

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Šablona](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Začínáme s konfigurací

Tento článek popisuje vytvoření interního nástroje pro vyrovnávání zatížení pomocí Azure Resource Manageru a modulu Azure PowerShellu. V modelu nasazení Resource Manager se objekty požadované k vytvoření interního nástroje pro vyrovnávání zatížení konfigurují jednotlivě. Po vytvoření a konfiguraci se objekty spojí a vytvoří nástroj pro vyrovnávání zatížení.

Pokud chcete nasadit nástroj pro vyrovnávání zatížení, musíte vytvořit následující objekty:

* Front-endový fond IP adres: Privátní IP adresa pro veškerý příchozí síťový provoz.
* Back-endový fond adres: Síťová rozhraní pro příjem provozu s vyrovnáváním zatížení z front-endové IP adresy.
* Pravidla vyrovnávání zatížení: Konfigurace portů (zdrojového a místního) pro nástroj pro vyrovnávání zatížení.
* Konfigurace sondy: Sondy stavu pro virtuální počítače.
* Příchozí pravidla NAT: Pravidla portů pro přímý přístup k virtuálním počítačům.

Další informace součástech nástroje pro vyrovnávání zatížení najdete v tématu [Podpora nástroje pro vyrovnávání zatížení v Azure Resource Manageru](load-balancer-arm.md).

V následujícím postupu se dozvíte, jak nakonfigurovat nástroj pro vyrovnávání zatížení mezi dvěma virtuálními počítači.

## <a name="set-up-powershell-to-use-resource-manager"></a>Nastavení prostředí PowerShell pro použití Resource Manageru

Ujistěte se, že máte nejnovější produkční verzi modulu Azure PowerShellu. PowerShell musí být správně nakonfigurovaný pro přístup k vašemu předplatnému Azure.

### <a name="step-1-start-powershell"></a>Krok 1: Spuštění PowerShellu

Spusťte modul PowerShellu pro Azure Resource Manager.

```powershell
Connect-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Krok 2: Zobrazení předplatných

Zkontrolujte dostupná předplatná Azure.

```powershell
Get-AzureRmSubscription
```

Po zobrazení výzvy k ověření zadejte své přihlašovací údaje.

### <a name="step-3-select-the-subscription-to-use"></a>Krok 3: Výběr předplatného, které se má použít

Zvolte, které z vašich předplatných Azure se má použít k nasazení nástroje pro vyrovnávání zatížení.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Krok 4: Výběr skupiny prostředků pro nástroj pro vyrovnávání zatížení

Vytvořte pro nástroj pro vyrovnávání zatížení novou skupinu prostředků. Tento krok přeskočte, pokud používáte některou ze stávajících skupin prostředků.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. Toto umístění slouží jako výchozí umístění pro všechny prostředky v příslušné skupině prostředků. Pro všechny příkazy související s vytvořením nástroje pro vyrovnávání zatížení vždy používejte stejnou skupinu prostředků.

V tomto příkladu jsme vytvořili skupinu prostředků s názvem **NRP-RG** a umístěním Západní USA.

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Vytvoření virtuální sítě a IP adresy pro front-endový fond IP adres

Vytvořte podsíť pro virtuální síť a přiřaďte ji do proměnné **$backendSubnet**.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Vytvořte virtuální síť.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

Vytvořila se virtuální síť. Do virtuální sítě **NRPVNet** se přidala podsíť **LB-Subnet-BE**. Tyto hodnoty jsou přiřazené do proměnné **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Vytvoření front-endového fondu IP adres a back-endového fondu adres

Vytvořte front-endový fond IP adres pro příchozí provoz a back-endový fond adres pro příjem provozu s vyrovnáváním zatížení.

### <a name="step-1-create-a-front-end-ip-pool"></a>Krok 1: Vytvoření front-endového fondu IP adres

Vytvořte front-endový fond IP adres s privátní IP adresou 10.0.2.5 pro podsíť 10.0.2.0/24. Tato adresa je koncovým bodem příchozího síťového provozu.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Krok 2: Vytvoření back-endového fondu adres

Vytvořte back-endový fond adres pro příjem příchozího provozu z front-endového fondu IP adres:

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Vytvoření pravidel konfigurace, sondy a nástroje pro vyrovnávání zatížení

Po vytvoření front-endového fondu IP adres a back--endového fondu adres zadejte pravidla pro prostředek nástroje pro vyrovnávání zatížení.

### <a name="step-1-create-the-configuration-rules"></a>Krok 1: Vytvoření pravidel konfigurace

Tento příklad vytvoří následující čtyři objekty pravidel:

* Příchozí pravidlo NAT pro protokol RDP (Remote Desktop Protocol): Přesměruje veškerý příchozí provoz na portu 3441 na port 3389.
* Druhé příchozí pravidlo NAT pro protokol RDP: Přesměruje veškerý příchozí provoz na portu 3442 na port 3389.
* Pravidlo sondy stavu: Kontroluje stav cesty k souboru HealthProbe.aspx.
* Pravidlo nástroje pro vyrovnávání zatížení: Vyrovnává zatížení veškerého příchozího provozu na veřejném portu 80 na místní port 80 v back-endovém fondu adres.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Krok 2: Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte nástroj pro vyrovnávání zatížení a zkombinujte objekty pravidel (příchozí NAT pro protokol RDP, nástroj pro vyrovnávání zatížení a sonda stavu):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Vytvoření síťových rozhraní

Po vytvoření interního nástroje pro vyrovnávání zatížení definujte, která síťová rozhraní budou přijímat příchozí síťový provoz s vyrovnáváním zatížení, pravidla NAT a sondu. Každé síťové rozhraní se konfiguruje samostatně a později se přiřadí k virtuálnímu počítači.

### <a name="step-1-create-the-first-network-interface"></a>Krok 1: Vytvoření prvního síťového rozhraní

Získejte virtuální síť a podsíť prostředku. Tyto hodnoty se použijí k vytvoření síťových rozhraní:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Vytvořte první síťové rozhraní s názvem **lb-nic1-be**. Přiřaďte toto rozhraní k back-endovému fondu nástroje pro vyrovnávání zatížení. Přidružte k tomuto síťovému rozhraní první pravidlo NAT pro protokol RDP:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Krok 2: Vytvoření druhého síťového rozhraní

Vytvořte druhé síťové rozhraní s názvem **lb-nic2-be**. Přiřaďte druhé síťové rozhraní ke stejnému back-endovému fondu nástroje pro vyrovnávání zatížení jako první rozhraní. Přidružte k druhému síťovému rozhraní druhé pravidlo NAT pro protokol RDP:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Zkontrolujte konfiguraci:

    $backendnic1

Nastavení by měl vypadat takto:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
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
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
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

Přiřaďte druhé síťové rozhraní k virtuálnímu počítači pomocí příkazu `Add-AzureRmVMNetworkInterface`.

Podrobné pokyny k vytvoření virtuálního počítače a přiřazení síťového rozhraní najdete v tématu [Vytvoření virtuálního počítače Azure pomocí PowerShellu](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Přidání síťového rozhraní

Po vytvoření virtuálního počítače přidejte síťové rozhraní.

### <a name="step-1-store-the-load-balancer-resource"></a>Krok 1: Uložení prostředku nástroje pro vyrovnávání zatížení

Uložte prostředek nástroje pro vyrovnávání zatížení do proměnné (pokud jste tak ještě neučinili). Používáme proměnnou s názvem **$lb**. Jako hodnoty atributů ve skriptu použijte názvy prostředků nástroje pro vyrovnávání zatížení vytvořené v předchozích krocích.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Krok 2: Uložení konfigurace back-endu

Uložte konfiguraci back-endu do proměnné **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Krok 3: Uložení síťového rozhraní

Do další proměnné uložte síťové rozhraní. Toto rozhraní jste vytvořili v kroku 1: Vytvoření síťových rozhraní. Používáme proměnnou s názvem **$nic1**. Použijte stejný název síťového rozhraní jako v předchozím příkladu.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Krok 4: Změna konfigurace back-endu

Změňte konfiguraci back-endu na síťovém rozhraní.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Krok 5: Uložení objektu síťového rozhraní

Uložte objekt síťového rozhraní.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Po přidání rozhraní do back-endového fondu se zatížení síťového provozu vyrovnává podle nastavených pravidel. Tato pravidla jste nakonfigurovali v části Vytvoření pravidel konfigurace, sondy a nástroje pro vyrovnávání zatížení.

## <a name="update-an-existing-load-balancer"></a>Aktualizace stávajícího nástroje pro vyrovnávání zatížení

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Krok 1: Přiřazení objektu nástroje pro vyrovnávání zatížení do proměnné

Přiřaďte objekt nástroje pro vyrovnávání zatížení (z předchozího příkladu) do proměnné **$slb** pomocí příkazu `Get-AzureRmLoadBalancer`:

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Krok 2: Přidání pravidla NAT

Přidejte do stávajícího nástroje pro vyrovnávání zatížení nové příchozí pravidlo NAT. Pro front-endový fond použijte port 81 a pro back-endový fond použijte port 8181:

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Krok 3: Uložení konfigurace

Uložte novou konfiguraci pomocí příkazu `Set-AzureLoadBalancer`:

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Odebrání stávajícího nástroje pro vyrovnávání zatížení

Odstraňte nástroj pro vyrovnávání zatížení **NRP-LB** ve skupině prostředků **NRP-RG** pomocí příkazu `Remove-AzureRmLoadBalancer`:

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Pokud chcete zabránit zobrazení výzvy k potvrzení odstranění, použijte volitelný přepínač **-Force**.

## <a name="next-steps"></a>Další postup

* [Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
