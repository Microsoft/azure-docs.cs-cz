---
title: Přidružení veřejné IP adresy k virtuálnímu počítači
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak přidružit veřejnou IP adresu k virtuálnímu počítači.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: kumud
ms.openlocfilehash: 5acda69ce08bc493d5349b084d1cfafc8432145b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647454"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Přidružení veřejné IP adresy k virtuálnímu počítači

V tomto článku se dozvíte, jak přidružit veřejnou IP adresu k existujícímu virtuálnímu počítači (VM). Pokud se chcete připojit k virtuálnímu virtuálnímu počítačům z Internetu, musí mít k němu přidruženou veřejnou IP adresu. Pokud chcete vytvořit nový virtuální počítač s veřejnou IP adresou, můžete tak učinit pomocí [portálu Azure](virtual-network-deploy-static-pip-arm-portal.md), [rozhraní příkazového řádku Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md)nebo [PowerShellu](virtual-network-deploy-static-pip-arm-ps.md). Veřejné IP adresy mají nominální poplatek. Podrobnosti naleznete v [tématu ceny](https://azure.microsoft.com/pricing/details/ip-addresses/). Počet veřejných IP adres, které můžete použít na jedno předplatné, je omezen. Podrobnosti naleznete v tématu [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

K přidružení veřejné IP adresy k virtuálnímu počítači můžete použít [portál Azure](#azure-portal), [rozhraní příkazového řádku](#azure-cli) Azure (CLI) nebo [PowerShell.](#powershell)

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyhledejte nebo vyhledejte virtuální počítač, do kterého chcete přidat veřejnou IP adresu, a vyberte ji.
3. V části **Nastavení**vyberte **Síť**a vyberte síťové rozhraní, do kterého chcete přidat veřejnou IP adresu, jak je znázorněno na následujícím obrázku:

   ![Vybrat síťové rozhraní](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Veřejné IP adresy jsou přidružené k síťovým rozhraním připojeným k virtuálnímu počítačům. V předchozím obrázku má virtuální modul jenom jedno síťové rozhraní. Pokud by virtuální počítač měl více síťových rozhraní, všechny by se zobrazily a vy byste vybrali síťové rozhraní, ke kterým chcete přidružit veřejnou IP adresu.

4. Vyberte **konfigurace IP** a pak vyberte konfiguraci IP, jak je znázorněno na následujícím obrázku:

   ![Vybrat konfiguraci IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Veřejné IP adresy jsou přidruženy ke konfiguracím IP pro síťové rozhraní. Na předchozím obrázku má síťové rozhraní jednu konfiguraci IP adresy. Pokud by síťové rozhraní mělo více konfigurací IP adres, všechny by se zobrazily v seznamu a vy byste vybrali konfiguraci IP adresy, ke které chcete přidružit veřejnou IP adresu.

5. Vyberte **Možnost Povoleno**, pak vyberte **adresu IP ( Konfigurovat*požadovaná nastavení*).** Zvolte existující veřejnou IP adresu, která automaticky zavře pole **Zvolit veřejnou IP adresu.** Pokud nemáte v seznamu žádné dostupné veřejné IP adresy, musíte je vytvořit. Informace o tom, jak [najdete v tématu Vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Vyberte **Uložit**, jak je znázorněno na následujícím obrázku, a zavřete pole konfigurace IP.

   ![Povolit veřejnou IP adresu](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Veřejné IP adresy, které se zobrazí, jsou ty, které existují ve stejné oblasti jako virtuální hod. Pokud máte v oblasti vytvořeno více veřejných IP adres, zobrazí se zde všechny. Pokud jsou některé z nich šedě, je to proto, že adresa je již přidružena k jinému prostředku.

6. Prohlédněte si veřejnou IP adresu přiřazenou konfiguraci IP adresy, jak je znázorněno na následujícím obrázku. Může trvat několik sekund, než se zobrazí adresa IP.

   ![Zobrazit přiřazenou veřejnou IP adresu](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adresa se přiřazuje z fondu adres používaných v každé oblasti Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v [tématu Rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Přiřazená adresa může být libovolná adresa ve fondech používaných pro oblast. Pokud potřebujete adresu, která má být přiřazena z určitého fondu v oblasti, použijte [předponu veřejné IP adresy](public-ip-address-prefix.md).

7. [Povolte síťový provoz na virtuální ms](#allow-network-traffic-to-the-vm) s pravidly zabezpečení ve skupině zabezpečení sítě.

## <a name="azure-cli"></a>Azure CLI

Nainstalujte [azure cli](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. V následujících příkazech příkazu příkazu příkazu příkazu příkazu vyberte tlačítko **Try it.** Výběrem **try vyvoláte** Cloud shell, pomocí kterého se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte příkazového konto místně v `az login`Bash, přihlaste se do Azure s .
2. Veřejná IP adresa je přidružena ke konfiguraci IP síťového rozhraní připojeného k virtuálnímu počítače. Pomocí příkazu [aktualizace az network nic-ip-config](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) přidružte veřejnou IP adresu ke konfiguraci PROTOKOLU IP. Následující příklad přidruží existující veřejnou IP adresu s názvem *myVMPublicIP* ke konfiguraci IP s názvem *ipconfigmyVM* existujícího síťového rozhraní s názvem *myVMVMNic,* které existuje ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Pokud nemáte existující veřejnou IP adresu, vytvořte ji pomocí příkazu [az network public-ip create.](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) Následující příkaz například vytvoří veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Předchozí příkaz vytvoří veřejnou adresu IP s výchozími hodnotami pro několik nastavení, která můžete přizpůsobit. Další informace o všech nastaveních veřejných IP adres najdete [v tématu Vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Adresa se přiřazuje z fondu veřejných IP adres používaných pro každou oblast Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v [tématu Rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Pokud neznáte název síťového rozhraní připojeného k virtuálnímu počítači, zobrazte je pomocí příkazu [az vm nic.](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) Například následující příkaz uvádí názvy síťových rozhraní připojených k virtuálnímu počítačům s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné následujícímu příkladu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     V předchozím příkladu *je myVMVMNic* název síťového rozhraní.

   - Pokud neznáte název konfigurace IP pro síťové rozhraní, načtěte je pomocí příkazu [az network nic ip-config.](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) Následující příkaz například uvádí názvy konfigurací PROTOKOLU IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Zobrazení veřejné IP adresy přiřazené konfiguraci IP adresy pomocí [příkazu az vm list-ip-addresses.](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) Následující příklad ukazuje IP adresy přiřazené k existujícímu virtuálnímu počítačům s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adresa se přiřazuje z fondu adres používaných v každé oblasti Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v [tématu Rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Přiřazená adresa může být libovolná adresa ve fondech používaných pro oblast. Pokud potřebujete adresu, která má být přiřazena z určitého fondu v oblasti, použijte [předponu veřejné IP adresy](public-ip-address-prefix.md).

4. [Povolte síťový provoz na virtuální ms](#allow-network-traffic-to-the-vm) s pravidly zabezpečení ve skupině zabezpečení sítě.

## <a name="powershell"></a>PowerShell

Nainstalujte [PowerShell](/powershell/azure/install-az-ps)nebo použijte Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí, které můžete spustit přímo z portálu Azure Portal. Má PowerShell předinstalovaný a nakonfigurovaný pro použití s vaším účtem. V následujících příkazech PowerShellu vyberte tlačítko **Try it.** Výběrem **try vyvoláte** Cloud shell, pomocí kterého se můžete přihlásit ke svému účtu Azure.

1. Pokud používáte PowerShell místně, přihlaste se k Azure s `Connect-AzAccount`.
2. Veřejná IP adresa je přidružena ke konfiguraci IP síťového rozhraní připojeného k virtuálnímu počítače. Pomocí příkazů [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) získáte virtuální síť a podsíť, ve kterých se síťové rozhraní nachází. Dále použijte příkaz [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) k získání síťového rozhraní a příkazu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) k získání existující veřejné IP adresy. Potom pomocí příkazu [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) přidružte veřejnou adresu IP ke konfiguraci protokolu IP a příkaz [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) k zápisu nové konfigurace protokolu IP do síťového rozhraní.

   Následující příklad přidruží existující veřejnou IP adresu s názvem *myVMPublicIP* ke konfiguraci IP s názvem *ipconfigmyVM* existujícího síťového rozhraní s názvem *myVMVMNic,* které existuje v podsíti s názvem *myVMSubnet* ve virtuální síti s názvem *myVMVNet*. Všechny prostředky jsou ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Pokud nemáte existující veřejnou IP adresu, vytvořte ji příkazem [New-AzPublicIpAddress.](/powershell/module/Az.Network/New-AzPublicIpAddress) Například následující příkaz vytvoří *dynamickou* veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup* v oblasti *eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Předchozí příkaz vytvoří veřejnou adresu IP s výchozími hodnotami pro několik nastavení, která můžete přizpůsobit. Další informace o všech nastaveních veřejných IP adres najdete [v tématu Vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Adresa se přiřazuje z fondu veřejných IP adres používaných pro každou oblast Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v [tématu Rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

   - Pokud neznáte název síťového rozhraní připojeného k virtuálnímu počítači, můžete je zobrazit pomocí příkazu [Get-AzVM.](/powershell/module/Az.Compute/Get-AzVM) Například následující příkaz uvádí názvy síťových rozhraní připojených k virtuálnímu počítačům s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné příkladu, který následuje. V ukázkovém výstupu je *myVMVMNic* název síťového rozhraní.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Pokud neznáte název virtuální sítě nebo podsítě, ve které se síťové `Get-AzNetworkInterface` rozhraní nachází, zobrazte informace pomocí příkazu. Například následující příkaz získá informace o virtuální síti a podsíti pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné příkladu, který následuje. V ukázkovém výstupu je *myVMVNET* název virtuální sítě a *myVMSubnet* je název podsítě.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Pokud neznáte název konfigurace IP pro síťové rozhraní, načtěte je pomocí příkazu [Get-AzNetworkInterface.](/powershell/module/Az.Network/Get-AzNetworkInterface) Následující příkaz například uvádí názvy konfigurací PROTOKOLU IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné příkladu, který následuje. V ukázkovém výstupu je *ipconfigmyVM* název konfigurace IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Pomocí příkazu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) můžete zobrazit veřejnou IP adresu přiřazenou konfiguraci PROTOKOLU IP. Následující příklad ukazuje adresu přiřazenou veřejné IP adrese s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Pokud neznáte název veřejné IP adresy přiřazené konfiguraci IP adresy, spusťte následující příkazy, abyste ji získali:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Výstup obsahuje jeden nebo více řádků, které jsou podobné příkladu, který následuje. V ukázkovém výstupu je *myVMPublicIP* název veřejné IP adresy přiřazené konfiguraci IP adresy.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adresa se přiřazuje z fondu adres používaných v každé oblasti Azure. Seznam fondů adres používaných v jednotlivých oblastech najdete v [tématu Rozsahy IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Přiřazená adresa může být libovolná adresa ve fondech používaných pro oblast. Pokud potřebujete adresu, která má být přiřazena z určitého fondu v oblasti, použijte [předponu veřejné IP adresy](public-ip-address-prefix.md).

4. [Povolte síťový provoz na virtuální ms](#allow-network-traffic-to-the-vm) s pravidly zabezpečení ve skupině zabezpečení sítě.

## <a name="allow-network-traffic-to-the-vm"></a>Povolení síťového provozu na virtuálním mísu

Před připojením k veřejné ip adrese z Internetu se ujistěte, že máte potřebné porty otevřené v libovolné skupině zabezpečení sítě, kterou jste mohli přidružit k síťovému rozhraní, podsíti, ve které se síťové rozhraní nachází, nebo v obou případech. Přestože skupiny zabezpečení filtrují provoz na privátní IP adresu síťového rozhraní, jakmile příchozí internetový provoz dorazí na veřejnou IP adresu, Azure převede veřejnou adresu na privátní IP adresu, takže pokud skupina zabezpečení sítě brání dopravního toku, komunikace s veřejnou IP adresou se nezdaří. Pomocí [portálu](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [rozhraní SE konstatování](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)nebo [prostředí PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)můžete zobrazit účinná pravidla zabezpečení pro síťové rozhraní a jeho podsíť .

## <a name="next-steps"></a>Další kroky

Povolit příchozí internetový provoz do virtuálního počítače se skupinou zabezpečení sítě. Informace o vytvoření skupiny zabezpečení sítě naleznete v tématu [Práce se skupinami zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups). Další informace o skupinách zabezpečení sítě naleznete v [tématu Skupiny zabezpečení](security-overview.md).
