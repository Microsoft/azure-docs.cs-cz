---
title: 'Konfigurace průchodu bránou sítě VPN pro partnerský vztah virtuální sítě: Azure Resource Manageru | Dokumentace Microsoftu'
description: Nakonfigurujte průchod bránou VPN pro partnerský vztah virtuální sítě.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: 5f8f282db9468d84c3a1fa16c5cd481f2dd0970e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415914"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurace průchodu bránou VPN pro partnerský vztah virtuální sítě

Tento článek vám pomůže nakonfigurovat průchod bránou pro partnerský vztah virtuální sítě. [Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) umožňuje hladké připojení ke dvěma virtuálním sítím Azure a pro účely připojení je sloučí do jedné. [Průchod bránou](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) je vlastnost partnerského uzlu, která umožňuje, aby jedna virtuální síť využila bránu VPN v partnerské virtuální síti pro připojení mezi více místy nebo virtuálními sítěmi. Následující diagram znázorňuje, jak funguje průchod bránou s využitím partnerského vztahu virtuální sítě.

![Průchod bránou](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

V diagramu průchod bránou umožňuje partnerským virtuálním sítím používat službu Azure VPN Gateway v centrálním Resource Manageru (Hub-RM). Možnosti připojení dostupné u brány VPN, včetně připojení S2S, P2S a připojení mezi virtuálními sítěmi, platí pro všechny tři virtuální sítě. Možnost průchodu je dostupná pro partnerský uzel mezi stejnými nebo různými modely nasazení. Z diagramu je patrné jedno omezení – brána VPN se může nacházet pouze ve virtuální síti, která používá model nasazení Resource Manager.

V hvězdicové síťové architektuře průchod bránou umožňuje koncovým virtuálním sítím sdílet bránu VPN v centrálním uzlu a není tedy nutné nasazovat brány VPN do každé koncové virtuální sítě. Pomocí průchodu bránou se trasy do virtuálních sítí připojených k bráně nebo do místních sítí rozšíří do směrovacích tabulek partnerských virtuálních sítí. Automatické rozšíření tras z brány VPN můžete zakázat. Vytvořte směrovací tabulku s možností **Zakázat šíření tras protokolu BGP** a přidružte směrovací tabulku k podsítím, abyste zabránili distribuci tras do těchto podsítí. Další informace najdete v článku o [směrovací tabulce virtuální sítě](../virtual-network/manage-route-table.md).

V tomto dokumentu jsou popsány dva scénáře:

1. Obě virtuální sítě používají model nasazení Resource Manager.
2. Koncová virtuální síť je klasická a centrální virtuální síť s bránou je v Resource Manageru.

> [!IMPORTANT]
> Průchod bránou se momentálně nepodporuje u globálního partnerského vztahu virtuální sítě.

## <a name="requirements"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Příklad v tomto dokumentu vyžaduje, aby byly vytvořeny následující prostředky:

1. Centrální virtuální síť v Resource Manageru (Hub-RM) s bránou VPN
2. Koncová virtuální síť v Resource Manageru (Spoke-RM)
3. Koncová klasická virtuální síť (Spoke-Classic) s klasickým modelem nasazení
4. Používaný účet vyžaduje nezbytné role a oprávnění. V části [Oprávnění](#permissions) v tomto článku najdete podrobnosti.

Pokyny najdete v následujících dokumentech, které se týkají:

1. [Vytvoření brány VPN ve virtuální síti](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Vytvoření partnerského vztahu virtuální sítě se stejným modelem nasazení](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Vytvoření partnerského vztahu virtuální sítě s různými modely nasazení](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a>Oprávnění

Účty použité k vytvoření partnerského uzlu virtuální sítě musí mít nezbytné role a oprávnění. Pokud jste vytvořili partnerský vztah mezi dvěma virtuálními sítěmi s názvem Hub-RM a Spoke-Classic, musí mít váš účet následující role nebo oprávnění pro každou virtuální síť, jak je znázorněno v následujícím příkladu:
    
|Virtuální síť|Model nasazení|Role|Oprávnění|
|---|---|---|---|
|Hub-RM|Resource Manager|[Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classic|[Přispěvatel klasických sítí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|neuvedeno|
|Spoke-Classic|Resource Manager|[Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classic|[Přispěvatel klasických sítí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Přečtěte si další informace o [integrovaných rolích](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) a přiřazení konkrétních oprávnění k [vlastním rolím](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (platí pouze pro Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Partnerský vztah Resource Managerů s průchodem bránou

Podle pokynů vytvořte nebo aktualizujte partnerské vztahy virtuálních sítí a povolte průchod bránou.

1. Na portálu Azure Portal vytvořte nebo aktualizujte partnerský vztah virtuální sítě ze Spoke-RM do Hub-RM. Přejděte na prostředek virtuální sítě Spoke-RM, klikněte na Partnerské vztahy a potom na Přidat:
    - Nastavte možnost Resource Manager.
    - Vyberte virtuální síť Hub-RM v odpovídajícím předplatném.
    - Zkontrolujte, že možnost Povolit přístup k virtuální síti je nastavena na Povoleno.
    - Nastavte možnost **Používat vzdálené brány**.
    - Klikněte na OK.

      ![Ze spokerm do hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Pokud jste partnerský vztah už vytvořili, přejděte na jeho prostředek a povolte možnost **Používat vzdálené brány** podobně jako na obrázku v kroku (1).

3. Na portálu Azure Portal vytvořte nebo aktualizujte partnerský vztah virtuální sítě z Hub-RM do Spoke-RM. Přejděte na prostředek virtuální sítě Hub-RM, klikněte na Partnerské vztahy a potom na Přidat:
    - Nastavte možnost Resource Manager.
    - Zkontrolujte, že možnost Povolit přístup k virtuální síti je nastavena na Povoleno.
    - Vyberte virtuální síť Spoke-RM v odpovídajícím předplatném.
    - Nastavte možnost **Povolit průchod bránou**.
    - Klikněte na OK.

      ![Z hubrm do spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Pokud jste partnerský vztah už vytvořili, přejděte na jeho prostředek a povolte možnost **Povolit průchod bránou** podobně jako na obrázku v kroku (3).

5. Zkontrolujte, že stav partnerského uzlu je **Připojeno** u obou virtuálních sítí.

### <a name="powershell-sample"></a>Ukázka PowerShellu

K vytvoření nebo aktualizaci partnerského uzlu v uvedeném příkladu můžete použít také PowerShell. Nahraďte proměnné názvy virtuálních sítí a skupin prostředků.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Partnerský vztah klasické sítě a Resource Manageru s průchodem bránou

Postup je podobný jako v případě Resource Manageru, ale operace se použijí pouze u virtuální sítě Hub-RM.

1. Na portálu Azure Portal vytvořte nebo aktualizujte partnerský vztah virtuální sítě z Hub-RM do Spoke-RM. Přejděte na prostředek virtuální sítě Hub-RM, klikněte na Partnerské vztahy a potom na Přidat:
    - Nastavte možnost Klasický pro model nasazení virtuální sítě.
    - Vyberte virtuální síť Spoke-Classic v odpovídajícím předplatném.
    - Zkontrolujte, že možnost Povolit přístup k virtuální síti je nastavena na Povoleno.
    - Nastavte možnost **Povolit průchod bránou**.
    - Klikněte na OK.

    ![Z hubrm do spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Pokud jste partnerský vztah už vytvořili, přejděte na jeho prostředek a povolte možnost **Povolit průchod bránou** podobně jako na obrázku v kroku (1).

3. U virtuální sítě Spoke-Classic neexistuje žádná operace.

4. Zkontrolujte, že stav partnerského uzlu je **Připojeno** u virtuální sítě Hub-RM.

Jakmile se u stavu objeví údaj Připojeno, koncové virtuální sítě mohou začít používat připojení virtuálních sítí nebo připojení mezi místními sítěmi přes bránu VPN v centrální virtuální síti.

### <a name="powershell-sample"></a>Ukázka PowerShellu

K vytvoření nebo aktualizaci partnerského uzlu v uvedeném příkladu můžete použít také PowerShell. Nahraďte proměnné a ID předplatného hodnotami vaší virtuální sítě a skupin prostředků a příslušným předplatným. Partnerský vztah virtuální sítě je třeba vytvořit pouze u centrální virtuální sítě.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Další postup

* Než se pustíte do vytváření partnerského vztahu virtuální sítě pro použití v produkčním prostředí, přečtěte si další informace o [omezeních a chování partnerského uzlu virtuální sítě](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) a [nastavení partnerského vztahu virtuální sítě](../virtual-network/virtual-network-manage-peering.md#create-a-peering).
* Přečtěte si, jak [vytvořit topologii centrální a koncové sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerským vztahem virtuální sítě a průchodem bránou.
