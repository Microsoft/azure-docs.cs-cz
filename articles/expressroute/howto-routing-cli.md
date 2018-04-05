---
title: 'Postup konfigurace směrování pro okruh Azure ExpressRoute: rozhraní příkazového řádku | Microsoft Docs'
description: Tento článek vám pomůže vytvořit a zřizování soukromého a veřejného a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2017
ms.author: cherylmc
ms.openlocfilehash: f4ad959de1425e828ce11fb658f8b5304e9d8775
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-modify-routing-for-an-expressroute-circuit-using-cli"></a>Vytvoření a úprava směrování pro okruh ExpressRoute pomocí rozhraní příkazového řádku

Tento článek vám pomůže vytvořit a spravovat konfigurace směrování pro okruh ExpressRoute v modelu nasazení Resource Manager pomocí rozhraní příkazového řádku. Můžete také zkontrolovat stav, aktualizace nebo odstranění a zrušení zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete použít jinou metodu pro práci se váš okruh, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - soukromého partnerského vztahu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - veřejného partnerského vztahu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - partnerského vztahu Microsoftu](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli).
* Ujistěte se, že jste si přečetli [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovního postupu](expressroute-workflows.md) stránky před zahájením konfigurace.
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený pro vás mohli ke spuštění příkazů v tomto článku.

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IPVPN, např. MPLS), poskytovatel připojení nakonfigurujete a správu směrování za vás.

Můžete nakonfigurovat jednu, dvě nebo všechny tři partnerské vztahy (Azure privátní, veřejný Azure a Microsoft) pro okruh ExpressRoute. Partnerské vztahy můžete konfigurovat v libovolném pořadí. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o směrování doménách a partnerských vztahů najdete v tématu [domény směrování ExpressRoute](expressroute-circuit-peerings.md).

## <a name="msft"></a>Partnerský vztah Microsoftu

Tato část vám umožňuje vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Okruhy ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 partnerského vztahu Microsoftu bude mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Okruhy ExpressRoute, které jsou nakonfigurované na nebo po 1 srpen 2017 partnerského vztahu Microsoftu nebude mít všechny předpony inzerované dokud trasy filtr je připojen k okruhu. Další informace najdete v tématu [Konfigurovat filtr trasy pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure. Použít nejnovější verzi z rozhraní příkazového řádku Azure (CLI). * Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

  ```azurecli
  az login
  ```

  Vyberte předplatné, pro který chcete vytvořit okruh ExpressRoute.

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, požádejte svého poskytovatele připojení partnerský vztah Microsoftu pro můžete povolit. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Ale pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, pokračujte v konfiguraci pomocí následující kroky. 

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený a také povolený. Použijte následující příklad:

  ```azurecli
  az network express-route list
  ```

  Odpověď je stejný jako v následujícím příkladu:

  ```azurecli
  "allowClassicOperations": false,
  "authorizations": [],
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
  "gatewayManagerEtag": null,
  "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
  "location": "westus",
  "name": "MyCircuit",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
  "serviceProviderNotes": null,
  "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
  },
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits]
  ```

4. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Před pokračováním se ujistěte, že máte k dispozici následující informace.

  * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
  * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy, kterou vlastníte a která je registrovaná u RIR/IRR.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
  * Inzerované předpony: Musíte poskytnout seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud chcete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
  * **Volitelné -** zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na číslo AS partnerského vztahu, můžete zadat číslo AS, do kterého jsou registrované.
  * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
  * **Volitelné -** algoritmus hash MD5, pokud se rozhodnete použít.

   Spusťte podle následujícího příkladu lze nakonfigurovat partnerský vztah Microsoftu pro váš okruh:

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
  ```

### <a name="getmsft"></a>K zobrazení podrobností partnerského vztahu Microsoftu

Můžete získat podrobnosti o konfiguraci pomocí následující příklad:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```

Výstup se podobá následujícímu příkladu:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Libovolnou část konfigurace můžete aktualizovat. Inzerované předpony okruhu jsou aktualizováno z hodnoty 123.1.0.0/24 na 124.1.0.0/24 v následujícím příkladu:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>Chcete-li přidat nastavení partnerského vztahu IPv6 Microsoft do stávající konfigurace IPv4

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Soukromý partnerský vztah Azure

Tato část vám umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfigurace soukromého partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure. Je nutné použít nejnovější verzi z rozhraní příkazového řádku Azure (CLI). * Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

  ```azurecli
  az login
  ```

  Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, požádejte svého poskytovatele připojení povolte soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Ale pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, pokračujte v konfiguraci pomocí následující kroky.

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený a také povolený. Použijte následující příklad:

  ```azurecli
  az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
  ```

  Odpověď je stejný jako v následujícím příkladu:

  ```azurecli
  "allowClassicOperations": false,
  "authorizations": [],
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
  "gatewayManagerEtag": null,
  "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
  "location": "westus",
  "name": "MyCircuit",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
  "serviceProviderNotes": null,
  "serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
  },
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits]
  ```

4. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Před zahájením dalších kroků se ujistěte, že máte k dispozici následující položky:

  * Podsíť /30 pro primární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
  * Podsíť /30 pro sekundární propojení. Podsítě nesmí být součástí žádného adresního prostor vyhrazeného pro virtuální sítě.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Zkontrolujte, že nepoužíváte 65515.
  * **Volitelné -** algoritmus hash MD5, pokud se rozhodnete použít.

  Následující příklad použijte ke konfiguraci soukromého partnerského vztahu Azure pro váš okruh:

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
  ```

  Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
  > 
  > 

### <a name="getprivate"></a>K zobrazení podrobností soukromého partnerského vztahu Azure

Můžete získat podrobnosti o konfiguraci pomocí následující příklad:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Výstup se podobá následujícímu příkladu:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace pomocí následujícího příkladu můžete aktualizovat. V tomto příkladu je ID sítě VLAN okruhu aktualizováno z 100 na 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

> [!WARNING]
> Je nutné zajistit, že všechny virtuální sítě jsou před spuštěním tohoto příkladu odpojit od okruhu ExpressRoute. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="public"></a>Veřejný partnerský vztah Azure

Tato část vám umožňuje vytvořit, získat, aktualizovat a odstranit veřejného partnerského vztahu konfiguraci Azure pro okruh ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Vytvoření veřejného partnerského vztahu Azure

1. Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure. Je nutné použít nejnovější verzi z rozhraní příkazového řádku Azure (CLI). * Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

  ```azurecli
  az login
  ```

  Vyberte předplatné, pro který chcete vytvořit okruh ExpressRoute.

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```
2. Vytvořte okruh ExpressRoute.  Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, požádejte svého poskytovatele připojení povolte veřejný partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Ale pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, pokračujte v konfiguraci pomocí následující kroky.

3. Zkontrolujte okruh ExpressRoute a ověřte je zřízený a také povolený. Použijte následující příklad:

  ```azurecli
  az network express-route list
  ```

  Odpověď je stejný jako v následujícím příkladu:

  ```azurecli
  "allowClassicOperations": false,
  "authorizations": [],
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
  "gatewayManagerEtag": null,
  "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
  "location": "westus",
  "name": "MyCircuit",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
  "serviceProviderNotes": null,
  "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
  },
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits]
  ```

4. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Ujistěte se, že máte následující informace předtím, než budete pokračovat.

  * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
  * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
  * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
  * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
  * **Volitelné -** algoritmus hash MD5, pokud se rozhodnete použít.

  Spusťte následující příklad konfigurace veřejného partnerského vztahu Azure pro váš okruh:

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
  ```

  Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

  ```azurecli
  az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.

### <a name="getpublic"></a>K zobrazení podrobností veřejného partnerského vztahu Azure

Můžete získat podrobnosti o konfiguraci pomocí následující příklad:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Výstup se podobá následujícímu příkladu:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Libovolnou část konfigurace pomocí následujícího příkladu můžete aktualizovat. V tomto příkladu je ID sítě VLAN okruhu aktualizováno z hodnoty 200 na hodnotu 600.

```azurecli
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="next-steps"></a>Další postup

Dalším krokem je [Propojení virtuální sítě s okruhem ExpressRoute](howto-linkvnet-cli.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).