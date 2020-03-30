---
title: Vytvoření a správa veřejného partnerského vztahu Azure ExpressRoute
description: Informace o veřejném partnerské vztahu Azure a správa
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481129"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Vytvoření a správa veřejného partnerského vztahu ExpressRoute

> [!div class="op_single_selector"]
> * [Článek - Veřejný peering](about-public-peering.md)
> * [Video - Veřejný partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Článek - Partnerský vztah Microsoftu](expressroute-circuit-peerings.md#microsoftpeering)
>

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování veřejného partnerského vztahu pro okruh ExpressRoute. Můžete také zkontrolovat stav, aktualizovat nebo odstranit a zrušit poskytování partnerských vztahů. Tento článek se vztahuje na okruhy Správce prostředků, které byly vytvořeny před zastaralou veřejnou partnerovou partnerkou. Pokud máte dříve existující okruh (vytvořený před zastaralou veřejnou partnerovou mocí), můžete spravovat nebo konfigurovat veřejný partnerský vztah pomocí [Azure PowerShellu](#powershell), [Azure CLI](#cli)a [portálu Azure](#portal).

>[!NOTE]
>Veřejný partnerský vztah je zastaralé. Veřejný partnerský vztah nelze vytvořit na nových obvodech ExpressRoute. Pokud máte nový okruh ExpressRoute, použijte místo toho [partnerský vztah Microsoftu](expressroute-circuit-peerings.md#microsoftpeering) pro vaše služby Azure.
>

## <a name="connectivity"></a>Připojení

Připojení se vždy inicializuje ze služby WAN do služby Microsoft Azure. Služby Microsoft Azure nebudou moci inicializovat připojení do vaší sítě prostřednictvím této směrovací domény. Pokud je okruh ExpressRoute povolený pro veřejný partnerský vztah Azure, můžete získat přístup k [rozsahům veřejných IP adres používaných v Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) přes okruh.

Po povolení veřejného partnerského vztahu se můžete připojit k většině služeb Azure. Neumožňujeme vám selektivně vybírat služby, na které inzerujeme trasy.

* Služby, jako je Azure Storage, SQL Databases a Weby, se nabízejí na veřejných IP adresách.
* Prostřednictvím veřejné domény směrování partnerského vztahu se můžete soukromě připojit ke službám hostovaným na veřejných IP adresách, včetně virtuálních ip adres cloudových služeb.
* Veřejnou doménu partnerského vztahu můžete připojit k vaší DMZ a připojit se ke všem službám Azure na jejich veřejných IP adresách z vaší sítě WAN, aniž byste se museli připojovat přes internet.

## <a name="services"></a><a name="services"></a>Služby

Tato část zobrazuje služby dostupné prostředpou veřejného partnerského vztahu. Vzhledem k tomu, že veřejný partnerský vztah je zastaralé, neexistuje žádný plán pro přidání nových nebo dalších služeb do veřejného partnerského vztahu. Pokud používáte veřejný partnerský vztah a služba, kterou chcete používat, je podporována pouze přes partnerský vztah Microsoftu, musíte přepnout do partnerského vztahu Microsoftu. Seznam podporovaných služeb najdete v [tématu Partnerský vztah Microsoftu.](expressroute-faqs.md#microsoft-peering)

**Podporovány:**

* Power BI
* Většina služeb Azure je podporovaná. Obraťte se přímo na službu, kterou chcete použít k ověření podpory.

**Není podporováno:**
  * CDN
  * Azure Front Door
  * Vícefaktorový ověřovací server (starší verze)
  * Traffic Manager

Chcete-li ověřit dostupnost pro konkrétní službu, můžete zkontrolovat dokumentaci pro tuto službu a zjistit, zda je pro tuto službu publikován vyhrazený rozsah. Pak můžete vyhledat rozsahy IP adres cílové služby a porovnat s rozsahy uvedenými v [azure IP rozsahy a značky služeb – veřejný cloud XML soubor](https://www.microsoft.com/download/details.aspx?id=56519). Případně můžete otevřít lístek podpory pro dotyčnou službu pro objasnění.

## <a name="peering-comparison"></a><a name="compare"></a>Porovnání partnerského vztahu

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Veřejný partnerský vztah Azure má 1 IP adresu NAT přidruženou ke každé relaci Protokolu BGP. Pro více než 2 IP adresy NAT přejděte do partnerského vztahu Microsoftu. Partnerský vztah společnosti Microsoft umožňuje konfigurovat vlastní přidělení navádění na virtuální sítě a také použít filtry tras pro selektivní reklamy předponou. Další informace naleznete v tématu [Přesun do partnerského vztahu Microsoftu](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).
>

## <a name="custom-route-filters"></a>Vlastní filtry tras

Můžete definovat vlastní filtry tras v rámci sítě, aby spotřebovávaly pouze trasy, které potřebujete. Podrobné informace o konfiguraci směrování naleznete na stránce [Směrování.](expressroute-routing.md)

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Kroky Azure PowerShellu


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Vzhledem k tomu, že veřejný partnerský vztah je zastaralé, nelze konfigurovat veřejný partnerský vztah na nový okruh ExpressRoute.

1. Ověřte, zda máte okruh ExpressRoute, který je zřízen a také povolen. Použijte následující příklad:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Odpověď bude podobná jako v následujícím příkladu:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Než budete pokračovat dále, ujistěte se, že máte následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Volitelné:
   * Hodnota hash MD5, pokud se ji rozhodnete použít.

   Spuštěním následujícího příkladu nakonfigurujte veřejný partnerský vztah Azure pro váš okruh

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Pokud se rozhodnete použít hash MD5, použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Získání podrobností o veřejném partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následující rutiny:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat v následujícím příkladu. V tomto příkladu je ID sítě VLAN okruhu aktualizováno z 200 na 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Kroky azure cli


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zřízena a také povolena. Použijte následující příklad:

   ```azurecli-interactive
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

2. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Než budete pokračovat dále, ujistěte se, že máte následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * **Nepovinné -** Hash MD5, pokud se rozhodnete použít jeden.

   Spusťte následující příklad konfigurace veřejného partnerského vztahu Azure pro váš okruh:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Pokud se rozhodnete použít hash MD5, použijte následující příklad:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat v následujícím příkladu:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Výstup se podobá následujícímu příkladu:

```output
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

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat v následujícím příkladu. V tomto příkladu je ID sítě VLAN okruhu aktualizováno z 200 na 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Kroky portálu Azure

Chcete-li nakonfigurovat partnerský vztah, použijte kroky PowerShellu nebo CLI obsažené v tomto článku. Chcete-li spravovat partnerský vztah, můžete použít následující části. Pro odkaz, tyto kroky vypadají podobně jako správa [partnerského vztahu Microsoftu na portálu](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Zobrazte vlastnosti veřejného partnerského vztahu Azure výběrem partnerského vztahu na portálu.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Vyberte řádek pro partnerský vztah a upravte vlastnosti partnerského vztahu.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Odstranění veřejného partnerského vztahu Azure

Odeberte konfiguraci partnerského vztahu výběrem ikony odstranění.

## <a name="next-steps"></a>Další kroky

Dalším krokem [je propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).