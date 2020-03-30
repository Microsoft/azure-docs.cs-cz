---
title: Odložky veřejné IP adresy od virtuálního počítače Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak oddělit veřejnou IP adresu od virtuálního počítačů.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: kumud
ms.openlocfilehash: 1c27af30f97ea967d170b2cccaefb2e95f8fedaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900746"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Odložky veřejné IP adresy od virtuálního počítače Azure 

V tomto článku se dozvíte, jak oddělit veřejnou IP adresu od virtuálního počítače Azure.

K odvedení veřejné IP adresy od virtuálního počítače můžete použít [portál Azure](#azure-portal), [rozhraní příkazového řádku](#azure-cli) Azure (CLI) nebo [PowerShell.](#powershell)

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyhledejte nebo vyhledejte virtuální počítač, ke kterému chcete zrušit přidružení veřejné IP adresy, a vyberte ji.
3. Na stránce Virtuální ho virtuálního zařízení vyberte **Přehled**, vyberte veřejnou IP adresu, jak je znázorněno na následujícím obrázku:

   ![Vybrat veřejnou IP adresu](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na stránce veřejné IP adresy vyberte **Přehled**a pak vyberte **Oddělit**, jak je znázorněno na následujícím obrázku:

    ![Oddělit veřejnou IP adresu](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. V **posuzované veřejné IP adrese**vyberte **Ano**.

## <a name="azure-cli"></a>Azure CLI

Nainstalujte [azure cli](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. V následujících příkazech příkazu příkazu příkazu příkazu příkazu vyberte tlačítko **Try it.** Výběrem **try vyvoláte** Cloud shell, pomocí kterého se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte příkazového konto místně v `az login`Bash, přihlaste se do Azure s .
2. Veřejná IP adresa je přidružena ke konfiguraci IP síťového rozhraní připojeného k virtuálnímu počítače. Pomocí příkazu [aktualizace az network nic-ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) můžete oddělit veřejnou IP adresu od konfigurace PROTOKOLU IP. Následující příklad disociuje veřejnou IP adresu s názvem *myVMPublicIP* z konfigurace IP s názvem *ipconfigmyVM* existujícího síťového rozhraní s názvem *myVMVMNic,* které je připojeno k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Pokud neznáte název síťového rozhraní připojeného k virtuálnímu počítači, zobrazte je pomocí příkazu [az vm nic.](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) Například následující příkaz uvádí názvy síťových rozhraní připojených k virtuálnímu počítačům s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     V předchozím příkladu *je myVMVMNic* název síťového rozhraní.

   - Pokud neznáte název konfigurace IP pro síťové rozhraní, načtěte je pomocí příkazu [az network nic ip-config.](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) Následující příkaz například uvádí názvy veřejných konfigurací IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Pokud neznáte název veřejné konfigurace IP pro síťové rozhraní, načtěte je pomocí příkazu [az network nic ip-config show.](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-show) Následující příkaz například uvádí názvy veřejných konfigurací IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Nainstalujte [PowerShell](/powershell/azure/install-az-ps)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí, které můžete spustit přímo z portálu Azure Portal. Má PowerShell předinstalovaný a nakonfigurovaný pro použití s vaším účtem. V následujících příkazech PowerShellu vyberte tlačítko **Try it.** Výběrem **try vyvoláte** Cloud shell, pomocí kterého se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte PowerShell místně, přihlaste se k Azure s `Connect-AzAccount`.
2. Veřejná IP adresa je přidružena ke konfiguraci IP síťového rozhraní připojeného k virtuálnímu počítače. Pomocí příkazu [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) získáte síťové rozhraní. Nastavte hodnotu veřejné IP adresy na hodnotu null a potom pomocí příkazu [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) zapište novou konfiguraci IP do síťového rozhraní.

   Následující příklad disociuje veřejnou IP adresu s názvem *myVMPublicIP* ze síťového rozhraní s názvem *myVMVMNic,* které je připojeno k virtuálnímu virtuálnímu virtuálnímu ma s názvem *myVM*. Všechny prostředky jsou ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Pokud neznáte název síťového rozhraní připojeného k virtuálnímu počítači, můžete je zobrazit pomocí příkazu [Get-AzVM.](/powershell/module/Az.Compute/Get-AzVM) Například následující příkaz uvádí názvy síťových rozhraní připojených k virtuálnímu počítačům s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné příkladu, který následuje. V ukázkovém výstupu je *myVMVMNic* název síťového rozhraní.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Pokud neznáte název konfigurace IP pro síťové rozhraní, načtěte je pomocí příkazu [Get-AzNetworkInterface.](/powershell/module/Az.Network/Get-AzNetworkInterface) Následující příkaz například uvádí názvy konfigurací PROTOKOLU IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné příkladu, který následuje. V ukázkovém výstupu je *ipconfigmyVM* název konfigurace IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [přidružit veřejnou IP adresu k virtuálnímu virtuálnímu virtuálnímu ms](associate-public-ip-address-vm.md).
