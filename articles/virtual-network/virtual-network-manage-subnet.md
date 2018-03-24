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
ms.openlocfilehash: 16ce5aac26abcf2ef2cf7664fb0b9aae600708d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Přidat, změnit nebo odstranit podsíť virtuální sítě

Zjistěte, jak přidat, změnit nebo odstranit podsíť virtuální sítě. Pokud nejste obeznámení s virtuálními sítěmi, před přidat, změnit nebo odstranit podsíť, doporučujeme, abyste si přečetli [Přehled virtuálních sítí Azure](virtual-networks-overview.md) a [vytvoření, změny a odstranění virtuální sítě](manage-virtual-network.md). Všechny prostředky Azure, které jsou nasazeny do virtuální sítě se nasadí do podsítě virtuální sítě.
 
## <a name="before-you-begin"></a>Než začnete

Před dokončením kroků v žádné části tohoto článku dokončete následující úlohy:

- Pokud nemáte účet Azure, si zaregistrovat [Bezplatný zkušební účet](https://azure.microsoft.com/free).
- Pokud používáte portál, otevřete https://portal.azure.coma přihlaste se pomocí účtu Azure.
- Pokud pomocí příkazů prostředí PowerShell k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.2.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Pokud používáte rozhraní příkazového řádku Azure (CLI) příkazy k dokončení úloh v tomto článku, buď spusťte příkazy [prostředí cloudu Azure](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku z vašeho počítače. Tento kurz vyžaduje Azure CLI verze 2.0.26 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení s Azure.

## <a name="add-a-subnet"></a>Přidat podsíť

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Ze seznamu virtuálních sítí vyberte virtuální síť, kterou chcete přidat podsíť, kterou chcete.
3. V části **nastavení**, vyberte **podsítě**.
4. Vyberte **+ podsítě**.
5. Zadejte hodnoty následujících parametrů:
    - **Název**: název musí být jedinečný v rámci virtuální sítě.
    - **Rozsah adres**: rozsahu musí být jedinečný v rámci adresního prostoru virtuální sítě. Rozsah se nesmí překrývat s rozsahy adres jiné podsítě v rámci virtuální sítě. Adresní prostor musí být určena pomocí notace CIDR (Classless Inter-Domain směrování) zápisu. Ve virtuální síti s 10.0.0.0/16 prostoru adres, můžete třeba definovat adresního prostoru podsítě 10.0.0.0/24. Nejmenší oblast, kterou můžete zadat je /29, který poskytuje osm IP adresy podsítě. Azure si vyhrazuje adresu první a poslední v každé podsíti pro protokol shoda. Tři další adresy jsou vyhrazené pro použití služby Azure. V důsledku toho definování podsíť s/29 adres rozsah výsledků v tři použitelných IP adresách v podsíti. Pokud máte v plánu pro připojení virtuální sítě k bráně VPN, musíte vytvořit podsíť brány. Další informace o [aspekty rozsah konkrétní adresu podsítě brány](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Po přidání podsítě pro určité podmínky, můžete změnit rozsah adres. Zjistěte, jak změnit rozsah adres podsítě, najdete v tématu [změnit nastavení podsítě](#change-subnet-settings).
    - **Skupina zabezpečení sítě**: můžete přidružit nula nebo jeden existující skupinu zabezpečení sítě k podsíti pro filtrování příchozích a odchozích přenosů sítě pro podsíť. Skupina zabezpečení sítě, musí existovat ve stejném předplatném a umístění jako virtuální síť. Další informace o [skupin zabezpečení sítě](security-overview.md) a [jak vytvořit skupinu zabezpečení sítě](virtual-networks-create-nsg-arm-pportal.md).
    - **Směrovací tabulka**: můžete přidružit žádnou nebo jednu stávající tabulka směrování pro podsíť řídit směrování provozu sítě k jiným sítím. Směrovací tabulka musí existovat ve stejném předplatném a umístění jako virtuální síť. Další informace o [Azure směrování](virtual-networks-udr-overview.md) a [postup vytvořit směrovací tabulku](tutorial-create-route-table-portal.md)
    - **Koncové body služby:** podsíť může mít žádnou položku nebo více koncových bodů služby pro ni povolit. Chcete-li povolit koncový bod služby pro službu, vyberte služby nebo služby, které chcete povolit koncové body služby pro z **služby** seznamu. Chcete-li odebrat koncového bodu služby, zrušte výběr službu, kterou chcete odebrat koncový bod služby pro. Další informace o koncových bodů služby najdete v tématu [přehled koncové body služby virtuálních sítí](virtual-network-service-endpoints-overview.md). Jakmile povolíte koncového bodu služby pro službu, musíte zároveň povolit přístup k síti pro podsíť pro prostředek vytvořené pomocí služby. Například, pokud povolíte koncový bod služby pro *Microsoft.Storage*, musíte zároveň povolit přístup k síti pro všechny účty Azure Storage, které chcete udělit přístup k síti na. Podrobnosti o tom, jak povolit přístup k síti na podsítě, které je k dispozici koncového bodu služby najdete v dokumentaci pro jednotlivé služby, kterou jste povolili pro koncový bod služby.
6. Chcete-li přidat podsíť virtuální sítě, kterou jste vybrali, vyberte **OK**.

**Příkazy**

- Azure CLI: [vytvořit az podsíti virtuální sítě](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Změňte nastavení podsítě

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Seznam virtuálních sítí vyberte virtuální síť, která obsahuje podsíť, kterou chcete změnit nastavení pro.
3. V části **nastavení**, vyberte **podsítě**.
4. V seznamu podsítí vyberte podsíť, kterou chcete změnit nastavení pro. Můžete změnit následující nastavení:

    - **Rozsah adres:** Pokud nenajdete žádné prostředky nasazené v rámci podsítě, můžete změnit rozsah adres. Pokud neexistují žádné prostředky v podsíti, musíte buď přesunout prostředky k jiné podsíti, nebo nejprve odstraňte z podsítě. Kroky, které je třeba provést přesuňte nebo odstraňte prostředku se liší v závislosti na prostředek. Naučit se přesuňte nebo odstraňte prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý typ prostředku, který chcete přesunout nebo odstranit. V tématu omezení **rozsahu adres** v kroku 5 [přidat podsíť](#add-a-subnet).
    - **Uživatelé**: můžete řídit přístup k podsíti pomocí předdefinované role nebo vlastní role. Další informace o přiřazování rolí a uživatelům přístup k podsíti, najdete v části [použít přiřazení role pro správu přístupu k prostředkům Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - Informace o změně **skupinu zabezpečení sítě**, **směrovací tabulku**, **uživatelé**, a **koncové body služby**, najdete v kroku 5 v [ Přidat podsíť](#add-a-subnet).
5. Vyberte **Uložit**.

**Příkazy**

- Azure CLI: [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Odstranit podsíť

Podsíť může odstranit pouze v případě, že neexistují žádné prostředky v podsíti. Pokud v podsíti zdrojů, je nutné odstranit prostředky, které jsou v podsíti, chcete-li odstranit podsíť. Kroky, které je třeba provést odstranění prostředku se liší v závislosti na prostředek. Chcete-li zjistit, jak odstranit prostředky, které jsou v podsítích, přečtěte si dokumentaci pro každý typ prostředku, který chcete odstranit.

1. Do vyhledávacího pole v horní části portálu, zadejte *virtuální sítě* do vyhledávacího pole. Když **virtuální sítě** se zobrazí ve výsledcích hledání, vyberte ho.
2. Seznam virtuálních sítí vyberte virtuální síť, která obsahuje podsítě, kterou chcete odstranit.
3. V části **nastavení**, vyberte **podsítě**.
4. V seznamu podsítí, vyberte **...** , na pravé straně, pro podsíť chcete odstranit
5. Vyberte **odstranit**a potom vyberte **Ano**.

**Příkazy**

- Azure CLI: [odstranit az sítě vnet](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Oprávnění

Úkoly v podsítích, musí mít váš účet přiřazenou k [Přispěvatel sítě](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolí nebo [vlastní](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role, která je přiřazena odpovídající oprávnění uvedených v následující tabulce:

|Operace                                                                |   Název operace                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Získat podsíť virtuální sítě                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Vytvořit nebo aktualizovat podsíť virtuální sítě      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Odstranit podsíť virtuální sítě                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Připojení k virtuální síti                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Připojení služby k podsíti                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Získat virtuální počítače podsítě virtuální sítě  |
