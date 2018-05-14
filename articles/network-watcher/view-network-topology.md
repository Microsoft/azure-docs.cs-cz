---
title: Zobrazení topologie virtuální síť Azure | Microsoft Docs
description: Zjistěte, jak zobrazit prostředky ve virtuální síti a vztahy mezi prostředky.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: jdial
ms.openlocfilehash: 6ef165ddc481bf84c6189635e36b97eb9518261e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Zobrazení topologii virtuální sítě Azure

V tomto článku zjistěte, jak zobrazit prostředky v Microsoft Azure virtual network a vztahy mezi prostředky. Například virtuální síť obsahuje podsítě. Podsítě obsahují prostředky, jako je například virtuální počítače Azure (VM). Virtuální počítače mít jeden nebo více síťových rozhraní. Každá podsíť může mít skupina zabezpečení sítě a přidružené k jeho směrovací tabulku. Topologie funkce sledovací proces sítě Azure můžete zobrazit všechny prostředky ve virtuální síti, prostředky přidružené k prostředkům ve virtuální síti a vztahy mezi prostředky.

Můžete použít [portál Azure](#azure-portal), [rozhraní příkazového řádku Azure](#azure-cli), nebo [prostředí PowerShell](#powershell) zobrazíte topologii.

## <a name = "azure-portal"></a>Zobrazení topologie – portál Azure

1. Přihlaste se [portál Azure](https://portal.azure.com) pomocí účtu, který má nezbytné [oprávnění](required-rbac-permissions.md).
2. V horní části levého horního rohu portálu, vyberte **všechny služby**.
3. V **všechny služby** filtrovat pole, zadejte *sledovací proces sítě*. Když **sledovací proces sítě** se zobrazí ve výsledcích, vyberte ho.
4. Vyberte **topologie**. Sledovací proces sítě ve stejné oblasti, která existuje virtuální síť, kterou chcete vygenerovat topologie pro generování topologii vyžaduje. Pokud nemáte k dispozici sledovací proces sítě v oblasti virtuální síť, kterou chcete vygenerovat topologie pro se povolené, jsou sledovací procesy sítě automaticky vytvoří pro vás ve všech oblastech. Sledovací procesy sítě jsou vytvořeny ve skupině prostředků s názvem **NetworkWatcherRG**.
5. Vyberte předplatné, skupinu prostředků virtuální sítě, které chcete zobrazit topologii, a potom vyberte virtuální síť. Na následujícím obrázku je zobrazený topologii pro virtuální síť s názvem *MyVnet*, ve skupině prostředků s názvem *MyResourceGroup*:

    ![Zobrazit topologii](./media/view-network-topology/view-topology.png)

    Jak je vidět na předchozím obrázku, virtuální síť obsahuje tři podsítě. Jednu podsíť má virtuální počítač v ní nasazená. Virtuální počítač má jedno síťové rozhraní, které jsou k němu připojen a veřejné IP adresy přidružené k němu. Dvě podsítě mít směrovací tabulka přidruženého k nim. Každý směrovací tabulka obsahuje dvě trasy. Jednu podsíť má skupinu zabezpečení sítě spojenou k němu. Informace o topologii je zobrazeno pouze pro prostředky, které jsou: – v rámci stejné skupině prostředků a oblasti jako *myVnet* virtuální sítě. Například skupinu zabezpečení sítě, existuje ve skupině prostředků než *MyResourceGroup*, není zobrazený, i v případě, že skupina zabezpečení sítě je přidružen k podsíti v *MyVnet* virtuální sítě .
        -V nebo přidružit k prostředkům v rámci, *myVnet* virtuální sítě. Například skupinu zabezpečení sítě, který není přidružený k rozhraní podsítě nebo sítě v *myVnet* virtuální sítě není zobrazený, i když je skupina zabezpečení sítě v *MyResourceGroup* Skupina prostředků.

    Topologie znázorněno na obrázku je pro virtuální síť vytvořili po nasazení **směrování provozu prostřednictvím ukázka skriptu virtuální zařízení sítě**, které můžete nasadit pomocí [rozhraní příkazového řádku Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), nebo [Prostředí PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Vyberte **stáhnout topologie** stáhnout jako soubor upravovat formát svg.

Prostředky vidět na obrázku jsou podmnožinou síťovými součástmi ve virtuální síti. Například když se zobrazí skupinu zabezpečení sítě, pravidla zabezpečení v něm nejsou zobrazeny v diagramu. I když není rozlišené v diagramu, řádky představují jeden dva vztahy: *omezení* nebo *přidružené*. Pokud chcete zobrazit úplný seznam prostředky ve virtuální síti a typ vztahu mezi prostředky, generovat topologie s [prostředí PowerShell](#powershell) nebo [rozhraní příkazového řádku Azure](#azure-cli).

## <a name = "azure-cli"></a>Zobrazit topologii – rozhraní příkazového řádku Azure

Příkazy můžete spustit v krocích, které následují:
- V prostředí cloudu Azure, tak, že vyberete **zkuste ho** v horní části napravo od libovolný příkaz. Prostředí cloudové služby Azure je bezplatná interaktivní prostředí, který má běžné nástroje Azure předinstalována a nakonfigurovaná pro použití s vaším účtem.
- Spuštěním rozhraní příkazového řádku z vašeho počítače. Pokud spustíte rozhraní příkazového řádku z vašeho počítače, kroky v tomto článku vyžadují Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

Účet, který používáte, musí mít nezbytné [oprávnění](required-rbac-permissions.md).

1. Pokud je již ve stejné oblasti jako virtuální síť, kterou chcete vytvořit topologie pro sledovací proces sítě, přeskočte ke kroku 3. Vytvořte skupinu prostředků obsahuje sledovací proces sítě s [vytvořit skupinu az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *eastus* oblasti:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Vytvoření sledovací proces sítě s [sledovací proces sítě az konfigurace](/cli/azure/network/watcher#az-network-watcher-configure). Následující příklad vytvoří sledovací proces sítě v *eastus* oblasti:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Zobrazení topologie s [sledovacích procesů zobrazit topologie sítě az](/cli/azure/network/watcher#az-network-watcher-show-topology). Následující příklad zobrazení topologie pro skupinu prostředků s názvem *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informace o topologii je vrácena pouze pro prostředky, které jsou ve stejné skupině prostředků, jako *MyResourceGroup* skupinu prostředků a stejné oblasti jako sledovací proces sítě. Například skupinu zabezpečení sítě, existuje ve skupině prostředků než *MyResourceGroup*, není zobrazený, i v případě, že skupina zabezpečení sítě je přidružen k podsíti v *MyVnet* virtuální sítě .

  Další informace o [vztahy](#relationhips) a [vlastnosti](#properties) ve vrácené výstupu. Pokud nemáte existující virtuální síť k zobrazení topologie pro, můžete vytvořit jeden pomocí [směrovat provoz přes virtuální síťové zařízení](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ukázka skriptu. Chcete-li zobrazení diagramu topologie možnosti jejího stažení naleznete v souboru upravovat, použijte [portál](#azure-portal).

## <a name = "powershell"></a>Zobrazení topologie – prostředí PowerShell

Příkazy můžete spustit v krocích, které následují:
- V prostředí cloudu Azure, tak, že vyberete **zkuste ho** v horní části napravo od libovolný příkaz. Prostředí cloudové služby Azure je bezplatná interaktivní prostředí, který má běžné nástroje Azure předinstalována a nakonfigurovaná pro použití s vaším účtem.
- Spuštěním prostředí PowerShell z vašeho počítače. Pokud spustíte z vašeho počítače prostředí PowerShell, kroky v tomto článku vyžadují verze 5.7.0 nebo novější AzureRm modulu. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

Účet, který používáte, musí mít nezbytné [oprávnění](required-rbac-permissions.md).

1. Pokud je již ve stejné oblasti jako virtuální síť, kterou chcete vytvořit topologie pro sledovací proces sítě, přeskočte ke kroku 3. Vytvořte skupinu prostředků obsahuje sledovací proces sítě s [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků *eastus* oblasti:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Vytvoření sledovací proces sítě s [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). Následující příklad vytvoří sledovací proces sítě v oblasti eastus:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Načtení instance sledovací proces sítě s [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). Následující příklad načte sledovací proces sítě v oblasti Východ USA:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Načtení topologie s [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). Následující příklad načte topologii pro virtuální síť ve skupině prostředků s názvem *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informace o topologii je vrácena pouze pro prostředky, které jsou ve stejné skupině prostředků, jako *MyResourceGroup* skupinu prostředků a stejné oblasti jako sledovací proces sítě. Například skupinu zabezpečení sítě, existuje ve skupině prostředků než *MyResourceGroup*, není zobrazený, i v případě, že skupina zabezpečení sítě je přidružen k podsíti v *MyVnet* virtuální sítě .

  Další informace o [vztahy](#relationhips) a [vlastnosti](#properties) ve vrácené výstupu. Pokud nemáte existující virtuální síť k zobrazení topologie pro, můžete vytvořit jeden pomocí [směrovat provoz přes virtuální síťové zařízení](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ukázka skriptu. Chcete-li zobrazení diagramu topologie možnosti jejího stažení naleznete v souboru upravovat, použijte [portál](#azure-portal).

## <a name="relationships"></a>Relace

Všechny prostředky, vrátí se v topologii mít jednu z následujících typů relace na jiný prostředek:

| Typ vztahu | Příklad:                                                                                                |
| ---               | ---                                                                                                    |
| Členství ve skupině       | Virtuální síť obsahuje podsíť. Podsíť obsahuje síťové rozhraní.                            |
| Související        | Síťové rozhraní je spojeno s virtuálního počítače. Veřejná IP adresa je přidružena k síťovému rozhraní. |

## <a name="properties"></a>Vlastnosti

Všechny prostředky, vrátí se v topologii mít následující vlastnosti:

- **Název**: název prostředku
- **ID**: identifikátoru URI prostředku.
- **Umístění**: oblast Azure, zda prostředek existuje v.
- **Přidružení**: seznam přidružení k odkazovaného objektu. Každé přidružení má následující vlastnosti:
    - **Třída AssociationType**: odkazuje vztah mezi podřízený objekt a nadřazený. Platné hodnoty jsou *obsahuje* nebo *přidružené*.
    - **Název**: název odkazovaného prostředku.
    - **ResourceId**:-identifikátoru URI prostředku, kterou se odkazuje v přidružení.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [diagnostikovat problém filtru síťový provoz do nebo z virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md) pomocí toku IP sledovací proces sítě ověřte funkce
- Zjistěte, jak [diagnostikovat problém směrování provozu sítě z virtuálního počítače](diagnose-vm-network-routing-problem.md) pomocí funkce další směrování sledovací proces sítě