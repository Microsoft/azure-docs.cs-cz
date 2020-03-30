---
title: Přidání nebo odebrání delegování podsítě ve virtuální síti Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak přidat nebo odebrat delegovanou podsíť pro službu v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201862"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Přidání nebo odebrání delegování podsítě

Delegování podsítě uděluje službě explicitní oprávnění k vytvoření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru při nasazování služby. Tento článek popisuje, jak přidat nebo odebrat delegovanou podsíť pro službu Azure.

## <a name="portal"></a>Portál

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť, kterou později delegujete na službu Azure.

1. Na levé horní straně obrazovky vyberte Vytvořit virtuální**Networking** > **síť** **pro síť o prostředku** > .
1. V **možnosti Vytvořit virtuální síť**zadejte nebo vyberte tyto informace:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název) | Zadejte *MyVirtualNetwork*. |
    | Adresní prostor | Zadejte *10.0.0.0/16*. |
    | Předplatné | Vyberte své předplatné.|
    | Skupina prostředků | Vyberte **Vytvořit nový**, zadejte *myResourceGroup*a pak vyberte **OK**. |
    | Umístění | Vyberte **možnost EastUS**.|
    | Podsíť – název | Zadejte *mySubnet*. |
    | Podsíť – Rozsah adres | Zadejte *10.0.0.0/24*. |
    |||
1. Zbytek ponechejte jako výchozí a pak vyberte **Vytvořit**.

### <a name="permissions"></a>Oprávnění

Pokud jste podsíť, kterou chcete delegovat, nevytvořili, potřebujete následující `Microsoft.Network/virtualNetworks/subnets/write`oprávnění: .

Předdefinovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) také obsahuje potřebná oprávnění.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, na službu Azure.

1. Na vyhledávacím panelu portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Ve výsledcích hledání vyberte *položku myVirtualNetwork*.
3. Vyberte **Podsítě**v části **NASTAVENÍ**a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* vyberte pro seznam **delegování podsítě** ze služeb uvedených v **podsíti Delegát službě** (například **Microsoft.DBforPostgreSQL/serversv2**).  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Odebrání delegování podsítě ze služby Azure

1. Na vyhledávacím panelu portálu zadejte *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
2. Ve výsledcích hledání vyberte *položku myVirtualNetwork*.
3. Vyberte **Podsítě**v části **NASTAVENÍ**a pak vyberte **mySubnet**.
4. Na stránce *mySubnet* vyberte pro seznam **delegování podsítě** **možnost Žádné** ze služeb uvedených v **podsíti Delegát ke službě**. 

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat Azure CLI místně místo, tento článek vyžaduje, abyste použili Azure CLI verze 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem **myResourceGroup** v umístění **eastus:**

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Pomocí příkazu [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) vytvořte virtuální síť **myVnet** s podsítí **mySubnet** ve skupině prostředků **myResourceGroup**.

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

Pokud jste podsíť, kterou chcete delegovat, nevytvořili, potřebujete následující `Microsoft.Network/virtualNetworks/subnets/write`oprávnění: .

Předdefinovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) také obsahuje potřebná oprávnění.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, na službu Azure. 

Pomocí [aktualizace podsítě sítě az](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) aktualizujte podsíť s názvem **mySubnet** delegováním na službu Azure.  V tomto příkladu **Microsoft.DBforPostgreSQL/serversv2** se používá pro příklad delegování:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Chcete-li ověřit, zda bylo delegování použito, použijte [pořad podsítě sítě AZ](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Ověřte, zda je služba delegována do podsítě v části vlastnost **serviceName**:

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

Pomocí [aktualizace podsítě sítě AZ](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) odeberte delegování z podsítě s názvem **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Chcete-li ověřit delegování bylo odebráno, použijte [az síť virtuální sítě podsítě show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Ověřte, zda je služba odebrána z podsítě pod názvem vlastnost **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
Výstup z příkazu je null závorka:
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
Vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť s názvem **myVnet** s podsítí s názvem **mySubnet** pomocí [nástroje New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) v **myResourceGroup** pomocí [nové azvirtualnetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Adresní prostor IP pro virtuální síť je **10.0.0.0/16**. Podsíť ve virtuální síti je **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Oprávnění

Pokud jste podsíť, kterou chcete delegovat, nevytvořili, potřebujete následující `Microsoft.Network/virtualNetworks/subnets/write`oprávnění: .

Předdefinovaná role [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) také obsahuje potřebná oprávnění.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegování podsítě na službu Azure

V této části delegujete podsíť, kterou jste vytvořili v předchozí části, na službu Azure. 

Pomocí [add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) aktualizujte podsíť s názvem **mySubnet** delegací s názvem **myDelegation** ke službě Azure.  V tomto příkladu **Microsoft.DBforPostgreSQL/serversv2** se používá pro příklad delegování:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
K ověření [delegování použijte příkaz Get-AzDelegation:](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest)

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

[Odebrání-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) slouží k odebrání delegování z podsítě s názvem **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Pomocí [příkazu Získat AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) ověřte odebrání delegování:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [spravovat podsítě v Azure](virtual-network-manage-subnet.md).
