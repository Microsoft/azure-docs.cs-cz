---
title: 'Zakázat zásady sítě pro zdrojovou IP adresu služby privátního propojení Azure '
description: Informace o zakázání zásad sítě pro privátní připojení Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: b2003b9c7520cfa3e82576fd3430063c20d452ff
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104561"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Zakázat zásady sítě pro zdrojovou IP adresu služby privátního propojení

Aby bylo možné zvolit zdrojovou IP adresu pro službu privátního propojení, je v podsíti vyžadováno `privateLinkServiceNetworkPolicies` explicitní nastavení zakázání. Toto nastavení se vztahuje pouze na konkrétní privátní IP adresu, kterou jste zvolili jako zdrojovou IP adresu služby privátního propojení. V případě jiných prostředků v podsíti se přístup řídí podle definice pravidel zabezpečení skupiny zabezpečení sítě (NSG). 
 
Při použití libovolného klienta Azure (PowerShell, CLI nebo šablony) se vyžaduje další krok, který tuto vlastnost změní. Zásadu můžete zakázat pomocí služby Cloud Shell z Azure Portal nebo místní instalace Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí šablon Azure Resource Manager.  
 
Použijte následující postup, chcete-li zakázat zásady sítě služby privátního propojení pro virtuální síť s názvem *myVirtualNetwork* s *výchozí* podsítí hostovanou ve skupině prostředků s názvem *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Tato část popisuje, jak zakázat zásady privátního koncového bodu podsítě pomocí Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork ` 
  | Select -ExpandProperty subnets ` 
  | Where-Object  {$_.Name -eq 'default'} ).privateLinkServiceNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Použití Azure CLI
Tato část popisuje, jak zakázat zásady privátního koncového bodu podsítě pomocí Azure CLI.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
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
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Další postup
- Další informace o [privátním koncovém bodu Azure](private-endpoint-overview.md)
 
