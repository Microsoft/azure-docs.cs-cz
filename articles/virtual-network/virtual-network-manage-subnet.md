---
title: Přidat, změnit nebo odstranit podsíť virtuální sítě Azure | Microsoft Docs
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
ms.openlocfilehash: ea16a9828bfb989c49f3cc8d656122b3083ee66a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702070"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Přidat, změnit nebo odstranit podsíť virtuální sítě

Zjistěte, jak přidat, změnit nebo odstranit podsíť virtuální sítě. Všechny prostředky Azure, které jsou nasazeny do virtuální sítě se nasadí do podsítě virtuální sítě. Pokud jste nový virtuální sítě, další informace o nich [Přehled virtuálních sítí](virtual-networks-overview.md) nebo provedením [kurzu](quick-create-portal.md). Pokud chcete vytvořit, změnit, nebo odstranit virtuální síť, najdete v části [spravovat virtuální sítě](manage-virtual-network.md).

## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.31 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

Účet přihlásit nebo připojit k Azure, musí být přiřazená k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role nebo [vlastní role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) přiřazené příslušné akce uvedené v [oprávnění ](#permissions).

## <a name="add-a-subnet"></a>Přidání podsítě

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, kterou chcete přidat podsíť, kterou chcete.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. Vyberte **+ podsítě**.
5. Zadejte hodnoty následujících parametrů:
    - **Název**: název musí být jedinečný v rámci virtuální sítě.
    - **Rozsah adres**: rozsahu musí být jedinečný v rámci adresního prostoru virtuální sítě. Rozsah se nesmí překrývat s rozsahy adres jiné podsítě v rámci virtuální sítě. Adresní prostor musí být určena pomocí notace CIDR (Classless Inter-Domain směrování) zápisu. Ve virtuální síti s 10.0.0.0/16 prostoru adres, můžete třeba definovat adresního prostoru podsítě 10.0.0.0/24. Nejmenší oblast, kterou můžete zadat je /29, který poskytuje osm IP adresy podsítě. Azure si vyhrazuje adresu první a poslední v každé podsíti pro protokol shoda. Tři další adresy jsou vyhrazené pro použití služby Azure. V důsledku toho definování podsíť s/29 adres rozsah výsledků v tři použitelných IP adresách v podsíti. Pokud máte v plánu pro připojení virtuální sítě k bráně VPN, musíte vytvořit podsíť brány. Další informace o [aspekty rozsah konkrétní adresu podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Po přidání podsítě pro určité podmínky, můžete změnit rozsah adres. Zjistěte, jak změnit rozsah adres podsítě, najdete v tématu [změnit nastavení podsítě](#change-subnet-settings).
    - **Skupina zabezpečení sítě**: můžete přidružit nula nebo jeden existující skupinu zabezpečení sítě k podsíti pro filtrování příchozích a odchozích přenosů sítě pro podsíť. Skupina zabezpečení sítě, musí existovat ve stejném předplatném a umístění jako virtuální síť. Další informace o [skupin zabezpečení sítě](security-overview.md) a [jak vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
    - **Směrovací tabulka**: můžete přidružit žádnou nebo jednu stávající tabulka směrování pro podsíť řídit směrování provozu sítě k jiným sítím. Směrovací tabulka musí existovat ve stejném předplatném a umístění jako virtuální síť. Další informace o [Azure směrování](virtual-networks-udr-overview.md) a [postup vytvořit směrovací tabulku](tutorial-create-route-table-portal.md)
    - **Koncové body služby:** podsíť může mít žádnou položku nebo více koncových bodů služby pro ni povolit. Chcete-li povolit koncový bod služby pro službu, vyberte služby nebo služby, které chcete povolit koncové body služby pro z **služby** seznamu. Umístění je automaticky nakonfigurován pro koncový bod. Ve výchozím nastavení jsou koncové body služby nakonfigurovaná pro virtuální síť oblast. Pro úložiště Azure pro podporu převzetí služeb při selhání regionální scénářů koncových bodů se automaticky konfigurují pro [Azure spárovat oblasti](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).

    Chcete-li odebrat koncového bodu služby, zrušte výběr službu, kterou chcete odebrat koncový bod služby pro. Další informace o koncových bodů služby a služby, lze povolit pro najdete v tématu [přehled koncové body služby virtuálních sítí](virtual-network-service-endpoints-overview.md). Jakmile povolíte koncového bodu služby pro službu, musíte zároveň povolit přístup k síti pro podsíť pro prostředek vytvořené pomocí služby. Například, pokud povolíte koncový bod služby pro *Microsoft.Storage*, musíte zároveň povolit přístup k síti pro všechny účty Azure Storage, které chcete udělit přístup k síti na. Podrobnosti o tom, jak povolit přístup k síti na podsítě, které je k dispozici koncového bodu služby najdete v dokumentaci pro jednotlivé služby, kterou jste povolili pro koncový bod služby.

    Chcete-li ověřit, že koncový bod služby je povolena pro podsíť, podívejte se [efektivní trasy](diagnose-network-routing-problem.md) pro všechny síťové rozhraní v podsíti. Pokud koncový bod je nakonfigurován, uvidíte *výchozí* trasa se předpony adres služby a nextHopType z **VirtualNetworkServiceEndpoint**. Další informace o směrování, najdete v části [Přehled směrování](virtual-networks-udr-overview.md).
6. Chcete-li přidat podsíť virtuální sítě, kterou jste vybrali, vyberte **OK**.

**Příkazy**

- Azure CLI: [vytvořit az podsíti virtuální sítě](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- Prostředí PowerShell: [přidat AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Změňte nastavení podsítě

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Seznam virtuálních sítí vyberte virtuální síť, která obsahuje podsíť, kterou chcete změnit nastavení pro.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. V seznamu podsítí vyberte podsíť, kterou chcete změnit nastavení pro. Můžete změnit následující nastavení:

    - **Rozsah adres:** Pokud nenajdete žádné prostředky nasazené v rámci podsítě, můžete změnit rozsah adres. Pokud neexistují žádné prostředky v podsíti, musíte buď přesunout prostředky k jiné podsíti, nebo nejprve odstraňte z podsítě. Kroky, které je třeba provést přesuňte nebo odstraňte prostředku se liší v závislosti na prostředek. Naučit se přesuňte nebo odstraňte prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý typ prostředku, který chcete přesunout nebo odstranit. V tématu omezení **rozsahu adres** v kroku 5 [přidat podsíť](#add-a-subnet).
    - **Uživatelé**: můžete řídit přístup k podsíti pomocí předdefinované role nebo vlastní role. Další informace o přiřazování rolí a uživatelům přístup k podsíti, najdete v části [použít přiřazení role pro správu přístupu k prostředkům Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - **Skupina zabezpečení sítě** a **směrovací tabulku**: informace najdete v kroku 5 tohoto [přidat podsíť](#add-a-subnet).
    - **Koncové body služby**: najdete v části koncové body služby v kroku 5 [přidat podsíť](#add-a-subnet). Při povolování koncového bodu služby pro existující podsíť, ujistěte se, že žádné kritické úlohy běží na prostředky v podsíti. Koncové body služby přepínače směrování na každé rozhraní sítě v podsíti z pomocí výchozí trasa *0.0.0.0/0* adres předpony a další typ směrování *Internet*, pomocí nové trasa se předpony služby a další typ směrování adres *VirtualNetworkServiceEndpoint*. Během přepínači může být ukončeno žádné otevřené připojení TCP. Koncový bod služby není povoleno, dokud tok přenosů dat ve službě pro všechna síťová rozhraní jsou aktualizované o novou trasu. Další informace o směrování, najdete v části [Přehled směrování](virtual-networks-udr-overview.md).
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- Prostředí PowerShell: [AzureRmVirtualNetworkSubnetConfig sady](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Odstranit podsíť

Podsíť může odstranit pouze v případě, že neexistují žádné prostředky v podsíti. Pokud v podsíti zdrojů, je nutné odstranit prostředky, které jsou v podsíti, chcete-li odstranit podsíť. Kroky, které je třeba provést odstranění prostředku se liší v závislosti na prostředek. Chcete-li zjistit, jak odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý typ prostředku, který chcete odstranit.

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** nezobrazí ve výsledcích hledání, vyberte ho.
2. Seznam virtuálních sítí vyberte virtuální síť, která obsahuje podsítě, kterou chcete odstranit.
3. V části **NASTAVENÍ** vyberte **Podsítě**.
4. V seznamu podsítí, vyberte **...** , na pravé straně, pro podsíť chcete odstranit
5. Vyberte **odstranit**a potom vyberte **Ano**.

**Příkazy**

- Azure CLI: [odstranit az sítě vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- Prostředí PowerShell: [AzureRmVirtualNetworkSubnetConfig odebrat](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

Úkoly v podsítích, musí mít váš účet přiřazenou k [Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena příslušné akce uvedené v následující tabulce:

|Akce                                                                   |   Název                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Přečtěte si podsíť virtuální sítě              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Vytvořit nebo aktualizovat podsíť virtuální sítě  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Odstranit podsíť virtuální sítě            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Připojení k virtuální síti                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Povolení koncového bodu služby pro podsíť     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Získat virtuální počítače v podsíti       |

## <a name="next-steps"></a>Další postup

- Vytvoření virtuální sítě a podsítě pomocí [prostředí PowerShell](powershell-samples.md) nebo [rozhraní příkazového řádku Azure](cli-samples.md) ukázkové skripty nebo pomocí Azure [šablony Resource Manageru](template-samples.md)
- Vytvoření a použití [Azure zásad](policy-samples.md) pro virtuální sítě