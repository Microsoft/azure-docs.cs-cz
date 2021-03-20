---
title: Přidání nebo odebrání delegování podsítě ve virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak přidat nebo odebrat delegovanou podsíť pro službu v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 2bb80ba421617d5fd1699826deda00e56f1e43af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943678"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Přidání nebo odebrání delegování podsítě

Delegování podsítě dává službě explicitní oprávnění k vytváření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru při nasazování služby. Tento článek popisuje, jak přidat nebo odebrat delegovanou podsíť pro službu Azure.

## <a name="portal"></a>Portál

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť, které později delegujete na službu Azure.

1. V levé horní části obrazovky vyberte **vytvořit prostředek**  >  **síť**  >  **virtuální síť**.
1. V nástroji **vytvořit virtuální síť** zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.0.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **vytvořit nový**, zadejte *myResourceGroup* a pak vyberte **OK**. |
    | Umístění | Vyberte **EastUS**.|
    | Název podsítě | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.0.0.0/24*. |
    |||
1. Ponechte REST jako výchozí a pak vyberte **vytvořit**.

### <a name="permissions"></a>Oprávnění

Pokud jste nevytvořili podsíť, kterou byste chtěli delegovat na službu Azure, budete potřebovat následující oprávnění: `Microsoft.Network/virtualNetworks/subnets/write` .

Integrovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) obsahuje taky potřebná oprávnění.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, do služby Azure.

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Ve výsledcích hledání vyberte *myVirtualNetwork*.
3. V části **Nastavení** vyberte **podsítě** a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* vyberte v seznamu **delegování podsítě** ze služeb uvedených v části **delegovat podsíť na službu** (například **Microsoft. DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Odebrání delegování podsítě ze služby Azure

1. Na panelu hledání na portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Ve výsledcích hledání vyberte *myVirtualNetwork*.
3. V části **Nastavení** vyberte **podsítě** a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* v seznamu **delegování podsítě** vyberte možnost **žádné** ze služeb uvedených v části **delegovat podsíť na službu**. 

## <a name="azure-cli"></a>Azure CLI

Připravte prostředí pro rozhraní příkazového řádku Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroup** v umístění **eastus** :

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Pomocí příkazu [az network vnet create](/cli/azure/network/vnet) vytvořte virtuální síť **myVnet** s podsítí **mySubnet** ve skupině prostředků **myResourceGroup**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Oprávnění

Pokud jste nevytvořili podsíť, kterou byste chtěli delegovat na službu Azure, budete potřebovat následující oprávnění: `Microsoft.Network/virtualNetworks/subnets/write` .

Integrovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) obsahuje taky potřebná oprávnění.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, do služby Azure. 

Pomocí [AZ Network VNet Subnet Update aktualizujte](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) podsíť s názvem **mySubnet** s delegováním na službu Azure.  V tomto příkladu se pro příklad delegování používá **Microsoft. DBforPostgreSQL/serversv2** :

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

K ověření použití delegování použijte [AZ Network VNet Subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Ověřte, zda je služba delegována do podsítě pod vlastností **ServiceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Odebrání delegování podsítě ze služby Azure

Pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) odeberte delegování z podsítě s názvem **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
K ověření odebrání delegace použijte [AZ Network VNet Subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show). Ověřte, že je služba z podsítě odebraná v rámci vlastnosti **ServiceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Výstup příkazu je znaková závorka, která je null:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Připojení k Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) v **myResourceGroup** pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Adresní prostor IP adres pro virtuální síť je **10.0.0.0/16**. Podsíť ve virtuální síti je **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Oprávnění

Pokud jste nevytvořili podsíť, kterou byste chtěli delegovat na službu Azure, budete potřebovat následující oprávnění: `Microsoft.Network/virtualNetworks/subnets/write` .

Integrovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) obsahuje taky potřebná oprávnění.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, do služby Azure. 

Pomocí [Add-AzDelegation](/powershell/module/az.network/add-azdelegation) aktualizujte podsíť s názvem **mySubnet** s delegováním **myDelegation** do služby Azure.  V tomto příkladu se pro příklad delegování používá **Microsoft. DBforPostgreSQL/serversv2** :

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
K ověření delegování použijte [příkaz Get-AzDelegation](/powershell/module/az.network/get-azdelegation) :

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Odebrání delegování podsítě ze služby Azure

Pomocí [Remove-AzDelegation](/powershell/module/az.network/remove-azdelegation) odeberte delegování z podsítě s názvem **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
K ověření odebrání delegování použijte [příkaz Get-AzDelegation](/powershell/module/az.network/get-azdelegation) :

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Další kroky
- Naučte se [Spravovat podsítě v Azure](virtual-network-manage-subnet.md).
