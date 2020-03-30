---
title: Zakázání zásad sítě pro privátní koncové body v Azure
description: Zjistěte, jak zakázat zásady sítě pro privátní koncové body.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: b5ab62e7ab57d32a11a45713519633034deb6a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453023"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Zakázání síťových zásad pro privátní koncové body

Zásady sítě, jako jsou skupiny zabezpečení sítě (NSG), nejsou podporovány pro privátní koncové body. Chcete-li nasadit soukromý koncový bod v dané podsíti, je v této podsíti vyžadováno explicitní nastavení zakázat. Toto nastavení platí pouze pro soukromý koncový bod. U ostatních prostředků v podsíti je přístup řízen na základě definice pravidel zabezpečení skupin zabezpečení sítě (NSG). 
 
Při použití portálu k vytvoření soukromého koncového bodu je toto nastavení automaticky zakázáno jako součást procesu vytváření. Nasazení pomocí jiných klientů vyžaduje další krok ke změně tohoto nastavení. Nastavení můžete zakázat pomocí cloudového prostředí z webu Azure Portal nebo místních instalací Azure PowerShellu, Azure CLI nebo použít šablony Azure Resource Manageru.  
 
Následující příklady popisují, `PrivateEndpointNetworkPolicies` jak zakázat pro virtuální síť s názvem *myVirtualNetwork* s *výchozí* podsítí hostovoci hostovoci ve skupině prostředků s názvem *myResourceGroup*.

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Tato část popisuje, jak zakázat zásady soukromého koncového bodu podsítě pomocí Azure PowerShellu.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Použití Azure CLI
Tato část popisuje, jak zakázat zásady soukromého koncového bodu podsítě pomocí rozhraní příkazového příkazového příkazu Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Použití šablony
Tato část popisuje, jak zakázat zásady soukromého koncového bodu podsítě pomocí šablony Správce prostředků Azure.
```json
{ 
          "name": "myVirtualNetwork", 
          "type": "Microsoft.Network/virtualNetworks", 
          "apiVersion": "2019-04-01", 
          "location": "WestUS", 
          "properties": { 
                "addressSpace": { 
                     "addressPrefixes": [ 
                          "10.0.0.0/16" 
                        ] 
                  }, 
                  "subnets": [ 
                         { 
                                "name": "default", 
                                "properties": { 
                                    "addressPrefix": "10.0.0.0/24", 
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Další kroky
- Další informace o [privátním koncovém bodu Azure](private-endpoint-overview.md)
 
