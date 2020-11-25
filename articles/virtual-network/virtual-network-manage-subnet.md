---
title: Přidání, změna nebo odstranění podsítě virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Naučte se, jak najít informace o virtuálních sítích a jak přidat, změnit nebo odstranit podsíť virtuální sítě v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 15fe5d6d16948875253d65e70d9d440214a4a2e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995654"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Přidání, změna nebo odstranění podsítě virtuální sítě

Naučte se přidávat, měnit nebo odstraňovat podsítě virtuální sítě. Všechny prostředky Azure nasazené do virtuální sítě se nasazují do podsítě ve virtuální síti. Pokud s virtuálními sítěmi začínáte, můžete o nich získat další informace v [přehledu virtuální sítě](virtual-networks-overview.md) nebo v tématu [rychlý Start](quick-create-portal.md). Další informace o správě virtuální sítě najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](manage-virtual-network.md).

## <a name="before-you-begin"></a>Než začnete

Pokud ho nemáte, nastavte účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Pak před zahájením kroků v jakékoli části tohoto článku dokončete jednu z těchto úloh: 

- **Uživatelé portálu**: Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo z počítače spusťte PowerShell. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě Azure Cloud Shell prohlížeč Najděte rozevírací seznam **Vybrat prostředí** a zvolte **PowerShell** , pokud ještě není vybraný.

    Pokud používáte PowerShell místně, použijte Azure PowerShell modul verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Také spusťte `Connect-AzAccount` pro vytvoření připojení k Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI)**: buď spusťte příkazy v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spusťte CLI z počítače. Pokud používáte Azure CLI místně, použijte Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Také spusťte `az login` pro vytvoření připojení k Azure.

Účet, ke kterému se přihlašujete, nebo ke kterému se chcete připojit k Azure pomocí, musí být přiřazený k roli [Role Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím uvedeným v [oprávněních](#permissions).

## <a name="add-a-subnet"></a>Přidání podsítě

1. Pokud si chcete zobrazit virtuální sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

2. Vyberte název virtuální sítě, do které chcete přidat podsíť.

3. V **Nastavení** vyberte podsíť **podsítě**  >  **Subnet**.

4. V dialogovém okně **Přidat podsíť** zadejte hodnoty pro následující nastavení:

    | Nastavení | Popis |
    | --- | --- |
    | **Název** | Název musí být v rámci virtuální sítě jedinečný. Pro maximální kompatibilitu s jinými službami Azure doporučujeme použít jako první znak názvu písmeno. Například Azure Application Gateway nebudete nasazovat do podsítě s názvem, který začíná číslem. |
    | **Rozsah adres** | <p>Rozsah musí být jedinečný v rámci adresního prostoru virtuální sítě. Rozsah se nemůže překrývat s ostatními rozsahy adres podsítí v rámci virtuální sítě. Adresní prostor musí být zadaný pomocí zápisu CIDR (Inter-Domain Routing) pro třídy.</p><p>Například ve virtuální síti s adresním prostorem *10.0.0.0/16* můžete definovat adresní prostor podsítě *10.0.0.0/22*. Nejmenší rozsah, který můžete zadat, je */29*, který poskytuje osm IP adres pro podsíť. Azure rezervuje v každé podsíti první a poslední adresu pro shodu protokolu. Tři další adresy jsou rezervované pro využití služeb Azure. V důsledku toho definování podsítě s rozsahem adres */29* má za následek tři použitelné IP adresy v podsíti.</p><p>Pokud plánujete připojit virtuální síť k bráně sítě VPN, musíte vytvořit podsíť brány. Přečtěte si další informace o [konkrétních požadavcích na rozsah adres pro podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete po přidání podsítě změnit za určitých podmínek. Informace o tom, jak změnit rozsah adres podsítě, najdete v tématu [Změna nastavení podsítě](#change-subnet-settings).</p> |
    | **Skupina zabezpečení sítě** | K filtrování příchozího a odchozího síťového provozu pro podsíť můžete k podsíti přidružit existující skupinu zabezpečení sítě. Skupina zabezpečení sítě musí existovat ve stejném předplatném a umístění jako virtuální síť. Přečtěte si další informace o [skupinách zabezpečení sítě](security-overview.md) a [o tom, jak vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md). |
    | **Směrovací tabulka** | Chcete-li řídit směrování provozu sítě do jiných sítí, můžete volitelně přidružit existující směrovací tabulku k podsíti. Směrovací tabulka musí existovat ve stejném předplatném a umístění jako virtuální síť. Přečtěte si další informace o [Směrování Azure](virtual-networks-udr-overview.md) a [o tom, jak vytvořit směrovací tabulku](tutorial-create-route-table-portal.md). |
    | **Koncové body služby** | <p>Podsíť může volitelně mít jeden nebo více koncových bodů služby, které jsou pro něj povoleny. Chcete-li povolit koncový bod služby pro službu, vyberte služby nebo služby, pro které chcete povolit koncové body služby ze seznamu **služeb** . Azure nakonfiguruje umístění automaticky pro koncový bod. Ve výchozím nastavení Azure nakonfiguruje koncové body služby pro oblast virtuální sítě. Pro podporu regionálních scénářů převzetí služeb při selhání Azure automaticky nakonfiguruje koncové body na [spárované oblasti Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) pro Azure Storage.</p><p>Chcete-li odebrat koncový bod služby, zrušte výběr služby, pro kterou chcete odebrat koncový bod služby. Další informace o koncových bodech služby a službách, pro které je možné je povolit, najdete v tématu [koncové body služby virtuální sítě](virtual-network-service-endpoints-overview.md). Po povolení koncového bodu služby pro službu musíte taky povolit přístup k síti pro podsíť pro prostředek vytvořený pomocí služby. Pokud například povolíte koncový bod služby pro **Microsoft. Storage**, musíte taky povolit přístup k síti pro všechny účty Azure Storage, ke kterým chcete udělit přístup k síti. Postup povolení síťového přístupu k podsítím, pro které je povolen koncový bod služby, najdete v dokumentaci k jednotlivé službě, pro kterou jste povolili koncový bod služby.</p><p>Pokud chcete ověřit, jestli je koncový bod služby pro podsíť povolený, podívejte se na [efektivní trasy](diagnose-network-routing-problem.md) pro jakékoli síťové rozhraní v podsíti. Při konfiguraci koncového bodu se zobrazí *výchozí* trasa s předponami adres služby a typem dalšího segmentu směrování **VirtualNetworkServiceEndpoint**. Další informace o směrování najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).</p> |
    | **Delegování podsítě** | Podsíť může volitelně mít povolený jeden nebo více delegování. Delegování podsítě dává službě explicitní oprávnění k vytváření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru během nasazování služby. Chcete-li delegovat službu, v seznamu **služeb** vyberte službu, ze které chcete delegovat oprávnění. |

5. Pokud chcete přidat podsíť do virtuální sítě, kterou jste vybrali, vyberte **OK**.

### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Změnit nastavení podsítě

1. Pokud si chcete zobrazit virtuální sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

2. Vyberte název virtuální sítě obsahující podsíť, kterou chcete změnit.

3. V **Nastavení** vyberte **podsítě**.

4. V seznamu podsítí vyberte podsíť, pro kterou chcete změnit nastavení.

5. Na stránce podsíť změňte kterékoli z následujících nastavení:

    | Nastavení | Popis |
    | --- | --- |
    | **Rozsah adres** | Pokud v rámci podsítě nejsou nasazené žádné prostředky, můžete změnit rozsah adres. Pokud v podsíti existují nějaké prostředky, musíte buď přesunout prostředky do jiné podsítě, nebo je nejdřív odstranit z podsítě. Postup přesunutí nebo odstranění prostředku se liší v závislosti na prostředku. Pokud chcete zjistit, jak přesunout nebo odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý z těchto typů prostředků. Prohlédněte si omezení pro **Rozsah adres** v kroku 4 [Přidání podsítě](#add-a-subnet). |
    | **Uživatelé** | Přístup k podsíti můžete řídit pomocí integrovaných rolí nebo vlastních rolí. Další informace o přiřazování rolí a uživatelů pro přístup k podsíti najdete v tématu [Přidání přiřazení role](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Skupina zabezpečení sítě** a **Tabulka směrování** | Viz krok 4 [Přidání podsítě](#add-a-subnet). |
    | **Koncové body služby** | <p>Viz koncové body služby v kroku 4 [Přidání podsítě](#add-a-subnet). Při povolování koncového bodu služby pro existující podsíť zajistěte, aby na žádném prostředku v podsíti neběžely žádné kritické úlohy. Koncové body služby přepínají trasy v každém síťovém rozhraní v podsíti. Koncové body služby se nacházejí v používání výchozí trasy s předponou adresy *0.0.0.0/0* a typem dalšího segmentu směrování *Internet*, aby bylo možné použít novou trasu s předponami adres služby a typem dalšího segmentu směrování *VirtualNetworkServiceEndpoint*.</p><p>Během přepínače se můžou ukončit všechna otevřená připojení TCP. Koncový bod služby není povolený, dokud se přenos toků ke službě pro všechna síťová rozhraní neaktualizuje pomocí nové trasy. Další informace o směrování najdete v tématu [směrování provozu virtuální sítě](virtual-networks-udr-overview.md).</p> |
    | **Delegování podsítě** | Viz koncové body služby v kroku 4 [Přidání podsítě](#add-a-subnet). Delegování podsítě lze změnit na hodnotu nula nebo více delegování, které jsou pro něj povoleny. Pokud je prostředek pro službu už v podsíti nasazený, delegování podsítě nejde přidat ani odebrat, dokud se neodstraní všechny prostředky pro tuto službu. Chcete-li delegovat jinou službu, v seznamu **služeb** vyberte službu, ze které chcete delegovat oprávnění. |

6. Vyberte **Uložit**.

### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Odstranění podsítě

Podsíť můžete odstranit pouze v případě, že v podsíti nejsou žádné prostředky. Pokud jsou prostředky v podsíti, musíte tyto prostředky odstranit, abyste mohli odstranit podsíť. Postup odstranění prostředku se liší v závislosti na prostředku. Pokud se chcete dozvědět, jak odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý z těchto typů prostředků.

1. Pokud si chcete zobrazit virtuální sítě, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte možnost **virtuální sítě**.

2. Vyberte název virtuální sítě obsahující podsíť, kterou chcete odstranit.

3. V **Nastavení** vyberte **podsítě**.

4. V seznamu podsítí vyberte podsíť, kterou chcete odstranit.

5. Vyberte **Odstranit** a potom v potvrzovacím dialogovém okně vyberte **Ano** .

### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [AZ Network VNet Subnet DELETE](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Oprávnění

Aby bylo možné provádět úlohy v podsítích, musí být váš účet přiřazen k [roli Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo k [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , která je přiřazená k příslušným akcím v následující tabulce:

|Akce                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft. Network/virtualNetworks/podsítí/čtení                           |   Čtení podsítě virtuální sítě              |
|Microsoft. Network/virtualNetworks/podsítí/Write                          |   Vytvoření nebo aktualizace podsítě virtuální sítě  |
|Microsoft. Network/virtualNetworks/podsítí/DELETE                         |   Odstraní podsíť virtuální sítě.            |
|Microsoft. Network/virtualNetworks/subnets/JOIN/Action                    |   Připojení k virtuální síti                     |
|Microsoft. Network/virtualNetworks/podsítí/joinViaServiceEndpoint/Action  |   Povolení koncového bodu služby pro podsíť     |
|Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read           |   Získání virtuálních počítačů v podsíti       |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuální sítě a podsítí pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo použití šablon Azure [Správce prostředků](template-samples.md)
- Vytvoření a přiřazení [definic Azure Policy](policy-samples.md) pro virtuální sítě
