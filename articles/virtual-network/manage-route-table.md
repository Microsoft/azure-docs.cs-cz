---
title: Vytvoření, změna nebo odstranění tabulky směrování Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak vytvořit, změnit nebo odstranit směrovací tabulku.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247057"
---
# <a name="create-change-or-delete-a-route-table"></a>Vytvoření, změna nebo odstranění směrovací tabulky

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud chcete změnit některé z výchozího směrování Azure, uděláte to vytvořením směrovací tabulky. Pokud s směrováním ve virtuálních sítích tečujete, můžete se o tom dozvědět víc ve [směrování provozu virtuální sítě](virtual-networks-udr-overview.md) nebo vyplněním [kurzu](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Než začnete

Pokud ho nemáte, nastavte si účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Před zahájením kroků v této části tohoto článku proveďte jeden z těchto úkolů:

- **Uživatelé portálu:** Přihlaste se na [portál Azure](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu:** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spusťte PowerShell z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě prohlížeče Azure Cloud Shell najděte rozevírací seznam **Select environment** a pak zvolte **PowerShell,** pokud ještě není vybraný.

    Pokud používáte PowerShell místně, použijte modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Spusťte `Connect-AzAccount` také k vytvoření připojení s Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI):** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash)nebo spusťte rozhraní příkazového řádku z vašeho počítače. Pokud používáte azure CLI verze 2.0.31 nebo novější, pokud používáte azure cli místně. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Spusťte `az login` také k vytvoření připojení s Azure.

Účet, ke kterému se přihlásíte nebo se připojíte k Azure, musí být přiřazen k [roli přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Existuje limit na to, kolik trasových tabulek můžete vytvořit na umístění azure a předplatné. Podrobnosti najdete [v tématu Omezení sítě – Správce prostředků Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte *tabulku Trasa*. Když se **tabulka Trasa** zobrazí ve výsledcích hledání, vyberte ji.

1. Na stránce **Tabulka trasa** vyberte **Vytvořit**.

1. V dialogovém okně **Vytvořit tabulku tras:**

    1. Zadejte **název** směrovací tabulky.
    1. Zvolte **předplatné**.
    1. Vyberte existující **skupinu prostředků** nebo vyberte **Vytvořit nový** a vytvořte novou skupinu prostředků.
    1. Zvolte **Umístění**.
    1. Pokud plánujete přidružit směrovací tabulku k podsíti ve virtuální síti, která je připojená k místní síti prostřednictvím brány VPN, a nechcete šířit místní trasy do síťových rozhraní v podsíti, nastavte **šíření trasy brány virtuální sítě** na **Zakázáno**.

1. Chcete-li vytvořit novou směrovací tabulku, vyberte **Vytvořit.**

### <a name="create-route-table---commands"></a>Vytvořit směrovací tabulku – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [Nová-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Zobrazit tabulky tras

Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou virtuální síť. Vyhledejte a vyberte **možnost Směrovat tabulky**. V seznamu jsou uvedeny směrovací tabulky, které existují ve vašem předplatném.

### <a name="view-route-table---commands"></a>Zobrazit směrovací tabulku - příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [seznam směrovací tabulky sítě az](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Zobrazení podrobností o tabulce tras

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou virtuální síť. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací tabulky vyberte tabulku postupu, pro kterou chcete zobrazit podrobnosti.

1. Na stránce tabulky trasy v části **Nastavení**zobrazte **trasy** v tabulce tras nebo **v podsítích,** ke které je tabulka tras přidružena.

Další informace o běžných nastaveních Azure najdete v následujících informacích:

- [Protokol aktivit](../azure-monitor/platform/platform-logs-overview.md)
- [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
- [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automatizační skript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Zobrazit podrobnosti o tabulce tras – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť ová tabulkový spoj](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Změna tabulky tras

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou virtuální síť. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací tabulky vyberte tabulku postupu, kterou chcete změnit.

Nejběžnější změny jsou [přidání](#create-a-route) tras, [odebrání](#delete-a-route) tras, [přidružení](#associate-a-route-table-to-a-subnet) tabulek tras k podsítí [mj.](#dissociate-a-route-table-from-a-subnet)

### <a name="change-a-route-table---commands"></a>Změna směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [aktualizace tabulky tras az](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Volitelně můžete přiřadit směrovací tabulku k podsíti. Tabulka tras může být přidružena k nule nebo více podsítím. Vzhledem k tomu, že směrovací tabulky nejsou přidruženy k virtuálním sítím, je nutné přidružit směrovací tabulku ke každé podsíti, ke které má být směrovací tabulka přidružena. Azure směruje veškerý provoz opouštějící podsíť na základě tras, které jste vytvořili v rámci směrovacích tabulek, [výchozích tras](virtual-networks-udr-overview.md#default)a tras šířených z místní sítě, pokud je virtuální síť připojená k bráně virtuální sítě Azure (ExpressRoute nebo VPN). Směrovací tabulku můžete přidružit jenom k podsítím ve virtuálních sítích, které existují ve stejném umístění Azure a předplatném jako směrovací tabulka.

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou virtuální síť. Vyhledejte a vyberte **virtuální sítě**.

1. V seznamu virtuálních sítí vyberte virtuální síť obsahující podsíť, ke které chcete přidružit směrovací tabulku.

1. V řádku nabídek virtuální sítě zvolte **Podsítě**.

1. Vyberte podsíť, ke které chcete přidružit směrovací tabulku.

1. V **tabulce Trasa**vyberte směrovací tabulku, kterou chcete k podsíti přidružit.

1. Vyberte **Uložit**.

Pokud je vaše virtuální síť připojená k bráně Azure VPN, nepřidružujte směrovací tabulku k [podsíti brány,](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) která obsahuje trasu s cílem *0.0.0.0/0*. Mohli byste tím bráně znemožnit správné fungování. Další informace o použití *0.0.0.0/0* v trase naleznete v [tématu Směrování provozu virtuální sítě](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Přidružení směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Odložky směrovací tabulky od podsítě

Když odpojíte směrovací tabulku od podsítě, Azure směruje provoz na základě [výchozích tras](virtual-networks-udr-overview.md#default).

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svou virtuální síť. Vyhledejte a vyberte **virtuální sítě**.

1. V seznamu virtuálních sítí vyberte virtuální síť obsahující podsíť, ze které chcete oddělit směrovací tabulku.

1. V řádku nabídek virtuální sítě zvolte **Podsítě**.

1. Vyberte podsíť, od které chcete trasovou tabulku oddělit.

1. V **tabulce Trasa**zvolte **Žádný**.

1. Vyberte **Uložit**.

### <a name="dissociate-a-route-table---commands"></a>Oddělit směrovací tabulku – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Odstranění směrovací tabulky

Směrovací tabulku, která je přidružena k žádným podsítím, nelze odstranit. Před pokusem o její odstranění [odkreslujte](#dissociate-a-route-table-from-a-subnet) směrovací tabulku od všech podsítí.

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své směrovací tabulky. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací chodníčka vyberte směrovací tabulku, kterou chcete odstranit.

1. Vyberte **Odstranit**a pak v potvrzovacím dialogovém okně vyberte **Ano.**

### <a name="delete-a-route-table---commands"></a>Odstranění směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síťová trasovací tabulka odstranit](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Odebrat-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Vytvoření trasy

Existuje limit na to, kolik tras na tabulku tras můžete vytvořit na umístění Azure a předplatné. Podrobnosti najdete [v tématu Omezení sítě – Správce prostředků Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své směrovací tabulky. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací chodvitu vyberte směrovací tabulku, do které chcete přidat trasu.

1. Na řádku nabídek tabulky tras zvolte **Trasy** > **přidat**.

1. Zadejte jedinečný **název trasy** v tabulce tras.

1. Zadejte **předponu Adresy**do notace CIDR beztřídního směrování mezi doménami, do kterého chcete směrovat provoz. Předponu nelze duplikovat ve více než jedné trase v rámci směrovací tabulky, i když předpona může být v rámci jiné předpony. Pokud jste například definovali *10.0.0.0/16* jako předponu v jedné trase, můžete stále definovat jinou trasu s předponou adresy *10.0.0.0/22.* Azure vybere trasu pro provoz na základě nejdelší shody předpony. Další informace najdete v [tématu Jak Azure vybírá trasu](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Zvolte **typ dalšího směrování**. Další informace o dalších typech směrování naleznete [v tématu Směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

1. Pokud jste zvolili **typ virtuálního zařízení**typu Další **směrování** , zadejte ip adresu pro další **adresu směrování**.

1. Vyberte **OK**.

### <a name="create-a-route---commands"></a>Vytvoření trasy – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [Nový-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Zobrazit trasy

Směrovací tabulka obsahuje nulu nebo více tras. Další informace o informacích uvedených při prohlížení tras naleznete v [tématu Směrování provozu ve virtuálních sítích](virtual-networks-udr-overview.md).

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své směrovací tabulky. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací chodníček vyberte tabulku tras, pro kterou chcete zobrazit trasy.

1. V řádku nabídek tabulky tras zvolte **Trasy,** chcete-li zobrazit seznam tras.

### <a name="view-routes---commands"></a>Zobrazit trasy – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az seznam trasových tabulek sítě az](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Získat-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své směrovací tabulky. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací tabulky vyberte tabulku tras obsahující trasu, pro kterou chcete zobrazit podrobnosti.

1. V řádku nabídek tabulky tras zvolte **Trasy,** chcete-li zobrazit seznam tras.

1. Vyberte trasu, na které chcete zobrazit podrobnosti.

### <a name="view-details-of-a-route---commands"></a>Zobrazit podrobnosti o trase – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť trasovací tabulka zobrazit](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Získat-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Změna trasy

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své směrovací tabulky. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací tabulky zvolte tabulku tras obsahující trasu, kterou chcete změnit.

1. V řádku nabídek tabulky tras zvolte **Trasy,** chcete-li zobrazit seznam tras.

1. Vyberte trasu, kterou chcete změnit.

1. Změňte stávající nastavení na nové nastavení a pak vyberte **Uložit**.

### <a name="change-a-route---commands"></a>Změna trasy – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [aktualizace trasy az network route-table](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Odstranění trasy

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své směrovací tabulky. Vyhledejte a vyberte **možnost Směrovat tabulky**.

1. V seznamu směrovací tabulky zvolte tabulku postupu obsahující trasu, kterou chcete odstranit.

1. V řádku nabídek tabulky tras zvolte **Trasy,** chcete-li zobrazit seznam tras.

1. Vyberte trasu, kterou chcete odstranit.

1. Vyberte **Odstranit**a v potvrzovacím dialogovém okně vyberte **Ano.**

### <a name="delete-a-route---commands"></a>Odstranění trasy – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť trasovací tabulka trasa odstranit](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Odebrat-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Zobrazení efektivních tras

Efektivní trasy pro každé síťové rozhraní připojené k virtuálním počítači jsou kombinací směrovacích tabulek, které jste vytvořili, výchozích tras Azure a všech tras šířených z místních sítí prostřednictvím protokolu BGP (Border Gateway Protocol) prostřednictvím virtuální sítě Azure Brána. Pochopení efektivní trasy pro síťové rozhraní je užitečné při řešení problémů směrování. Můžete zobrazit efektivní trasy pro libovolné síťové rozhraní, které je připojené ke spuštěnému virtuálnímu virtuálnímu síti.

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte virtuální počítače. Vyhledejte a vyberte **virtuální počítače**.

1. V seznamu virtuálních strojů vyberte virtuální počítač, pro který chcete zobrazit efektivní trasy.

1. Na řádku nabídek virtuálního virtuálního zařízení zvolte **Networking**.

1. Vyberte název síťového rozhraní.

1. V řádku nabídek síťového rozhraní vyberte **možnost Efektivní trasy**.

1. Zkontrolujte seznam efektivních tras a zjistěte, zda existuje správná trasa pro místo, kam chcete směrovat provoz. Další informace o dalších typech směrování, které se zobrazí v tomto seznamu, naleznete ve [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Zobrazit efektivní trasy – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az síť nic show-efektivní-trasa-tabulka](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Ověření směrování mezi dvěma koncovými body

Můžete určit další typ směrování mezi virtuálním počítačem a IP adresou jiného prostředku Azure, místního prostředku nebo prostředku v Internetu. Určení směrování Azure je užitečné při řešení problémů se směrováním. Chcete-li dokončit tento úkol, musíte mít existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte jej provedením kroků v [instanci Vytvořit sledovací modul sítě](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte své sledovací procesy sítě. Vyhledejte a vyberte **sledování sítě**.

1. V řádku nabídek sledování sítě zvolte **Další směrování**.

1. V modulu **Sledování sítě | Další stránka směrování:**

    1. Zvolte **předplatné** a **skupinu prostředků** zdrojového virtuálního počítače, ze kterého chcete ověřit směrování.

    1. Zvolte **virtuální počítač** a síťové **rozhraní,** které je připojené k virtuálnímu počítači.
    
    1. Zadejte **zdrojovou adresu IP** přiřazenou síťovému rozhraní, ze kterého chcete ověřit směrování.

    1. Zadejte **cílovou ADRESU IP,** na kterou chcete ověřit směrování.

1. Vyberte **další směrování**.

Po krátkém čekání vám Azure sdělí další typ směrování a ID trasy, která směrovala provoz. Další informace o dalších typech směrování, které se zobrazí vrácené ve [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Ověření směrování mezi dvěma koncovými body – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Oprávnění

Chcete-li provést úkoly na trasových tabulkách a trasách, musí být váš účet přiřazen k [roli přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v následující tabulce:

| Akce                                                          |   Name (Název)                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Čtení tabulky tras                                    |
| Microsoft.Network/routeTables/write                             |   Vytvoření nebo aktualizace tabulky postupu                        |
| Microsoft.Network/routeTables/delete                            |   Odstranění směrovací tabulky                                  |
| Microsoft.Network/routeTables/join/action                       |   Přidružení směrovací tabulky k podsíti                   |
| Microsoft.Network/routeTables/routes/read                       |   Čtení trasy                                          |
| Microsoft.Network/routeTables/routes/write                      |   Vytvoření nebo aktualizace trasy                              |
| Microsoft.Network/routeTables/routes/delete                     |   Odstranění trasy                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Získání efektivní směrovací tabulky pro síťové rozhraní |
| Microsoft.Network/networkWatchers/nextHop/action                |   Získá další směrování z virtuálního virtuálního mísy                           |

## <a name="next-steps"></a>Další kroky

- Vytvoření směrovací tabulky pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo šablon Azure [Resource Manageru](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
