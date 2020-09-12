---
title: 'Azure ExpressRoute: propojení virtuální sítě k okruhu: CLI'
description: V tomto článku se dozvíte, jak propojit virtuální sítě (virtuální sítě) se ExpressRoute okruhy pomocí modelu nasazení Správce prostředků a rozhraní příkazového řádku.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/27/2020
ms.author: duau
ms.openlocfilehash: ac36e303cbeaf5167b5bbec3cea503c37b276058
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393203"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí rozhraní příkazového řádku

Tento článek vám pomůže propojit virtuální sítě (virtuální sítě) se všemi okruhy Azure ExpressRoute pomocí rozhraní příkazového řádku (CLI). Aby bylo možné propojit pomocí rozhraní příkazového řádku Azure, musí být virtuální sítě vytvořené pomocí modelu nasazení Správce prostředků. Můžou být buď ve stejném předplatném, nebo v rámci jiného předplatného. Pokud chcete k připojení virtuální sítě k okruhu ExpressRoute použít jinou metodu, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Potřebujete nejnovější verzi rozhraní příkazového řádku (CLI). Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Než začnete s konfigurací, musíte si projít [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute. 
  * Podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a pořiďte okruh povolený vaším poskytovatelem připojení. 
  * Ujistěte se, že máte pro váš okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny k směrování najdete v článku věnovaném [konfiguraci směrování](howto-routing-cli.md) . 
  * Ujistěte se, že je nakonfigurovaný privátní partnerský vztah Azure. Partnerský vztah protokolu BGP mezi vaší sítí a Microsoftem musí být zapnutý, aby bylo možné povolit kompletní připojení.
  * Ujistěte se, že máte virtuální síť a že byla vytvořena a plně zřízena Brána virtuální sítě. Podle pokynů [nakonfigurujte bránu virtuální sítě pro ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Nezapomeňte použít `--gateway-type ExpressRoute` .

* Na standardní okruh ExpressRoute můžete propojit až 10 virtuálních sítí. Pokud používáte standardní okruh ExpressRoute, musí být všechny virtuální sítě ve stejné geopolitické oblasti. 

* Jedna virtuální síť může být propojená s až čtyřmi okruhy ExpressRoute. Pomocí níže uvedeného postupu vytvořte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute můžou být ve stejném předplatném, různých předplatných nebo kombinaci obou.

* Pokud povolíte doplněk ExpressRoute Premium, můžete propojit virtuální síť mimo geopolitickou oblast okruhu ExpressRoute nebo k okruhu ExpressRoute připojit větší počet virtuálních sítí. Další informace o doplňku Premium najdete v části [Nejčastější dotazy](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě ve stejném předplatném k okruhu

Bránu virtuální sítě můžete připojit k okruhu ExpressRoute pomocí příkladu. Ujistěte se, že je brána virtuální sítě vytvořená a je připravená k propojení před spuštěním příkazu.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu

Okruh ExpressRoute můžete sdílet mezi několika předplatnými. Následující obrázek ukazuje jednoduché schéma, jak sdílení funguje pro okruhy ExpressRoute napříč několika předplatnými.

Každý z menších cloudů ve velkém cloudu se používá k reprezentaci předplatných, která patří do různých oddělení v rámci organizace. Každé oddělení v rámci organizace může používat vlastní předplatné pro nasazení svých služeb – ale může sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Jeden oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Další předplatná v rámci organizace můžou používat okruh ExpressRoute.

> [!NOTE]
> Pro vlastníka okruhu ExpressRoute se použijí poplatky za připojení a šířku pásma pro vyhrazený okruh. Všechny virtuální sítě mají stejnou šířku pásma.
> 
> 

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Správa – vlastníci okruhu a uživatelé okruhu

' Okruh Owner ' je autorizovaným uživatelem prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizaci, které může uplatnit "okruh uživatelů". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžou uplatnit autorizaci (jednu autorizaci na virtuální síť).

Vlastník okruhu má kdykoliv oprávnění upravovat a odvolat. V případě odvolání autorizace se všechna připojení propojení odstraňují z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace**

Vlastník okruhu vytvoří autorizaci, která vytvoří autorizační klíč, který může uživatel okruhu použít k připojení bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná jenom pro jedno připojení.

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

Vlastník okruhu může zkontrolovat všechna autorizační oprávnění, která jsou vydaná na konkrétním okruhu, a to spuštěním následujícího příkladu:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Přidání autorizací**

Vlastník okruhu může přidat autorizaci pomocí následujícího příkladu:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**Odstranění autorizací**

Vlastník okruhu může pro uživatele odvolat nebo odstranit autorizaci spuštěním následujícího příkladu:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operace uživatele obvodu

Uživatel okruhu potřebuje partnerského ID a autorizační klíč od vlastníka okruhu. Autorizační klíč je identifikátor GUID.

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Uplatnění autorizace připojení**

Uživatel okruhu může spustit následující příklad pro uplatnění autorizace propojení:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Uvolnění autorizace připojení**

Autorizaci můžete uvolnit odstraněním připojení, které propojuje okruh ExpressRoute s virtuální sítí.

## <a name="modify-a-virtual-network-connection"></a>Úprava připojení k virtuální síti
Můžete aktualizovat některé vlastnosti připojení k virtuální síti. 

**Aktualizace váhy připojení**

Vaše virtuální síť se může připojit k několika okruhům ExpressRoute. Můžete obdržet stejnou předponu z více než jednoho okruhu ExpressRoute. Pokud chcete zvolit připojení pro odesílání provozu určené pro tuto předponu, můžete změnit *RoutingWeight* připojení. Provoz se pošle na připojení s nejvyšší *RoutingWeight*.

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Rozsah *RoutingWeight* je 0 až 32000. Výchozí hodnota je 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurace ExpressRoute FastPath 
Můžete povolit [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) , pokud je vaše brána virtuální sítě v ultra Performance nebo ErGw3AZ. FastPath vylepšuje cestu dat preformance, jako jsou pakety za sekundu a připojení za sekundu mezi vaší místní sítí a virtuální sítí. 

**Konfigurace FastPath pro nové připojení**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Aktualizace existujícího připojení pro povolení FastPath**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
