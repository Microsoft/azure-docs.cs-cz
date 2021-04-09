---
title: Vytvoření, změna nebo odstranění tabulky směrování Azure
titlesuffix: Azure Virtual Network
description: Naučte se, jak najít informace o směrování provozu virtuální sítě a jak vytvořit, změnit nebo odstranit směrovací tabulku.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 642a9a9f798492d85ee2a9784a1fe5ad4f854d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574122"
---
# <a name="create-change-or-delete-a-route-table"></a>Vytvoření, změna nebo odstranění směrovací tabulky

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud chcete změnit některý z výchozích směrování Azure, provedete to tak, že vytvoříte směrovací tabulku. Pokud s směrováním ve virtuálních sítích začínáte, můžete se o něm dozvědět víc v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md) nebo v tomto [kurzu](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Než začnete

Pokud ho nemáte, nastavte účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Pak před zahájením kroků v jakékoli části tohoto článku dokončete jednu z těchto úloh:

- **Uživatelé portálu**: Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo z počítače spusťte PowerShell. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě Azure Cloud Shell prohlížeč Najděte rozevírací seznam **Vybrat prostředí** a zvolte **PowerShell** , pokud ještě není vybraný.

    Pokud používáte PowerShell místně, použijte Azure PowerShell modul verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Také spusťte `Connect-AzAccount` pro vytvoření připojení k Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI)**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spusťte CLI z počítače. Pokud používáte Azure CLI místně, použijte Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Také spusťte `az login` pro vytvoření připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k [roli Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Existuje omezení počtu směrovacích tabulek, které můžete vytvořit pro každé umístění a předplatné Azure. Podrobnosti najdete v tématu [omezení sítě – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. V nabídce webu [Azure Portal](https://portal.azure.com) nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

1. Do vyhledávacího pole zadejte *směrovací tabulku*. Pokud se ve výsledcích hledání zobrazí **tabulka směrování** , vyberte ji.

1. Na stránce **směrovací tabulka** vyberte **vytvořit**.

1. V dialogovém okně **vytvořit tabulku směrování** :

    1. Zadejte **název** směrovací tabulky.
    1. Vyberte své **předplatné**.
    1. Zvolte existující **skupinu prostředků** nebo vyberte **vytvořit novou** a vytvořte novou skupinu prostředků.
    1. Vyberte **umístění**.
    1. Pokud plánujete přidružit směrovací tabulku k podsíti ve virtuální síti, která je připojená k vaší místní síti prostřednictvím brány VPN, a nechcete rozšířit vaše místní trasy na síťová rozhraní v podsíti, nastavte **šíření trasy brány virtuální sítě** na **zakázané**.

1. Vyberte **vytvořit** a vytvořte novou směrovací tabulku.

### <a name="create-route-table---commands"></a>Vytvoření směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Zobrazit směrovací tabulky

Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**. Zobrazí se seznam směrovacích tabulek, které existují ve vašem předplatném.

### <a name="view-route-table---commands"></a>Zobrazit směrovací tabulku – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table list](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Zobrazit podrobnosti směrovací tabulky

1. Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku, pro kterou chcete zobrazit podrobnosti.

1. Na stránce směrovací tabulka v části **Nastavení** zobrazte **trasy** v tabulce směrování nebo v **podsítích** , ke kterým je tabulka směrování přidružená.

Další informace o běžných nastaveních Azure najdete v následujících informacích:

- [Protokol aktivit](../azure-monitor/essentials/platform-logs-overview.md)
- [Řízení přístupu (IAM)](../role-based-access-control/overview.md)
- [Značky](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zámky](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automatizační skript](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Zobrazení podrobností směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Změna směrovací tabulky

1. Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku, kterou chcete změnit.

Nejběžnějšími změnami je [Přidání](#create-a-route) tras, [Odebrání](#delete-a-route) tras, přidružení směrovacích tabulek k podsítím nebo [zrušení](#associate-a-route-table-to-a-subnet) [přidružení](#dissociate-a-route-table-from-a-subnet) tabulek směrování z podsítí.

### <a name="change-a-route-table---commands"></a>Změna směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Update](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Volitelně můžete k podsíti přidružit směrovací tabulku. Směrovací tabulka může být přidružená k nule nebo více podsítím. Vzhledem k tomu, že směrovací tabulky nejsou přidružené k virtuálním sítím, je třeba přidružit směrovací tabulku ke každé podsíti, ke které chcete směrovací tabulku přidružit. Azure směruje veškerý provoz, který opouští podsíť, na základě tras, které jste vytvořili v rámci směrovacích tabulek, [výchozích tras](virtual-networks-udr-overview.md#default)a tras, které jsou šířené z místní sítě, pokud je virtuální síť připojená k bráně virtuální sítě Azure (EXPRESSROUTE nebo VPN). Směrovací tabulku můžete přidružit jenom k podsítím ve virtuálních sítích, které existují ve stejném umístění Azure a předplatném, jako je tabulka směrování.

1. Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

1. V seznamu virtuální síť vyberte virtuální síť, která obsahuje podsíť, ke které chcete přidružit směrovací tabulku.

1. V panelu nabídky virtuální síť vyberte **podsítě**.

1. Vyberte podsíť, ke které chcete přidružit směrovací tabulku.

1. V **tabulce směrování** vyberte směrovací tabulku, kterou chcete přidružit k podsíti.

1. Vyberte **Uložit**.

Pokud je vaše virtuální síť připojená k bráně Azure VPN Gateway, nepřidružte směrovací tabulku k [podsíti brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) , která obsahuje trasu s cílem *0.0.0.0/0*. Mohli byste tím bráně znemožnit správné fungování. Další informace o použití adresy *0.0.0.0/0* v trase najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Přidružení příkazů směrovací tabulky

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Zrušení přidružení směrovací tabulky z podsítě

Když zrušíte přidružení směrovací tabulky z podsítě, Azure směruje provoz na základě [výchozích tras](virtual-networks-udr-overview.md#default).

1. Pokud chcete spravovat svoji virtuální síť, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

1. V seznamu virtuální síť vyberte virtuální síť, která obsahuje podsíť, ze které chcete zrušit přidružení tabulky směrování.

1. V panelu nabídky virtuální síť vyberte **podsítě**.

1. Vyberte podsíť, ze které chcete zrušit přidružení tabulky směrování.

1. V **tabulce směrování** vyberte **None (žádné**).

1. Vyberte **Uložit**.

### <a name="dissociate-a-route-table---commands"></a>Zrušení přidružení příkazů směrovací tabulky

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Odstranění směrovací tabulky

Směrovací tabulku, která je přidružená k žádným podsítím, nelze odstranit. Před pokusem o odstranění zrušte [přidružení](#dissociate-a-route-table-from-a-subnet) směrovací tabulky ze všech podsítí.

1. Pokud chcete spravovat směrovací tabulky, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku, kterou chcete odstranit.

1. Vyberte **Odstranit** a potom v potvrzovacím dialogovém okně vyberte **Ano** .

### <a name="delete-a-route-table---commands"></a>Odstranění směrovací tabulky – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table DELETE](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Vytvoření trasy

Existuje omezení, kolik tras na směrovací tabulce může vytvořit pro každé umístění a předplatné Azure. Podrobnosti najdete v tématu [omezení sítě – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Pokud chcete spravovat směrovací tabulky, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku, do které chcete přidat trasu.

1. Na řádku nabídek směrovací tabulky vyberte **trasy**  >  **Přidat**.

1. Zadejte jedinečný **název trasy** trasy v tabulce směrování.

1. Zadejte **předponu adresy** v zápisu CIDR (Classless Inter-Domain Routing), do které chcete směrovat provoz. Předpona nemůže být duplikována ve více než jedné trase v tabulce směrování, i když předpona může být v jiné předponě. Pokud jste například v jedné trase definovali *10.0.0.0/16* jako předponu, můžete i nadále definovat jinou trasu s předponou adresy *10.0.0.0/22* . Azure vybere trasu pro provoz na základě nejdelší shody předpony. Další informace najdete v tématu [jak Azure vybírá trasu](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Vyberte **typ dalšího segmentu směrování**. Další informace o typech dalších segmentů směrování najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

1. Pokud jste zvolili **typ dalšího** segmentu směrování **virtuálního zařízení**, zadejte IP adresu pro **adresu dalšího segmentu směrování**.

1. Vyberte **OK**.

### <a name="create-a-route---commands"></a>Vytvoření příkazů route

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Zobrazit trasy

Směrovací tabulka obsahuje nula nebo více tras. Další informace o informacích uvedených při zobrazení tras najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

1. Pokud chcete spravovat směrovací tabulky, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku, pro kterou chcete zobrazit trasy.

1. V řádku nabídek směrovací tabulky vyberte možnost **trasy** . zobrazí se seznam tras.

### <a name="view-routes---commands"></a>Zobrazit trasy – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route list](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Zobrazení podrobností o trase

1. Pokud chcete spravovat směrovací tabulky, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku obsahující trasu, pro kterou chcete zobrazit podrobnosti.

1. V řádku nabídek směrovací tabulky vyberte možnost **trasy** . zobrazí se seznam tras.

1. Vyberte trasu, pro kterou chcete zobrazit podrobnosti.

### <a name="view-details-of-a-route---commands"></a>Zobrazit podrobnosti o příkazech Route

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Změna trasy

1. Pokud chcete spravovat směrovací tabulky, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku obsahující trasu, kterou chcete změnit.

1. V řádku nabídek směrovací tabulky vyberte možnost **trasy** . zobrazí se seznam tras.

1. Vyberte trasu, kterou chcete změnit.

1. Změňte existující nastavení na nové nastavení a pak vyberte **Uložit**.

### <a name="change-a-route---commands"></a>Změna směrování – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route Update](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Odstranění trasy

1. Pokud chcete spravovat směrovací tabulky, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **směrovací tabulky**.

1. V seznamu směrovací tabulka Vyberte směrovací tabulku obsahující trasu, kterou chcete odstranit.

1. V řádku nabídek směrovací tabulky vyberte možnost **trasy** . zobrazí se seznam tras.

1. Vyberte trasu, kterou chcete odstranit.

1. Vyberte **Odstranit** a potom v potvrzovacím dialogovém okně vyberte **Ano** .

### <a name="delete-a-route---commands"></a>Odstranění příkazů route

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Route-Table Route DELETE](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Zobrazit efektivní trasy

Efektivní trasy pro každé síťové rozhraní připojené k virtuálnímu počítači jsou kombinací směrovacích tabulek, které jste vytvořili, výchozí trasy Azure a jakékoli trasy šířené z místních sítí prostřednictvím služby Border Gateway Protocol (BGP) prostřednictvím brány virtuální sítě Azure. Princip efektivních tras pro síťové rozhraní je užitečné při řešení potíží s směrováním. Platné trasy můžete zobrazit pro jakékoli síťové rozhraní připojené ke spuštěnému virtuálnímu počítači.

1. Pokud chcete spravovat své virtuální počítače, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **virtuální počítače**.

1. V seznamu virtuální počítač vyberte virtuální počítač, pro který chcete zobrazit efektivní trasy.

1. V řádku nabídek virtuálního počítače vyberte **síť**.

1. Vyberte název síťového rozhraní.

1. V panelu nabídek síťové rozhraní vyberte možnost **efektivní trasy**.

1. Projděte si seznam efektivních tras a zjistěte, zda správná trasa existuje pro umístění, do kterého chcete směrovat provoz. Přečtěte si další informace o typech dalších segmentů směrování, které vidíte v tomto seznamu v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Zobrazení efektivních tras – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network nic show-efektivní-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Ověření směrování mezi dvěma koncovými body

Můžete určit typ dalšího segmentu směrování mezi virtuálním počítačem a IP adresou jiného prostředku Azure, místního prostředku nebo prostředku na internetu. Určení směrování Azure je užitečné při řešení potíží s směrováním. K dokončení této úlohy musíte mít existující sledovací proces sítě. Pokud nemáte existující sledovací proces sítě, vytvořte ho provedením kroků v tématu [vytvoření instance Network Watcher](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Pokud chcete spravovat sledovací procesy sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **Network Watcher**.

1. V řádku nabídek sledovacího procesu sítě klikněte na tlačítko **Další segment směrování**.

1. V **Network Watcher | Stránka dalšího směrování** :

    1. Vyberte **předplatné** a **skupinu prostředků** zdrojového virtuálního počítače, ze kterého chcete ověřit směrování.

    1. Vyberte **virtuální počítač** a **síťové rozhraní** , které je připojené k virtuálnímu počítači.
    
    1. Zadejte **zdrojovou IP adresu** přiřazenou síťovému rozhraní, ze kterého chcete ověřit směrování.

    1. Zadejte **cílovou IP adresu** , na kterou chcete směrování ověřit.

1. Vyberte **Další segment směrování**.

Po krátkém čekání vám Azure ukáže typ dalšího segmentu směrování a ID trasy, která směrovala provoz. Přečtěte si další informace o typech dalších segmentů směrování, které vidíte ve [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Ověření směrování mezi dvěma koncovými body – příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network Watcher show-Next-Hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy s směrovacími tabulkami a trasami, musí být váš účet přiřazen k [roli Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v následující tabulce:

| Akce                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft. Network/routeTables/Read                              |   Čtení směrovací tabulky                                    |
| Microsoft. Network/routeTables/Write                             |   Vytvoří nebo aktualizuje směrovací tabulku.                        |
| Microsoft. Network/routeTables/DELETE                            |   Odstranění směrovací tabulky                                  |
| Microsoft. Network/routeTables/JOIN/Action                       |   Přidružení směrovací tabulky k podsíti                   |
| Microsoft. Network/routeTables/Routes/Read                       |   Čtení trasy                                          |
| Microsoft. Network/routeTables/Routes/Write                      |   Vytvořit nebo aktualizovat trasu                              |
| Microsoft. Network/routeTables/Routes/DELETE                     |   Odstranění trasy                                        |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/Action  |   Získat platnou směrovací tabulku pro síťové rozhraní |
| Microsoft. Network/networkWatchers/nextHop/Action                |   Získá další segment směrování z virtuálního počítače.                           |

## <a name="next-steps"></a>Další kroky

- Vytvoření směrovací tabulky pomocí [PowerShellu](powershell-samples.md) nebo ukázkových skriptů [Azure CLI](cli-samples.md) nebo [šablon Azure správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](./policy-reference.md) pro virtuální sítě