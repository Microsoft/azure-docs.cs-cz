---
title: 'Azure ExpressRoute: Konfigurace globálního dosahu ExpressRoute: CLI'
description: Tento článek vám pomůže propojit okruhy ExpressRoute dohromady a vytvořit privátní síť mezi místními sítěmi a povolit globální dosah.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: a39cf4e09a70ca2b1225d699c84abf0e7f1d2eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476402"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurace globálního dosahu ExpressRoute pomocí rozhraní příkazového příkazu Azure

Tento článek vám pomůže nakonfigurovat Azure ExpressRoute Global Reach pomocí rozhraní příkazového příkazu Konto Azure. Další informace najdete v článku [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Před zahájením konfigurace proveďte následující požadavky:

* Nainstalujte nejnovější verzi příkazového příkazového příkazu k Azure. Viz témata [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).
* Seznamte se s [pracovními postupy](expressroute-workflows.md)zřizování okruhů ExpressRoute .
* Ujistěte se, že vaše okruhy ExpressRoute jsou ve stavu Zřízeno.
* Ujistěte se, že privátní partnerský vztah Azure je nakonfigurovaný na vašich okruhech ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Chcete-li spustit konfiguraci, přihlaste se ke svému účtu Azure. Následující příkaz otevře výchozí prohlížeč a zobrazí výzvu k zadání přihlašovacích přihlašovacích údajů k účtu Azure:  

```azurecli
az login
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro účet:

```azurecli
az account list
```

Zadejte předplatné, které chcete použít:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifikace okruhů ExpressRoute pro konfiguraci

Globální dosah ExpressRoute můžete povolit mezi libovolnými dvěma okruhy ExpressRoute, pokud jsou umístěny v podporovaných zemích nebo oblastech a byly vytvořeny v různých partnerských umístěních. Pokud vaše předplatné vlastní oba okruhy, můžete zvolit buď okruh pro spuštění konfigurace, jak je vysvětleno dále v tomto článku. Pokud jsou dva okruhy v různých předplatných Azure, musíte mít autorizaci z jednoho předplatného Azure a musí předat v jeho autorizační klíč při spuštění příkazu konfigurace v jiném předplatném Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Povolení připojení mezi místními sítěmi

Při spuštění příkazu pro povolení připojení si všimněte následujících požadavků na hodnoty parametrů:

* *peer-circuit* by měl být úplné ID prostředku. Například:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *adresa-předpona* musí být podsíť IPv4 "/29" (například "10.0.0.0/29"). Ip adresy v této podsíti používáme k navázání připojení mezi dvěma okruhy ExpressRoute. V této podsíti nesmíte používat adresy ve virtuálních sítích Azure ani v místních sítích.

Spusťte následující příkaz příkazu PŘÍKAZ K ONO pro připojení dvou okruhů ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Výstup cli vypadá takto:

```output
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

Po dokončení této operace budete mít připojení mezi místními sítěmi na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Povolení připojení mezi okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci vygenerujete autorizaci v předplatném okruhu 2 a předajíte autorizační klíč okruhu 1.

1. Generovat autorizační klíč:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Výstup cli vypadá takto:

   ```output
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

1. Poznamenejte si ID prostředku i autorizační klíč pro okruh 2.

1. Spusťte následující příkaz proti okruhu 1, předání id prostředku okruhu 2 a autorizačního klíče:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Po dokončení této operace budete mít připojení mezi místními sítěmi na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Získání a ověření konfigurace

Pomocí následujícího příkazu ověřte konfiguraci na okruhu, ve kterém byla konfigurace provedena (okruh 1 v předchozím příkladu):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Ve výstupu cli uvidíte *CircuitConnectionStatus*. Informuje o tom, zda je navázáno připojení mezi těmito dvěma obvody ("Připojeno") nebo zda není navázáno ("Odpojeno"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Zakázání připojení mezi místními sítěmi

Chcete-li zakázat připojení, spusťte následující příkaz proti okruhu, ve kterém byla konfigurace provedena (okruh 1 v předchozím příkladu).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Pomocí ```show``` příkazu ověřte stav.

Po dokončení této operace již nebudete mít připojení mezi místními sítěmi prostřednictvím okruhů ExpressRoute.

## <a name="next-steps"></a>Další kroky

* [Další informace o globálním dosahu ExpressRoute](expressroute-global-reach.md)
* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Propojení okruhu ExpressRoute s virtuální sítí](expressroute-howto-linkvnet-arm.md)
