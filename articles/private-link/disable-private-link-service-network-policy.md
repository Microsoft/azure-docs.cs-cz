---
title: 'Zakázat zásady sítě pro zdrojovou IP adresu služby privátního propojení Azure '
description: Informace o zakázání zásad sítě pro privátní připojení Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1062f126da8be6b37f6b52eee520425b3edcde16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744336"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Zakázat zásady sítě pro zdrojovou IP adresu služby privátního propojení

Aby bylo možné zvolit zdrojovou IP adresu pro službu privátního propojení, `privateLinkServiceNetworkPolicies` je v podsíti vyžadováno explicitní nastavení zakázání. Toto nastavení se vztahuje pouze na konkrétní privátní IP adresu, kterou jste zvolili jako zdrojovou IP adresu služby privátního propojení. V případě jiných prostředků v podsíti se přístup řídí podle definice pravidel zabezpečení skupiny zabezpečení sítě (NSG). 
 
Při použití libovolného klienta Azure (PowerShell, CLI nebo šablony) se vyžaduje další krok, který tuto vlastnost změní. Zásadu můžete zakázat pomocí služby Cloud Shell z Azure Portal nebo místní instalace Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí šablon Azure Resource Manager.  
 
Použijte následující postup, chcete-li zakázat zásady sítě služby privátního propojení pro virtuální síť s názvem *myVirtualNetwork* s *výchozí* podsítí hostovanou ve skupině prostředků s názvem *myResourceGroup*. 

## <a name="using-azure-powershell"></a>Použití Azure Powershell
Tato část popisuje, jak zakázat zásady privátního koncového bodu podsítě pomocí Azure PowerShell.
V kódu nahraďte "default" názvem virtuální podsítě.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
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
## <a name="next-steps"></a>Další kroky
- Další informace o [privátním koncovém bodu Azure](private-endpoint-overview.md)
 
