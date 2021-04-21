---
title: Zobrazit topologii virtuální sítě Azure | Microsoft Docs
description: Naučte se zobrazovat prostředky ve virtuální síti a vztahy mezi prostředky.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: f20fa22dac3fba4d01cbc5e398bafa4113e94a96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780294"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Zobrazení topologie virtuální sítě Azure

V tomto článku se dozvíte, jak zobrazit prostředky ve Microsoft Azure virtuální síti a vztahy mezi prostředky. Například virtuální síť obsahuje podsítě. Podsítě obsahují prostředky, jako je například Azure Virtual Machines (VM). Virtuální počítače mají jedno nebo víc síťových rozhraní. Každá podsíť může mít skupinu zabezpečení sítě a přidruženou směrovací tabulku. Schopnost topologie Azure Network Watcher umožňuje zobrazit všechny prostředky ve virtuální síti, prostředky přidružené k prostředkům ve virtuální síti a vztahy mezi prostředky.

K zobrazení topologie můžete použít [Azure Portal](#azure-portal), [Azure CLI](#azure-cli)nebo [PowerShell](#powershell) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Zobrazit topologii – Azure Portal

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu, který má potřebná [oprávnění](required-rbac-permissions.md).
2. V levém horním rohu portálu vyberte **všechny služby**.
3. Do pole Filtr **všechny služby** zadejte *Network Watcher*. Pokud se ve výsledcích zobrazí **Network Watcher** , vyberte ji.
4. Vyberte **Topologie**. Generování topologie vyžaduje sledovací proces sítě ve stejné oblasti, ve které je virtuální síť, pro kterou chcete vygenerovat topologii. Pokud nemáte zapnutý sledovací proces sítě v oblasti, ve které je virtuální síť, pro kterou chcete vygenerovat topologii, pro vás ve všech oblastech automaticky vytvoří sledovací procesy sítě. Sledovací procesy sítě se vytvoří ve skupině prostředků s názvem **NetworkWatcherRG**.
5. Vyberte předplatné, skupinu prostředků virtuální sítě, pro kterou chcete zobrazit topologii, a pak vyberte virtuální síť. Na následujícím obrázku se zobrazí topologie pro virtuální síť s názvem *MyVnet* ve skupině prostředků s názvem *MyResourceGroup*:

    ![Zobrazit topologii](./media/view-network-topology/view-topology.png)

    Jak vidíte na předchozím obrázku, virtuální síť obsahuje tři podsítě. Jedna podsíť má nasazený virtuální počítač. K virtuálnímu počítači je připojeno jedno síťové rozhraní a přidružená veřejná IP adresa. K ostatním dvěma podsítím je přidružena směrovací tabulka. Každá směrovací tabulka obsahuje dvě trasy. Jedna podsíť má přidruženou skupinu zabezpečení sítě. Informace o topologii se zobrazují jenom pro prostředky, které jsou:
    
    - Ve stejné skupině prostředků a oblasti jako virtuální síť *myVnet* . Například skupina zabezpečení sítě, která existuje ve skupině prostředků jiné než *MyResourceGroup*, se nezobrazí, i když je skupina zabezpečení sítě přidružena k podsíti ve virtuální síti *MyVnet* .
    - V rámci nebo přidružených k prostředkům v rámci služby *myVnet* Virtual Network. Například skupina zabezpečení sítě, která není přidružená k podsíti nebo síťovému rozhraní ve virtuální síti *myVnet* , není zobrazená, i když je skupina zabezpečení sítě ve skupině prostředků *MyResourceGroup* .

   Topologie zobrazená na obrázku je určená pro virtuální síť vytvořenou po nasazení **směrování provozu prostřednictvím ukázkového skriptu síťového virtuálního zařízení**, který můžete nasadit pomocí rozhraní příkazového [řádku Azure](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)nebo [PowerShellu](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Vyberte **Stáhnout topologii** pro stažení obrázku jako upravitelný soubor ve formátu SVG.

Prostředky zobrazené v diagramu jsou podmnožinou síťových součástí ve virtuální síti. Například když je zobrazena skupina zabezpečení sítě, pravidla zabezpečení v ní nejsou v diagramu zobrazena. I když v diagramu nerozlišuje, řádky reprezentují jednu ze dvou vztahů: *omezení* nebo *přidružení*. Pokud chcete zobrazit úplný seznam prostředků ve virtuální síti a typ relace mezi prostředky, vygenerujte topologii pomocí [PowerShellu](#powershell) nebo rozhraní příkazového [řádku Azure CLI](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Zobrazení topologie – Azure CLI

Příkazy můžete spustit v následujících krocích:
- V Azure Cloud Shell výběrem možnosti **vyzkoušet** v pravém horním rohu libovolného příkazu. Azure Cloud Shell je bezplatné interaktivní prostředí, které má předinstalované společné nástroje Azure a které je nakonfigurované pro použití s vaším účtem.
- Spuštěním rozhraní příkazového řádku z počítače. Pokud spouštíte rozhraní příkazového řádku z počítače, kroky v tomto článku vyžadují Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.

Účet, který použijete, musí mít potřebná [oprávnění](required-rbac-permissions.md).

1. Pokud již máte sledovací proces sítě ve stejné oblasti jako virtuální síť, pro kterou chcete vytvořit topologii, přejděte ke kroku 3. Vytvořte skupinu prostředků, která bude obsahovat sledovací proces sítě pomocí [AZ Group Create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků v oblasti *eastus* :

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Vytvořte sledovací proces sítě pomocí [AZ Network Watch Configure](/cli/azure/network/watcher#az_network_watcher_configure). Následující příklad vytvoří sledovací proces sítě v oblasti *eastus* :

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Prohlédněte si topologii pomocí [AZ Network sledovacího procesu show-Topology](/cli/azure/network/watcher#az_network_watcher_show_topology). V následujícím příkladu se zobrazí topologie pro skupinu prostředků s názvem *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informace o topologii se vrátí jenom pro prostředky, které jsou ve stejné skupině prostředků jako skupina prostředků *MyResourceGroup* , a stejnou oblast jako sledovací proces sítě. Například skupina zabezpečení sítě, která existuje ve skupině prostředků jiné než *MyResourceGroup*, se nezobrazí, i když je skupina zabezpečení sítě přidružena k podsíti ve virtuální síti *MyVnet* .

   Přečtěte si další informace o relacích a [vlastnostech](#properties) ve vráceném výstupu. Pokud nemáte existující virtuální síť, pro kterou chcete zobrazit topologii, můžete ji vytvořit pomocí ukázkového skriptu [síťového virtuálního zařízení](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) . Pokud chcete zobrazit diagram topologie a stáhnout ho do upravitelného souboru, použijte [portál](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Zobrazení topologie – PowerShell

Příkazy můžete spustit v následujících krocích:
- V Azure Cloud Shell výběrem možnosti **vyzkoušet** v pravém horním rohu libovolného příkazu. Azure Cloud Shell je bezplatné interaktivní prostředí, které má předinstalované společné nástroje Azure a které je nakonfigurované pro použití s vaším účtem.
- Spuštěním prostředí PowerShell z počítače. Pokud spustíte PowerShell z počítače, Tento článek vyžaduje `Az` modul Azure PowerShell. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Účet, který použijete, musí mít potřebná [oprávnění](required-rbac-permissions.md).

1. Pokud již máte sledovací proces sítě ve stejné oblasti jako virtuální síť, pro kterou chcete vytvořit topologii, přejděte ke kroku 3. Vytvořte skupinu prostředků, která bude obsahovat sledovací proces sítě pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků v oblasti *eastus* :

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Vytvořte sledovací proces sítě pomocí [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). Následující příklad vytvoří sledovací proces sítě v oblasti eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Načtěte instanci Network Watcher pomocí [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). Následující příklad načte sledovací proces sítě v Východní USA oblasti:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Načtěte topologii pomocí [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). V následujícím příkladu se načte topologie virtuální sítě ve skupině prostředků s názvem *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informace o topologii se vrátí jenom pro prostředky, které jsou ve stejné skupině prostředků jako skupina prostředků *MyResourceGroup* , a stejnou oblast jako sledovací proces sítě. Například skupina zabezpečení sítě, která existuje ve skupině prostředků jiné než *MyResourceGroup*, se nezobrazí, i když je skupina zabezpečení sítě přidružena k podsíti ve virtuální síti *MyVnet* .

   Přečtěte si další informace o relacích a [vlastnostech](#properties) ve vráceném výstupu. Pokud nemáte existující virtuální síť, pro kterou chcete zobrazit topologii, můžete ji vytvořit pomocí ukázkového skriptu [síťového virtuálního zařízení](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) . Pokud chcete zobrazit diagram topologie a stáhnout ho do upravitelného souboru, použijte [portál](#azure-portal).

## <a name="relationships"></a>Relace

Všechny prostředky vracené v topologii mají jeden z následujících typů vztahů k jinému prostředku:

| Typ vztahu | Příklad                                                                                                |
| ---               | ---                                                                                                    |
| Členství ve skupině       | Virtuální síť obsahuje podsíť. Podsíť obsahuje síťové rozhraní.                            |
| Přidružené        | K virtuálnímu počítači je přidruženo síťové rozhraní. Veřejná IP adresa je přidružená k síťovému rozhraní. |

## <a name="properties"></a>Vlastnosti

Všechny prostředky vracené v topologii mají následující vlastnosti:

- **Název**: název prostředku.
- **ID**: identifikátor URI prostředku.
- **Umístění**: oblast Azure, ve které prostředek existuje.
- **Associations**: seznam přidružení k odkazovanému objektu. Každé přidružení má následující vlastnosti:
    - **AssociationType**: odkazuje na vztah mezi podřízeným objektem a nadřazeným objektem. Platné hodnoty jsou *obsaženy* nebo *přidruženy*.
    - **Name (název**): název odkazovaného prostředku.
    - **ResourceID**: – identifikátor URI prostředku, na který se odkazuje v přidružení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [diagnostikovat problém s filtrováním síťového provozu do nebo z virtuálního počítače](diagnose-vm-network-traffic-filtering-problem.md) pomocí schopnosti ověření toku IP Network Watcher.
- Naučte se [diagnostikovat problém při směrování síťového provozu z virtuálního počítače](diagnose-vm-network-routing-problem.md) pomocí možnosti dalšího směrování Network Watcher.
