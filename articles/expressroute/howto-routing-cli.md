---
title: 'Kurz: konfigurace partnerského vztahu pro okruh ExpressRoute – Azure CLI'
description: V tomto kurzu se dozvíte, jak vytvořit a zřídit soukromé, veřejné a partnerské vztahy Microsoftu okruhu ExpressRoute. Tento článek také ukazuje, jak kontrolovat stav partnerských vztahů pro váš okruh, aktualizovat je nebo je odstranit.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: duau
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 7a482e268137946222f1c8b427424598bd78f935
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735100"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Kurz: vytvoření a úprava partnerského vztahu pro okruh ExpressRoute pomocí rozhraní příkazového řádku

V tomto kurzu se dozvíte, jak vytvořit a spravovat konfiguraci směrování/partnerský vztah pro okruh ExpressRoute v modelu nasazení Správce prostředků pomocí rozhraní příkazového řádku (CLI). Můžete také kontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení partnerských vztahů pro okruh ExpressRoute. Pokud chcete pro práci s okruhem použít jinou metodu, vyberte článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Veřejné partnerské vztahy](about-public-peering.md)
> * [Video – privátní partnerské vztahy](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – partnerský vztah Microsoftu](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-routing-classic.md)
> 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Konfigurace, aktualizace a odstranění partnerského vztahu Microsoftu pro okruh
> - Konfigurace, aktualizace a odstranění privátního partnerského vztahu Azure pro okruh

## <a name="prerequisites"></a>Předpoklady

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI](/cli/azure/install-azure-cli).
* Než začnete s konfigurací, ujistěte [se,](expressroute-prerequisites.md)že jste zkontrolovali požadavky, [požadavky na směrování](expressroute-routing.md)a stránky [pracovního postupu](expressroute-workflows.md) .
* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, postupujte podle pokynů pro [vytvoření okruhu ExpressRoute](howto-circuit-cli.md) a před pokračováním dodržujte okruh povolený vaším poskytovatelem připojení. Aby bylo možné spustit příkazy v tomto článku, musí být okruh ExpressRoute ve zřízeném a povoleném stavu.

Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb nabízejícími služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IPVPN, jako je MPLS), poskytovatel připojení bude konfigurovat a spravovat směrování za vás.

Pro okruh ExpressRoute můžete nakonfigurovat privátní partnerské vztahy a partnerské vztahy Microsoftu. Partnerské vztahy se dají konfigurovat v libovolném pořadí, ve kterém si zvolíte. Musíte se ale přesvědčit, že jste vždy konfiguraci každého partnerského vztahu dokončili. Další informace o doménách směrování a partnerských vztazích najdete v tématu věnovaném [doménám směrování ExpressRoute](expressroute-circuit-peerings.md).

## <a name="microsoft-peering"></a><a name="msft"></a>Partnerský vztah s Microsoftem

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci partnerského vztahu Microsoftu pro okruh ExpressRoute.

> [!IMPORTANT]
> Partnerské vztahy Microsoftu okruhů ExpressRoute, které byly nakonfigurované před 1. srpna 2017, budou mít všechny předpony služby inzerované prostřednictvím partnerského vztahu Microsoftu, a to i v případě, že nejsou definované filtry směrování. Partnerský vztah Microsoftu pro okruhy ExpressRoute, které jsou nakonfigurované na nebo od 1. srpna 2017, nebudou mít inzerované předpony, dokud není k okruhu připojen filtr tras. Další informace najdete v tématu [Konfigurace filtru tras pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).
> 


### <a name="to-create-microsoft-peering"></a>Vytvoření partnerského vztahu Microsoftu

1. Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI. Použijte nejnovější verzi rozhraní příkazového řádku Azure (CLI).

   ```azurecli
   az login
   ```

   Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

1. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil partnerský vztah Microsoftu. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků. 

1. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zajištěný a také povolený. Použijte následující příklad:

   ```azurecli
   az network express-route list
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```output
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

4. Nakonfigurujte partnerský vztah Microsoftu pro okruh. Než budete pokračovat, ujistěte se, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Blok adres musí být platnou předponou veřejné IPv4 adresy, kterou vlastníte a zaregistrovali v RIR/IRR.
   * Podsíť /30 pro sekundární propojení. Blok adres musí být platnou předponou veřejné IPv4 adresy, kterou vlastníte a zaregistrovali v RIR/IRR.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Inzerované předpony: Zadejte seznam všech předpon, které plánujete inzerovat přes relaci protokolu BGP. Přijímají se jenom předpony veřejných IP adres. Pokud plánujete odeslat sadu předpon, můžete odeslat seznam oddělený čárkami. Tyto předpony musí být v RIR/IRR zaregistrované na vás.
   * **Volitelné –** Zákaznické číslo ASN: Pokud inzerujete předpony, které nejsou registrované na partnerské vztahy jako číslo, můžete zadat číslo AS, na které jsou zaregistrované.
   * Název registru směrování: Můžete zadat RIR/IRR, kde jsou předpony a číslo AS registrované.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.

   Spusťte následující příklad ke konfiguraci partnerského vztahu Microsoftu pro váš okruh:

   ```azurecli
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Zobrazení podrobností partnerského vztahu Microsoftu

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```
> [!IMPORTANT]
> Microsoft ověří, jestli se vám v registru směrování Internetu přiřadí zadané "inzerované veřejné předpony" a "partnerské číslo ASN" (nebo číslo ASN zákazníka). Pokud získáváte veřejné předpony z jiné entity a pokud přiřazení není zaznamenané u registru směrování, automatické ověřování se nedokončí a bude vyžadovat ruční ověření. Pokud automatické ověření selže, zobrazí se ve výstupu výše uvedeného příkazu "AdvertisedPublicPrefixesState" jako "vyžadováno ověření". 
> 
> Pokud se zobrazí zpráva požadovaná kontrola, shromážděte dokumenty, které zobrazují veřejné předpony, do vaší organizace, která je uvedená jako vlastník předpony v registru směrování, a odešlete tyto dokumenty pro ruční ověření otevřením lístku podpory. 
> 
>

Výstup se podobá následujícímu příkladu:

```output
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

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aktualizace konfigurace partnerského vztahu Microsoftu

Libovolnou část konfigurace můžete aktualizovat. Inzerované předpony okruhu se aktualizují z 123.1.0.0/24 na 124.1.0.0/24 v následujícím příkladu:

```azurecli
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="to-add-ipv6-microsoft-peering-settings-to-an-existing-ipv4-configuration"></a><a name="addIPv6msft"></a>Přidání nastavení partnerského vztahu Microsoftu IPv6 do existující konfigurace IPv4

```azurecli
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

## <a name="azure-private-peering"></a><a name="private"></a>Privátní partnerský vztah s Azure

Tato část vám pomůže vytvořit, získat, aktualizovat a odstranit konfiguraci privátního partnerského vztahu Azure pro okruh ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Vytvoření soukromého partnerského vztahu Azure

1. Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI.
1. 
   ```azurecli
   az login
   ```

   Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```
1. Vytvořte okruh ExpressRoute. Podle pokynů vytvořte [okruh ExpressRoute](howto-circuit-cli.md) a mějte ho zřízený poskytovatelem připojení. Pokud poskytovatel připojení nabízí spravované služby vrstvy 3, můžete požádat poskytovatele připojení, aby pro vás povolil privátní partnerské vztahy Azure. V takovém případě nebudete muset postupovat podle pokynů uvedených v dalších částech. Pokud ale poskytovatel připojení nespravuje směrování za vás, po vytvoření okruhu pokračujte v konfiguraci pomocí dalších kroků.

1. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zajištěný a také povolený. Použijte následující příklad:

   ```azurecli
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```output
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

1. Nakonfigurujte soukromý partnerský vztah Azure pro okruh. Než budete pokračovat v dalších krocích, ujistěte se, že máte následující položky:

   * Podsíť /30 pro primární propojení. Podsíť nesmí být součástí žádného adresního prostoru rezervovaného pro virtuální sítě.
   * Podsíť /30 pro sekundární propojení. Podsíť nesmí být součástí žádného adresního prostoru rezervovaného pro virtuální sítě.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS. Pro tento partnerský vztah můžete použít soukromé číslo AS. Ujistěte se, že nepoužíváte 65515.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.

   Pro konfiguraci privátního partnerského vztahu Azure pro váš okruh použijte následující příklad:

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

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Zobrazení podrobností soukromého partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Výstup se podobá následujícímu příkladu:

```output
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

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aktualizace konfigurace soukromého partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat pomocí následujícího příkladu. V tomto příkladu je ID sítě VLAN okruhu Aktualizováno z 100 na 500.

```azurecli
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Odstranění partnerského vztahu Microsoftu

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Odstranění soukromého partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

> [!WARNING]
> Před spuštěním tohoto příkladu je nutné zajistit, aby byly odebrány všechny virtuální sítě a připojení ExpressRoute Global Reach. 
> 
> 

```azurecli
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="next-steps"></a>Další kroky

Po dokončení konfigurace privátního partnerského vztahu Azure můžete propojit virtuální sítě s okruhem, viz: 

> [!div class="nextstepaction"]
> [Propojení virtuální sítě s okruhem ExpressRoute](howto-linkvnet-cli.md)
