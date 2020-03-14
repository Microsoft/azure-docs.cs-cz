---
title: 'Azure ExpressRoute: konfigurace partnerského vztahu: rozhraní příkazového řádku'
description: Tento článek pomůže při vytváření a zřizování soukromého a veřejného a partnerského vztahu Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: dbda73e022ebaad283641ce2f54a5962aeb4cb60
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264762"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí rozhraní příkazového řádku

Tento článek pomáhá vytvářet a spravovat směrování konfigurace/vytvoření partnerského vztahu pro okruh ExpressRoute v modelu nasazení Resource Manageru pomocí rozhraní příkazového řádku. Můžete také zkontrolovat stav, update nebo delete a zrušit jejich zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete použít jinou metodu pro práci se váš okruh, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejné partnerské vztahy](about-public-peering.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI](/cli/azure/install-azure-cli).
* Než začnete s konfigurací, ujistěte [se,](expressroute-prerequisites.md)že jste zkontrolovali požadavky, [požadavky na směrování](expressroute-routing.md)a stránky [pracovního postupu](expressroute-workflows.md) .
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený pro vás bude moct spouštět příkazy v tomto článku.

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IPVPN, např. MPLS), se svého poskytovatele připojení, konfiguraci a správu směrování za vás.

Pro okruh ExpressRoute můžete nakonfigurovat privátní partnerské vztahy a partnerské vztahy Microsoftu (veřejný partnerský vztah Azure se pro nové okruhy už nepoužívá). Partnerské vztahy se dají konfigurovat v libovolném pořadí, ve kterém si zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o doménách směrování a partnerských vztazích najdete v tématu věnovaném [doménám směrování ExpressRoute](expressroute-circuit-peerings.md). Informace o veřejném partnerském vztahu najdete v tématu [veřejné partnerské vztahy ExpressRoute](about-public-peering.md).

## <a name="msft"></a>Partnerský vztah Microsoftu

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu. Další informace najdete v tématu [Konfigurace filtru tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nainstalujte nejnovější verzi Azure CLI. Použijte nejnovější verzi rozhraní příkazového řádku Azure (CLI). * před zahájením konfigurace zkontrolujte [předpoklady](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .

   ```azurecli-interactive
   az login
   ```

   Vyberte předplatné, pro kterou chcete vytvořit okruh ExpressRoute.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení partnerského vztahu Microsoftu pro můžete povolit. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace. 

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený a také povolený. Použijte následující příklad:

   ```azurecli-interactive
   az network express-route list
   ```

   Odpověď bude podobná jako v následujícím příkladu:

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
   * **Volitelné –** Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na partnerské vztahy jako číslo, můžete zadat číslo AS, na které jsou registrované.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.

   Spusťte následující příklad konfigurace partnerského vztahu Microsoftu pro váš okruh:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="getmsft"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud automatické ověření selže, zobrazí se ve výstupu výše uvedeného příkazu "AdvertisedPublicPrefixesState" jako "vyžadováno ověření". 
> 
> Pokud se zobrazí zpráva potřebná pro ověření, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace podle entity, která je uvedena jako vlastník předpon v registru směrování, a odešlete tyto dokumenty k ručnímu ověření pomocí Otevřete lístek podpory, jak je znázorněno níže. 
> 
>

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

Libovolnou část konfigurace můžete aktualizovat. Inzerované předpony okruh se aktualizují z 123.1.0.0/24 124.1.0.0/24 v následujícím příkladu:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>Přidání nastavení partnerského vztahu Microsoftu IPv6 do existující konfigurace IPv4

```azurecli-interactive
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Soukromý partnerský vztah Azure

Tato část umožňuje vytvořit, získat, aktualizovat a odstranit Azure konfiguraci soukromého partnerského vztahu pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nainstalujte nejnovější verzi Azure CLI. Je nutné použít nejnovější verzi rozhraní příkazového řádku (CLI) Azure. * před zahájením konfigurace zkontrolujte [předpoklady](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .

   ```azurecli-interactive
   az login
   ```

   Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat svého poskytovatele připojení povolit soukromý partnerský vztah Azure za vás. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud poskytovatel připojení nespravuje směrování, po vytvoření okruhu, ale dál používat další kroky konfigurace.

3. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízený a také povolený. Použijte následující příklad:

   ```azurecli-interactive
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Odpověď bude podobná jako v následujícím příkladu:

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
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.

   Můžete nakonfigurovat soukromý partnerský vztah Azure pro váš okruh v následujícím příkladu:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
   > 
   > 

### <a name="getprivate"></a>Zobrazení podrobností privátního partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurecli-interactive
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

### <a name="updateprivate"></a>Aktualizace konfigurace privátního partnerského vztahu Azure

Libovolnou část konfigurace podle následujícího příkladu můžete aktualizovat. V tomto příkladu je ID sítě VLAN okruhu aktualizované ze 100 na 500.

```azurecli-interactive
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>Odstranění privátního partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následující příklad:

> [!WARNING]
> Ujistěte se, že před spuštěním tohoto příkladu se odeberou všechny virtuální sítě a připojení ExpressRoute globální dosah. 
> 
> 

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```


## <a name="next-steps"></a>Další kroky

Dalším krokem je [Propojení virtuální sítě s okruhem ExpressRoute](howto-linkvnet-cli.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).
