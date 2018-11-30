---
title: Přidat, změnit nebo odstranit podsíť virtuální sítě Azure | Dokumentace Microsoftu
description: Zjistěte, jak přidat, změnit nebo odstranit podsíť virtuální sítě v Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 442aa7034c3fec57b3b9394e6b0f46d4dec47849
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633108"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Přidání, změna nebo odstranění podsítě virtuální sítě

Zjistěte, jak přidat, změnit nebo odstranit podsíť virtuální sítě. Všechny prostředky Azure, které jsou nasazené do virtuální sítě se nasazuje do podsítě ve virtuální síti. Pokud začínáte s virtuálními sítěmi, další informace o nich v [Přehled virtuálních sítí](virtual-networks-overview.md) nebo provedením [kurzu](quick-create-portal.md). Pokud chcete vytvořit, změnit, nebo odstranění virtuální sítě, naleznete v tématu [Správa virtuální sítě](manage-virtual-network.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v jakékoli části tohoto článku, proveďte následující úkoly:

- Pokud ještě nemáte účet Azure, zaregistrujte si [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí svého účtu Azure.
- Pokud používáte příkazy prostředí PowerShell k dokončení úkolů v tomto článku, buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud k dokončení úkolů v tomto článku pomocí příkazů rozhraní příkazového řádku Azure (CLI), buď spusťte příkazy [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

Účet přihlásit nebo připojit k Azure, musíte být přiřazeni k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní roli](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="add-a-subnet"></a>Přidání podsítě

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, kterou chcete přidat do podsítě.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. Vyberte **+ podsíť**.
5. Zadejte hodnoty následujících parametrů:
    - **Název**: název musí být jedinečný v rámci virtuální sítě. Pro maximální kompatibilita s ostatními službami Azure doporučujeme použít písmenem jako první znak názvu. Azure Application Gateway se například nenasadí do podsítě, který má název, který začíná číslem.
    - **Rozsah adres**: rozsah musí být jedinečný v rámci adresního prostoru virtuální sítě. Rozsahu se nesmí překrývat s rozsahy adres jiné podsítě ve virtuální síti. Adresní prostor musí být určena pomocí notace CIDR (Classless Inter-Domain Routing) zápisu. Ve virtuální síti s adresní prostor 10.0.0.0/16, můžete třeba definovat adresní prostor podsítě 10.0.0.0/24. Nejmenší oblast, kterou můžete určit je minimální velikostí/29, který nabízí osm IP adres podsítě. Azure si vyhrazuje první a poslední adresa v každé podsíti pro udržování souladu s protokoly. Tři další adresy jsou vyhrazené pro použití služby Azure. Proto definování podsítě je/29 řešit tzv tři použitelné IP adresy v podsíti. Pokud máte v plánu připojit virtuální sítě k bráně VPN, musíte vytvořit podsíť brány. Další informace o [aspekty konkrétní adresu rozsahu podsítí brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Rozsah adres podsítě je přidán za určitých podmínek můžete změnit. Zjistěte, jak změnit rozsah adres podsítě, najdete v článku [změnit nastavení podsítě](#change-subnet-settings).
    - **Skupina zabezpečení sítě**: můžete přiřadit nula nebo jednu existující skupinu zabezpečení sítě k podsíti pro filtrování příchozího a odchozího provozu sítě pro podsíť. Skupina zabezpečení sítě musí existovat ve stejném předplatném a umístění jako virtuální síť. Další informace o [skupiny zabezpečení sítě](security-overview.md) a [jak vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
    - **Směrovací tabulka**: můžete přiřadit žádnou nebo jednu existující směrovací tabulky podsítě řídit směrování provozu sítě k jiným sítím. Směrovací tabulka musí existovat ve stejném předplatném a umístění jako virtuální síť. Další informace o [směrování Azure](virtual-networks-udr-overview.md) a [Vytvoření směrovací tabulky](tutorial-create-route-table-portal.md)
    - **Koncové body služby:** podsíť může mít nula nebo více koncových bodů služby povolená. Pokud chcete povolit koncový bod služby pro službu, vyberte službu nebo služby, které chcete povolit koncové body služby z **služby** seznamu. Umístění je automaticky nakonfigurován pro koncový bod. Ve výchozím nastavení jsou nakonfigurované koncové body služeb pro oblast virtuální sítě. Pro službu Azure Storage pro zajištění podpory scénářů místního převzetí služeb při selhání koncových bodů se automaticky konfigurují pro [spárovaných oblastech Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
    - **Delegování podsítě:** podsíť může mít nula více delegování povoleno pro něj. Podsíť delegování dává explicitní oprávnění ke službě vytvářet prostředky specifické pro služby v podsíti při nasazení služby pomocí jedinečného identifikátoru. K delegování pro službu, vyberte službu chcete delegovat z **služby** seznamu. 

    Odebrání koncového bodu služby, zrušte výběr služby, kterou chcete odstranit koncový bod služby pro. Další informace o koncových bodů služby a služby, je možné povolit pro najdete v tématu [přehled koncových bodů služeb virtuální sítě](virtual-network-service-endpoints-overview.md). Po povolení koncového bodu služby pro službu, musíte také povolit přístup k síti, podsíti pro prostředek vytvořený ve službě. Například, pokud povolíte koncový bod služby pro *Microsoft.Storage*, musíte také povolit síťový přístup všem účtům Azure Storage, kterým chcete udělit přístup k síti na. Podrobnosti o tom, jak povolit přístup k síti na podsítě, které je povolený koncový bod služby pro, najdete v dokumentaci pro jednotlivé služby povolíte koncový bod služby pro.

    Chcete-li ověřit, že koncový bod služby je povolená pro určitou podsíť, zobrazte [efektivní trasy](diagnose-network-routing-problem.md) u všech síťových rozhraních v podsíti. Když je nakonfigurovaný koncový bod, zobrazí *výchozí* trasy s předpony adres služby a typ dalšího segmentu z **VirtualNetworkServiceEndpoint**. Další informace o směrování najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
6. Chcete-li přidat podsíť pro virtuální síť, kterou jste vybrali, **OK**.

**Příkazy**

- Azure CLI: [az podsíti virtuální sítě vytvořit](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- Prostředí PowerShell: [přidat AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Změnit nastavení podsítě

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, která obsahuje podsíť, kterou chcete změnit nastavení.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. V seznamu podsítí vyberte podsíť, kterou chcete změnit nastavení pro. Můžete změnit následující nastavení:

    - **Rozsah adres:** Pokud žádné prostředky se nasadí v rámci podsítě, můžete změnit rozsah adres. Pokud všechny prostředky v podsíti, musíte buď prostředky přesunout do jiné podsítě nebo nejprve odstraňte z podsítě. Kroky, jak můžete přesunout nebo odstranit prostředek se liší v závislosti na prostředku. Zjistěte, jak se přesunout nebo odstranit prostředky, které jsou v podsítích, najdete v dokumentaci pro každý typ prostředku, který chcete přesunout nebo odstranit. V tématu omezení pro **rozsah adres** v kroku 5 [přidat podsíť](#add-a-subnet).
    - **Uživatelé**: přístup k podsíti můžete řídit pomocí předdefinovaných nebo vlastní role. Další informace o přiřazování rolí a uživatelů pro přístup k podsíti, najdete v článku [použití přiřazení rolí ke správě přístupu k prostředkům Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Skupina zabezpečení sítě** a **směrovací tabulka**: najdete v kroku 5 části [přidat podsíť](#add-a-subnet).
    - **Koncové body služby**: zobrazit koncové body služby v kroku 5 části [přidat podsíť](#add-a-subnet). Při povolení koncového bodu služby pro existující podsíť, ujistěte se, že žádné důležité úlohy běží na prostředky v podsíti. Koncové body služby přepnout trasy na každé síťové rozhraní v podsíti z pomocí výchozí trasa s *0.0.0.0/0* předponu a dalším segmentem směrování typu *Internet*, pomocí novou trasu s služby a typem dalšího segmentu směrování z předpony adres *VirtualNetworkServiceEndpoint*. Během přepnutí může být ukončena všechna otevřená připojení TCP. Koncový bod služby není povoleno, dokud přenosové toky ve službě pro všechna síťová rozhraní jsou aktualizovány pomocí nového trasy. Další informace o směrování najdete v tématu [Přehled směrování](virtual-networks-udr-overview.md).
    - **Delegování podsítě:** zobrazit koncové body služby v kroku 5 části [přidat podsíť](#add-a-subnet). Delegování podsítě můžete upravit tak, aby nula nebo více delegování povoleno pro něj. Pokud je prostředek pro službu již nasazený v podsíti, delegování podsíť nejde odstranit, dokud se odeberou všechny prostředky pro službu. Na delegáta pro jiné služby, vyberte službu chcete delegovat z **služby** seznamu. 
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- Prostředí PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Odstranit podsíť

Podsítě můžete odstranit pouze v případě, že nejsou žádné prostředky v podsíti. Pokud existují prostředky v podsíti, je nutné odstranit prostředky, které jsou v podsíti, než budete moct odstranit podsíť. Kroky, jak jste se odstranit prostředek se liší v závislosti na prostředku. Zjistěte, jak odstranit prostředky, které jsou v podsítích, najdete v dokumentaci pro každý typ prostředku, který chcete odstranit.

1. Do vyhledávacího pole v horní části portálu zadejte *virtuálních sítí* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ji.
2. Seznam virtuálních sítí vyberte virtuální síť, která obsahuje podsíť, ve které chcete odstranit.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. Vyberte v seznamu podsítí **...** , na pravé straně, pro podsíť chcete odstranit
5. Vyberte **odstranit**a pak vyberte **Ano**.

**Příkazy**

- Azure CLI: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- Prostředí PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

K provádění úloh v podsítích, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

|Akce                                                                   |   Název                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Přečtěte si podsíti virtuální sítě              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Vytvoří nebo aktualizuje podsíť virtuální sítě  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Odstranění podsítě virtuální sítě            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Připojte se k virtuální síti                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Povolení koncového bodu služby podsítě     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Získejte virtuální počítače v podsíti       |

## <a name="next-steps"></a>Další postup

- Vytvoření virtuální sítě a podsítě se používá [PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablon Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure policy](policy-samples.md) pro virtuální sítě
