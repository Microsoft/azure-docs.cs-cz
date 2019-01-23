---
title: Vytvoření, změna nebo odstranění Azure směrovací tabulky
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit, změnit nebo odstranit tabulku směrování.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 3381bf32bfd3df538641b11b35a64b53fda6744f
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54466869"
---
# <a name="create-change-or-delete-a-route-table"></a>Vytvořit, změnit nebo odstranit tabulku směrování

Azure automaticky směruje provoz mezi Azure podsítěmi virtuálních sítí a místní sítí. Pokud chcete změnit výchozí Azure pro směrování, to provést tak, že vytvoříte směrovací tabulku. Pokud začínáte směrování ve virtuálních sítích, další informace o řezu [Přehled směrování](virtual-networks-udr-overview.md) nebo provedením [kurzu](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Před zahájením

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Platí omezení na počet směrovacích tabulek můžete vytvořit na umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
2. Vyberte **sítě**a pak vyberte **směrovací tabulka**.
3. Zadejte **název** směrovací tabulky, vyberte vaše **předplatné**, vytvořte nový **skupiny prostředků**, nebo vyberte existující skupinu prostředků, vyberte **umístění** a pak vyberte **vytvořit**. Pokud máte v úmyslu přidružení směrovací tabulky k podsíti ve virtuální síti, který je připojený k vaší místní síti prostřednictvím brány sítě VPN, a zakážete **šíření tras BGP**, místní trasy nejsou šířeny do Síťová rozhraní v podsíti.

**Příkazy**

- Azure CLI: [vytvořit sítě az route-table](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## <a name="view-route-tables"></a>Zobrazení tabulky směrování

Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji. Směrovací tabulky, které existují ve vašem předplatném jsou uvedeny.

**Příkazy**

- Azure CLI: [az network route-table seznamu](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="view-details-of-a-route-table"></a>Zobrazit podrobnosti směrovací tabulky

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte v seznamu, který chcete zobrazit podrobnosti pro směrovací tabulky. V části **nastavení**, můžete zobrazit **trasy** ve směrovací tabulce a **podsítě** směrovací tabulka je přidružen k.
3. Další informace o běžných nastavení služby Azure, najdete v následující informace:
    *   [Protokol aktivit](../azure-monitor/platform/activity-logs-overview.md)
    *   [Řízení přístupu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zámky](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automatizační skript](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Příkazy**

- Azure CLI: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## <a name="change-a-route-table"></a>Změnit tabulku směrování

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte směrovací tabulku, kterou chcete změnit. Výčet nejběžnějších změn jsou [přidání](#create-a-route) nebo [odebrání](#delete-a-route) trasy a [přidružení](#associate-a-route-table-to-a-subnet) směrovací tabulky, nebo [ruší se přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulky z podsítě.

**Příkazy**

- Azure CLI: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Podsíť může mít žádnou nebo jednu směrovací tabulky přidružené k němu. Směrovací tabulky můžou být přidružené k nula nebo více podsítí. Protože směrovací tabulky nejsou přidružené k virtuálním sítím, třeba přidružení směrovací tabulky do každé podsítě má směrovací tabulky přidružené k. Všechny přenosů z podsítě se směruje podle trasy, které jste vytvořili v rámci směrovacích tabulek [výchozí trasy](virtual-networks-udr-overview.md#default), a šíření tras z místní sítě, pokud virtuální síť připojená k (bránu virtuální sítě Azure ExpressRoute nebo VPN, pokud pomocí protokolu BGP s bránou VPN). Můžete přidružit pouze směrovací tabulku do podsítí ve virtuálních sítích, které existují ve stejném umístění Azure a předplatné jako směrovací tabulky.

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. V seznamu, který obsahuje podsítě a přidružte směrovací tabulku pro výběr virtuální sítě.
3. Vyberte **podsítě** pod **nastavení**.
4. Vyberte podsíť, a přidružte směrovací tabulku k.
5. Vyberte **směrovací tabulka**, vyberte směrovací tabulka, kterou chcete přidružit k podsíti a pak vyberte **Uložit**.

Pokud je vaše virtuální síť připojená k Azure VPN gateway, nepřidružujte k [podsíti brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) směrovací tabulku, která má směrování s cílem 0.0.0.0/0. Mohli byste tím bráně znemožnit správné fungování. Další informace o používání 0.0.0.0/0 v trase, naleznete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md#default-route).

**Příkazy**

- Azure CLI: [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet?view=azure-cli-latest)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušit přidružení směrovací tabulky z podsítě

Pokud zrušíte přidružení směrovací tabulky z podsítě, Azure směruje provoz na základě jeho [výchozí trasy](virtual-networks-udr-overview.md#default).

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte virtuální síť, která obsahuje podsítě, které chcete zrušit přidružení směrovací tabulky z.
3. Vyberte **podsítě** pod **nastavení**.
4. Vyberte podsíť, které chcete zrušit přidružení směrovací tabulky z.
5. Vyberte **směrovací tabulka**vyberte **žádný**a pak vyberte **Uložit**.

**Příkazy**

- Azure CLI: [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet?view=azure-cli-latest)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## <a name="delete-a-route-table"></a>Odstranit tabulku směrování

Směrovací tabulka je přidružen k žádné podsítě, nelze odstranit. [Zrušit přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulku ze všech podsítí, než se pokusíte odstranit.

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte **...**  na pravé straně směrovací tabulka, kterou chcete odstranit.
3. Vyberte **odstranit**a pak vyberte **Ano**.

**Příkazy**

- Azure CLI: [az sítě route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Remove-AzureRmRouteTable](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermroutetable?view=azurermps-6.8.1) 

## <a name="create-a-route"></a>Vytvoření trasy

Platí omezení na tom, kolik tras ve směrovací tabulce můžete vytvořit pro jedno umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte ze seznamu, který chcete přidat trasu k směrovací tabulky.
3. Vyberte **trasy**v části **nastavení**.
4. Vyberte **+ Přidat**.
5. Zadejte jedinečný **název** trasy do směrovací tabulky.
6. Zadejte **předponu adresy**, v notaci CIDR, které chcete směrovat provoz do. Předpona, která nelze zkopírovat do víc tras ve směrovací tabulce, i když předpona, která může být v rámci jinou předponu. Například pokud jste definovali jako předponu v jednu trasu 10.0.0.0/16, můžete definovat další trasu s předponou adresy 10.0.0.0/24. Azure vybírá trasu pro provoz na základě nejdelší shody předpony. Další informace o tom, jak Azure vybere trasách najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Vyberte **typem dalšího segmentu směrování**. Podrobný popis všech typů dalších segmentů směrování, naleznete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
8. Zadejte IP adresu pro **adresa dalšího segmentu**. Můžete pouze zadat adresu, pokud jste vybrali *virtuální zařízení* pro **typem dalšího segmentu směrování**.
9. Vyberte **OK**.

**Příkazy**

- Azure CLI: [vytvořit az network route-table trasy](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## <a name="view-routes"></a>Zobrazit trasy

Směrovací tabulka obsahuje nula nebo více tras. Další informace o informace při zobrazení trasy, naleznete v tématu [Přehled směrování](virtual-networks-udr-overview.md).

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte ze seznamu, který chcete zobrazit trasy pro směrovací tabulky.
3. Vyberte **trasy** pod **nastavení**.

**Příkazy**

- Azure CLI: [az network route-table trasy seznamu](/cli/azure/network/route-table/route?view=azure-cli-latest)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte směrovací tabulku, kterou chcete zobrazit podrobnosti o trasu pro.
3. Vyberte **trasy**.
4. Vyberte trasy, které chcete zobrazit podrobnosti.

**Příkazy**

- Azure CLI: [az sítě route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## <a name="change-a-route"></a>Změna trasy

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Směrovací tabulka, kterou chcete změnit trasu pro výběr.
3. Vyberte **trasy**.
4. Vyberte trasy, které chcete změnit.
5. Změnit existující nastavení své nové nastavení a pak vyberte **Uložit**.

**Příkazy**

- Azure CLI: [az sítě route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## <a name="delete-a-route"></a>Odstranit trasu

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
2. Směrovací tabulka, kterou chcete odstranit trasu pro výběr.
3. Vyberte **trasy**.
4. Seznam tras, vyberte **...**  na pravé straně trasy, která chcete odstranit.
5. Vyberte **odstranit**a pak vyberte **Ano**.

**Příkazy**

- Azure CLI: [az sítě route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## <a name="view-effective-routes"></a>Zobrazení efektivních tras

Efektivní trasy pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací směrovacích tabulek, které jste vytvořili, výchozí trasy a všechny trasy rozšířena z místní sítě přes protokol BGP prostřednictvím brány virtuální sítě Azure. Principy efektivní trasy pro síťové rozhraní je užitečné při řešení potíží se směrováním. Můžete zobrazit efektivní trasy pro každé síťové rozhraní, který je připojený k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, které chcete zobrazit efektivní trasy pro. Pokud si nejste jisti názvem virtuálního počítače, zadejte *virtuálních počítačů* do vyhledávacího pole. Když **virtuálních počítačů** ve výsledcích hledání se zobrazí, vyberte ho a vyberte virtuální počítač ze seznamu.
2. Vyberte **sítě** pod **nastavení**.
3. Vyberte název síťového rozhraní.
4. Vyberte **efektivní trasy** pod **podpora a řešení potíží**.
5. Projděte si seznam efektivní trasy a určete, jestli pro místo, kam chcete směrovat provoz do existuje správné směrování. Další informace o typy dalších segmentů směrování, které se zobrazí v tomto seznamu v [Přehled směrování](virtual-networks-udr-overview.md).

**Příkazy**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 

## <a name="validate-routing-between-two-endpoints"></a>Ověřit směrování mezi dva koncové body služby

Můžete určit typ dalšího segmentu směrování mezi virtuálním počítačem a IP adresu jiného prostředku Azure, místnímu prostředku nebo prostředku na Internetu. Určení Azure směrování je užitečné při řešení potíží se směrováním. Tento úkol provést, musíte mít existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ho provedením kroků v [vytvoření instance Network Watcheru](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Do vyhledávacího pole v horní části portálu zadejte *network watcheru* do vyhledávacího pole. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
2. Vyberte **směrování** pod **nástroje pro diagnostiku sítě**.
3. Vyberte vaše **předplatné** a **skupiny prostředků** zdrojového virtuálního počítače, kterou chcete ověřit směrování.
4. Vyberte **virtuálního počítače**, **síťové rozhraní** připojené k virtuálnímu počítači a **IP adresa zdroje** přiřazené k síťovému rozhraní, který chcete ověřit směrování.
5. Zadejte **cílová IP adresa** , kterou chcete ověřit směrování.
6. Vyberte **směrování**.
7. Po krátkém čekání se informace vrácené, které sděluje, typ dalšího segmentu směrování a ID trasy, která směruje provoz. Další informace o typy dalších segmentů směrování, které vidíte vrácené v [Přehled směrování](virtual-networks-udr-overview.md).

**Příkazy**

- Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## <a name="permissions"></a>Oprávnění

K provádění úloh na směrovací tabulky a trasy, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                                          |   Název                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Přečíst tabulku směrování                                    |
| Microsoft.Network/routeTables/write                             |   Vytvořit nebo aktualizovat tabulku směrování                        |
| Microsoft.Network/routeTables/delete                            |   Odstranit tabulku směrování                                  |
| Microsoft.Network/routeTables/join/action                       |   Přidružení směrovací tabulky k podsíti                   |
| Microsoft.Network/routeTables/routes/read                       |   Přečtěte si trasu                                          |
| Microsoft.Network/routeTables/routes/write                      |   Vytvořit nebo aktualizovat trasu                              |
| Microsoft.Network/routeTables/routes/delete                     |   Odstranit trasu                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Získejte aktuálně platná směrovací tabulka pro síťové rozhraní |
| Microsoft.Network/networkWatchers/nextHop/action                |   Získá další segment směrování z virtuálního počítače                           |

## <a name="next-steps"></a>Další postup

- Vytvoření směrovací tabulky pomocí [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
