---
title: Přidání, změna nebo odstranění podsítě virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si, jak přidat, změnit nebo odstranit podsíť virtuální sítě v Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246938"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Přidání, změna nebo odstranění podsítě virtuální sítě

Přečtěte si, jak přidat, změnit nebo odstranit podsíť virtuální sítě. Všechny prostředky Azure nasazené do virtuální sítě se nasazují do podsítě ve virtuální síti. Pokud s virtuálními sítěmi teču, můžete se o nich dozvědět víc v [přehledu virtuální sítě](virtual-networks-overview.md) nebo vyplněním [rychlého startu](quick-create-portal.md). Další informace o správě virtuální sítě najdete v tématu [Vytvoření, změna nebo odstranění virtuální sítě](manage-virtual-network.md).

## <a name="before-you-begin"></a>Než začnete

Pokud ho nemáte, nastavte si účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Před zahájením kroků v této části tohoto článku proveďte jeden z těchto úkolů: 

- **Uživatelé portálu:** Přihlaste se na [portál Azure](https://portal.azure.com) pomocí svého účtu Azure.

- **Uživatelé PowerShellu:** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spusťte PowerShell z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Na kartě prohlížeče Azure Cloud Shell najděte rozevírací seznam **Select environment** a pak zvolte **PowerShell,** pokud ještě není vybraný.

    Pokud používáte PowerShell místně, použijte modul Azure PowerShell verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az.Network`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Spusťte `Connect-AzAccount` také k vytvoření připojení s Azure.

- **Uživatelé rozhraní příkazového řádku Azure (CLI):** Buď spusťte příkazy v [prostředí Azure Cloud Shell](https://shell.azure.com/bash)nebo spusťte rozhraní příkazového řádku z vašeho počítače. Pokud používáte azure CLI verze 2.0.31 nebo novější, pokud používáte azure cli místně. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Spusťte `az login` také k vytvoření připojení s Azure.

Účet, ke kterému se přihlašujete nebo se s ním připojujete, musí být přiřazen k roli [přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) které jsou přiřazeny příslušné akce uvedené v [části Oprávnění](#permissions).

## <a name="add-a-subnet"></a>Přidání podsítě

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si své virtuální sítě. Vyhledejte a vyberte **virtuální sítě**.

2. Vyberte název virtuální sítě, do které chcete přidat podsíť.

3. V **části Nastavení**vyberte **podsíť Podsítí podsítí** > **Subnet**.

4. V dialogovém okně **Přidat podsíť** zadejte hodnoty pro následující nastavení:

    | Nastavení | Popis |
    | --- | --- |
    | **Název** | Název musí být v rámci virtuální sítě jedinečný. Pro maximální kompatibilitu s jinými službami Azure doporučujeme použít písmeno jako první znak názvu. Například Azure Application Gateway nebude nasadit do podsítě, která má název, který začíná číslo. |
    | **Rozsah adres** | <p>Rozsah musí být jedinečný v rámci adresního prostoru pro virtuální síť. Rozsah se nemůže překrývat s rozsahy adres jiných podsítí v rámci virtuální sítě. Adresní prostor musí být určen pomocí zápisu CIDR (Classless Inter-Domain Routing).</p><p>Například ve virtuální síti s adresním prostorem *10.0.0.0/16*můžete definovat adresní prostor podsítě *10.0.0.0/22*. Nejmenší rozsah, který můžete zadat, je */29*, který poskytuje osm adres IP pro podsíť. Azure si vyhrazuje první a poslední adresu v každé podsíti pro shodu protokolu. Tři další adresy jsou vyhrazeny pro využití služby Azure. V důsledku toho definování podsítě s *rozsahem /29* adres má za následek tři použitelné ADRESY IP v podsíti.</p><p>Pokud plánujete připojit virtuální síť k bráně VPN, musíte vytvořit podsíť brány. Přečtěte si další informace o [konkrétních aspektech rozsahu adres pro podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres můžete za určitých podmínek změnit po přidání podsítě. Informace o změně rozsahu adres podsítě naleznete v tématu [Změna nastavení podsítě](#change-subnet-settings).</p> |
    | **Skupina zabezpečení sítě** | Chcete-li filtrovat příchozí a odchozí síťový provoz pro podsíť, můžete k podsíti přidružit existující skupinu zabezpečení sítě. Skupina zabezpečení sítě musí existovat ve stejném předplatném a umístění jako virtuální síť. Přečtěte si další informace o [skupinách zabezpečení sítě](security-overview.md) a [o vytvoření skupiny zabezpečení sítě](tutorial-filter-network-traffic.md). |
    | **Tabulka směrování** | Chcete-li řídit směrování síťového provozu do jiných sítí, můžete volitelně přidružit existující směrovací tabulku k podsíti. Směrovací tabulka musí existovat ve stejném předplatném a umístění jako virtuální síť. Přečtěte si další informace o [směrování Azure](virtual-networks-udr-overview.md) a [o tom, jak vytvořit směrovací tabulku](tutorial-create-route-table-portal.md). |
    | **Koncové body služby** | <p>Podsíť může mít volitelně jeden nebo více koncových bodů služby pro něj povolen. Chcete-li povolit koncový bod služby pro službu, vyberte službu nebo služby, které chcete povolit koncové body služby ze seznamu **Služby.** Azure konfiguruje umístění automaticky pro koncový bod. Ve výchozím nastavení Azure konfiguruje koncové body služby pro oblast virtuální sítě. Pro podporu místních scénářů převzetí služeb při selhání Azure automaticky konfiguruje koncové body do [spárovaných oblastí Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) pro Azure Storage.</p><p>Chcete-li odebrat koncový bod služby, zrušte výběr služby, pro kterou chcete odebrat koncový bod služby. Další informace o koncových bodech služby a službách, pro které mohou být povoleny, naleznete v [tématu Koncové body služby virtuální sítě](virtual-network-service-endpoints-overview.md). Jakmile povolíte koncový bod služby pro službu, musíte také povolit přístup k síti pro podsíť pro prostředek vytvořený pomocí služby. Pokud například povolíte koncový bod služby pro **Microsoft.Storage**, musíte také povolit přístup k síti ke všem účtům Azure Storage, kterým chcete udělit přístup k síti. Chcete-li povolit přístup k síti podsítí, pro které je povolen koncový bod služby, přečtěte si dokumentaci k jednotlivé službě, pro kterou jste povolili koncový bod služby.</p><p>Chcete-li ověřit, zda je koncový bod [služby](diagnose-network-routing-problem.md) povolen pro podsíť, zobrazte efektivní trasy pro libovolné síťové rozhraní v podsíti. Při konfiguraci koncového bodu se zobrazí *výchozí* trasa s předponou adresy služby a další typ směrování **VirtualNetworkServiceEndpoint**. Další informace o směrování najdete [v tématu Směrování provozu virtuální chodsítě](virtual-networks-udr-overview.md).</p> |
    | **Delegování podsítě** | V podsíti může být volitelně povoleno jedno nebo více delegací. Delegování podsítě uděluje službě explicitní oprávnění k vytvoření prostředků specifických pro službu v podsíti pomocí jedinečného identifikátoru během nasazení služby. Chcete-li delegovat službu, vyberte službu, na kterou chcete delegovat, ze seznamu **Služby.** |

5. Chcete-li přidat podsíť do vybrané virtuální sítě, vyberte **ok**.

### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Doplněk AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Změna nastavení podsítě

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si své virtuální sítě. Vyhledejte a vyberte **virtuální sítě**.

2. Vyberte název virtuální sítě obsahující podsíť, kterou chcete změnit.

3. V **části Nastavení**vyberte **Podsítě**.

4. V seznamu podsítí vyberte podsíť, pro kterou chcete změnit nastavení.

5. Na stránce podsítě změňte libovolné z následujících nastavení:

    | Nastavení | Popis |
    | --- | --- |
    | **Rozsah adres** | Pokud nejsou v podsíti nasazeny žádné prostředky, můžete změnit rozsah adres. Pokud v podsíti existují nějaké prostředky, musíte je přesunout do jiné podsítě nebo je nejprve odstranit z podsítě. Kroky, které provedete při přesunutí nebo odstranění prostředku, se liší v závislosti na prostředku. Chcete-li se dozvědět, jak přesunout nebo odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý z těchto typů prostředků. Viz omezení rozsahu **Adresa** v kroku 4 [přidání podsítě](#add-a-subnet). |
    | **Uživatelé** | Přístup k podsíti můžete řídit pomocí předdefinovaných rolí nebo vlastních rolí. Další informace o přiřazování rolí a uživatelům k přístupu k podsíti najdete [v tématu Přidání přiřazení role](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Skupina zabezpečení sítě** a **Tabulka směrování** | Viz krok 4 [přidání podsítě](#add-a-subnet). |
    | **Koncové body služby** | <p>Viz koncové body služby v kroku 4 [přidání podsítě](#add-a-subnet). Při povolení koncového bodu služby pro existující podsíť se ujistěte, že v žádném prostředku v podsíti nejsou spuštěny žádné kritické úlohy. Koncové body služby přepínají trasy na každém síťovém rozhraní v podsíti. Koncové body služby přejít od použití výchozí ho postupu s předponou adresy *0.0.0.0/0* a dalšího typu směrování *v Internetu*, k použití nové trasy s předčíslím adresy služby a dalším typem směrování *VirtualNetworkServiceEndpoint*.</p><p>Během přepínače mohou být všechna otevřená připojení TCP ukončena. Koncový bod služby není povolen, dokud se provoz na službu pro všechna síťová rozhraní neaktualizuje s novou trasou. Další informace o směrování najdete [v tématu Směrování provozu virtuální chodsítě](virtual-networks-udr-overview.md).</p> |
    | **Delegování podsítě** | Viz koncové body služby v kroku 4 [přidání podsítě](#add-a-subnet). Delegování podsítě lze upravit na nulu nebo více delegací povoleno pro něj. Pokud je prostředek pro službu již nasazen v podsíti, delegování podsítě nelze přidat nebo odebrat, dokud nebudou odebrány všechny prostředky pro službu. Chcete-li delegovat jinou službu, vyberte službu, na kterou chcete delegovat, ze seznamu **Služby.** |

6. Vyberte **Uložit**.

### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Odstranění podsítě

Podsíť můžete odstranit pouze v případě, že v podsíti nejsou žádné prostředky. Pokud jsou prostředky v podsíti, je nutné tyto prostředky před odstraněním odstranit. Kroky k odstranění prostředku se liší v závislosti na prostředku. Chcete-li se dozvědět, jak odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý z těchto typů prostředků.

1. Přejděte na [portál Azure](https://portal.azure.com) a prohlédněte si své virtuální sítě. Vyhledejte a vyberte **virtuální sítě**.

2. Vyberte název virtuální sítě obsahující podsíť, kterou chcete odstranit.

3. V **části Nastavení**vyberte **Podsítě**.

4. V seznamu podsítí vyberte podsíť, kterou chcete odstranit.

5. Vyberte **Odstranit**a pak v potvrzovacím dialogovém okně vyberte **Ano.**

### <a name="commands"></a>Příkazy

| Nástroj | Příkaz |
| ---- | ------- |
| Azure CLI | [Odstranění podsítě sítě AZ](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Odebrat-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Oprávnění

Chcete-li dělat úkoly v podsítích, musí být váš účet přiřazen k [roli přispěvatele sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) nebo [k vlastní roli,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) která je přiřazena příslušné akce v následující tabulce:

|Akce                                                                   |   Name (Název)                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/podsítě/čtení                           |   Čtení podsítě virtuální sítě              |
|Microsoft.Network/virtualNetworks/podsítě/zápis                          |   Vytvoření nebo aktualizace podsítě virtuální sítě  |
|Microsoft.Network/virtualNetworks/podsítě/delete                         |   Odstranění podsítě virtuální sítě            |
|Microsoft.Network/virtualNetworks/podsítě/join/action                    |   Připojení k virtuální síti                     |
|Microsoft.Network/virtualNetworks/podsítě/joinViaServiceEndpoint/action  |   Povolení koncového bodu služby pro podsíť     |
|Microsoft.Network/virtualNetworks/podsítě/virtualMachines/read           |   Získání virtuálních počítačů v podsíti       |

## <a name="next-steps"></a>Další kroky

- Vytvoření virtuální sítě a podsítí pomocí ukázkových skriptů [PowerShellu](powershell-samples.md) nebo [Azure CLI](cli-samples.md) nebo pomocí šablon Azure [Resource Manager](template-samples.md)
- Vytvoření a použití [zásad Azure](policy-samples.md) pro virtuální sítě
