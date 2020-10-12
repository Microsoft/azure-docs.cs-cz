---
title: Přidružení veřejné IP adresy k virtuálnímu počítači
titlesuffix: Azure Virtual Network
description: Naučte se přidružit veřejnou IP adresu k virtuálnímu počítači pomocí Azure Portal nebo Azure CLI.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 9b5fc9e4118d98905138b7f205f61d85a96b60b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035463"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Přidružení veřejné IP adresy k virtuálnímu počítači

V tomto článku se dozvíte, jak přidružit veřejnou IP adresu k existujícímu virtuálnímu počítači (VM). Pokud se chcete připojit k virtuálnímu počítači z Internetu, musí mít virtuální počítač přiřazenou veřejnou IP adresu. Pokud chcete vytvořit nový virtuální počítač s veřejnou IP adresou, můžete to udělat pomocí [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [rozhraní příkazového řádku Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md)nebo [PowerShellu](virtual-network-deploy-static-pip-arm-ps.md). Veřejné IP adresy mají nominální poplatek. Podrobnosti najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/ip-addresses/). Počet veřejných IP adres, které můžete použít v rámci předplatného, je omezený. Podrobnosti najdete v tématu [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

K virtuálnímu počítači můžete přidružit veřejnou IP adresu pomocí [Azure Portal](#azure-portal), [rozhraní příkazového řádku](#azure-cli) Azure (CLI) nebo [PowerShellu](#powershell) .

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Vyhledejte virtuální počítač, do kterého chcete přidat veřejnou IP adresu, nebo ho vyhledejte a vyberte.
3. V části **Nastavení**vyberte **sítě**a pak vyberte síťové rozhraní, do kterého chcete přidat veřejnou IP adresu, jak je znázorněno na následujícím obrázku:

   ![Vybrat síťové rozhraní](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Veřejné IP adresy jsou přidružené k síťovým rozhraním připojeným k virtuálnímu počítači. Na předchozím obrázku má virtuální počítač jenom jedno síťové rozhraní. Pokud má virtuální počítač několik síťových rozhraní, zobrazí se všechny a Vy byste vybrali síťové rozhraní, ke kterému chcete přiřadit veřejnou IP adresu.

4. Vyberte konfigurace **protokolu IP** a pak vyberte konfiguraci protokolu IP, jak je znázorněno na následujícím obrázku:

   ![Vybrat konfiguraci protokolu IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Veřejné IP adresy jsou přidruženy k konfiguracím protokolu IP pro síťové rozhraní. V předchozím obrázku má síťové rozhraní jednu konfiguraci protokolu IP. Pokud má síťové rozhraní více konfigurací protokolu IP, všechny se zobrazí v seznamu a Vy byste vybrali konfiguraci protokolu IP, ke které chcete přidružit veřejnou IP adresu.

5. Vyberte možnost **povoleno**a pak vyberte možnost **IP adresa (*Konfigurace požadovaných nastavení*)**. Vyberte existující veřejnou IP adresu, která automaticky zavře pole **zvolit veřejnou IP adresu** . Pokud nemáte uvedené žádné veřejné IP adresy, musíte si ho vytvořit. Informace o postupu najdete v tématu [Vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Vyberte **Save (Uložit**), jak je znázorněno na následujícím obrázku, a potom pole pro konfiguraci protokolu IP zavřete.

   ![Povolit veřejnou IP adresu](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Veřejné IP adresy, které se zobrazí, jsou ty, které existují ve stejné oblasti jako virtuální počítač. Pokud máte v oblasti vytvořené víc veřejných IP adres, zobrazí se tady. Pokud jsou některé z nich zobrazené šedě, je to proto, že adresa je už přidružená k jinému prostředku.

6. Zobrazte veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP, jak je znázorněno na následujícím obrázku. Zobrazení IP adresy může trvat několik sekund.

   ![Zobrazit přiřazenou veřejnou IP adresu](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adresa je přiřazena z fondu adres, které se používají v každé oblasti Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v tématu [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Přiřazená adresa může být libovolná adresa ve fondech používaných pro danou oblast. Pokud potřebujete, aby se adresa přiřadila z konkrétního fondu v oblasti, použijte [předponu veřejné IP adresy](public-ip-address-prefix.md).

7. [Povolí síťový provoz do virtuálního počítače](#allow-network-traffic-to-the-vm) s pravidly zabezpečení ve skupině zabezpečení sítě.

## <a name="azure-cli"></a>Azure CLI

Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. V následujících příkazech rozhraní příkazového řádku vyberte tlačítko **vyzkoušet** . Výběrem možnosti **vyzkoušet** vyvoláte Cloud Shell, pomocí které se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte rozhraní příkazového řádku místně v bash, přihlaste se k Azure pomocí `az login` .
2. Veřejná IP adresa je přidružená ke konfiguraci protokolu IP síťového rozhraní připojeného k virtuálnímu počítači. Pomocí příkazu [AZ Network nic-IP-config Update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) PŘIDRUŽTE veřejnou IP adresu ke konfiguraci protokolu IP. Následující příklad přidruží existující veřejnou IP adresu s názvem *myVMPublicIP* k konfiguraci protokolu IP s názvem *ipconfigmyVM* stávajícího síťového rozhraní s názvem *myVMVMNic* , které existuje ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Pokud nemáte existující veřejnou IP adresu, vytvořte ji pomocí příkazu [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) . Například následující příkaz vytvoří veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Předchozí příkaz vytvoří veřejnou IP adresu s výchozími hodnotami pro několik nastavení, která budete možná chtít přizpůsobit. Další informace o všech nastaveních veřejné IP adresy najdete v tématu [Vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Adresa je přiřazená z fondu veřejných IP adres, které se používají pro každou oblast Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v tématu [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653).

   - Pokud neznáte název síťového rozhraní připojeného k vašemu VIRTUÁLNÍmu počítači, zobrazte ho pomocí příkazu [AZ VM nic list](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) . Například následující příkaz vypíše názvy síťových rozhraní připojených k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     V předchozím příkladu je *myVMVMNic* název síťového rozhraní.

   - Pokud název konfigurace protokolu IP pro síťové rozhraní neznáte, načtěte ho pomocí příkazu [AZ Network nic IP-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) . Například následující příkaz vypíše názvy konfigurací IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Pomocí příkazu [AZ VM list-IP-](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) addresss ZOBRAZTE veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP. Následující příklad zobrazuje IP adresy přiřazené existujícímu virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adresa je přiřazena z fondu adres, které se používají v každé oblasti Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v tématu [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Přiřazená adresa může být libovolná adresa ve fondech používaných pro danou oblast. Pokud potřebujete, aby se adresa přiřadila z konkrétního fondu v oblasti, použijte [předponu veřejné IP adresy](public-ip-address-prefix.md).

4. [Povolí síťový provoz do virtuálního počítače](#allow-network-traffic-to-the-vm) s pravidly zabezpečení ve skupině zabezpečení sítě.

## <a name="powershell"></a>PowerShell

Nainstalujte [PowerShell](/powershell/azure/install-az-ps)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí, které můžete spustit přímo z portálu Azure Portal. Má předinstalované prostředí PowerShell a je nakonfigurováno pro použití s vaším účtem. V následujících příkazech PowerShellu vyberte tlačítko **vyzkoušet** . Výběrem možnosti **vyzkoušet** vyvoláte Cloud Shell, pomocí které se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte PowerShell místně, přihlaste se k Azure pomocí `Connect-AzAccount` .
2. Veřejná IP adresa je přidružená ke konfiguraci protokolu IP síťového rozhraní připojeného k virtuálnímu počítači. K získání virtuální sítě a podsítě, ve které je síťové rozhraní, použijte příkazy [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) . Pak použijte příkaz [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) k získání síťového rozhraní a příkazu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) k získání existující veřejné IP adresy. Pak pomocí příkazu [set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) PŘIDRUŽTE veřejnou IP adresu ke konfiguraci protokolu IP a příkazu [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) pro zápis nové konfigurace IP adresy do síťového rozhraní.

   Následující příklad přidruží existující veřejnou IP adresu s názvem *myVMPublicIP* k konfiguraci protokolu IP s názvem *ipconfigmyVM* stávajícího síťového rozhraní s názvem *myVMVMNic* , které existuje v podsíti s názvem *myVMSubnet* ve virtuální síti s názvem *myVMVNet*. Všechny prostředky jsou ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Pokud nemáte existující veřejnou IP adresu, vytvořte ji pomocí příkazu [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) . Například následující příkaz vytvoří *dynamickou* veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup* v oblasti *eastus* .
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Předchozí příkaz vytvoří veřejnou IP adresu s výchozími hodnotami pro několik nastavení, která budete možná chtít přizpůsobit. Další informace o všech nastaveních veřejné IP adresy najdete v tématu [Vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Adresa je přiřazená z fondu veřejných IP adres, které se používají pro každou oblast Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v tématu [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653).

   - Pokud neznáte název síťového rozhraní připojeného k vašemu VIRTUÁLNÍmu počítači, zobrazte ho pomocí příkazu [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) . Například následující příkaz vypíše názvy síťových rozhraní připojených k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu. V příkladu výstupu je *myVMVMNic* název síťového rozhraní.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Pokud neznáte název virtuální sítě nebo podsítě, ve které je síťové rozhraní, použijte `Get-AzNetworkInterface` příkaz pro zobrazení informací. Například následující příkaz načte virtuální síť a informace o podsíti pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu. V příkladu výstupu je *myVMVNET* názvem virtuální sítě a *myVMSubnet* je název podsítě.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Pokud název konfigurace protokolu IP pro síťové rozhraní neznáte, načtěte ho pomocí příkazu [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) . Například následující příkaz vypíše názvy konfigurací IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu. V příkladu výstupu je *ipconfigmyVM* název konfigurace protokolu IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Pomocí příkazu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) ZOBRAZTE veřejnou IP adresu přiřazenou ke konfiguraci protokolu IP. Následující příklad ukazuje adresu přiřazenou veřejné IP adrese s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Pokud neznáte název veřejné IP adresy přiřazené konfiguraci protokolu IP, spusťte následující příkazy, které vám pomohou:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu. V příkladu výstupu je *myVMPublicIP* název veřejné IP adresy přiřazené ke konfiguraci protokolu IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adresa je přiřazena z fondu adres, které se používají v každé oblasti Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v tématu [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653). Přiřazená adresa může být libovolná adresa ve fondech používaných pro danou oblast. Pokud potřebujete, aby se adresa přiřadila z konkrétního fondu v oblasti, použijte [předponu veřejné IP adresy](public-ip-address-prefix.md).

4. [Povolí síťový provoz do virtuálního počítače](#allow-network-traffic-to-the-vm) s pravidly zabezpečení ve skupině zabezpečení sítě.

## <a name="allow-network-traffic-to-the-vm"></a>Povolení síťového provozu virtuálního počítače

Než se budete moct připojit k veřejné IP adrese z Internetu, ujistěte se, že máte potřebné porty otevřené v libovolné skupině zabezpečení sítě, kterou jste mohli přidružit k síťovému rozhraní, podsíti, ve které je síťové rozhraní, nebo obojí. I když skupiny zabezpečení filtrují provoz na privátní IP adresu síťového rozhraní, po přijetí příchozího internetového provozu na veřejné IP adrese Azure přeloží veřejnou adresu na soukromou IP adresu, takže pokud skupina zabezpečení sítě zabrání toku provozu, komunikace s veřejnou IP adresou se nezdařila. Platná pravidla zabezpečení pro síťové rozhraní a jeho podsíť můžete zobrazit pomocí [portálu](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), rozhraní příkazového [řádku](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)nebo [PowerShellu](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Další kroky

Povolí příchozí internetový provoz do virtuálního počítače pomocí skupiny zabezpečení sítě. Informace o tom, jak vytvořit skupinu zabezpečení sítě, najdete v tématu [práce se skupinami zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups). Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení](security-overview.md).
