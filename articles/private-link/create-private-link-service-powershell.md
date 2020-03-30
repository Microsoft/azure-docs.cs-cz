---
title: Vytvoření služby privátního propojení Azure pomocí Azure PowerShellu| Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit službu privátního propojení Azure pomocí Azure PowerShellu
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932084"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Vytvoření služby Privátního propojení pomocí Azure PowerShellu
Tento článek ukazuje, jak vytvořit službu privátního propojení v Azure pomocí Azure PowerShellu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento článek vyžaduje nejnovější verzi modulu Azure PowerShell. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením privátního odkazu je nutné vytvořit skupinu prostředků s [new-azresourcegroup](/powershell/module/az.resources/new-azresourcegroup). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *WestCentralUS:*

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť pro privátní propojení s [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem *myvnet* s podsítí pro frontend (*frontendSubnet),* back-end (*backendSubnet*), privátní propojení (*otherSubnet*):

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
## <a name="create-internal-load-balancer"></a>Vytvořit interní vyrovnávání zatížení
Vytvořte interní standardní vyvažovač zatížení s [new-azloadbalancer](/powershell/module/az.network/new-azloadbalancer). Následující příklad vytvoří interní standardní nástroj pro vyrovnávání zatížení pomocí frontendové konfigurace IP adresy, sondy, pravidla a back-endového fondu, který jste vytvořili v předchozích krocích:

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
## <a name="create-a-private-link-service"></a>Vytvoření služby soukromého propojení
Vytvořte službu privátního propojení pomocí [služby New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  Tento příklad vytvoří službu privátního propojení s názvem *myPLS* pomocí standardního vykladače zatížení ve skupině prostředků s názvem *myResourceGroup*. 
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

### <a name="get-private-link-service"></a>Získat službu soukromého propojení
Získejte podrobnosti o službě privátního odkazu pomocí [služby Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) následujícím způsobem:

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

V této fázi je služba Private Link Service úspěšně vytvořena a je připravena přijímat přenosy. Všimněte si, že výše uvedený příklad je pouze demonstrovat vytváření služby Private Link Service pomocí prostředí PowerShell.  Ještě jsme nenakonfigurovali back-endové fondy vyrovnávání zatížení ani žádnou aplikaci v back-endových fondech, aby naslouchaly provozu. Pokud chcete zobrazit toky přenosů od konce do konce, důrazně doporučujeme nakonfigurovat aplikaci za standardním vyrovnáváním zatížení. 

Dále ukážeme, jak mapovat tuto službu na soukromý koncový bod v různých virtuálních sítích pomocí PowerShellu. Opět příklad je omezen na vytvoření private endpoint a připojení k private link service vytvořené výše. Virtuální počítače můžete vytvořit ve virtuální síti pro odesílání a přijímání přenosů do soukromého koncového bodu pro vytváření scénáře. 

## <a name="create-a-private-endpoint"></a>Vytvoření privátního koncového bodu
### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Vytvořte virtuální síť pro svůj soukromý koncový bod pomocí [new-azvirtualnetwork](/powershell/module/az.network/new-azvirtualnetwork). Tento příklad vytvoří virtuální síť s názvem *vnetPE* ve skupině prostředků s názvem *myResourceGroup*:
 
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
Vytvořte privátní koncový bod pro využívání služby privátního propojení vytvořené výše ve virtuální síti:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Získání soukromého koncového bodu
Získejte IP adresu privátního koncového bodu `Get-AzPrivateEndpoint` takto:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>Schválení připojení privátního koncového bodu
Schválení připojení privátního koncového bodu ke službě privátního propojení pomocí služby Approve-AzPrivateEndpointConnection.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Další kroky
- Další informace o [privátním propojení Azure](private-link-overview.md)
 
