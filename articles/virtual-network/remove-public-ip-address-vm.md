---
title: Zrušení přidružení veřejné IP adresy z virtuálního počítače Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, jak zrušit přidružení veřejné IP adresy z virtuálního počítače.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934187"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Zrušení přidružení veřejné IP adresy z virtuálního počítače Azure 

V tomto článku se dozvíte, jak zrušit přidružení veřejné IP adresy z virtuálního počítače Azure (VM).

Pomocí [Azure Portal](#azure-portal), [rozhraní příkazového řádku](#azure-cli) Azure (CLI) nebo [PowerShellu](#powershell) můžete oddělit veřejnou IP adresu z virtuálního počítače.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyhledejte virtuální počítač, ze kterého chcete zrušit přidružení veřejné IP adresy, nebo jej vyhledejte.
3. Na stránce virtuální počítač vyberte **Přehled**, vyberte veřejnou IP adresu, jak je znázorněno na následujícím obrázku:

   ![Vybrat veřejnou IP adresu](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. Na stránce Veřejná IP adresa vyberte **Přehled** a pak vyberte zrušit **přidružení**, jak je znázorněno na následujícím obrázku:

    ![Zrušit přidružení veřejné IP adresy](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. V položku zrušit **přidružení veřejné IP adresy** vyberte **Ano**.

## <a name="azure-cli"></a>Azure CLI

Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. V následujících příkazech rozhraní příkazového řádku vyberte tlačítko **vyzkoušet** . Výběrem možnosti **vyzkoušet** vyvoláte Cloud Shell, pomocí které se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte rozhraní příkazového řádku místně v bash, přihlaste se k Azure pomocí `az login` .
2. Veřejná IP adresa je přidružená ke konfiguraci protokolu IP síťového rozhraní připojeného k virtuálnímu počítači. Pomocí příkazu [AZ Network nic-IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) oddělit veřejnou IP adresu z konfigurace protokolu IP. Následující příklad dissociates veřejnou IP adresu s názvem *myVMPublicIP* z konfigurace protokolu IP s názvem *ipconfigmyVM* existujícího síťového rozhraní s názvem *myVMVMNic* , který je připojen k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Pokud neznáte název síťového rozhraní připojeného k vašemu VIRTUÁLNÍmu počítači, zobrazte ho pomocí příkazu [AZ VM nic list](/cli/azure/vm/nic#az-vm-nic-list) . Například následující příkaz vypíše názvy síťových rozhraní připojených k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     V předchozím příkladu je *myVMVMNic* název síťového rozhraní.

   - Pokud název konfigurace protokolu IP pro síťové rozhraní neznáte, načtěte ho pomocí příkazu [AZ Network nic IP-config list](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) . Například následující příkaz zobrazí seznam názvů konfigurací veřejné IP adresy pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Pokud neznáte název konfigurace veřejné IP adresy pro síťové rozhraní, načtěte ho pomocí příkazu [AZ Network nic IP-config show](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) . Například následující příkaz zobrazí seznam názvů konfigurací veřejné IP adresy pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Nainstalujte [PowerShell](/powershell/azure/install-az-ps)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí, které můžete spustit přímo z portálu Azure Portal. Má předinstalované prostředí PowerShell a je nakonfigurováno pro použití s vaším účtem. V následujících příkazech PowerShellu vyberte tlačítko **vyzkoušet** . Výběrem možnosti **vyzkoušet** vyvoláte Cloud Shell, pomocí které se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte PowerShell místně, přihlaste se k Azure pomocí `Connect-AzAccount` .
2. Veřejná IP adresa je přidružená ke konfiguraci protokolu IP síťového rozhraní připojeného k virtuálnímu počítači. K získání síťového rozhraní použijte příkaz [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) . Nastavte hodnotu veřejné IP adresy na null a pak pomocí příkazu [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) zapište novou konfiguraci protokolu IP do síťového rozhraní.

   Následující příklad dissociates veřejnou IP adresu s názvem *myVMPublicIP* ze síťového rozhraní s názvem *myVMVMNic* , které je připojeno k virtuálnímu počítači s názvem *myVM*. Všechny prostředky jsou ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Pokud neznáte název síťového rozhraní připojeného k vašemu VIRTUÁLNÍmu počítači, zobrazte ho pomocí příkazu [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) . Například následující příkaz vypíše názvy síťových rozhraní připojených k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu. V příkladu výstupu je *myVMVMNic* název síťového rozhraní.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Pokud název konfigurace protokolu IP pro síťové rozhraní neznáte, načtěte ho pomocí příkazu [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) . Například následující příkaz vypíše názvy konfigurací IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu. V příkladu výstupu je *ipconfigmyVM* název konfigurace protokolu IP.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [přidružit veřejnou IP adresu k virtuálnímu počítači](associate-public-ip-address-vm.md).
