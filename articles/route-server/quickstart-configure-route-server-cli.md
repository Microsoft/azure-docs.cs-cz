---
title: 'Rychlý Start: vytvoření a konfigurace serveru Směrování pomocí Azure CLI'
description: V tomto rychlém startu se dozvíte, jak vytvořit a nakonfigurovat server směrování pomocí Azure CLI.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: ef41c52fa1b63094d952dc34f81db36f7aeaac95
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521284"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Rychlý Start: vytvoření a konfigurace serveru Směrování pomocí Azure CLI 

Tento článek vám pomůže s použitím rozhraní příkazového řádku Azure ke konfiguraci serveru Směrování Azure pro partnery se síťovými virtuálními zařízeními (síťové virtuální zařízení) ve vaší virtuální síti. Azure Route Server se dozví o trasách z síťové virtuální zařízení a programuje je pro virtuální počítače ve virtuální síti. Bude také inzerovat trasy virtuální sítě do síťové virtuální zařízení. Další informace najdete v tématu [Azure Route Server](overview.md).

> [!IMPORTANT]
> Služba Azure Route Server (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Požadavky 

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Ujistěte se, že máte nejnovější rozhraní příkazového řádku Azure CLI nebo na portálu můžete použít Azure Cloud Shell. 
* Zkontrolujte [omezení služby pro Azure Route Server](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Vytvoření směrovacího serveru 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné. 

Pokud chcete zahájit konfiguraci, přihlaste se ke svému účtu Azure. Pokud použijete Cloud Shell "vyzkoušet ho", jste přihlášeni automaticky. Následující příklady vám pomůžou s připojením:

```azurecli-interactive
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli-interactive
az account list
```

Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě 

Než budete moct vytvořit směrovací server Azure, budete k hostování nasazení potřebovat virtuální síť. Pomocí příkazu níže vytvořte skupinu prostředků a virtuální síť. Pokud již máte virtuální síť, můžete přejít k další části.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Přidání podsítě 

1. Přidejte podsíť s názvem *RouteServerSubnet* , do které se nasadí Server směrování Azure. Tato podsíť je vyhrazená podsíť jenom pro server Azure Route. RouteServerSubnet musí být/27 nebo kratší předpona (například/26,/25) nebo se vám při přidávání serveru tras Azure zobrazí chybová zpráva.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Získejte ID RouteServerSubnet. Chcete-li zobrazit ID prostředku všech podsítí ve virtuální síti, použijte tento příkaz: 

    ```azurecli-interactive 
    subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

ID RouteServerSubnet vypadá takto: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Vytvoření směrovacího serveru 

Vytvoření směrovacího serveru pomocí tohoto příkazu: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

Umístění musí odpovídat umístění vaší virtuální sítě. HostedSubnet je ID RouteServerSubnet, které jste získali v předchozí části. 

## <a name="create-peering-with-an-nva"></a>Vytvoření partnerského vztahu s síťové virtuální zařízení 

K navázání partnerského vztahu ze serveru tras na síťové virtuální zařízení použijte následující příkaz: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip" je IP adresa virtuální sítě přiřazená k síťové virtuální zařízení. "nva_asn" je číslo autonomního systému (ASN), které je konfigurováno v síťové virtuální zařízení. ASN může být jakékoli 16bitové číslo jiné než v rozsahu 65515-65520. Tato škála čísla ASN je vyhrazena společností Microsoft. 

K nastavení partnerského vztahu s různými síťové virtuální zařízení nebo jinou instancí stejného síťové virtuální zařízení pro redundanci použijte tento příkaz:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Dokončete konfiguraci na síťové virtuální zařízení 

K dokončení konfigurace síťové virtuální zařízení a povolení relací protokolu BGP budete potřebovat IP adresu a ASN serveru tras Azure. Tyto informace můžete získat pomocí tohoto příkazu: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

Výstup obsahuje následující informace. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Konfigurace výměny trasy 

Pokud máte bránu ExpressRoute a bránu Azure VPN ve stejné virtuální síti a chcete, aby vyměňovat trasy, můžete na serveru tras Azure povolit směrování na trase.

> [!IMPORTANT]
> U bezserverová nasazení nezapomeňte před vytvořením serveru Azure Route vytvořit bránu Azure VPN. v opačném případě se nasazení služby Azure VPN Gateway nezdaří.
> 

1. Pokud chcete povolit výměnu tras mezi serverem směrování Azure a branami, použijte tento příkaz:

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Pokud chcete zakázat výměnu tras mezi serverem směrování Azure a branami, použijte tento příkaz:

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Řešení potíží 

Pomocí tohoto příkazu můžete zobrazit trasy inzerované a přijímané serverem Azure Route:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up"></a>Vyčištění 

Pokud už nepotřebujete Server tras Azure, pomocí těchto příkazů odeberte vytvoření partnerského vztahu protokolu BGP a pak odeberte server směrování. 

1. Pomocí tohoto příkazu odeberte vytvoření partnerského vztahu protokolu BGP mezi serverem Azure Route Server a síťové virtuální zařízení:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Odeberte server služby Azure Route pomocí tohoto příkazu: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Další kroky

Po vytvoření serveru Směrování Azure si přečtěte informace o tom, jak server tras Azure komunikuje s ExpressRoute a branami VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute a podpora Azure VPN](expressroute-vpn-support.md)
 
