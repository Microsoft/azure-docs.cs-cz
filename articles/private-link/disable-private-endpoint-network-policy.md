---
title: Zakázání zásad sítě pro privátní koncové body v Azure
description: Přečtěte si, jak zakázat zásady sítě pro privátní koncové body.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84737508"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Zakázání zásad sítě pro privátní koncové body

Zásady sítě, jako jsou skupiny zabezpečení sítě (NSG), nejsou podporovány u privátních koncových bodů. Aby bylo možné v dané podsíti nasadit privátní koncový bod, je v této podsíti vyžadováno explicitní nastavení zakázání. Toto nastavení platí jenom pro soukromý koncový bod. V případě jiných prostředků v podsíti se přístup řídí podle definice pravidel zabezpečení skupiny zabezpečení sítě (NSG). 
 
Když použijete portál k vytvoření privátního koncového bodu, toto nastavení se automaticky zakáže jako součást procesu vytvoření. Nasazení pomocí jiných klientů vyžaduje další krok ke změně tohoto nastavení. Nastavení můžete zakázat pomocí služby Cloud Shell z Azure Portal nebo místní instalace Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí šablon Azure Resource Manager.  
 
Následující příklady popisují, jak zakázat `PrivateEndpointNetworkPolicies` virtuální síť s názvem *myVirtualNetwork* s *výchozí* podsítí hostovanou ve skupině prostředků s názvem *myResourceGroup*.

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Tato část popisuje, jak zakázat zásady privátního koncového bodu podsítě pomocí Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Použití Azure CLI
Tato část popisuje, jak zakázat zásady privátního koncového bodu podsítě pomocí Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
```
## <a name="using-a-template"></a>Použití šablony
Tato část popisuje, jak zakázat zásady privátního koncového bodu podsítě pomocí šablony Azure Resource Manager.
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
 
