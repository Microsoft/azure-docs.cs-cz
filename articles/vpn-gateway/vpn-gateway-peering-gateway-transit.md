---
title: Konfigurace průchodu bránou VPN pro partnerský vztah virtuální sítě
description: Konfigurací přenosu brány pro partnerský vztah virtuálních sítí umožníte bezproblémové připojení dvou virtuálních sítí Azure k jednomu pro účely připojení.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/30/2020
ms.author: cherylmc
ms.openlocfilehash: 73a7d76de34d29b2d51c54569b234cd8221b08f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872175"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurace průchodu bránou VPN pro partnerský vztah virtuální sítě

Tento článek vám pomůže nakonfigurovat průchod bránou pro partnerský vztah virtuální sítě. [Partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) umožňuje hladké připojení ke dvěma virtuálním sítím Azure a pro účely připojení je sloučí do jedné. [Přenos brány](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) je vlastnost partnerského vztahu, která umožňuje, aby jedna virtuální síť používala bránu VPN v partnerské virtuální síti pro připojení mezi místními nebo VNet-to-VNet. Následující diagram znázorňuje, jak funguje průchod bránou s využitím partnerského vztahu virtuální sítě.

![Diagram přenosu brány](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

V diagramu průchod bránou umožňuje partnerským virtuálním sítím používat službu Azure VPN Gateway v centrálním Resource Manageru (Hub-RM). Možnosti připojení dostupné u brány VPN, včetně připojení S2S, P2S a připojení mezi virtuálními sítěmi, platí pro všechny tři virtuální sítě. Možnost přenosu je dostupná pro partnerský vztah mezi stejnými nebo různými modely nasazení. Pokud konfigurujete přenos mezi různými modely nasazení, musí být virtuální síť rozbočovače a Brána virtuální sítě v modelu nasazení Správce prostředků, nikoli v modelu nasazení Classic.
>

V hvězdicové síťové architektuře průchod bránou umožňuje koncovým virtuálním sítím sdílet bránu VPN v centrálním uzlu a není tedy nutné nasazovat brány VPN do každé koncové virtuální sítě. Pomocí průchodu bránou se trasy do virtuálních sítí připojených k bráně nebo do místních sítí rozšíří do směrovacích tabulek partnerských virtuálních sítí. Automatické rozšíření tras z brány VPN můžete zakázat. Vytvořte směrovací tabulku s možností **Zakázat šíření tras protokolu BGP** a přidružte směrovací tabulku k podsítím, abyste zabránili distribuci tras do těchto podsítí. Další informace najdete v článku o [směrovací tabulce virtuální sítě](../virtual-network/manage-route-table.md).

V tomto článku jsou dva scénáře:

* **Stejný model nasazení**: obě virtuální sítě se vytvářejí v modelu nasazení Správce prostředků.
* **Různé modely nasazení**: virtuální síť paprsků je vytvořená v modelu nasazení Classic a virtuální síť centra a brána jsou v modelu nasazení Správce prostředků.

>[!NOTE]
> Pokud provedete změnu topologie sítě a máte klienty VPN se systémem Windows, musí se balíček klienta VPN pro klienty se systémem Windows stáhnout a nainstalovat znovu, aby se změny projevily u klienta.
>

## <a name="prerequisites"></a>Požadavky

Než začnete, ověřte, zda máte následující virtuální sítě a oprávnění:

### <a name="virtual-networks"></a><a name="vnet"></a>Virtuální sítě

|Virtuální síť|Model nasazení| Brána virtuální sítě|
|---|---|---|---|
| Hub-RM| [Resource Manager](./tutorial-site-to-site-portal.md)| [Ano](tutorial-create-gateway-portal.md)|
| Paprskový – RM | [Resource Manager](./tutorial-site-to-site-portal.md)| No |
| Spoke-Classic | [Standardním](vpn-gateway-howto-site-to-site-classic-portal.md#CreatVNet) | No |

### <a name="permissions"></a><a name="permissions"></a>Oprávnění

Účty použité k vytvoření partnerského uzlu virtuální sítě musí mít nezbytné role a oprávnění. Pokud jste v níže uvedeném příkladu nastavili partnerský vztah mezi dvěma virtuálními sítěmi s názvem **hub-RM** a **paprskovým klasický**, váš účet musí mít pro každou virtuální síť následující role nebo oprávnění:

|Virtuální síť|Model nasazení|Role|Oprávnění|
|---|---|---|---|
|Hub-RM|Resource Manager|[Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klasický|[Přispěvatel klasických sítí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|–|
|Spoke-Classic|Resource Manager|[Přispěvatel sítě](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klasický|[Přispěvatel klasických sítí](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Přečtěte si další informace o [integrovaných rolích](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (platí pouze pro Resource Manager).

## <a name="same-deployment-model"></a><a name="same"></a>Stejný model nasazení

V tomto scénáři jsou virtuální sítě v modelu nasazení Správce prostředků. Pomocí následujících kroků můžete vytvořit nebo aktualizovat partnerské vztahy virtuální sítě a povolit tak přenos brány.

### <a name="to-add-a-peering-and-enable-transit"></a>Přidání partnerského vztahu a povolení přenosu

1. V [Azure Portal](https://portal.azure.com)vytvořte nebo aktualizujte partnerský vztah virtuální sítě z centra – RM. Přejděte do virtuální sítě **hub-RM** . Vyberte **partnerské vztahy** a potom **+ Přidat** pro otevření **Přidat partnerský vztah**.
1. Na stránce **Přidat partnerský vztah** nakonfigurujte hodnoty pro **tuto virtuální síť**.

   * Název propojení partnerského vztahu: název odkazu. Příklad: **HubRMToSpokeRM**
   * Provoz do vzdálené virtuální sítě: **Povolení**
   * Přenosy předané ze vzdálené virtuální sítě: **Povolení**
   * Brána virtuální sítě: **použít tuto bránu virtuální sítě**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-vnet.png" alt-text="Snímek obrazovky ukazuje přidat partnerský vztah.":::

1. Na stejné stránce pokračujte a nakonfigurujte hodnoty pro **vzdálenou virtuální síť**.

   * Název propojení partnerského vztahu: název odkazu. Příklad: **SpokeRMtoHubRM**
   * Model nasazení: **Správce prostředků**
   * Virtual Network: **paprskový – RM**
   * Provoz do vzdálené virtuální sítě: **Povolení**
   * Přenosy předané ze vzdálené virtuální sítě: **Povolení**
   * Brána virtuální sítě: **použijte bránu vzdálené virtuální sítě** .

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-remote.png" alt-text="Snímek obrazovky zobrazuje hodnoty pro vzdálenou virtuální síť.":::

1. Vyberte **Přidat** a vytvořte partnerský vztah.
1. Ověřte, že stav partnerského vztahu je v obou virtuálních sítích **připojeno** .

### <a name="to-modify-an-existing-peering-for-transit"></a>Úprava existujícího partnerského vztahu pro přenos

Pokud byl partnerský vztah již vytvořen, můžete upravit partnerský vztah pro přenos.

1. Přejděte do virtuální sítě. Vyberte **partnerské vztahy** a vyberte partnerské vztahy, které chcete upravit.

   :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-modify.png" alt-text="Snímek obrazovky zobrazuje výběr partnerských vztahů.":::

1. Aktualizujte partnerský vztah virtuální sítě.

   * Provoz do vzdálené virtuální sítě: **Povolení**
   * Provoz předaný do virtuální sítě; **Povolení**
   * Brána virtuální sítě: **použít bránu vzdálené virtuální sítě**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/modify-peering-settings.png" alt-text="Snímek obrazovky ukazuje upravit bránu partnerského vztahu.":::

1. **Uložte** nastavení partnerského vztahu.

### <a name="powershell-sample"></a><a name="ps-same"></a>Ukázka PowerShellu

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

## <a name="different-deployment-models"></a><a name="different"></a>Různé modely nasazení

V této konfiguraci je paprskový **uzel VNet-Classic** v modelu nasazení Classic a rozbočovač VNet hub **– RM** je v modelu nasazení Správce prostředků. Při konfiguraci přenosu mezi modely nasazení je potřeba nakonfigurovat bránu virtuální sítě pro Správce prostředků VNet, ne pro klasickou virtuální síť.

Pro tuto konfiguraci stačí nakonfigurovat virtuální síť **hub-RM** . Nemusíte nic konfigurovat pro virtuální síť **paprsků-Classic** .

1. V Azure Portal přejděte do virtuální sítě **hub-RM** , vyberte **partnerské vztahy** a pak vyberte **+ Přidat**.
1. Na stránce **Přidat partnerský vztah** nakonfigurujte následující hodnoty:

   * Název propojení partnerského vztahu: název odkazu. Příklad: **HubRMToClassic**
   * Provoz do vzdálené virtuální sítě: **Povolení**
   * Přenosy předané ze vzdálené virtuální sítě: **Povolení**
   * Brána virtuální sítě: **použít tuto bránu virtuální sítě**
   * Vzdálená virtuální síť: **klasický**

     :::image type="content" source="./media/vpn-gateway-peering-gateway-transit/peering-classic.png" alt-text="Přidat stránku partnerského vztahu pro paprskový – klasický":::

1. Ověřte, že je předplatné správné, a pak v rozevíracím seznamu vyberte virtuální síť.
1. Vyberte **Přidat** a přidejte partnerský vztah.
1. Ověřte stav partnerského vztahu **připojeného** ve virtuální síti hub-RM. 

Pro tuto konfiguraci nemusíte konfigurovat žádné součásti na virtuální síti **paprsk-Classic** . Jakmile se stav zobrazí jako **připojeno**, může virtuální síť paprsků používat připojení prostřednictvím brány VPN ve virtuální síti rozbočovače.

### <a name="powershell-sample"></a><a name="ps-different"></a>Ukázka PowerShellu

K vytvoření nebo aktualizaci partnerského uzlu v uvedeném příkladu můžete použít také PowerShell. Nahraďte proměnné a ID předplatného hodnotami vaší virtuální sítě a skupin prostředků a příslušným předplatným. Partnerský vztah virtuální sítě je třeba vytvořit pouze u centrální virtuální sítě.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToClassic `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Další kroky

* Než se pustíte do vytváření partnerského vztahu virtuální sítě pro použití v produkčním prostředí, přečtěte si další informace o [omezeních a chování partnerského uzlu virtuální sítě](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) a [nastavení partnerského vztahu virtuální sítě](../virtual-network/virtual-network-manage-peering.md#create-a-peering).
* Přečtěte si, jak [vytvořit topologii centrální a koncové sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s partnerským vztahem virtuální sítě a průchodem bránou.
* [Vytvořte partnerský vztah virtuálních sítí se stejným modelem nasazení](../virtual-network/tutorial-connect-virtual-networks-portal.md).
* [Vytvořte partnerský vztah virtuálních sítí s různými modely nasazení](../virtual-network/create-peering-different-deployment-models.md).