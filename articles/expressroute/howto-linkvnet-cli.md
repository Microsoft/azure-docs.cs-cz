---
title: 'Propojení virtuální sítě k okruhu ExpressRoute: rozhraní příkazového řádku: Azure | Microsoft Docs'
description: Tento dokument obsahuje přehled o tom, jak propojit virtuální sítě (virtuální sítě) pro okruhy ExpressRoute pomocí modelu nasazení Resource Manager a rozhraní příkazového řádku.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlit
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: anzaman,cherylmc
ms.openlocfilehash: 5e8d1739aa3d7f5be6c6450edcad43bc83db71fb
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875398"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Připojit virtuální sítě k okruhu ExpressRoute pomocí rozhraní příkazového řádku

Tento článek pomáhá propojení virtuálních sítí (virtuální sítě) pro okruhy Azure ExpressRoute pomocí rozhraní příkazového řádku. Odkaz, pomocí rozhraní příkazového řádku Azure, musí být virtuální sítě vytvořené pomocí modelu nasazení Resource Manager. Může být buď ve stejném předplatném nebo součástí jiné předplatné. Pokud chcete použít jinou metodu pro připojení virtuální sítě k okruhu ExpressRoute, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Je třeba nejnovější verzi rozhraní příkazového řádku (CLI). Další informace najdete v tématu [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Je potřeba posoudit [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

* Musí mít aktivní okruh ExpressRoute. 
  * Postupujte podle pokynů a [vytvoření okruhu ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. 
  * Ujistěte se, abyste měli soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Najdete v článku [konfigurace směrování](howto-routing-cli.md) směrování pokyny najdete v článku. 
  * Zkontrolujte, jestli je nakonfigurovaný soukromého partnerského vztahu Azure. Protokol BGP partnerský vztah mezi vaší sítí a Microsoftem musí být si tak, že povolíte připojení klient server.
  * Ujistěte se, že máte virtuální sítě a brány virtuální sítě vytvoří a plně zřízený. Postupujte podle pokynů a [nakonfigurovat bránu virtuální sítě pro ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli). Nezapomeňte použít `--gateway-type ExpressRoute`.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute. 

* Jeden virtuální sítě může být propojený až čtyři okruhy ExpressRoute. Můžete vytvořit nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být v rámci stejného předplatného, různých předplatných nebo kombinaci obou.

* Pokud povolíte doplněk ExpressRoute premium, můžete propojit virtuální sítě mimo geopolitické oblasti okruhu ExpressRoute nebo připojení je větší počet virtuální sítě k okruhu ExpressRoute. Další informace o doplňku premium najdete v tématu [– nejčastější dotazy](expressroute-faqs.md).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojit k okruhu virtuální sítě v rámci stejného předplatného

Brána virtuální sítě k okruhu ExpressRoute můžete připojit pomocí příkladu. Ujistěte se, že Brána virtuální sítě se vytvoří a je připravený pro propojování před spuštěním příkazu.

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu

Okruh ExpressRoute můžete sdílet mezi více předplatných. Následující obrázek znázorňuje jednoduchý schéma o tom, jak sdílení funguje pro okruhy ExpressRoute napříč více předplatných.

Každý z menší cloudy v rámci velké cloudu se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Každé oddělení v organizaci můžete použít vlastní předplatné nasazování jejich služeb – ale můžete sdílet jeden okruh ExpressRoute zpětné připojení k síti na pracovišti. Jednoho oddělení (v tomto příkladu: IT) můžete vlastní okruh ExpressRoute. Okruh ExpressRoute můžete použít jiné odběry v rámci organizace.

> [!NOTE]
> Připojení a šířku pásma poplatky za vyhrazené okruh uplatní na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejné šířky pásma.
> 
> 

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Správa - vlastníky okruhu a okruh uživatelů

Vlastníka okruhu je oprávněný uživatel Power prostředku okruhu ExpressRoute. Vlastníka okruhu můžete vytvořit autorizací, které lze uplatnit podle 'Okruh uživatele'. Okruh uživatelů se vlastníci brány virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžete uplatnit autorizací (jedním autorizačním na jednu virtuální síť).

Vlastníka okruhu má právo upravit a kdykoli odvolat oprávnění. Při povolení odvolán, všechna připojení odkaz se odstraní z předplatného, jejichž přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Chcete-li vytvořit povolení**

Vlastníka okruhu vytvoří autorizaci, které vytvoří autorizační klíč, který lze použít uživatelem okruh připojit své brány virtuální sítě k okruhu ExpressRoute. Povolení je platný pro jenom jedno připojení.

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

**Chcete-li zkontrolovat autorizací**

Vlastníka okruhu můžete zkontrolovat všechny autorizací, které jsou vydány na konkrétní okruhu spuštěním v následujícím příkladu:

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**Chcete-li přidat autorizací**

Vlastníka okruhu můžete přidat oprávnění pomocí v následujícím příkladu:

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**K odstranění autorizací**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací pro uživatele tak, že spustíte v následujícím příkladu:

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Operace okruh uživatele

ID partnera a autorizační klíč z vlastníka okruhu, musí uživatel okruh. Autorizační klíč je identifikátor GUID.

```azurecli
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Pokud chcete uplatnit autorizace připojení**

Uživatele okruhu můžete spustit podle následujícího příkladu lze uplatnit odkaz autorizace:

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Chcete-li uvolnit autorizace připojení**

Povolení můžete vydat odstraněním připojení, který odkazuje okruhu ExpressRoute do virtuální sítě.

## <a name="next-steps"></a>Další postup

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).