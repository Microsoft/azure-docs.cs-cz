---
title: 'Zakázání zásad sítě pro zdrojovou IP adresu služby Azure Private Link '
description: Přečtěte si, jak zakázat zásady sítě pro privátní propojení Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 4c6bd64d141341e0b7fa5641e04320a95d7951bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452993"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Zakázání zásad sítě pro zdrojovou ip adresu služby Private Link

Chcete-li zvolit zdrojovou ADRESU IP pro službu `privateLinkServiceNetworkPolicies` Private Link, je v podsíti vyžadováno explicitní nastavení zakázání. Toto nastavení platí pouze pro konkrétní privátní IP adresu, kterou jste zvolili jako zdrojovou IP adresu služby Private Link. U ostatních prostředků v podsíti je přístup řízen na základě definice pravidel zabezpečení skupin zabezpečení sítě (NSG). 
 
Při použití libovolného klienta Azure (PowerShell, CLI nebo šablony), je nutné další krok ke změně této vlastnosti. Zásadu můžete zakázat pomocí cloudového prostředí z webu Azure Portal nebo místních instalací Azure PowerShellu, Azure CLI nebo použít šablony Azure Resource Manageru.  
 
Podle následujících kroků zakažte zásady sítě privátní služby propojení pro virtuální síť s názvem *myVirtualNetwork* s *výchozí* podsítí hostovohouji ve skupině prostředků s názvem *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Tato část popisuje, jak zakázat zásady soukromého koncového bodu podsítě pomocí Azure PowerShellu.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Použití Azure CLI
Tato část popisuje, jak zakázat zásady soukromého koncového bodu podsítě pomocí rozhraní příkazového příkazového příkazu Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Další kroky
- Další informace o [Azure Private Endpoint](private-endpoint-overview.md)
 
