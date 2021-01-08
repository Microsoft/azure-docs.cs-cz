---
title: 'Azure ExpressRoute: Konfigurace ExpressRoute Global Reach: CLI'
description: Přečtěte si, jak propojit okruhy ExpressRoute dohromady a vytvořit privátní síť mezi místními sítěmi a povolit Global Reach pomocí Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 27f16ac7d7d799c5467b11fd93352dc5fdef666c
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028059"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurace Global Reach ExpressRoute pomocí Azure CLI

Tento článek vám pomůže nakonfigurovat Global Reach Azure ExpressRoute pomocí Azure CLI. Další informace najdete v článku [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Než začnete s konfigurací, proveďte následující požadavky:

* Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI. Viz témata [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).
* Seznamte se s [pracovními postupy](expressroute-workflows.md)zřizování okruhů ExpressRoute.
* Zajistěte, aby byly okruhy ExpressRoute ve stavu zřizování.
* Ujistěte se, že na okruhech ExpressRoute je nakonfigurovaná privátní partnerské vztahy Azure.  

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Konfiguraci spustíte tak, že se přihlásíte ke svému účtu Azure. Následující příkaz otevře výchozí prohlížeč a zobrazí výzvu k zadání přihlašovacích údajů pro váš účet Azure:  

```azurecli
az login
```

Pokud máte více předplatných Azure, podívejte se na předplatná pro tento účet:

```azurecli
az account list
```

Zadejte předplatné, které chcete použít:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifikujte okruhy ExpressRoute pro konfiguraci.

ExpressRoute Global Reach můžete povolit mezi dvěma ExpressRoute okruhy. Okruhy musí být v podporovaných zemích nebo oblastech a vytvořily se v různých umístěních partnerských vztahů. Pokud vaše předplatné vlastní oba okruhy, můžete pro spuštění konfigurace vybrat buď okruh. Pokud se ale dva okruhy nacházejí v různých předplatných Azure, musíte vytvořit autorizační klíč z jednoho z okruhů. Pomocí autorizačního klíče vygenerovaného z prvního okruhu můžete povolit Global Reach pro druhý okruh.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Umožnění připojení mezi místními sítěmi

Při spuštění příkazu pro povolení připojení si všimněte následujících požadavků pro hodnoty parametrů:

* *okruh partnerského vztahu* by měl být úplné ID prostředku. Například:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *předpona adresy* musí být podsíť IPv4 "/29" (například "10.0.0.0/29"). IP adresy v této podsíti používáme k navázání připojení mezi dvěma okruhy ExpressRoute. V této podsíti nemůžete používat adresy v Azure Virtual Networks ani v místních sítích.

Spusťte následující příkaz rozhraní příkazového řádku, abyste připojili dva okruhy ExpressRoute:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Výstup rozhraní příkazového řádku vypadá takto:

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

Po dokončení této operace budete mít propojení mezi místními sítěmi na obou stranách prostřednictvím obou okruhů ExpressRoute.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Povolení připojení mezi okruhy ExpressRoute v různých předplatných Azure

Pokud tyto dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci vygenerujete autorizaci v rámci předplatného okruhu 2. Potom předáte autorizační klíč do okruhu 1.

1. Vygenerujte autorizační klíč:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Výstup rozhraní příkazového řádku vypadá takto:

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

1. Spusťte následující příkaz na okruhu 1 a předejte mu ID prostředku okruhu 2 a autorizační klíč:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Po dokončení této operace budete mít propojení mezi místními sítěmi na obou stranách prostřednictvím obou okruhů ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Získat a ověřit konfiguraci

Pomocí následujícího příkazu Ověřte konfiguraci v okruhu, ve kterém byla provedena konfigurace (okruh 1 v předchozím příkladu):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Ve výstupu rozhraní příkazového řádku se zobrazí *CircuitConnectionStatus*. Dozvíte se, zda je připojení mezi dvěma okruhy vytvořeno ("připojeno") nebo není stanoveno ("Odpojeno"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Vypnutí připojení mezi místními sítěmi

Chcete-li zakázat připojení, spusťte následující příkaz proti okruhu, ve kterém byla provedena konfigurace (okruh 1 v předchozím příkladu).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Pomocí ```show``` příkazu ověřte stav.

Po dokončení této operace už nebudete mít propojení mezi místními sítěmi přes okruhy ExpressRoute.

## <a name="next-steps"></a>Další kroky

* [Další informace o ExpressRoute Global Reach](expressroute-global-reach.md)
* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Propojení okruhu ExpressRoute k virtuální síti](expressroute-howto-linkvnet-arm.md)
