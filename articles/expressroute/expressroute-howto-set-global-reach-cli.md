---
title: 'Konfigurace ExpressRoute globálním dosahem: Rozhraní příkazového řádku Azure | Dokumentace Microsoftu'
description: Tento článek vám pomůže propojit okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi a povolit globální dosah.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: be10489d731b9e01d148ce1ac7892cb6de956662
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659316"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli-preview"></a>Konfigurace ExpressRoute globální dosah s použitím rozhraní příkazového řádku Azure (Preview)

Tento článek vám pomůže nakonfigurovat globální dosah Azure ExpressRoute pomocí Azure CLI. Další informace najdete v článku [ExpressRoute Global Reach](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Před zahájením

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Před zahájením konfigurace, splňte následující požadavky:

* Nainstalujte nejnovější verzi Azure CLI. Viz témata [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).
* Vysvětlení ExpressRoute-zřizování [pracovních postupů](expressroute-workflows.md).
* Zajistěte, aby že vaše okruhy ExpressRoute jsou ve stavu zřízená.
* Ujistěte se, že soukromý partnerský vztah Azure je nakonfigurovaný na okruhů ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Spustit konfiguraci, přihlaste se ke svému účtu Azure. Následující příkaz spustí výchozí prohlížeč a zobrazí výzvu pro přihlašovací údaje ke svému účtu Azure:  

```azurecli
az login
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro tento účet:

```azurecli
az account list
```

Určete předplatné, které chcete použít:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifikujte okruhů ExpressRoute pro konfiguraci

Můžete povolit globální dosah ExpressRoute mezi jakékoli dva okruhy ExpressRoute, za předpokladu, nacházejí se v podporované země a byly vytvořeny v různých umístění partnerského vztahu. Pokud vaše předplatné vlastní obou okruzích, můžete buď okruh a spustit konfiguraci, jak je popsáno dále v tomto článku. Pokud jsou dva okruhy v různých předplatných Azure, musí mít povolení k jednomu předplatnému Azure a musí projít v jeho autorizační klíč při spuštění příkazu konfigurace v rámci předplatného Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Povolit připojení mezi místními sítěmi

Při spuštění příkazu umožnění konektivity, mějte na paměti následující požadavky na hodnoty parametrů:

* *okruh peer* by měl být ID úplné prostředku. Příklad:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *Předpona adresy* musí být "/ 29" podsíť IPv4 (například "10.0.0.0/29"). K navázání připojení mezi dva okruhy ExpressRoute používáme IP adres v této podsíti. Ve virtuálním sítím Azure nebo v místních sítí, nesmí používat adresy v této podsíti.

Spuštěním následujícího příkazu rozhraní příkazového řádku se připojte dva okruhy ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Výstup příkazového řádku by měl vypadat takto:

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Po dokončení této operace musíte připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Povolit připojení mezi okruhů ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, musíte autorizaci. V následující konfiguraci můžete generovat autorizace v předplatném okruh 2 a předat autorizační klíč k okruhu 1.

1. Generovat autorizačního klíče:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Výstup příkazového řádku by měl vypadat takto:

   ```azurecli
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Poznamenejte si ID prostředku a autorizační klíč pro okruh. 2.

1. Spusťte následující příkaz okruh 1, předejte ID a autorizační klíč prostředku okruh 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Po dokončení této operace musíte připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Získání a ověřit konfiguraci

Použijte následující příkaz k ověření konfigurace na okruh, kde konfigurace proběhla (okruh 1 v předchozím příkladu):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Ve výstupu rozhraní příkazového řádku, zobrazí se vám *CircuitConnectionStatus*. Ta vám zjistí, zda je připojení mezi dva okruhy navázat ("připojeno"), nebo nejde navázat spojení ("odpojeno"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Zakázat připojení mezi místními sítěmi

Chcete-li zakázat připojení, spusťte následující příkaz je okruh ve kterém byla konfigurace provedené (okruh 1 v předchozím příkladu).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Použití ```show``` příkazu ověřte stav.

Po dokončení této operace se již budete mít připojení mezi vaší místní sítě prostřednictvím okruhů ExpressRoute.

## <a name="next-steps"></a>Další postup

* [Další informace o ExpressRoute globální dosah](expressroute-global-reach.md)
* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Propojení okruhu ExpressRoute k virtuální síti](expressroute-howto-linkvnet-arm.md)
