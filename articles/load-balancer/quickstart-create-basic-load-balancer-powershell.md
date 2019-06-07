---
title: 'Rychlý start: Vytvoření Load balanceru úrovně Basic – Azure PowerShell'
titlesuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit Load Balancer úrovně Basic pomocí PowerShellu.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 5b77087557bc4ca1bea3cb4a36a50d787a5edf4d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729849"
---
# <a name="get-started"></a>Rychlý start: Vytvoření veřejného load balanceru úrovně pomocí Azure Powershellu

Tento rychlý start vám ukáže, jak vytvořit Load Balancer úrovně Basic pomocí Azure PowerShellu. K otestování nástroje pro vyrovnávání zatížení nasadíte dva virtuální počítače s Windows Serverem, které mezi sebou budou vyrovnávat zatížení webové aplikace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Než vytvoříte nástroj pro vyrovnávání zatížení, musíte vytvořit skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků *myResourceGroupLB* v umístění *EastUS*:

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```

## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy
Pokud chcete mít k aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Vytvoření veřejné IP adresy s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Následující příklad vytvoří veřejnou IP adresu s názvem *myPublicIP* ve skupině prostředků *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

## <a name="create-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

V této části se konfigurace IP adresy front-endu a back endových adres fondu nástroje pro vyrovnávání zatížení a pak vytvořte Load balanceru úrovně Basic.

### <a name="create-front-end-ip"></a>Vytvoření front-endové IP adresy

Vytvoření front-endové IP adresy s [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Následující příklad vytvoří front-endovou IP adresu s názvem *myFrontEnd* a připojí adresu *myPublicIP*:

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-back-end-address-pool"></a>Konfigurace back endového fondu adres

Vytvořte fond back endových adres s [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Virtuální počítače připojit k tomuto fondu back-end ve zbývajících krocích. Následující příklad vytvoří fond back endových adres s názvem *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

### <a name="create-a-health-probe"></a>Vytvoření sondy stavu
Pokud chcete nástroji pro vyrovnávání zatížení povolit monitorování stavu vaší aplikace, použijte sondu stavu. Sonda stavu dynamicky přidává virtuální počítače do oběhu nástroje pro vyrovnávání zatížení nebo je z něj odebírá na základě jejich reakce na kontroly stavu. Ve výchozím nastavení se virtuální počítač odebere z distribuce nástroje pro vyrovnávání zatížení po dvou selháních po sobě v 15sekundových intervalech. Sondu stavu můžete vytvořit na základě protokolu nebo konkrétní stránky kontroly stavu pro vaši aplikaci. 

Následující příklad vytvoří sondu protokolu TCP. Pokud potřebujete jemněji odstupňované kontroly stavu, můžete vytvářet i vlastní sondy protokolu HTTP. Pokud použijete vlastní sondu protokolu HTTP, musíte vytvořit stránku kontroly stavu, například *healthcheck.aspx*. Aby nástroj pro vyrovnávání zatížení udržel hostitele v oběhu, musí sonda vracet odpověď **HTTP 200 OK**.

K vytvoření sondy stavu protokolu TCP, použijete [přidat AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig). Následující příklad vytvoří sondu stavu s názvem *myHealthProbe* monitorující jednotlivé virtuální počítače na portu *HTTP* *80*:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení slouží k definování způsobu distribuce provozu do virtuálních počítačů. Definujte konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Abyste zajistili, že provoz budou přijímat pouze virtuální počítače, které jsou v pořádku, nadefinujte také sondu stavu, která se má použít.

Vytvořte pravidlo nástroje pro vyrovnávání zatížení s [přidat AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). Následující příklad vytvoří pravidlo nástroje pro vyrovnávání zatížení *myLoadBalancerRule* a nastaví vyrovnávání provozu na portu *TCP* *80*:

```azurepowershell-interactive
$lbrule = New-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Vytvoření pravidel překladu adres

Vytvoření pravidel NAT s [přidat AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig). Následující příklad vytvoří pravidla překladu adres s názvem *myLoadBalancerRDP1* a *myLoadBalancerRDP2*, která na portech 4221 a 4222 umožní připojení RDP k back-end serverům:

```azurepowershell-interactive
$natrule1 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte Load balanceru úrovně Basic s [nové AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří pomocí dříve vytvořené konfigurace front-endové IP adresy, back-endového fondu, sondy stavu, pravidla vyrovnávání zatížení a pravidel překladu adres veřejný Load Balancer úrovně Basic s názvem myLoadBalancer:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, musíte vytvořit podpůrné síťové prostředky – virtuální síť a virtuální síťové karty. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvoření virtuální sítě s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myVnet* s podsítí *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="create-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě, která definuje příchozí připojení k vaší virtuální síti.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 3389

Vytvořte pravidlo skupiny zabezpečení sítě umožňující připojení RDP přes port 3389 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Vytvoření pravidla skupiny zabezpečení sítě pro port 80

Vytvořte pravidlo skupiny zabezpečení sítě, které povolí příchozí připojení přes port 80 s [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```

#### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

Vytvořte skupinu zabezpečení sítě pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Vytvoření síťových rozhraní

Vytvořit virtuální síťové adaptéry vytvořené pomocí [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Následující příklad vytvoří dvě virtuální síťové karty. (Jednu virtuální síťovou kartu pro každý virtuální počítač, který pro svou aplikaci vytvoříte v následujících krocích). Kdykoli můžete vytvořit další virtuální síťové karty a virtuální počítače a přidat je do nástroje pro vyrovnávání zatížení:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyVM2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pokud chcete zlepšit vysokou dostupnost aplikace, umístěte své virtuální počítače do skupiny dostupnosti.

Vytvořit skupinu dostupnosti pomocí [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Následující příklad vytvoří skupinu dostupnosti s názvem *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Pomocí rutiny [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) nastavte uživatelské jméno a heslo správce virtuálních počítačů:

```azurepowershell-interactive
$cred = Get-Credential
```

Nyní můžete vytvořit virtuální počítače s [rutiny New-AzVM](/powershell/module/az.compute/new-azvm). Následující příklad vytvoří dva virtuální počítače a požadované komponenty virtuální sítě, pokud ještě neexistují. V tomto příkladu, síťová rozhraní (*VM1* a *VM2*) vytvořené v předchozím kroku jsou automaticky přiřazeny k virtuálním počítačům *VM1* a *VM2*protože mají stejný název a jsou přiřazeny stejné virtuální síti (*myVnet*) a podsíti (*mySubnet*). Kromě toho protože síťová rozhraní jsou přidružené k back-endového fondu nástroje pro vyrovnávání zatížení, virtuální počítače se automaticky přidají do back-endový fond.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Parametr `-AsJob` vytvoří virtuální počítač jako úlohu na pozadí, takže budete mít k dispozici příkazový řádek PowerShellu. Podrobnosti úloh na pozadí můžete zobrazit pomocí rutiny `Job`. Vytvoření a konfigurace dvou virtuálních počítačů bude trvat několik minut.

### <a name="install-iis-with-custom-web-page"></a>Instalace služby IIS s vlastní webovou stránkou
 
Následujícím způsobem nainstalujte na obou back-endových virtuálních počítačích službu IIS s vlastní webovou stránkou:

1. Získejte veřejnou IP adresu Load Balanceru. Pomocí rutiny `Get-AzPublicIPAddress` získejte veřejnou IP adresu Load Balanceru.

   ```azurepowershell-interactive
    Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
   ```
2. Vytvořte pomocí veřejné IP adresy z předchozího kroku připojení ke vzdálené ploše virtuálního počítače VM1. 

   ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
   ```
3. Zadáním přihlašovacích údajů pro virtuální počítač *VM1* spusťte relaci RDP.
4. Spusťte na virtuálním počítači VM1 Windows PowerShell a pomocí následujících příkazů nainstalujte server služby IIS a aktualizujte výchozí soubor htm.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Ukončete připojení RDP k virtuálnímu počítači *myVM1*.
6. Spuštěním příkazu `mstsc /v:PublicIpAddress:4222` vytvořte připojení RDP k virtuálnímu počítači *myVM2* a zopakujte na něm čtvrtý krok.

## <a name="test-load-balancer"></a>Test nástroje pro vyrovnávání zatížení
Získat veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení s [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). Následující příklad získá dříve vytvořenou IP adresu pro *myPublicIP*:

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Veřejnou IP adresu pak můžete zadat do webového prohlížeče. Zobrazí se web, včetně názvu hostitele virtuálního počítače, do kterého nástroj pro vyrovnávání zatížení distribuoval provoz, jako v následujícím příkladu:

![Test nástroje pro vyrovnávání zatížení](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Pokud chcete zobrazit distribuci provozu nástrojem pro vyrovnávání zatížení mezi oba virtuální počítače, na kterých je vaše aplikace spuštěná, můžete vynutit aktualizaci webového prohlížeče.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, virtuálního počítače, a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Další postup

V rámci tohoto rychlého startu jste vytvořili Load Balancer úrovně Basic, připojili jste k němu virtuální počítače, nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Chcete-li zjistit další informace o službě Azure Load Balancer, přejděte ke kurzům pro Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)