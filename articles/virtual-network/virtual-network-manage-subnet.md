---
title: Přidání, změna nebo odstranění podsítě virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Naučte se přidávat, měnit nebo odstraňovat podsítě virtuální sítě v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: e8717d10f61dfd50b9cdfa20a91203a5842d4c7d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185385"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Přidání, změna nebo odstranění podsítě virtuální sítě

Naučte se přidávat, měnit nebo odstraňovat podsítě virtuální sítě. Všechny prostředky Azure nasazené ve virtuální síti se nasazují do podsítě v rámci virtuální sítě. Pokud s virtuálními sítěmi začínáte, můžete o nich získat další informace v [přehledu virtuální sítě](virtual-networks-overview.md) nebo v tématu dokončení [kurzu](quick-create-portal.md). Informace o vytvoření, změně nebo odstranění virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Před dokončením kroků v jakékoli části tohoto článku proveďte následující úlohy:

- Pokud ještě nemáte účet Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy prostředí PowerShell, buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku používáte příkazy rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, je také potřeba spustit `az login` a vytvořit připojení k Azure.

Účet, ke kterému se přihlašujete, nebo se k Azure připojíte pomocí nástroje, musí být přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="add-a-subnet"></a>Přidání podsítě

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, do které chcete přidat podsíť.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. Vyberte **+ podsíť**.
5. Zadejte hodnoty pro následující parametry:
   - **Název**: název musí být v rámci virtuální sítě jedinečný. Pro maximální kompatibilitu s jinými službami Azure doporučujeme použít jako první znak názvu písmeno. Například Azure Application Gateway nebudete nasazovat do podsítě s názvem, který začíná číslem.
   - **Rozsah adres**: rozsah musí být jedinečný v rámci adresního prostoru virtuální sítě. Rozsah se nesmí překrývat s ostatními rozsahy adres podsítí v rámci virtuální sítě. Adresní prostor se musí zadat pomocí zápisu CIDR (Classless Inter-Domain Routing). Například ve virtuální síti s adresním prostorem 10.0.0.0/16 můžete definovat adresní prostor podsítě 10.0.0.0/24. Nejmenší rozsah, který můžete zadat, je/29, který poskytuje osm IP adres pro podsíť. Azure rezervuje v každé podsíti první a poslední adresu pro shodu protokolu. Tři další adresy jsou rezervované pro využití služeb Azure. V důsledku toho definování podsítě s rozsahem adres/29 má za následek tři použitelné IP adresy v podsíti. Pokud plánujete připojit virtuální síť k bráně sítě VPN, musíte vytvořit podsíť brány. Přečtěte si další informace o [konkrétních požadavcích na rozsah adres pro podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete po přidání podsítě změnit za určitých podmínek. Informace o tom, jak změnit rozsah adres podsítě, najdete v tématu [Změna nastavení podsítě](#change-subnet-settings).
   - **Skupina zabezpečení sítě**: k podsíti můžete přidružit žádnou nebo jednu existující skupinu zabezpečení sítě, aby bylo možné filtrovat příchozí a odchozí síťový provoz pro danou podsíť. Skupina zabezpečení sítě musí existovat ve stejném předplatném a umístění jako virtuální síť. Přečtěte si další informace o [skupinách zabezpečení sítě](security-overview.md) a [o tom, jak vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
   - **Směrovací tabulka**: k podsíti můžete přidružit žádnou nebo jednu existující směrovací tabulku k řízení směrování provozu sítě do jiných sítí. Směrovací tabulka musí existovat ve stejném předplatném a umístění jako virtuální síť. Přečtěte si další informace o [Směrování Azure](virtual-networks-udr-overview.md) a [o tom, jak vytvořit směrovací tabulku](tutorial-create-route-table-portal.md) .
   - **Koncové body služby:** Podsíť může mít pro ni nula nebo více koncových bodů služby. Chcete-li povolit koncový bod služby pro službu, vyberte služby nebo služby, pro které chcete povolit koncové body služby ze seznamu **služeb** . Umístění je konfigurováno automaticky pro koncový bod. Ve výchozím nastavení jsou koncové body služby nakonfigurované pro oblast virtuální sítě. Pro Azure Storage pro podporu regionálních scénářů převzetí služeb při selhání se koncové body automaticky nakonfigurují na [spárované oblasti Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   - **Delegování podsítě:** Pro podsíť může být k dispozici nula pro více delegování. Delegování podsítě dává službě explicitní oprávnění k vytváření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru při nasazování služby. Chcete-li delegovat službu, v seznamu **služeb** vyberte službu, ze které chcete delegovat oprávnění.

       Chcete-li odebrat koncový bod služby, zrušte výběr služby, pro kterou chcete odebrat koncový bod služby. Další informace o koncových bodech služby a službách, pro které je možné je povolit, najdete v tématu [Přehled koncových bodů služby virtuální sítě](virtual-network-service-endpoints-overview.md). Po povolení koncového bodu služby pro službu musíte taky povolit přístup k síti pro podsíť pro prostředek vytvořený pomocí služby. Pokud například povolíte koncový bod služby pro *Microsoft. Storage*, musíte taky povolit přístup k síti pro všechny účty Azure Storage, ke kterým chcete udělit přístup k síti. Podrobnosti o tom, jak povolit síťový přístup k podsítím, pro které je povolen koncový bod služby, najdete v dokumentaci k jednotlivé službě, pro kterou jste povolili koncový bod služby.

     Pokud chcete ověřit, jestli je koncový bod služby pro podsíť povolený, podívejte se na [efektivní trasy](diagnose-network-routing-problem.md) pro jakékoli síťové rozhraní v podsíti. Při konfiguraci koncového bodu se zobrazí *výchozí* trasa s předponami adres služby a typem **VirtualNetworkServiceEndpoint**. Další informace o směrování najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
6. Pokud chcete přidat podsíť do virtuální sítě, kterou jste vybrali, vyberte **OK**.

**Příkaz**

- Azure CLI: [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Změnit nastavení podsítě

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, která obsahuje podsíť, pro kterou chcete změnit nastavení.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. V seznamu podsítí vyberte podsíť, pro kterou chcete změnit nastavení. Můžete změnit následující nastavení:

    - **Rozsah adres:** Pokud v rámci podsítě nejsou nasazené žádné prostředky, můžete změnit rozsah adres. Pokud v podsíti existují nějaké prostředky, musíte buď přesunout prostředky do jiné podsítě, nebo je nejdřív odstranit z podsítě. Postup přesunutí nebo odstranění prostředku se liší v závislosti na prostředku. Pokud chcete zjistit, jak přesunout nebo odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý typ prostředku, který chcete přesunout nebo odstranit. Prohlédněte si omezení pro **Rozsah adres** v kroku 5 části [Přidání podsítě](#add-a-subnet).
    - **Uživatelé**: přístup k podsíti můžete řídit pomocí integrovaných rolí nebo vlastních rolí. Další informace o přiřazování rolí a uživatelů pro přístup k podsíti najdete v tématu [použití přiřazení rolí ke správě přístupu k prostředkům Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Skupina zabezpečení sítě** a **směrovací tabulka**: viz krok 5 [Přidání podsítě](#add-a-subnet).
    - **Koncové body služby**: v kroku 5 části [Přidání podsítě](#add-a-subnet)se podívejte na koncové body služby. Při povolování koncového bodu služby pro existující podsíť zajistěte, aby na žádném prostředku v podsíti neběžely žádné kritické úlohy. Koncové body služby přepínají trasy v každém síťovém rozhraní v podsíti, aby používaly výchozí trasu s předponou adresy *0.0.0.0/0* a typem dalšího segmentu směrování *Internet*, aby používaly novou trasu s předponami adres služby a typ dalšího segmentu směrování *VirtualNetworkServiceEndpoint*. Během přepínače se můžou ukončit všechna otevřená připojení TCP. Koncový bod služby není povolený, dokud se přenos toků ke službě pro všechna síťová rozhraní neaktualizuje pomocí nové trasy. Další informace o směrování najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
    - **Delegování podsítě:** Viz koncové body služby v kroku 5 části [Přidání podsítě](#add-a-subnet). Delegování podsítě lze změnit na hodnotu nula nebo více delegování, které jsou pro něj povoleny. Pokud je v podsíti již nasazen prostředek pro službu, nelze přidat ani odebrat delegování podsítě, dokud nebudou odebrány všechny prostředky pro službu. Chcete-li delegovat jinou službu, v seznamu **služeb** vyberte službu, ze které chcete delegovat oprávnění.
5. Vyberte **Uložit**.

**Příkaz**

- Azure CLI: [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Odstranění podsítě

Podsíť můžete odstranit pouze v případě, že v podsíti nejsou žádné prostředky. Pokud v podsíti existují prostředky, je nutné odstranit prostředky, které jsou v podsíti, než budete moci odstranit podsíť. Postup odstranění prostředku se liší v závislosti na prostředku. Pokud se chcete dozvědět, jak odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý typ prostředku, který chcete odstranit.

1. Do vyhledávacího pole v horní části portálu zadejte do vyhledávacího pole *virtuální sítě* . Pokud se ve výsledcích hledání zobrazí **virtuální sítě** , vyberte ji.
2. V seznamu virtuálních sítí vyberte virtuální síť, která obsahuje podsíť, kterou chcete odstranit.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. V seznamu **podsítí vyberte na**pravé straně pro podsíť, kterou chcete odstranit.
5. Vyberte **Odstranit**a pak vyberte **Ano**.

**Příkaz**

- Azure CLI: [AZ Network VNet Subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy v podsítích, musí být váš účet přiřazen k roli [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, která je přiřazena k příslušným akcím uvedeným v následující tabulce:

|Akce                                                                   |   Název                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Čtení podsítě virtuální sítě              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Vytvoření nebo aktualizace podsítě virtuální sítě  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Odstraní podsíť virtuální sítě.            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Připojení k virtuální síti                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Povolení koncového bodu služby pro podsíť     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Získání virtuálních počítačů v podsíti       |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuální sítě a podsítí pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo použití šablon Azure [Správce prostředků](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
