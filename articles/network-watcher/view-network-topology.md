---
title: Zobrazit topologii virtuální sítě Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak zobrazit prostředky ve virtuální síti a vztahy mezi prostředky.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: 675919db55932d3ccc04fd5397f6f673832b4900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840566"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Zobrazení topologie virtuální sítě Azure

V tomto článku se dozvíte, jak zobrazit prostředky ve virtuální síti Microsoft Azure a vztahy mezi prostředky. Například virtuální síť obsahuje podsítě. Podsítě obsahují prostředky, jako jsou virtuální počítače Azure (VM). Virtuální aplikace mají jedno nebo více síťových rozhraní. Ke každé podsíti může být přidružena skupina zabezpečení sítě a směrovací tabulka. Funkce topologie Azure Network Watcher umožňuje zobrazit všechny prostředky ve virtuální síti, prostředky přidružené k prostředkům ve virtuální síti a vztahy mezi prostředky.

K zobrazení topologie můžete použít [portál Azure](#azure-portal), [azure cli](#azure-cli)nebo [PowerShell.](#powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Zobrazit topologii – portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu, který má potřebná [oprávnění](required-rbac-permissions.md).
2. V levém horním rohu portálu vyberte **Všechny služby**.
3. Do pole **Filtr Všechny služby** zadejte *Sledovací modul sítě*. Jakmile se služba **Network Watcher** zobrazí ve výsledcích, vyberte ji.
4. Vyberte **Topologie**. Generování topologie vyžaduje sledovací proces sítě ve stejné oblasti, ve které existuje virtuální síť, pro kterou chcete generovat topologii. Pokud nemáte v oblasti, ve které virtuální síť, pro kterou chcete generovat topologii, povolen sledovací proces sítě, budou pro vás ve všech oblastech automaticky vytvořeny sledovací procesy sítě. Sledování sítě jsou vytvořeny ve skupině prostředků s názvem **NetworkWatcherRG**.
5. Vyberte předplatné, skupinu prostředků virtuální sítě, pro kterou chcete zobrazit topologii, a pak vyberte virtuální síť. Na následujícím obrázku je zobrazena topologie pro virtuální síť s názvem *MyVnet*ve skupině prostředků s názvem *MyResourceGroup*:

    ![Zobrazit topologii](./media/view-network-topology/view-topology.png)

    Jak můžete vidět na předchozím obrázku, virtuální síť obsahuje tři podsítě. Jedna podsíť má virtuální ho nasazené v něm. Virtuální ho virtuálního počítačů je připojeno jedno síťové rozhraní a je k němu přidružena veřejná IP adresa. Další dvě podsítě mají přidruženou směrovací tabulku. Každá tabulka tras obsahuje dvě trasy. K jedné podsíti je přidružena skupina zabezpečení sítě. Informace o topologii jsou zobrazeny pouze pro zdroje, které jsou:
    
    - V rámci stejné skupiny prostředků a oblasti jako virtuální síť *myVnet.* Například skupina zabezpečení sítě, která existuje v jiné skupině prostředků než *MyResourceGroup*, se nezobrazí, a to ani v případě, že je skupina zabezpečení sítě přidružena k podsíti ve virtuální síti *MyVnet.*
    - Virtuální síť myVnet nebo přidružené k prostředkům v rámci virtuální sítě *myVnet.* Například skupina zabezpečení sítě, která není přidružena k podsíti nebo síťovému rozhraní ve virtuální síti *myVnet,* se nezobrazí, a to ani v případě, že je skupina zabezpečení sítě ve skupině prostředků *MyResourceGroup.*

   Topologie zobrazená na obrázku je pro virtuální síť vytvořenou po nasazení **provozu Route prostřednictvím ukázky skriptu síťového virtuálního zařízení**, kterou můžete nasadit pomocí azure [CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)nebo [PowerShellu](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Vyberte **Stáhnout topologii,** chcete-li obrázek stáhnout jako upravitelný soubor ve formátu svg.

Prostředky uvedené v diagramu jsou podmnožinou síťových součástí ve virtuální síti. Například při zobrazení skupiny zabezpečení sítě, pravidla zabezpečení v něm nejsou zobrazeny v diagramu. Ačkoli nejsou v diagramu rozlišeny, řádky představují jeden ze dvou vztahů: *Uzavření* nebo *přidružené*. Chcete-li zobrazit úplný seznam prostředků ve virtuální síti a typ vztahu mezi prostředky, vygenerujte topologii pomocí [Prostředí PowerShell](#powershell) nebo [Azure CLI](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Zobrazit topologii – Azure CLI

Příkazy můžete spustit v následujících krocích:
- V prostředí Azure Cloud Shell výběrem **try it** v pravém horním rohu libovolného příkazu. Azure Cloud Shell je bezplatná interaktivní prostředí, které má společné nástroje Azure předinstalované a nakonfigurované pro použití s vaším účtem.
- Spuštěním zapisování/li se konto z počítače. Pokud spustíte příkaz cli z počítače, kroky v tomto článku vyžadují Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.

Účet, který používáte, musí mít potřebná [oprávnění](required-rbac-permissions.md).

1. Pokud již máte sledovací proces sítě ve stejné oblasti jako virtuální síť, pro kterou chcete vytvořit topologii, přeskočte ke kroku 3. Vytvořte skupinu prostředků, která bude obsahovat sledovací proces sítě s [vytvořením skupiny az](/cli/azure/group). Následující příklad vytvoří skupinu prostředků v oblasti *eastus:*

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Vytvořte sledovací proces sítě s [konfigurací sledování sítě az](/cli/azure/network/watcher#az-network-watcher-configure). Následující příklad vytvoří sledovací proces sítě v oblasti *eastus:*

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Zobrazení topologie pomocí [sledování sítě az show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). Následující příklad zobrazí topologii skupiny prostředků s názvem *MyResourceGroup*:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Informace o topologii jsou vráceny pouze pro prostředky, které jsou ve stejné skupině prostředků jako skupina prostředků *MyResourceGroup* a ve stejné oblasti jako sledovací proces sítě. Například skupina zabezpečení sítě, která existuje v jiné skupině prostředků než *MyResourceGroup*, se nezobrazí, a to ani v případě, že je skupina zabezpečení sítě přidružena k podsíti ve virtuální síti *MyVnet.*

   Další informace o vztazích a [vlastnostech](#properties) v vráceném výstupu. Pokud nemáte existující virtuální síť pro zobrazení topologie pro, můžete vytvořit pomocí [směrovat provoz prostřednictvím ukázky skriptu virtuálního zařízení sítě.](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Chcete-li zobrazit diagram topologie a stáhnout jej do upravitelného souboru, použijte [portál](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Zobrazit topologii - PowerShell

Příkazy můžete spustit v následujících krocích:
- V prostředí Azure Cloud Shell výběrem **try it** v pravém horním rohu libovolného příkazu. Azure Cloud Shell je bezplatná interaktivní prostředí, které má společné nástroje Azure předinstalované a nakonfigurované pro použití s vaším účtem.
- Spuštěním prostředí PowerShell z počítače. Pokud spustíte PowerShell z počítače, tento článek `Az` vyžaduje modul Azure PowerShell. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Účet, který používáte, musí mít potřebná [oprávnění](required-rbac-permissions.md).

1. Pokud již máte sledovací proces sítě ve stejné oblasti jako virtuální síť, pro kterou chcete vytvořit topologii, přeskočte ke kroku 3. Vytvořte skupinu prostředků, která bude obsahovat sledovací proces sítě s [new-azresourcegroup](/powershell/module/az.Resources/New-azResourceGroup). Následující příklad vytvoří skupinu prostředků v oblasti *eastus:*

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Vytvořte sledovací proces sítě pomocí [funkce New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). Následující příklad vytvoří sledovací proces sítě v oblasti eastus:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Načíst instanci Network Watcher pomocí [služby Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). Následující příklad načte sledovací proces sítě v oblasti USA – východ:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Načíst topologii s [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). Následující příklad načte topologii pro virtuální síť ve skupině prostředků s názvem *MyResourceGroup*:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Informace o topologii jsou vráceny pouze pro prostředky, které jsou ve stejné skupině prostředků jako skupina prostředků *MyResourceGroup* a ve stejné oblasti jako sledovací proces sítě. Například skupina zabezpečení sítě, která existuje v jiné skupině prostředků než *MyResourceGroup*, se nezobrazí, a to ani v případě, že je skupina zabezpečení sítě přidružena k podsíti ve virtuální síti *MyVnet.*

   Další informace o vztazích a [vlastnostech](#properties) v vráceném výstupu. Pokud nemáte existující virtuální síť pro zobrazení topologie pro, můžete vytvořit pomocí [směrovat provoz prostřednictvím ukázky skriptu virtuálního zařízení sítě.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Chcete-li zobrazit diagram topologie a stáhnout jej do upravitelného souboru, použijte [portál](#azure-portal).

## <a name="relationships"></a>Relace

Všechny prostředky vrácené v topologii mají jeden z následujících typů vztahu k jinému zdroji:

| Typ vztahu | Příklad                                                                                                |
| ---               | ---                                                                                                    |
| Členství ve skupině       | Virtuální síť obsahuje podsíť. Podsíť obsahuje síťové rozhraní.                            |
| Přidružené        | Síťové rozhraní je přidružené k virtuálnímu virtuálnímu modulu. K síťovému rozhraní je přidružena veřejná IP adresa. |

## <a name="properties"></a>Vlastnosti

Všechny prostředky vrácené v topologii mají následující vlastnosti:

- **Název**: Název prostředku
- **Id**: Identifikátor URI prostředku.
- **Umístění**: Oblast Azure, ve které prostředek existuje.
- **Přidružení**: Seznam přidružení k odkazovaný objekt. Každé přidružení má následující vlastnosti:
    - **AssociationType**: Odkazuje na vztah mezi podřízeným objektem a nadřazeným objektem. Platné hodnoty jsou *Contains* nebo *Associated*.
    - **Název**: Název odkazovaného prostředku.
    - **ResourceId**: - Identifikátor URI prostředku, na který je v přidružení odkazováno.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [diagnostikovat problém s filtrem síťového provozu do nebo z virtuálního počítačů](diagnose-vm-network-traffic-filtering-problem.md) pomocí funkce ověření toku IP sledovacího programu sítě
- Zjistěte, jak [diagnostikovat problém se směrováním síťového provozu z virtuálního směrovače](diagnose-vm-network-routing-problem.md) pomocí další možnosti směrování sledování sítě
