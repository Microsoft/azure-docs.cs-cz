---
title: 'Kurz: Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi pomocí Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Začněte s tímto kurzem nasazení Azure Load Balancer mezi oblastmi pomocí Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/10/2021
ms.openlocfilehash: 88e400cea764be84521c003a681aa74885dc29ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721340"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-powershell"></a>Kurz: vytvoření Azure Load Balancer mezi oblastmi pomocí Azure PowerShell

Nástroj pro vyrovnávání zatížení mezi oblastmi zajišťuje globálně dostupnou službu napříč několika oblastmi Azure. Pokud dojde k výpadku jedné oblasti, provoz se směruje na další nejbližší zdravý Nástroj pro vyrovnávání zatížení.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte Nástroj pro vyrovnávání zatížení mezi oblastmi.
> * Vytvořte pravidlo nástroje pro vyrovnávání zatížení.
> * Vytvořte back-end fond, který obsahuje dvě místní nástroje pro vyrovnávání zatížení.
> * Otestujte Nástroj pro vyrovnávání zatížení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure.
- Dvě **standardní** SKU služby Vyrovnávání zatížení Azure s back-end fondy nasazenými ve dvou různých oblastech Azure.
    - Informace o tom, jak vytvořit místní standardní nástroj pro vyrovnávání zatížení a virtuální počítače pro back-endové fondy, najdete v tématu [rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md).
        - Připojit názvy nástrojů pro vyrovnávání zatížení a virtuálních počítačů do každé oblasti s přepínači **-R1** a **-R2**. 
- Azure PowerShell nainstalovány lokálně nebo Azure Cloud Shell.


Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-cross-region-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení mezi oblastmi


### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).


```azurepowershell-interactive
$rg = @{
    Name = 'MyResourceGroupLB-CR'
    Location = 'westus'
}
New-AzResourceGroup @rg

```

### <a name="create-cross-region-load-balancer-resources"></a>Vytvoření prostředků nástroje pro vyrovnávání zatížení mezi oblastmi

V této části vytvoříte prostředky potřebné pro nástroj pro vyrovnávání zatížení mezi oblastmi.

Pro front-end Nástroj pro vyrovnávání zatížení mezi oblastmi se používá veřejná IP adresa globálního SKU.

* K vytvoření veřejné IP adresy použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) .

* Vytvořte konfiguraci front-end IP adresy pomocí [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig).

* Vytvořte fond back-endové adresy pomocí [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig).

* Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig).

* Vytvořte Nástroj pro vyrovnávání zatížení mezi oblastmi pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Create global IP address for load balancer ##
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'MyResourceGroupLB-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create frontend configuration ##
$fe = @{
    Name = 'myFrontEnd-CR'
    PublicIpAddress = $publicIP
}
$feip = New-AzLoadBalancerFrontendIpConfig @fe

## Create back-end address pool ##
$be = @{
    Name = 'myBackEndPool-CR'
}
$bepool = New-AzLoadBalancerBackendAddressPoolConfig @be

## Create the load balancer rule ##
$rul = @{
    Name = 'myHTTPRule-CR'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
}
$rule = New-AzLoadBalancerRuleConfig @rul

## Create cross-region load balancer resource ##
$lbp = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    Name = 'myLoadBalancer-CR'
    Location = 'westus'
    Sku = 'Standard'
    Tier = 'Global'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bepool
    LoadBalancingRule = $rule
}
$lb = New-AzLoadBalancer @lbp
```

## <a name="configure-backend-pool"></a>Konfigurovat back-end fond

V této části přidáte do back-endu nástroje pro vyrovnávání zatížení mezi oblastmi dva místní standardní nástroje pro vyrovnávání zatížení.

> [!IMPORTANT]
> Pokud chcete tento postup provést, ujistěte se, že ve vašem předplatném je nasazené dvě místní nástroje pro vyrovnávání zatížení s back-end fondy.  Další informace najdete v tématu **[rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure PowerShell](quickstart-load-balancer-standard-public-powershell.md)**.

* Pomocí [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) a [Get-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/get-azloadbalancerfrontendipconfig) můžete ukládat informace o místním nástroji pro vyrovnávání zatížení do proměnných.

* Pomocí [New-AzLoadBalancerBackendAddressConfig](/powershell/module/az.network/new-azloadbalancerbackendaddressconfig) vytvořte konfiguraci fondu adres back-endu pro nástroj pro vyrovnávání zatížení.

* Pomocí [set-AzLoadBalancerBackendAddressPool](/powershell/module/az.network/new-azloadbalancerbackendaddresspool) přidejte místní front-end Nástroj pro vyrovnávání zatížení do back-endového fondu mezi oblastmi.

```azurepowershell-interactive
## Place the region one load balancer configuration in a variable ##
$region1 = @{
    Name = 'myLoadBalancer-R1'
    ResourceGroupName = 'CreatePubLBQS-rg-r1'
}
$R1 = Get-AzLoadBalancer @region1

## Place the region two load balancer configuration in a variable ##
$region2 = @{
    Name = 'myLoadBalancer-R2'
    ResourceGroupName = 'CreatePubLBQS-rg-r2'
}
$R2 = Get-AzLoadBalancer @region2

## Place the region one load balancer front-end configuration in a variable ##
$region1fe = @{
    Name = 'MyFrontEnd-R1'
    LoadBalancer = $R1
}
$R1FE = Get-AzLoadBalancerFrontendIpConfig @region1fe

## Place the region two load balancer front-end configuration in a variable ##
$region2fe = @{
    Name = 'MyFrontEnd-R2'
    LoadBalancer = $R2
}
$R2FE = Get-AzLoadBalancerFrontendIpConfig @region2fe

## Create the cross-region backend address pool configuration for region 1 ##
$region1ap = @{
    Name = 'MyBackendPoolConfig-R1'
    LoadBalancerFrontendIPConfigurationId = $R1FE.Id
}
$beaddressconfigR1 = New-AzLoadBalancerBackendAddressConfig @region1ap

## Create the cross-region backend address pool configuration for region 2 ##
$region2ap = @{
    Name = 'MyBackendPoolConfig-R2'
    LoadBalancerFrontendIPConfigurationId = $R2FE.Id
}
$beaddressconfigR2 = New-AzLoadBalancerBackendAddressConfig @region2ap

## Apply the backend address pool configuration for the cross-region load balancer ##
$bepoolcr = @{
    ResourceGroupName = 'myResourceGroupLB-CR'
    LoadBalancerName = 'myLoadBalancer-CR'
    Name = 'myBackEndPool-CR'
    LoadBalancerBackendAddress = $beaddressconfigR1,$beaddressconfigR2
}
Set-AzLoadBalancerBackendAddressPool @bepoolcr

```

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

V této části otestujete Nástroj pro vyrovnávání zatížení mezi oblastmi. Připojíte se k veřejné IP adrese ve webovém prohlížeči.  Virtuální počítače zabráníte v jednom z back-endového fondu služby pro vyrovnávání zatížení a budete sledovat převzetí služeb při selhání.

1. K získání veřejné IP adresy nástroje pro vyrovnávání zatížení použijte [příkaz Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) :

```azurepowershell-interactive
$ip = @{
    Name = 'myPublicIP-CR'
    ResourceGroupName = 'myResourceGroupLB-CR'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```
2. Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče. V prohlížeči se zobrazí výchozí stránka webového serveru služby IIS.

3. Zastavte virtuální počítače v back-end fondu jednoho z regionálních nástrojů pro vyrovnávání zatížení.

4. Aktualizujte webový prohlížeč a sledujte převzetí služeb při selhání připojení k druhému místnímu nástroji pro vyrovnávání zatížení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a zbývajících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupLB-CR'
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

* Byla vytvořena globální IP adresa.
* Vytvořil se nástroj pro vyrovnávání zatížení mezi oblastmi.
* Bylo vytvořeno pravidlo vyrovnávání zatížení.
* Přidání místních nástrojů pro vyrovnávání zatížení do back-endu pro nástroj pro vyrovnávání zatížení mezi oblastmi.
* Otestování nástroje pro vyrovnávání zatížení.


V dalším článku se naučíte, jak...
> [!div class="nextstepaction"]
> [Virtuální počítače nástroje pro vyrovnávání zatížení napříč zónami dostupnosti](tutorial-load-balancer-standard-public-zone-redundant-portal.md)