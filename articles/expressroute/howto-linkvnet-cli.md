---
title: 'Azure ExpressRoute: Propojení virtuální sítě s okruhem: rozhraní příkazového příkazu'
description: Tento článek ukazuje, jak propojit virtuální sítě (Virtuální sítě) s okruhy ExpressRoute pomocí modelu nasazení Resource Manager a CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: fdd809bcba703dbd8f9ee1e7c18185fd20e4586f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476130"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí cli

Tento článek vám pomůže propojit virtuální sítě (Virtuální sítě) s okruhy Azure ExpressRoute pomocí rozhraní příkazového příkazového příkazu. Chcete-li propojit pomocí azure cli, virtuální sítě musí být vytvořeny pomocí modelu nasazení Resource Manager. Mohou být buď ve stejném předplatném, nebo část jiného předplatného. Pokud chcete použít jinou metodu pro připojení virtuální sítě k okruhu ExpressRoute, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Potřebujete nejnovější verzi rozhraní příkazového řádku (CLI). Další informace najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Před zahájením konfigurace je třeba zkontrolovat [požadavky](expressroute-prerequisites.md)na směrování , [požadavky na směrování](expressroute-routing.md)a pracovní [postupy.](expressroute-workflows.md)

* Musí mít aktivní okruh ExpressRoute. 
  * Podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte obvod povolený poskytovatelem připojení. 
  * Ujistěte se, že máte azure privátní partnerský vztah nakonfigurovaný pro váš okruh. Pokyny pro směrování naleznete v článku [konfigurace směrování.](howto-routing-cli.md) 
  * Ujistěte se, že je nakonfigurovaný privátní partnerský vztah Azure. Partnerský vztah protokolu BGP mezi sítí a společností Microsoft musí být nahoru, aby bylo možné povolit připojení mezi koncovými soubory.
  * Ujistěte se, že máte vytvořenou a plně zřízené virtuální síť a bránu virtuální sítě. Podle pokynů [nakonfigurujte bránu virtuální sítě pro ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Ujistěte se, že používáte `--gateway-type ExpressRoute`.

* Ke standardnímu okruhu ExpressRoute můžete propojit až 10 virtuálních sítí. Všechny virtuální sítě musí být ve stejné geopolitické oblasti při použití standardního okruhu ExpressRoute. 

* Jedna virtuální síť může být propojena až ke čtyřem okruhům ExpressRoute. Pomocí níže uvedeného procesu vytvořte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute může být ve stejném předplatném, různých předplatných nebo kombinací obou.

* Pokud povolíte doplněk Premium ExpressRoute, můžete propojit virtuální síť mimo geopolitickou oblast okruhu ExpressRoute nebo připojit větší počet virtuálních sítí k okruhu ExpressRoute. Další informace o prémiovém doplňku najdete v [nejčastějších dotazech](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě ve stejném předplatném k okruhu

Bránu virtuální sítě můžete připojit k okruhu ExpressRoute pomocí příkladu. Ujistěte se, že brána virtuální sítě je vytvořen a je připraven k propojení před spuštěním příkazu.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu

Okruh ExpressRoute můžete sdílet mezi více předplatnými. Následující obrázek ukazuje jednoduché schéma fungování sdílení pro okruhy ExpressRoute napříč více předplatnými.

Každý z menších cloudů v rámci velkého cloudu se používá k reprezentaci předplatných, které patří do různých oddělení v rámci organizace. Každé oddělení v rámci organizace můžete použít své vlastní předplatné pro nasazení svých služeb – ale mohou sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Okruh ExpressRoute může vlastnit jedno oddělení (v tomto příkladu: IT). Ostatní předplatná v rámci organizace můžete použít okruh ExpressRoute.

> [!NOTE]
> Poplatky za připojení a šířku pásma vyhrazeného okruhu budou použity na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojení napříč předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Správa - Vlastníci obvodů a uživatelů okruhů

Vlastník obvodu je oprávněným uživatelem napájení prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizace, které mohou být uplatněny 'Uživatelé okruhu'. Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute. Uživatelé okruhu mohou uplatnit autorizace (jedna autorizace na virtuální síť).

Vlastník obvodu má pravomoc kdykoli upravit a odvolat autorizace. Při odvolání autorizace jsou všechna připojení propojení odstraněna z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace**

Vlastník okruhu vytvoří autorizaci, která vytvoří autorizační klíč, který může uživatel okruhu použít k připojení svých bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná pouze pro jedno připojení.

Následující příklad ukazuje, jak vytvořit autorizaci:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Odpověď obsahuje autorizační klíč a stav:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Kontrola autorizací**

Vlastník okruhu může zkontrolovat všechna oprávnění, která jsou vydána na určitém okruhu spuštěním následujícího příkladu:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Přidání autorizací**

Vlastník okruhu můžete přidat autorizace pomocí následujícího příkladu:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Odstranění autorizací**

Vlastník okruhu může odvolat nebo odstranit autorizace pro uživatele spuštěním následujícího příkladu:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operace uživatele okruhu

Uživatel okruhu potřebuje ID druhé strany a autorizační klíč od vlastníka okruhu. Autorizační klíč je identifikátor GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Uplatnění autorizace připojení**

Uživatel okruhu může spustit následující příklad pro uplatnění autorizace odkazu:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Uvolnění autorizace připojení**

Autorizaci můžete uvolnit odstraněním připojení, které propojuje okruh ExpressRoute s virtuální sítí.

## <a name="modify-a-virtual-network-connection"></a>Úprava připojení k virtuální síti
Můžete aktualizovat určité vlastnosti připojení k virtuální síti. 

**Aktualizace hmotnosti připojení**

Virtuální síť může být připojena k více okruhům ExpressRoute. Můžete obdržet stejnou předponu z více než jednoho okruhu ExpressRoute. Chcete-li zvolit, které připojení má být odesláno pro tuto předponu, můžete změnit *tloušťku směrování* připojení. Provoz bude odeslán na připojení s nejvyšší *směrovací hmotností*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Rozsah *Tloušťka směrování* je 0 až 32000. Výchozí hodnota je 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurace rychlé cesty ExpressRoute 
[ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) můžete povolit, pokud je okruh ExpressRoute na [ExpressRoute Direct](expressroute-erdirect-about.md) a vaše virtuální brána newtork je Ultra Performance nebo ErGw3AZ. FastPath vylepšuje předreformanci datových cest, jako jsou pakety za sekundu a připojení za sekundu mezi místní sítí a virtuální sítí. 

**Konfigurace aplikace FastPath pro nové připojení**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Aktualizace existujícího připojení pro povolení rychlé cesty**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Další kroky

Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
