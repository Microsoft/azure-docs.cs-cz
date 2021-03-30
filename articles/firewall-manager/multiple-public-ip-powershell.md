---
title: Nasazení Azure Firewall s více veřejnými IP adresami pomocí Azure PowerShell
description: Nasazení brány firewall s několika veřejnými IP adresami pro ochranu virtuálního rozbočovače
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87006955"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Nasazení Azure Firewall s více veřejnými IP adresami

Pokud chcete chránit virtuální rozbočovač pomocí Azure Firewall, můžete bránu firewall nasadit s více veřejnými IP adresami pomocí Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Následující příklad Azure PowerShell použijte k nasazení Azure Firewall s více veřejnými IP adresami pro ochranu virtuálního rozbočovače.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>Aktualizace veřejné IP adresy

K aktualizaci veřejné IP adresy pro Azure Firewall můžete použít Azure PowerShell. Následující příklad odstraní jednu veřejnou IP adresu z brány firewall. Začíná třemi veřejnými IP adresami.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Další kroky

[Co je zabezpečené virtuální centrum?](secured-virtual-hub.md)