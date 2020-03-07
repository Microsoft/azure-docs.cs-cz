---
title: Vytvoření, změna nebo odstranění tabulky směrování Azure
titlesuffix: Azure Virtual Network
description: Naučte se, jak vytvořit, změnit nebo odstranit směrovací tabulku.
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
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356656"
---
# <a name="create-change-or-delete-a-route-table"></a>Vytvoření, změna nebo odstranění směrovací tabulky

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud chcete změnit některý z výchozích směrování Azure, provedete to tak, že vytvoříte směrovací tabulku. Pokud s směrováním v rámci virtuálních sítí začínáte, můžete o něm získat další informace v tématu [Přehled směrování](virtual-networks-udr-overview.md) nebo dokončení [kurzu](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

* Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).<br>
* Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.<br>
* Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.<br>
* Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, je také potřeba spustit `az login` a vytvořit připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazena k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Existuje omezení počtu směrovacích tabulek, které můžete vytvořit pro každé umístění a předplatné Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V levém horním rohu portálu vyberte **+ vytvořit prostředek**.
1. Vyberte **sítě**a pak vybrat **směrovací tabulku**.
1. Zadejte **název** směrovací tabulky, vyberte své **předplatné**, vytvořte novou **skupinu prostředků**nebo vyberte existující skupinu prostředků, vyberte **umístění**a pak vyberte **vytvořit**. Pokud plánujete přidružit směrovací tabulku k podsíti ve virtuální síti, která je připojená k vaší místní síti prostřednictvím brány VPN, a zakažte **šíření tras brány virtuální sítě**, vaše místní trasy se nerozšíří na síťová rozhraní v podsíti.

### <a name="create-route-table---commands"></a>Vytvoření směrovací tabulky – příkazy

* Azure CLI: [AZ Network Route-Table Create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Zobrazit směrovací tabulky

Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji. Zobrazí se seznam směrovacích tabulek, které existují ve vašem předplatném.

### <a name="view-route-table---commands"></a>Zobrazit směrovací tabulku – příkazy

* Azure CLI: [AZ Network Route-Table list](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Zobrazit podrobnosti směrovací tabulky

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. V seznamu vyberte směrovací tabulku, pro kterou chcete zobrazit podrobnosti. V části **Nastavení**můžete zobrazit **trasy** v tabulce směrování a v **podsítích** , ke kterým je tabulka směrování přidružená.
1. Další informace o běžných nastaveních Azure najdete v následujících informacích:

    * [Protokol aktivit](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Řízení přístupu (IAM)](../role-based-access-control/overview.md)<br>
    * [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Počtu](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Skript Automation](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Zobrazení podrobností směrovací tabulky – příkazy

* Azure CLI: [AZ Network Route-Table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Změna směrovací tabulky

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. Vyberte směrovací tabulku, kterou chcete změnit. Nejběžnější změny jsou [přidávání](#create-a-route) a [odebírání](#delete-a-route) tras a [přiřazování](#associate-a-route-table-to-a-subnet) směrovacích tabulek k nebo [ruší](#dissociate-a-route-table-from-a-subnet) směrovacích tabulek z podsítí.

### <a name="change-a-route-table---commands"></a>Změna směrovací tabulky – příkazy

* Azure CLI: [AZ Network Route-Table Update](/cli/azure/network/route-table/route)<br>
* PowerShell: [set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

K podsíti může být přidružena nula nebo jedna směrovací tabulka. Směrovací tabulka může být přidružená k nule nebo několika podsítím. Vzhledem k tomu, že tabulky směrování nejsou přidruženy k virtuálním sítím, je nutné přiřadit směrovací tabulku ke každé podsíti, ke které chcete směrovací tabulku přidružit. Veškerý provoz, který opouští podsíť, se směruje na základě tras, které jste vytvořili v rámci směrovacích tabulek, [výchozích tras](virtual-networks-udr-overview.md#default)a tras, které jsou šířené z místní sítě, pokud je virtuální síť připojená k bráně virtuální sítě Azure (EXPRESSROUTE nebo VPN). Směrovací tabulku můžete přidružit jenom k podsítím ve virtuálních sítích, které existují ve stejném umístění Azure a předplatném, jako je tabulka směrování.

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
1. V seznamu vyberte virtuální síť, která obsahuje podsíť, ke které chcete přidružit směrovací tabulku.
1. V části **Nastavení**vyberte **podsítě** .
1. Vyberte podsíť, ke které chcete přidružit směrovací tabulku.
1. Vyberte **směrovací tabulka**, vyberte směrovací tabulku, kterou chcete přidružit k podsíti, a pak vyberte **Uložit**.

Pokud je vaše virtuální síť připojená k Azure VPN gateway, nepřidružujte k [podsíti brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) směrovací tabulku, která má směrování s cílem 0.0.0.0/0. Mohli byste tím bráně znemožnit správné fungování. Další informace o použití adresy 0.0.0.0/0 v trase najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Přidružení příkazů směrovací tabulky

* Azure CLI: [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušení přidružení směrovací tabulky z podsítě

Když zrušíte přidružení směrovací tabulky z podsítě, Azure směruje provoz na základě [výchozích tras](virtual-networks-udr-overview.md#default).

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
1. Vyberte virtuální síť, která obsahuje podsíť, ze které chcete zrušit přidružení tabulky směrování.
1. V části **Nastavení**vyberte **podsítě** .
1. Vyberte podsíť, ze které chcete zrušit přidružení tabulky směrování.
1. Vyberte **směrovací tabulka**, vyberte **žádná**a pak vyberte **Uložit**.

### <a name="dissociate-a-route-table---commands"></a>Zrušení přidružení příkazů směrovací tabulky

* Azure CLI: [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Odstranění směrovací tabulky

Pokud je směrovací tabulka přidružená k některým podsítím, nelze ji odstranit. Před pokusem o odstranění zrušte [přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulky ze všech podsítí.

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. Na pravé straně směrovací tabulky, kterou chcete odstranit, vyberte **...**
1. Vyberte **Odstranit**a pak vyberte **Ano**.

### <a name="delete-a-route-table---commands"></a>Odstranění směrovací tabulky – příkazy

* Azure CLI: [AZ Network Route-Table Delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Vytvoření trasy

Existuje omezení, kolik tras na směrovací tabulce může vytvořit pro každé umístění a předplatné Azure. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. V seznamu vyberte směrovací tabulku, do které chcete přidat trasu.
1. V části **Nastavení**vyberte **trasy**.
1. Vyberte **+ Přidat**.
1. Zadejte jedinečný **název** trasy v tabulce směrování.
1. Zadejte **předponu adresy**v notaci CIDR, do které chcete směrovat provoz. Předpona nemůže být duplikována ve více než jedné trase v tabulce směrování, i když předpona může být v jiné předponě. Pokud jste například v jedné trase definovali 10.0.0.0/16 jako předponu, můžete i nadále definovat jinou trasu s předponou adresy 10.0.0.0/24. Azure vybere trasu pro provoz na základě nejdelší shody předpony. Další informace o tom, jak Azure vybírá trasy, najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Vyberte **typ dalšího segmentu směrování**. Podrobný popis všech typů dalších segmentů směrování najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
1. Zadejte IP adresu pro **adresu dalšího segmentu směrování**. Pokud jste pro **typ dalšího segmentu směrování**vybrali *virtuální zařízení* , můžete zadat jenom adresu.
1. Vyberte **OK**.

### <a name="create-a-route---commands"></a>Vytvoření příkazů route

* Azure CLI: [AZ Network Route-Table Route Create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Zobrazit trasy

Směrovací tabulka obsahuje nula nebo více tras. Další informace o informacích uvedených při zobrazení tras najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. V seznamu vyberte směrovací tabulku, pro kterou chcete zobrazit trasy.
1. V části **Nastavení**vyberte **trasy** .

### <a name="view-routes---commands"></a>Zobrazit trasy – příkazy

* Azure CLI: [AZ Network Route-Table Route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. Vyberte směrovací tabulku, pro kterou chcete zobrazit podrobnosti o trase.
1. Vyberte **trasy**.
1. Vyberte trasu, pro kterou chcete zobrazit podrobnosti.

### <a name="view-details-of-a-route---commands"></a>Zobrazit podrobnosti o příkazech Route

* Azure CLI: [AZ Network Route-Table Route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Změna trasy

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. Vyberte směrovací tabulku, pro kterou chcete změnit trasu.
1. Vyberte **trasy**.
1. Vyberte trasu, kterou chcete změnit.
1. Změňte existující nastavení na nové nastavení a pak vyberte **Uložit**.

### <a name="change-a-route---commands"></a>Změna směrování – příkazy

* Azure CLI: [AZ Network Route-Table Route Update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Odstranění trasy

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *směrovací tabulky* . Pokud se ve výsledcích hledání zobrazí **směrovací tabulky** , vyberte ji.
1. Vyberte směrovací tabulku, pro kterou chcete odstranit trasu.
1. Vyberte **trasy**.
1. V seznamu tras vyberte **...** na pravé straně trasy, kterou chcete odstranit.
1. Vyberte **Odstranit**a pak vyberte **Ano**.

### <a name="delete-a-route---commands"></a>Odstranění příkazů route

* Azure CLI: [AZ Network Route-Table Route Delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Zobrazit efektivní trasy

Efektivní trasy pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací směrovacích tabulek, které jste vytvořili, výchozích tras Azure a všech tras, které se šíří z místních sítí přes protokol BGP prostřednictvím brány virtuální sítě Azure. Princip efektivních tras pro síťové rozhraní je užitečné při řešení potíží s směrováním. Můžete si prohlédnout efektivní trasy pro jakékoli síťové rozhraní, které je připojené k běžícímu virtuálnímu počítači.

1. Do vyhledávacího pole v horní části portálu zadejte název virtuálního počítače, pro který chcete zobrazit efektivní trasy. Pokud neznáte název virtuálního počítače, do vyhledávacího pole zadejte *virtuální počítače* . Když se **virtuální počítače** zobrazí ve výsledcích hledání, vyberte ji a v seznamu vyberte virtuální počítač.
1. V části **Nastavení**vyberte **sítě** .
1. Vyberte název síťového rozhraní.
1. V části **Podpora a řešení potíží**vyberte **platné trasy** .
1. Projděte si seznam efektivních tras, abyste zjistili, jestli existuje správná trasa pro umístění, do kterého chcete směrovat provoz. Přečtěte si další informace o typech dalších segmentů směrování, které vidíte v tomto seznamu v tématu [Přehled směrování](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Zobrazení efektivních tras – příkazy

* Azure CLI: [AZ Network nic show-efektivní-Route-Table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Ověření směrování mezi dvěma koncovými body

Můžete určit typ dalšího segmentu směrování mezi virtuálním počítačem a IP adresou jiného prostředku Azure, místního prostředku nebo prostředku na internetu. Určení směrování Azure je užitečné při řešení potíží s směrováním. K dokončení této úlohy musíte mít existující Network Watcher. Pokud nemáte existující Network Watcher, vytvořte ji provedením kroků v tématu [vytvoření instance Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Do vyhledávacího pole v horní části portálu zadejte *sledovací proces sítě* do vyhledávacího pole. Jakmile se služba**Network Watcher** zobrazí ve výsledcích hledání, vyberte ji.
1. V části nástroje pro **diagnostiku sítě**vyberte **Další segment směrování** .
1. Vyberte **předplatné** a **skupinu prostředků** zdrojového virtuálního počítače, ze kterého chcete ověřit směrování.
1. Vyberte **virtuální počítač**, **síťové rozhraní** připojené k virtuálnímu počítači a **zdrojovou IP adresu** přiřazenou k síťovému rozhraní, ze kterého chcete ověřit směrování.
1. Zadejte **cílovou IP adresu** , na kterou chcete směrování ověřit.
1. Vyberte **Další segment směrování**.
1. Po krátkém čekání se vrátí informace, které vám oznámí typ dalšího segmentu směrování a ID trasy směrované na provoz. Přečtěte si další informace o typech dalších segmentů směrování vrácených v tématu [Přehled směrování](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Ověření směrování mezi dvěma koncovými body – příkazy

* Azure CLI: [AZ Network sledovacího procesu show-Next-Hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy s směrovacími tabulkami a trasami, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

| Akce                                                          |   Název                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/Read                              |   Čtení směrovací tabulky                                    |
| Microsoft. Network/routeTables/Write                             |   Vytvoří nebo aktualizuje směrovací tabulku.                        |
| Microsoft. Network/routeTables/DELETE                            |   Odstranění směrovací tabulky                                  |
| Microsoft. Network/routeTables/JOIN/Action                       |   Přidružení směrovací tabulky k podsíti                   |
| Microsoft. Network/routeTables/Routes/Read                       |   Čtení trasy                                          |
| Microsoft. Network/routeTables/Routes/Write                      |   Vytvořit nebo aktualizovat trasu                              |
| Microsoft. Network/routeTables/Routes/DELETE                     |   Odstranění trasy                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Získat platnou směrovací tabulku pro síťové rozhraní |
| Microsoft.Network/networkWatchers/nextHop/action                |   Získá další segment směrování z virtuálního počítače.                           |

## <a name="next-steps"></a>Další kroky

* Vytvoření směrovací tabulky pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo používání [šablon Azure správce prostředků](template-samples.md)<br>
* Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
