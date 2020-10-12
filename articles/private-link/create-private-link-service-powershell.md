---
title: Vytvoření služby privátního propojení Azure pomocí Azure PowerShell | Microsoft Docs
description: Naučte se vytvořit službu privátního propojení Azure pomocí Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 3c808623269b8fabc32134a165b964a3b0747d4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88505610"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Vytvoření služby privátního propojení pomocí Azure PowerShell
V tomto článku se dozvíte, jak vytvořit službu privátního propojení v Azure pomocí Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek nejnovější verzi modulu Azure PowerShell. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Předtím, než budete moci vytvořit privátní odkaz, je nutné vytvořit skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť pro privátní propojení pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myvnet* s podsítí pro front-end (*frontendSubnet*), back-end (*backendSubnet*), privátní odkaz (*otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet
```
## <a name="create-internal-load-balancer"></a>Vytvořit interní Load Balancer
Vytvořte interní Standard Load Balancer pomocí [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří interní Standard Load Balancer pomocí konfigurace IP adresy front-endu, testu, pravidla a back-end fondu, který jste vytvořili v předchozích krocích:

```azurepowershell

$lbBackendName = "LB-backend"
$lbFrontName = "LB-frontend"
$lbName = "lb"

#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard
```
## <a name="create-a-private-link-service"></a>Vytvoření služby privátního propojení
Vytvořte službu privátního propojení pomocí [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Tento příklad vytvoří službu privátního propojení s názvem *myPLS* pomocí Standard Load Balancer ve skupině prostředků s názvem *myResourceGroup*.
```azurepowershell

$plsIpConfigName = "PLS-ipconfig"
$plsName = "pls"
$peName = "pe"
 
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig
```

### <a name="get-private-link-service"></a>Získat službu privátního propojení
Získejte podrobnosti o službě privátního propojení pomocí příkazu [Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) následujícím způsobem:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName
```

V této fázi je vaše služba privátního propojení úspěšně vytvořená a připravená na příjem provozu. Všimněte si, že výše uvedený příklad je pouze ukázka vytvoření služby privátního propojení pomocí prostředí PowerShell.  Nenakonfigurovali jsme back-end fondy nástroje pro vyrovnávání zatížení ani žádné aplikace na back-end fondu, aby naslouchaly provozu. Pokud se chcete podívat na koncové toky přenosů dat, důrazně doporučujeme nakonfigurovat aplikaci za vaším standardním nástrojem pro vyrovnávání zatížení.

Dále vám ukážeme, jak namapovat tuto službu na privátní koncový bod v jiné virtuální síti pomocí PowerShellu. V tomto příkladu je tento příklad omezený na vytvoření privátního koncového bodu a připojení ke službě privátního propojení vytvořené výše. V Virtual Network můžete vytvořit Virtual Machines pro odesílání a příjem provozu do privátního koncového bodu pro vytvoření scénáře.

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť pro privátní koncový bod pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Tento příklad vytvoří virtuální síť s názvem *vnetPE*   ve skupině prostředků s názvem *myResourceGroup*:

```azurepowershell
$virtualNetworkNamePE = "vnetPE"

# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled"

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet
```

### <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
Vytvořte privátní koncový bod pro využívání služby privátního propojení vytvořené výše ve vaší virtuální síti:

```azurepowershell

$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest
```

### <a name="get-private-endpoint"></a>Získat soukromý koncový bod
Získejte IP adresu privátního koncového bodu následujícím `Get-AzPrivateEndpoint` způsobem:

```azurepowershell
# Get Private Endpoint and its IP Address
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress

```

### <a name="approve-the-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu
Schvalte připojení privátního koncového bodu ke službě privátního propojení pomocí příkazu "schválit-AzPrivateEndpointConnection".

```azurepowershell

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved"

```

## <a name="next-steps"></a>Další kroky
- Další informace o [privátním propojení Azure](private-link-overview.md)

