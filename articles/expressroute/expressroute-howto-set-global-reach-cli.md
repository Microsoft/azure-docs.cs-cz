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
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384040"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Konfigurace ExpressRoute globální dosah pomocí rozhraní příkazového řádku Azure (Preview)
Tento článek vám pomůže nakonfigurovat ExpressRoute globální dosah pomocí Azure CLI. Další informace najdete v tématu [ExpressRouteRoute globální dosah](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Před zahájením
> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Před zahájením konfigurace, je potřeba zkontrolovat následující požadavky.

* Nainstalujte nejnovější verzi Azure CLI. Zobrazit [instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).
* Vysvětlení zřizování okruhů ExpressRoute [pracovních postupů](expressroute-workflows.md).
* Zajistěte, aby že vaše okruhy ExpressRoute jsou ve stavu zřízená.
* Ujistěte se, že soukromý partnerský vztah Azure je nakonfigurovaný na okruhů ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Přihlaste se do svého účtu Azure
Spustit konfiguraci, musíte se přihlásit ke svému účtu Azure. Příkaz Otevřít váš výchozí prohlížeč, který se vyzve k zadání přihlašovacích údajů přihlašovací jméno ke svému účtu Azure.  

```azurecli
az login
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```azurecli
az account list
```

Určete předplatné, které chcete použít.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifikujte okruhů ExpressRoute pro konfiguraci
Můžete povolit globální dosah ExpressRoute mezi jakékoli dva okruhy ExpressRoute nacházejí v podporované země a jejich vytvoření při různých umístění partnerského vztahu. Pokud vaše předplatné vlastní obou okruzích můžete buď okruh a spustit konfiguraci v následujících částech. Pokud dva okruhy jsou v různých předplatných Azure, bude nutné autorizace z jednoho předplatného Azure a předat autorizační klíč při spuštění příkazu konfigurace v rámci předplatného Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Povolit připojení mezi místními sítěmi

Při spuštění příkazu umožnění konektivity, vezměte v úvahu následující hodnoty:

* *okruh peer* by měl být ID úplné prostředku. Příklad: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* *-AddressPrefix* musí být IPv4 je/29 podsítě, například "10.0.0.0/29". IP adresy v této podsíti použije k navázání připojení mezi dva okruhy ExpressRoute. Ve virtuálních sítích Azure nebo v místních sítí, nesmí používat adresy v této podsíti.

Spusťte následující rozhraní příkazového řádku pro připojení dvou okruhů ExpressRoute. Použijte příkaz v následujícím příkladu:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Výstup příkazového řádku by měl vypadat jako v následujícím příkladu:

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

Po dokončení výše uvedené operace by měla mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci autorizace je vygenerována v předplatném okruh 2 a autorizační klíč je předána na okruh 1.

Generovat autorizačního klíče. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

Výstup rozhraní příkazového řádku by měl vypadat nějak takto.

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

Poznamenejte si okruh 2 Id prostředku stejně jako autorizační klíč.

Spusťte následující příkaz okruh 1. Předejte Id prostředku okruh 2 a autorizační klíč 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

Po dokončení výše uvedené operace by měla mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Získání a ověřit konfiguraci

Použijte následující příkaz k ověření konfigurace na okruh, kde konfigurace byla provedena, například 1 okruh v předchozím příkladu.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

V rozhraní příkazového řádku výstupu se zobrazí *CircuitConnectionStatus*. To zjistíte, zda připojení mezi dva okruhy pokládáme stav, "Připojeno", nebo Ne, "Odpojené". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Zakázat připojení mezi místními sítěmi

Pro jeho zakázání spuštěním příkazů proti okruh, kde konfigurace byla provedena, například 1 okruh v předchozím příkladu.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Můžete použít rozhraní příkazového řádku zobrazit ověření stavu. 

Po dokončení výše uvedené operace se už nebude mít připojení mezi vaší místní síti prostřednictvím okruhů ExpressRoute. 


## <a name="next-steps"></a>Další postup
* [Další informace o ExpressRoute globální dosah](expressroute-global-reach.md)
* [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)


