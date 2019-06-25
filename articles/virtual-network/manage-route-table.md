---
title: Vytvoření, změna nebo odstranění Azure směrovací tabulky
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit, změnit nebo odstranit tabulku směrování.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849804"
---
# <a name="create-change-or-delete-a-route-table"></a>Vytvořit, změnit nebo odstranit tabulku směrování

Azure automaticky směruje provoz mezi Azure podsítěmi virtuálních sítí a místní sítí. Pokud chcete změnit výchozí Azure pro směrování, to provést tak, že vytvoříte směrovací tabulku. Pokud začínáte směrování ve virtuálních sítích, další informace o řezu [Přehled směrování](virtual-networks-udr-overview.md) nebo provedením [kurzu](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

* Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).<br>
* Pokud používáte portál, otevřete https://portal.azure.com a přihlaste se pomocí svého účtu Azure.<br>
* Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novějším. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.<br>
* Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Platí omezení na počet směrovacích tabulek můžete vytvořit na umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V levém horním rohu portálu, vyberte **+ vytvořit prostředek**.
1. Vyberte **sítě**a pak vyberte **směrovací tabulka**.
1. Zadejte **název** směrovací tabulky, vyberte vaše **předplatné**, vytvořte nový **skupiny prostředků**, nebo vyberte existující skupinu prostředků, vyberte **umístění** a pak vyberte **vytvořit**. Pokud máte v úmyslu přidružení směrovací tabulky k podsíti ve virtuální síti, který je připojený k vaší místní síti prostřednictvím brány sítě VPN, a zakážete **šíření tras brány virtuální sítě**, nejsou místní trasy rozšíří na síťová rozhraní v podsíti.

### <a name="create-route-table---commands"></a>Vytvoření směrovací tabulky – příkazy

* Azure CLI: [vytvořit sítě az route-table](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Zobrazení tabulky směrování

Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji. Směrovací tabulky, které existují ve vašem předplatném jsou uvedeny.

### <a name="view-route-table---commands"></a>Zobrazení směrovací tabulky – příkazy

* Azure CLI: [az network route-table seznamu](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Zobrazit podrobnosti směrovací tabulky

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte v seznamu, který chcete zobrazit podrobnosti pro směrovací tabulky. V části **nastavení**, můžete zobrazit **trasy** ve směrovací tabulce a **podsítě** směrovací tabulka je přidružen k.
1. Další informace o běžných nastavení služby Azure, najdete v následující informace:

    * [Protokol aktivit](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [Řízení přístupu (IAM)](../role-based-access-control/overview.md)<br>
    * [Značky](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Zámky](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Automatizační skript](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>Zobrazit podrobnosti směrovací tabulky – příkazy

* Azure CLI: [az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Změnit tabulku směrování

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte směrovací tabulku, kterou chcete změnit. Výčet nejběžnějších změn jsou [přidání](#create-a-route) nebo [odebrání](#delete-a-route) trasy a [přidružení](#associate-a-route-table-to-a-subnet) směrovací tabulky, nebo [ruší se přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulky z podsítě.

### <a name="change-a-route-table---commands"></a>Změnit tabulku směrování – příkazy

* Azure CLI: [az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell: [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Podsíť může mít žádnou nebo jednu směrovací tabulky přidružené k němu. Směrovací tabulky můžou být přidružené k nula nebo více podsítí. Protože směrovací tabulky nejsou přidružené k virtuálním sítím, třeba přidružení směrovací tabulky do každé podsítě má směrovací tabulky přidružené k. Všechny přenosů z podsítě se směruje podle trasy, které jste vytvořili v rámci směrovacích tabulek [výchozí trasy](virtual-networks-udr-overview.md#default), a šíření tras z místní sítě, pokud virtuální síť připojená k (bránu virtuální sítě Azure ExpressRoute nebo VPN). Můžete přidružit pouze směrovací tabulku do podsítí ve virtuálních sítích, které existují ve stejném umístění Azure a předplatné jako směrovací tabulky.

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
1. V seznamu, který obsahuje podsítě a přidružte směrovací tabulku pro výběr virtuální sítě.
1. Vyberte **podsítě** pod **nastavení**.
1. Vyberte podsíť, a přidružte směrovací tabulku k.
1. Vyberte **směrovací tabulka**, vyberte směrovací tabulka, kterou chcete přidružit k podsíti a pak vyberte **Uložit**.

Pokud je vaše virtuální síť připojená k Azure VPN gateway, nepřidružujte k [podsíti brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) směrovací tabulku, která má směrování s cílem 0.0.0.0/0. Mohli byste tím bráně znemožnit správné fungování. Další informace o používání 0.0.0.0/0 v trase, naleznete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Přidružení směrovací tabulky – příkazy

* Azure CLI: [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušit přidružení směrovací tabulky z podsítě

Pokud zrušíte přidružení směrovací tabulky z podsítě, Azure směruje provoz na základě jeho [výchozí trasy](virtual-networks-udr-overview.md#default).

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte virtuální síť, která obsahuje podsítě, které chcete zrušit přidružení směrovací tabulky z.
1. Vyberte **podsítě** pod **nastavení**.
1. Vyberte podsíť, které chcete zrušit přidružení směrovací tabulky z.
1. Vyberte **směrovací tabulka**vyberte **žádný**a pak vyberte **Uložit**.

### <a name="dissociate-a-route-table---commands"></a>Zrušit přidružení směrovací tabulky – příkazy

* Azure CLI: [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Odstranit tabulku směrování

Směrovací tabulka je přidružen k žádné podsítě, nelze odstranit. [Zrušit přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulku ze všech podsítí, než se pokusíte odstranit.

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte **...**  na pravé straně směrovací tabulka, kterou chcete odstranit.
1. Vyberte **odstranit**a pak vyberte **Ano**.

### <a name="delete-a-route-table---commands"></a>Odstranit tabulku směrování – příkazy

* Azure CLI: [az sítě route-table delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Vytvoření trasy

Platí omezení na tom, kolik tras ve směrovací tabulce můžete vytvořit pro jedno umístění a předplatném Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte ze seznamu, který chcete přidat trasu k směrovací tabulky.
1. Vyberte **trasy**v části **nastavení**.
1. Vyberte **+ Přidat**.
1. Zadejte jedinečný **název** trasy do směrovací tabulky.
1. Zadejte **předponu adresy**, v notaci CIDR, které chcete směrovat provoz do. Předpona, která nelze zkopírovat do víc tras ve směrovací tabulce, i když předpona, která může být v rámci jinou předponu. Například pokud jste definovali jako předponu v jednu trasu 10.0.0.0/16, můžete definovat další trasu s předponou adresy 10.0.0.0/24. Azure vybírá trasu pro provoz na základě nejdelší shody předpony. Další informace o tom, jak Azure vybere trasách najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Vyberte **typem dalšího segmentu směrování**. Podrobný popis všech typů dalších segmentů směrování, naleznete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
1. Zadejte IP adresu pro **adresa dalšího segmentu**. Můžete pouze zadat adresu, pokud jste vybrali *virtuální zařízení* pro **typem dalšího segmentu směrování**.
1. Vyberte **OK**.

### <a name="create-a-route---commands"></a>Vytvořit trasu – příkazy

* Azure CLI: [vytvořit az network route-table trasy](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Zobrazit trasy

Směrovací tabulka obsahuje nula nebo více tras. Další informace o informace při zobrazení trasy, naleznete v tématu [Přehled směrování](virtual-networks-udr-overview.md).

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte ze seznamu, který chcete zobrazit trasy pro směrovací tabulky.
1. Vyberte **trasy** pod **nastavení**.

### <a name="view-routes---commands"></a>Zobrazit trasy – příkazy

* Azure CLI: [az network route-table trasy seznamu](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte směrovací tabulku, kterou chcete zobrazit podrobnosti o trasu pro.
1. Vyberte **trasy**.
1. Vyberte trasy, které chcete zobrazit podrobnosti.

### <a name="view-details-of-a-route---commands"></a>Zobrazit podrobnosti o postupu – příkazy

* Azure CLI: [az sítě route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Změna trasy

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Směrovací tabulka, kterou chcete změnit trasu pro výběr.
1. Vyberte **trasy**.
1. Vyberte trasy, které chcete změnit.
1. Změnit existující nastavení své nové nastavení a pak vyberte **Uložit**.

### <a name="change-a-route---commands"></a>Změnit směrování – příkazy

* Azure CLI: [az sítě route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Odstranit trasu

1. Do vyhledávacího pole v horní části portálu zadejte *směrovací tabulky* do vyhledávacího pole. Když **směrovací tabulky** nezobrazí ve výsledcích hledání, vyberte ji.
1. Směrovací tabulka, kterou chcete odstranit trasu pro výběr.
1. Vyberte **trasy**.
1. Seznam tras, vyberte **...**  na pravé straně trasy, která chcete odstranit.
1. Vyberte **odstranit**a pak vyberte **Ano**.

### <a name="delete-a-route---commands"></a>Odstranit cestu – příkazy

* Azure CLI: [az sítě route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Zobrazení efektivních tras

Efektivní trasy pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací směrovacích tabulek, které jste vytvořili, výchozí trasy a všechny trasy rozšířena z místní sítě přes protokol BGP prostřednictvím brány virtuální sítě Azure. Principy efektivní trasy pro síťové rozhraní je užitečné při řešení potíží se směrováním. Můžete zobrazit efektivní trasy pro každé síťové rozhraní, který je připojený k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, které chcete zobrazit efektivní trasy pro. Pokud si nejste jisti názvem virtuálního počítače, zadejte *virtuálních počítačů* do vyhledávacího pole. Když **virtuálních počítačů** ve výsledcích hledání se zobrazí, vyberte ho a vyberte virtuální počítač ze seznamu.
1. Vyberte **sítě** pod **nastavení**.
1. Vyberte název síťového rozhraní.
1. Vyberte **efektivní trasy** pod **podpora a řešení potíží**.
1. Projděte si seznam efektivní trasy a určete, jestli pro místo, kam chcete směrovat provoz do existuje správné směrování. Další informace o typy dalších segmentů směrování, které se zobrazí v tomto seznamu v [Přehled směrování](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Zobrazení efektivních tras – příkazy

* Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Ověřit směrování mezi dva koncové body služby

Můžete určit typ dalšího segmentu směrování mezi virtuálním počítačem a IP adresu jiného prostředku Azure, místnímu prostředku nebo prostředku na Internetu. Určení Azure směrování je užitečné při řešení potíží se směrováním. Tento úkol provést, musíte mít existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ho provedením kroků v [vytvoření instance Network Watcheru](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Do vyhledávacího pole v horní části portálu zadejte *network watcheru* do vyhledávacího pole. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
1. Vyberte **směrování** pod **nástroje pro diagnostiku sítě**.
1. Vyberte vaše **předplatné** a **skupiny prostředků** zdrojového virtuálního počítače, kterou chcete ověřit směrování.
1. Vyberte **virtuálního počítače**, **síťové rozhraní** připojené k virtuálnímu počítači a **IP adresa zdroje** přiřazené k síťovému rozhraní, který chcete ověřit směrování.
1. Zadejte **cílová IP adresa** , kterou chcete ověřit směrování.
1. Vyberte **směrování**.
1. Po krátkém čekání se informace vrácené, které sděluje, typ dalšího segmentu směrování a ID trasy, která směruje provoz. Další informace o typy dalších segmentů směrování, které vidíte vrácené v [Přehled směrování](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Ověřit směrování mezi dva koncové body – příkazy

* Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Oprávnění

K provádění úloh na směrovací tabulky a trasy, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

| Akce                                                          |   Name                                                  |
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

* Vytvoření směrovací tabulky pomocí [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)<br>
* Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
