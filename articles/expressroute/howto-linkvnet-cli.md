---
title: 'Azure ExpressRoute: propojení virtuální sítě k okruhu: CLI'
description: Tento článek ukazuje, jak propojit virtuální sítě (Vnet) pro okruhy ExpressRoute pomocí modelu nasazení Resource Manager a rozhraní příkazového řádku.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.openlocfilehash: c80c667cb281168de6f11bbb6a536c01fefb7935
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206958"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí rozhraní příkazového řádku

Tento článek pomáhá propojení virtuálních sítí (Vnet) pro okruhy Azure ExpressRoute pomocí rozhraní příkazového řádku. Pokud chcete propojit pomocí Azure CLI, musí být virtuální sítě vytvořené pomocí modelu nasazení Resource Manager. Může být buď ve stejném předplatném nebo součástí jiného předplatného. Pokud chcete použít jinou metodu, chcete-li propojit vaši virtuální síť k okruhu ExpressRoute, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Budete potřebovat nejnovější verzi rozhraní příkazového řádku (CLI). Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Než začnete s konfigurací, musíte si projít [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute. 
  * Podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a pořiďte okruh povolený vaším poskytovatelem připojení. 
  * Ujistěte se, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Pokyny k směrování najdete v článku věnovaném [konfiguraci směrování](howto-routing-cli.md) . 
  * Ujistěte se, že je nakonfigurovaný soukromého partnerského vztahu Azure. Partnerský vztah mezi vaší sítí a Microsoftem protokolu BGP musí být nahoru tak, že povolíte připojení k začátku do konce.
  * Ujistěte se, že máte virtuální síť a Brána virtuální sítě vytvořené a plně zřízený. Podle pokynů [nakonfigurujte bránu virtuální sítě pro ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Nezapomeňte použít `--gateway-type ExpressRoute`.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute. 

* Jedné virtuální sítě nesmí být propojení až čtyři okruhy ExpressRoute. Můžete vytvořit nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být ve stejném předplatném, různá předplatná nebo kombinaci obou.

* Pokud povolíte doplněk ExpressRoute premium, můžete propojit virtuální síť mimo geopolitické oblasti okruh ExpressRoute nebo připojit větší počet virtuálních sítí pro váš okruh ExpressRoute. Další informace o doplňku Premium najdete v části [Nejčastější dotazy](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě v rámci stejného předplatného k okruhu

Brána virtuální sítě k okruhu ExpressRoute můžete připojit pomocí příkladu. Ujistěte se, že brány virtuální sítě je vytvořen a připraven k propojení před spuštěním příkazu.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu

Okruh ExpressRoute můžete sdílet napříč několika předplatnými. Následující obrázek znázorňuje jednoduchý způsob sdílení prací pro okruhy ExpressRoute schéma napříč několika předplatnými.

Každá z menších cloudy v rámci velkých cloudových se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Vlastní předplatné každé oddělení v rámci organizace můžete použít k nasazení svých služeb – ale můžete sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Jednoho oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Okruh ExpressRoute můžete použít další předplatná v rámci organizace.

> [!NOTE]
> Poplatky za připojení a šířku pásma pro vyhrazeného okruhu uplatní se na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojením mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Správa – okruh uživatelů a vlastníků okruh

Vlastníka okruhu je autorizovaný uživatel Power prostředku okruhu ExpressRoute. Vlastníka okruhu můžete vytvářet autorizace, které můžou uplatnit: uživatelé okruhu". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute. Uživatelé okruhu můžete uplatnění autorizací (jedním autorizačním jednu virtuální síť).

Vlastníka okruhu má schopnost upravovat dokumentů a odvolání přístupu autorizace kdykoli. Při povolení odvolán, odstraní se všechna připojení odkaz z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace**

Vlastníka okruhu vytvoří autorizaci, které vytvoří autorizační klíč, který je možné připojit své brány virtuální sítě k okruhu ExpressRoute uživatelem okruhu. Povolení platí jenom jedno připojení.

Následující příklad ukazuje, jak vytvořit povolení:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Odpověď obsahuje autorizační klíč a stav:

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**Kontrola autorizací**

Vlastníka okruhu můžete zkontrolovat všechny autorizace, které jsou vydány na konkrétní okruhu spuštěním následujícího příkladu:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Přidání autorizací**

Vlastníka okruhu můžete přidat autorizace pomocí v následujícím příkladu:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Odstranění autorizací**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací uživateli spuštěním následujícího příkladu:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operace pro uživatele okruhu

ID partnera a autorizační klíč z vlastníka okruhu, musí uživatel okruhu. Autorizační klíč je identifikátor GUID.

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Uplatnění autorizace připojení**

Uživatel okruhu můžete spustit podle následujícího příkladu lze uplatnit autorizaci odkaz:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Uvolnění autorizace připojení**

Povolení můžete uvolnit tak, že odstraníte okruh ExpressRoute k virtuální síti, která odkazuje připojení.

## <a name="modify-a-virtual-network-connection"></a>Upravit připojení k virtuální síti
Můžete aktualizovat některé vlastnosti připojení k virtuální síti. 

**Aktualizace váhy připojení**

Virtuální síť lze připojit k více okruhům ExpressRoute. Může se zobrazit stejné předpony z více než jeden okruh ExpressRoute. Pokud chcete zvolit připojení pro odesílání provozu určené pro tuto předponu, můžete změnit *RoutingWeight* připojení. Provoz se pošle na připojení s nejvyšší *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Rozsah *RoutingWeight* je 0 až 32000. Výchozí hodnota je 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurace ExpressRoute FastPath 
Můžete povolit [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) , pokud je váš okruh ExpressRoute na [ExpressRoute Direct](expressroute-erdirect-about.md) a vaše virtuální síť. Brána je Ultra Performance nebo ErGw3AZ. FastPath vylepšuje cestu dat preformance, jako jsou pakety za sekundu a připojení za sekundu mezi vaší místní sítí a virtuální sítí. 

**Konfigurace FastPath pro nové připojení**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Aktualizace existujícího připojení pro povolení FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
