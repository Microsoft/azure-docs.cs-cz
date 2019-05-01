---
title: Přidružit veřejnou IP adresu virtuálnímu počítači
titlesuffix: Azure Virtual Network
description: Zjistěte, jak přidružit veřejnou IP adresu virtuálnímu počítači.
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
ms.openlocfilehash: 69460a111e6fd879807b4025d6832b3ac515a9b4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691992"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Přidružit veřejnou IP adresu virtuálnímu počítači

V tomto článku se dozvíte, jak přidružit veřejnou IP adresu do existujícího virtuálního počítače (VM). Pokud se chcete připojit k virtuálnímu počítači z Internetu, musí mít virtuální počítač k němu přidružená veřejná IP adresa. Pokud chcete vytvořit nový virtuální počítač s veřejnou IP adresu, můžete to udělat pomocí [webu Azure portal](virtual-network-deploy-static-pip-arm-portal.md), [rozhraní příkazového řádku Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md), nebo [Powershellu](virtual-network-deploy-static-pip-arm-ps.md). Veřejné IP adresy mají nominální poplatek. Podrobnosti najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/ip-addresses/). Platí omezení na počet veřejné IP adresy, které můžete použít jedno předplatné. Podrobnosti najdete v tématu [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Můžete použít [webu Azure portal](#azure-portal), Azure [rozhraní příkazového řádku](#azure-cli) (CLI), nebo [Powershellu](#powershell) přidružit veřejnou IP adresu pro virtuální počítač.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyhledejte nebo vyhledejte virtuální počítač, který chcete přidat k veřejnou IP adresu a poté jej vyberte.
3. V části **nastavení**vyberte **sítě**a pak vyberte síťové rozhraní, které chcete přidat veřejnou IP adresu, jak je znázorněno na následujícím obrázku:

   ![Vyberte síťové rozhraní](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Veřejné IP adresy jsou přidružené k síťovým rozhraním připojeným k virtuálnímu počítači. Na předchozím obrázku má virtuální počítač pouze jedno síťové rozhraní. Pokud virtuální počítač několik síťových rozhraní, všechny by se zobrazily a vyberete a přidružte veřejnou IP adresu pro síťové rozhraní.

4. Vyberte **konfigurací protokolu IP** a pak vyberte konfiguraci IP, jak je znázorněno na následujícím obrázku:

   ![Vyberte konfigurace IP adresy](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Veřejné IP adresy jsou přidružené k konfigurací protokolu IP pro síťové rozhraní. Na předchozím obrázku síťové rozhraní má jednu konfiguraci protokolu IP. Pokud síťové rozhraní více konfigurací protokolu IP, všechny by se zobrazily v seznamu a vyberete, kterou chcete přidružit veřejnou IP adresu pro konfiguraci protokolu IP.

5. Vyberte **povoleno**a pak vyberte **IP adresa (*konfigurovat požadované nastavení*)**. Zvolit stávající veřejnou IP adresu, která automaticky uzavře **zvolte veřejnou IP adresu** pole. Pokud nemáte žádné dostupné veřejné IP adresy uvedené, musíte ho vytvořit. Další informace o postupu [vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Vyberte **Uložit**, jak je znázorněno na obrázku, který následuje a potom zavřete okno pro konfiguraci protokolu IP.

   ![Povolit veřejné IP adresy](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Veřejné IP adresy, které se zobrazují jsou ty, které existují ve stejné oblasti jako virtuální počítač. Pokud máte více veřejné IP adresy vytvořené v oblasti, se zobrazí tady. Pokud některé jsou zobrazena šedě, bude to, že adresa je již přidružena k jinému prostředku.

6. Zobrazte veřejnou IP adresu, přiřadit ke konfiguraci IP adresy, jak je znázorněno na následujícím obrázku. Může trvat několik sekund, IP adresa se zobrazí.

   ![Zobrazit přiřazené veřejné IP adresy](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adresa se přiřadí z fondu adres použitých v každé oblasti Azure. Seznam fondů adres v jednotlivých oblastech používají, najdete v sekci [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Adresu přiřazenou může být jakákoli adresa ve fondech použít pro danou oblast. Pokud budete potřebovat adresu, kterou chcete přiřadit z konkrétního fondu v oblasti, použijte [předpona veřejné IP adresy](public-ip-address-prefix.md).

7. [Povolit síťový provoz do virtuálního počítače](#allow-network-traffic-to-the-vm) pravidlům zabezpečení ve skupině zabezpečení sítě.

## <a name="azure-cli"></a>Azure CLI

Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json), nebo použít Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Vyberte **vyzkoušet** tlačítko v rozhraní příkazového řádku příkazy, které následují. Výběr **vyzkoušet** Cloud Shell, která dokáže přihlásit ke svému účtu Azure se vyvolá.

1. Pokud používáte rozhraní příkazového řádku místně v prostředí Bash, přihlaste se k Azure s využitím `az login`.
2. Veřejná IP adresa je přidružená ke konfiguraci protokolu IP síťového rozhraní připojené k virtuálnímu počítači. Použití [az network nic ip-config update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) příkazu přidružit veřejnou IP adresu ke konfiguraci IP. Následující příklad přiřadí stávající veřejnou IP adresu s názvem *myVMPublicIP* ke konfiguraci IP adresy s názvem *ipconfigmyVM* existující síťové rozhraní s názvem *myVMVMNic* , který existuje ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Pokud nemáte stávající veřejnou IP adresu, použijte [az network public-ip vytvořit](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) příkaz k jejímu vytvoření. Například následující příkaz vytvoří veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Předchozí příkaz vytvoří veřejnou IP adresu s použitím výchozích hodnot pro několik nastavení, které můžete chtít přizpůsobit. Další informace o všech nastavení veřejné IP adresy najdete v tématu [vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Z fondu veřejných IP adres pro každou oblast Azure je přiřazena adresa. Seznam fondů adres v jednotlivých oblastech používají, najdete v sekci [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653).

   - Pokud si nejste jisti názvem síťové rozhraní připojené k virtuálnímu počítači, použijte [az vm nic seznamu](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) příkazu zobrazte je. Například následující příkaz zobrazí seznam názvů síťovým rozhraním připojeným k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné jako v následujícím příkladu:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     V předchozím příkladu *myVMVMNic* je název síťového rozhraní.

   - Pokud neznáte název konfigurace protokolu IP pro síťové rozhraní, použijte [az network nic ip-config list](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) příkaz, který je načíst. Například následující příkaz vypíše názvy konfigurací protokolu IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Zobrazit přiřazenou ke konfiguraci IP adresy s veřejnou IP adresu [az vm list-ip-addresses](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) příkazu. Následující příklad ukazuje IP adresy přiřazené k existujícímu virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adresa se přiřadí z fondu adres použitých v každé oblasti Azure. Seznam fondů adres v jednotlivých oblastech používají, najdete v sekci [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Adresu přiřazenou může být jakákoli adresa ve fondech použít pro danou oblast. Pokud budete potřebovat adresu, kterou chcete přiřadit z konkrétního fondu v oblasti, použijte [předpona veřejné IP adresy](public-ip-address-prefix.md).

4. [Povolit síťový provoz do virtuálního počítače](#allow-network-traffic-to-the-vm) pravidlům zabezpečení ve skupině zabezpečení sítě.

## <a name="powershell"></a>PowerShell

Nainstalujte [Powershellu](/powershell/azure/install-az-ps), nebo použít Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí, které můžete spustit přímo z portálu Azure Portal. Obsahuje prostředí PowerShell předem nainstalován a nakonfigurován pro použití s vaším účtem. Vyberte **vyzkoušet** tlačítko v Powershellu příkazy, které následují. Výběr **vyzkoušet** Cloud Shell, která dokáže přihlásit ke svému účtu Azure se vyvolá.

1. Pokud používáte PowerShell místně, přihlaste se k Azure s využitím `Connect-AzAccount`.
2. Veřejná IP adresa je přidružená ke konfiguraci protokolu IP síťového rozhraní připojené k virtuálnímu počítači. Použití [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) příkazy k získání virtuální síť a podsíť, která se síťové rozhraní. Pak pomocí [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) příkazu získejte síťové rozhraní a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) příkazu získejte stávající veřejnou IP adresu. Pak pomocí [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) příkazu přidružit veřejnou IP adresu ke konfiguraci IP adresy a [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) příkaz pro zápis nové konfigurace IP adresy na síťové rozhraní.

   Následující příklad přiřadí stávající veřejnou IP adresu s názvem *myVMPublicIP* ke konfiguraci IP adresy s názvem *ipconfigmyVM* existující síťové rozhraní s názvem *myVMVMNic* , která existuje v podsíti s názvem *myVMSubnet* ve virtuální síti s názvem *myVMVNet*. Všechny prostředky jsou ve skupině prostředků s názvem *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Pokud nemáte stávající veřejnou IP adresu, použijte [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) příkaz k jejímu vytvoření. Například následující příkaz vytvoří *dynamické* veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup* v  *eastus* oblasti.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Předchozí příkaz vytvoří veřejnou IP adresu s použitím výchozích hodnot pro několik nastavení, které můžete chtít přizpůsobit. Další informace o všech nastavení veřejné IP adresy najdete v tématu [vytvoření veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address). Z fondu veřejných IP adres pro každou oblast Azure je přiřazena adresa. Seznam fondů adres v jednotlivých oblastech používají, najdete v sekci [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653).

   - Pokud si nejste jisti názvem síťové rozhraní připojené k virtuálnímu počítači, použijte [rutiny Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) příkazu zobrazte je. Například následující příkaz zobrazí seznam názvů síťovým rozhraním připojeným k virtuálnímu počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné jako v příkladu, který následuje. Ukázkový výstup *myVMVMNic* je název síťového rozhraní.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Pokud neznáte název virtuální sítě nebo podsítě, které se síťové rozhraní nachází, použijte `Get-AzNetworkInterface` příkazu zobrazíte informace. Například následující příkaz načte informace virtuální síť a podsíť pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné jako v příkladu, který následuje. Ukázkový výstup *myVMVNET* je název virtuální sítě a *myVMSubnet* je název podsítě.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Pokud neznáte název konfigurace protokolu IP pro síťové rozhraní, použijte [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) příkaz, který je načíst. Například následující příkaz vypíše názvy konfigurací protokolu IP pro síťové rozhraní s názvem *myVMVMNic* ve skupině prostředků s názvem *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Výstup obsahuje jeden nebo více řádků, které jsou podobné jako v příkladu, který následuje. Ukázkový výstup *ipconfigmyVM* je název konfigurace protokolu IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Zobrazit přiřazenou ke konfiguraci IP adresy s veřejnou IP adresu [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) příkazu. Následující příklad ukazuje adresu přiřazenou veřejnou IP adresu s názvem *myVMPublicIP* ve skupině prostředků s názvem *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Pokud neznáte název veřejné IP adresy přiřazené konfigurace protokolu IP, spusťte následující příkazy se dá stáhnout:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Výstup obsahuje jeden nebo více řádků, které jsou podobné jako v příkladu, který následuje. Ukázkový výstup *myVMPublicIP* je název veřejné IP adresy přiřazené ke konfiguraci IP adresy.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adresa se přiřadí z fondu adres použitých v každé oblasti Azure. Seznam fondů adres v jednotlivých oblastech používají, najdete v sekci [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Adresu přiřazenou může být jakákoli adresa ve fondech použít pro danou oblast. Pokud budete potřebovat adresu, kterou chcete přiřadit z konkrétního fondu v oblasti, použijte [předpona veřejné IP adresy](public-ip-address-prefix.md).

4. [Povolit síťový provoz do virtuálního počítače](#allow-network-traffic-to-the-vm) pravidlům zabezpečení ve skupině zabezpečení sítě.

## <a name="allow-network-traffic-to-the-vm"></a>Povolit síťový provoz do virtuálního počítače

Před připojením k veřejné IP adrese z Internetu, ujistěte se, že máte nezbytné porty otevřete v žádnou skupinu zabezpečení sítě, které vám mohou být přidruženy k síťové rozhraní a podsíti, síťové rozhraní se nachází v. I když převádí zabezpečení skupin filtrovat provoz na privátní IP adresu síťového rozhraní, jakmile dorazí příchozí internetový provoz na veřejnou IP adresu, Azure veřejnou adresu, která privátní IP adresa, takže pokud se skupina zabezpečení sítě brání tok přenosů, se nezdaří komunikace s veřejnou IP adresu. Můžete zobrazit platná pravidla zabezpečení pro síťové rozhraní a jeho použití podsítě [portál](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [rozhraní příkazového řádku](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli), nebo [Powershellu](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Další postup

Povolí příchozí provoz na Internetu k virtuálnímu počítači se skupinou zabezpečení sítě. Zjistěte, jak vytvořit skupinu zabezpečení sítě, najdete v článku [práce se skupinami zabezpečení sítě](manage-network-security-group.md#work-with-network-security-groups). Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení](security-overview.md).
