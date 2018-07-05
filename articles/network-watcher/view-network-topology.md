---
title: Zobrazení topologie virtuální sítě Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: 1725a3d6a4eb82ca57078f648efa14866d2fe390
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2018
ms.locfileid: "35903195"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Zobrazení topologie služby Azure virtual network

V tomto článku se dozvíte, jak k zobrazení prostředků ve virtuální síti Microsoft Azure a vztahy mezi prostředky. Například virtuální síť obsahuje podsítě. Podsítě obsahují prostředky, jako jsou Azure Virtual Machines (VM). Virtuální počítače mají jednu nebo víc síťových rozhraní. Každá podsíť může mít skupina zabezpečení sítě a směrovací tabulky přidružené k němu. Schopnosti topologie produktu Azure Network Watcher umožňuje zobrazit všechny prostředky ve virtuální síti prostředky přidružené k prostředkům ve virtuální síti a vztahy mezi prostředky.

Můžete použít [webu Azure portal](#azure-portal), [rozhraní příkazového řádku Azure](#azure-cli), nebo [Powershellu](#powershell) Chcete-li zobrazit topologii.

## <a name = "azure-portal"></a>Zobrazení topologie – Azure portal

1. Přihlaste se [webu Azure portal](https://portal.azure.com) pomocí účtu, který obsahuje nezbytné [oprávnění](required-rbac-permissions.md).
2. V horní části levém horním rohu portálu, vyberte **všechny služby**.
3. V **všechny služby** filtrování pole, zadejte *Network Watcher*. Jakmile se služba **Network Watcher** zobrazí ve výsledcích, vyberte ji.
4. Vyberte **topologie**. Sledovací proces sítě ve stejné oblasti, v existující virtuální síť, kterou chcete vygenerovat topologie pro generování topologii vyžaduje. Pokud nemáte k dispozici sledovací proces sítě povolené v oblasti, kde se virtuální síť, kterou chcete vygenerovat topologie pro v, jsou sledovací procesy sítě automaticky vytvoří za vás ve všech oblastech. Sledovací procesy sítě se vytvoří ve skupině prostředků s názvem **NetworkWatcherRG**.
5. Vyberte předplatné, skupinu prostředků, virtuální sítě, které chcete zobrazit topologii, a potom vyberte virtuální síť. Na následujícím obrázku je pro virtuální síť s názvem zobrazen topologii *MyVnet*, ve skupině prostředků s názvem *MyResourceGroup*:

    ![Zobrazit topologii](./media/view-network-topology/view-topology.png)

    Jak je vidět na předchozím obrázku, virtuální síť obsahuje tři podsítě. Jednu podsíť má virtuální počítač v ní nasazená. Virtuální počítač má jedno síťové rozhraní připojené k němu a k němu přidružená veřejná IP adresa. Těmito dvěma podsítěmi mít směrovací tabulky přidružené k nim. Každý směrovací tabulka obsahuje dvě trasy. Skupinu zabezpečení sítě přidruženou k němu má jednu podsíť. Informace o topologii se zobrazuje pouze pro prostředky, které jsou:
    
    - V rámci stejné skupiny prostředků a oblasti jako *myVnet* virtuální sítě. Například skupinu zabezpečení sítě, který existuje ve skupině prostředků než *MyResourceGroup*, není zobrazený, i když je skupina zabezpečení sítě přidružená k podsíti v *MyVnet* virtuální sítě .
    - V průběhu nebo přidružit k prostředkům v rámci, *myVnet* virtuální sítě. Například skupinu zabezpečení sítě, která není přidružená k podsíti nebo síťovému rozhraní ve *myVnet* virtuální sítě není zobrazený, i v případě, že se skupina zabezpečení sítě *MyResourceGroup* Skupina prostředků.

  Topologie na obrázku je pro virtuální síť vytvořili po nasazení **směrování provozu přes síťové virtuální zařízení skript ukázku**, které můžete nasadit pomocí [rozhraní příkazového řádku Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), nebo [Powershellu](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Vyberte **stáhnout typologii** stáhnout jako soubor upravovat ve formátu svg.

Prostředky uvedené v diagramu jsou podmnožinou síťové součásti ve virtuální síti. Například když se zobrazí skupiny zabezpečení sítě, pravidla zabezpečení v něm nejsou zobrazeny v diagramu. I když není rozlišené v diagramu, řádky představovat jednu z dvou vztahů: *členství ve skupině* nebo *přidružené*. Pokud chcete zobrazit úplný seznam prostředků v rámci virtuální sítě a druh vztahu mezi prostředky, generovat topologie s [PowerShell](#powershell) nebo [rozhraní příkazového řádku Azure](#azure-cli).

## <a name = "azure-cli"></a>Zobrazení topologie – rozhraní příkazového řádku Azure

Spusťte příkazy v následujících kroků:
- Ve službě Azure Cloud Shell, tak, že vyberete **vyzkoušet** nahoře napravo od libovolného příkazu. Azure Cloud Shell je bezplatné interaktivní prostředí, který má běžné nástroje Azure, včetně a nakonfigurovány pro použití s vaším účtem.
- Pomocí rozhraní příkazového řádku z vašeho počítače. Při spuštění rozhraní příkazového řádku z počítače, kroky v tomto článku vyžadují použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Vámi používaný účet musí mít potřebná [oprávnění](required-rbac-permissions.md).

1. Pokud už máte sledovací proces sítě ve stejné oblasti jako virtuální síť, kterou chcete vytvořit topologie pro, přejděte ke kroku 3. Vytvořte skupinu prostředků obsahuje sledovací proces sítě s [vytvořit skupiny az](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *eastus* oblasti:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Vytvořit network watcher se [konfigurace network watcheru az](/cli/azure/network/watcher#az-network-watcher-configure). Následující příklad vytvoří network watcher ve službě *eastus* oblasti:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Zobrazení topologie s [az network watcher show topologie](/cli/azure/network/watcher#az-network-watcher-show-topology). Následující příklad zobrazí topologii pro skupinu prostředků s názvem *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informace o topologii dochází pouze za prostředky, které jsou ve stejné skupině prostředků jako *MyResourceGroup* skupinu prostředků a stejné oblasti jako sledovací proces sítě. Například skupinu zabezpečení sítě, který existuje ve skupině prostředků než *MyResourceGroup*, není zobrazený, i když je skupina zabezpečení sítě přidružená k podsíti v *MyVnet* virtuální sítě .

  Další informace o [vztahy](#relationhips) a [vlastnosti](#properties) ve vrácené výstupu. Pokud nemáte k dispozici k zobrazení topologie pro existující virtuální síť, můžete vytvořit jednu [směrování provozu přes síťové virtuální zařízení](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ukázkový skript. Chcete-li zobrazit diagram topologie a stáhněte ho v souboru upravovat, použijte [portál](#azure-portal).

## <a name = "powershell"></a>Zobrazení topologie – PowerShell

Spusťte příkazy v následujících kroků:
- Ve službě Azure Cloud Shell, tak, že vyberete **vyzkoušet** nahoře napravo od libovolného příkazu. Azure Cloud Shell je bezplatné interaktivní prostředí, který má běžné nástroje Azure, včetně a nakonfigurovány pro použití s vaším účtem.
- Spuštění Powershellu z vašeho počítače. Při spuštění PowerShell z počítače, kroky v tomto článku vyžadují verzi 5.7.0 nebo novějším modulu AzureRm. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

Vámi používaný účet musí mít potřebná [oprávnění](required-rbac-permissions.md).

1. Pokud už máte sledovací proces sítě ve stejné oblasti jako virtuální síť, kterou chcete vytvořit topologie pro, přejděte ke kroku 3. Vytvořte skupinu prostředků obsahuje sledovací proces sítě s [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). Následující příklad vytvoří skupinu prostředků *eastus* oblasti:

    ```azurepowershell-interactive
    New-AzureRmResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Vytvořit network watcher se [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher). Následující příklad vytvoří v oblasti eastus network watcher:

    ```azurepowershell-interactive
    New-AzureRmNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Načtení instance Network Watcheru s [Get-AzureRmNetworkWatcher](/powershell/module/azurerm.network/get-azurermnetworkwatcher). Následující příklad načte network watcheru v oblasti USA – východ:

    ```azurepowershell-interactive
    $nw = Get-AzurermResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzureRmNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Načíst topologie s [Get-AzureRmNetworkWatcherTopology](/powershell/module/azurerm.network/get-azurermnetworkwatchertopology). Následující příklad načte topologie pro virtuální síť ve skupině prostředků s názvem *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzureRmNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informace o topologii dochází pouze za prostředky, které jsou ve stejné skupině prostředků jako *MyResourceGroup* skupinu prostředků a stejné oblasti jako sledovací proces sítě. Například skupinu zabezpečení sítě, který existuje ve skupině prostředků než *MyResourceGroup*, není zobrazený, i když je skupina zabezpečení sítě přidružená k podsíti v *MyVnet* virtuální sítě .

  Další informace o [vztahy](#relationhips) a [vlastnosti](#properties) ve vrácené výstupu. Pokud nemáte k dispozici k zobrazení topologie pro existující virtuální síť, můžete vytvořit jednu [směrování provozu přes síťové virtuální zařízení](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ukázkový skript. Chcete-li zobrazit diagram topologie a stáhněte ho v souboru upravovat, použijte [portál](#azure-portal).

## <a name="relationships"></a>Relace

Všechny prostředky, které jsou vráceny v topologii mít jednu z následujících typů vztahu do jiného prostředku:

| Typ vztahu | Příklad:                                                                                                |
| ---               | ---                                                                                                    |
| Členství ve skupině       | Virtuální síť obsahuje podsítě. Podsíť obsahuje síťové rozhraní.                            |
| Související        | Síťové rozhraní je přidružený virtuální počítač. Veřejná IP adresa je přidružená k síťovému rozhraní. |

## <a name="properties"></a>Vlastnosti

Všechny prostředky, které jsou vráceny v topologii mají následující vlastnosti:

- **Název**: název prostředku
- **ID**: identifikátor URI prostředku.
- **Umístění**: oblast Azure, zda prostředek existuje v.
- **Přidružení**: seznam přidružení k odkazovaného objektu. Každé přidružení má následující vlastnosti:
    - **AssociationType**: odkazuje na vztah mezi objektem podřízené a nadřazené. Platné hodnoty jsou *obsahuje* nebo *přidružené*.
    - **Název**: název odkazovaného prostředku.
    - **ResourceId**:-identifikátor URI prostředku, který odkazuje přidružení.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [diagnostikovat sítě filtrováním provozu do nebo z virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md) pomocí toku protokolu IP Network Watcheru ověřte funkce
- Zjistěte, jak [diagnostikovat problém směrování provozu sítě z virtuálního počítače](diagnose-vm-network-routing-problem.md) funkce dalšího segmentu směrování ve službě Network Watcher
