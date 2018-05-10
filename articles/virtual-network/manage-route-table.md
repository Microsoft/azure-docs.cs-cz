---
title: Vytvoření, změnit nebo odstranit tabulku směrování Azure | Microsoft Docs
description: Zjistěte, jak vytvořit, změnit nebo odstranit tabulku směrování.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 065ac8b2e9cb48408c7922a1937e541521ccd8cf
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="create-change-or-delete-a-route-table"></a>Vytvoření, změnit nebo odstranit tabulku směrování

Azure automaticky směruje provoz mezi Azure podsítě virtuální sítě a místní sítě. Pokud chcete změnit výchozí Azure a směrování, uděláte tak, že vytvoříte směrovací tabulku. Pokud začínáte směrování ve virtuálních sítích, můžete další informace o jeho [směrování přehled](virtual-networks-udr-overview.md) nebo provedením [kurzu](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

Účet přihlásit nebo připojit k Azure, musí být přiřazená k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Je omezena na tom, kolik směrovací tabulky, můžete vytvořit na umístění Azure a předplatné. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
2. Vyberte **sítě**, pak vyberte **směrovací tabulku**.
3. Zadejte **název** směrovací tabulka, vyberte vaše **předplatné**, vytvořte novou **skupiny prostředků**, nebo vyberte existující skupinu prostředků, vyberte **umístění** , pak vyberte **vytvořit**. **Šíření trasy protokolu BGP zakázat** možnost brání místní trasy z rozšířen přes protokol BGP pro rozhraní sítě v žádné podsítě, která směrovací tabulka je přidružené k. Pokud virtuální sítě není připojený k bránu Azure sítě (VPN nebo ExpressRoute), ponechte možnost *zakázané*.

**Příkazy**

- Azure CLI: [vytvořit az sítě-tabulka směrování](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Zobrazení směrovací tabulky

Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho. Jsou uvedeny směrovací tabulky, které existují v rámci vašeho předplatného.

**Příkazy**

- Azure CLI: [seznam směrovací tabulku az sítě](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Zobrazení podrobností směrovací tabulku

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Směrovací tabulka vyberte v seznamu, který chcete zobrazit podrobnosti. V části **nastavení**, můžete zobrazit **trasy** v tabulce směrování a **podsítě** směrovací tabulka je přidružena k.
3. Další informace o běžných nastavení Azure, přečtěte si následující informace:
    *   [Protokol aktivit](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Řízení přístupu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zámky.](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skriptu pro automatizaci](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Příkazy**

- Azure CLI: [az sítě směrovací tabulku zobrazit](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Změnit směrovací tabulku

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte tabulku směrování, které chcete změnit. Nejběžnější změny jsou [přidání](#create-a-route) nebo [odebrání](#delete-a-route) trasy a [přidružení](#associate-a-route-table-to-a-subnet) směrovacích tabulek, nebo [zrušení přidružení](#dissociate-a-route-table-from-a-subnet) směrovacích tabulek z podsítě.

**Příkazy**

- Azure CLI: [aktualizace směrovací tabulku az sítě](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Podsíť může mít žádnou nebo jednu směrovací tabulku přidružené k němu. Směrovací tabulka může být přidružena k nula nebo více podsítí. Vzhledem k tomu, že směrovací tabulky nejsou přidružené k virtuálním sítím, je nutné přidružit směrovací tabulka pro každou podsíť, které chcete přidružené k tabulce směrování. Všechny odchozího provozu z podsítě se směruje podle tras, které jste vytvořili v rámci směrovací tabulky [výchozí trasy](virtual-networks-udr-overview.md#default), a šíření tras z místní sítě, pokud je virtuální síť připojená k (brány virtuální sítě Azure ExpressRoute nebo VPN, pokud pomocí protokolu BGP brána sítě VPN). Můžete přidružit pouze směrovací tabulku do podsítí ve virtuálních sítích, které se nacházejí ve stejné oblasti Azure a předplatné jako tabulku směrování.

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte v seznamu, který obsahuje podsítě, které chcete přidružit tabulku směrování pro virtuální síť.
3. Vyberte **podsítě** pod **nastavení**.
4. Vyberte podsíť, které chcete přidružit směrovací tabulka pro.
5. Vyberte **směrovací tabulku**, vyberte tabulku trasy, které chcete přidružit k podsíti a pak vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- Prostředí PowerShell: [AzureRmVirtualNetworkSubnetConfig sady](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušit přidružení směrovací tabulku z podsítě

Pokud zrušíte přidružení směrovací tabulku z podsítě, Azure směruje provoz na základě jeho [výchozí trasy](virtual-networks-udr-overview.md#default).

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte virtuální síť, která obsahuje podsíť, kterou chcete zrušit přidružení směrovací tabulku z.
3. Vyberte **podsítě** pod **nastavení**.
4. Vyberte podsíť, kterou chcete zrušit přidružení směrovací tabulka z.
5. Vyberte **směrovací tabulku**, vyberte **žádné**, pak vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az_network_vnet_subnet_update)
- Prostředí PowerShell: [AzureRmVirtualNetworkSubnetConfig sady](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Odstranit tabulku směrování

Směrovací tabulka je přidružen k žádné podsítě, nelze odstranit. [Zrušit přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulku ze všech podsítí před pokusem o jeho odstranění.

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **...**  na pravé straně chcete odstranit tabulku směrování.
3. Vyberte **odstranit**a potom vyberte **Ano**.

**Příkazy**

- Azure CLI: [odstranit az sítě-tabulka směrování](/cli/azure/network/route-table/route#az_network_route_table_delete)
- Prostředí PowerShell: [AzureRmRouteTable odstranění](/powershell/module/azurerm.network/delete-azurermroutetable) 

## <a name="create-a-route"></a>Vytvoření trasy

Je omezena na tom, kolik tras ve směrovací tabulce můžete vytvořit na umístění Azure a předplatné. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte ze seznamu, který chcete přidat trasu k směrovací tabulka.
3. Vyberte **trasy**v části **nastavení**.
4. Vyberte **+ Přidat**.
5. Zadejte jedinečný **název** trasy do směrovací tabulky.
6. Zadejte **předpona adresy**, v notaci CIDR, který chcete přesměrovat provoz na. Předpona, která nemůže být v víc tras ve směrovací tabulka duplikovaný, přestože předponu můžou být v rámci jiné předpony. Například pokud jste definovali 10.0.0.0/16 jako předpony v jednoho z nich, můžete definovat další směrování s předponou adresy 10.0.0.0/24. Azure vybere trasu pro provoz na základě nejdelší shody předpony. Další informace o tom, jak Azure vybere trasy, najdete v části [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Vyberte **typ dalšího směrování**. Podrobný popis všechny další typy směrování, najdete v části [Přehled směrování](virtual-networks-udr-overview.md).
8. Zadejte IP adresu pro **adresa dalšího směrování**. Adresu lze zadat pouze v případě, že jste vybrali *virtuální zařízení* pro **typ dalšího směrování**.
9. Vyberte **OK**.

**Příkazy**

- Azure CLI: [vytvořit az síťovou směrovací tabulku trasu](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Zobrazení trasy

Směrovací tabulka obsahuje nula nebo víc tras. Další informace o informace uvedené při prohlížení trasy, najdete v části [Přehled směrování](virtual-networks-udr-overview.md).

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte ze seznamu, který chcete zobrazit trasy pro směrovací tabulka.
3. Vyberte **trasy** pod **nastavení**.

**Příkazy**

- Azure CLI: [seznam az sítě směrovací tabulka trasy](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_list)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Zobrazení podrobností trasu

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte tabulku trasy, které chcete zobrazit podrobnosti o trasu pro.
3. Vyberte **trasy**.
4. Vyberte trasy, které chcete zobrazit podrobnosti.

**Příkazy**

- Azure CLI: [az sítě směrovací tabulka trasy zobrazit](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Změnit trasu

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte směrovací tabulka, kterou chcete změnit trasu pro.
3. Vyberte **trasy**.
4. Vyberte trasy, k níž chcete změnit.
5. Změňte stávající nastavení na jejich nové nastavení a pak vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace az sítě směrovací tabulka trasy](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_update)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Odstranit trasu

1. Do vyhledávacího pole v horní části portálu, zadejte *směrovacích tabulek* do vyhledávacího pole. Když **směrovacích tabulek** nezobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte, chcete odstranit trasu pro tabulku směrování.
3. Vyberte **trasy**.
4. Seznam tras, vyberte **...**  na pravé straně trasy, která chcete odstranit.
5. Vyberte **odstranit**, pak vyberte **Ano**.

**Příkazy**

- Azure CLI: [az síť směrovací tabulka trasy odstranit](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Zobrazit účinné postupy

Efektivní trasy pro každé síťové rozhraní, který je připojen k virtuálnímu počítači jsou kombinaci směrovací tabulky, které jste vytvořili, Azure, je výchozí trasy a všechny trasy rozšíří z místní sítě prostřednictvím protokolu BGP přes bránu virtuální sítě Azure. Principy efektivní trasy pro síťové rozhraní je užitečné při odstraňování problémů směrování. Můžete zobrazit efektivní trasy pro síťové rozhraní, který je připojen k spuštěného virtuálního počítače.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, které chcete zobrazit efektivní trasy pro. Pokud neznáte název virtuálního počítače, zadejte *virtuální počítače* do vyhledávacího pole. Když **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ho a vyberte virtuální počítač ze seznamu.
2. Vyberte **sítě** pod **nastavení**.
3. Vyberte název síťového rozhraní.
4. Vyberte **efektivní trasy** pod **podporu + Poradce při potížích s**.
5. Projděte si seznam efektivní trasy k určení, zda existuje správné směrování pro které chcete směrovat provoz. Další informace o další typy směrování, které se zobrazí v tomto seznamu v [Přehled směrování](virtual-networks-udr-overview.md).

**Příkazy**

- Azure CLI: [az sítě seskupování zobrazit – platné – trasy – tabulka](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/remove-azurermrouteconfig) 

## <a name="validate-routing-between-two-endpoints"></a>Ověření směrování mezi dva koncové body

Můžete určit typ dalšího směrování mezi virtuálním počítačem a IP adresu jiného prostředku Azure, místnímu prostředku nebo prostředku na Internetu. Určení Azure směrování je užitečné při řešení potíží s problémy se směrováním. Tuto úlohu dokončit, musí mít existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ho pomocí kroků v [vytvořit instanci sledovací proces sítě](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Do vyhledávacího pole v horní části portálu, zadejte *sledovací proces sítě* do vyhledávacího pole. Když **sledovací proces sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Vyberte **dalšího směrování** pod **nástroj pro diagnostiku sítě**.
3. Vyberte vaše **předplatné** a **skupiny prostředků** zdrojového virtuálního počítače, kterou chcete ověřit směrování z.
4. Vyberte **virtuálního počítače**, **síťové rozhraní** připojen k virtuálnímu počítači a **IP adresa zdroje** přiřazené síťového rozhraní, které chcete ověřit směrování z.
5. Zadejte **cílová IP adresa** , kterou chcete ověřit směrování do.
6. Vyberte **dalšího směrování**.
7. Po krátké čekání se vrátí informace, které sděluje typ dalšího segmentu a ID trasy, která směrovat provoz. Další informace o další typy směrování, který se zobrazí, vrátí se v [Přehled směrování](virtual-networks-udr-overview.md).

**Příkazy**

- Azure CLI: [az sítě zobrazit sledovacích procesů-dalšího směrování](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Oprávnění

K provádění úloh na směrovací tabulky a směrování, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                                          |   Název                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Čtení tabulky trasy                                    |
| Microsoft.Network/routeTables/write                             |   Vytvořit nebo aktualizovat směrovací tabulku                        |
| Microsoft.Network/routeTables/delete                            |   Odstranit tabulku směrování                                  |
| Microsoft.Network/routeTables/join/action                       |   Přidružení směrovací tabulky k podsíti                   |
| Microsoft.Network/routeTables/routes/read                       |   Přečtěte si trasu                                          |
| Microsoft.Network/routeTables/routes/write                      |   Vytvořit nebo aktualizovat trasu                              |
| Microsoft.Network/routeTables/routes/delete                     |   Odstranit trasu                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Získat aktuálně platná směrovací tabulka pro rozhraní sítě |
| Microsoft.Network/networkWatchers/nextHop/action                |   Získá další segment z virtuálního počítače                           |

## <a name="next-steps"></a>Další postup

- Vytvoření pomocí tabulky trasy [prostředí PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablony Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure zásad](policy-samples.md) pro virtuální sítě
