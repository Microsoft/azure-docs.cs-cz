---
title: Vytvoření a správa veřejného partnerského vztahu Azure ExpressRoute
description: Další informace o správě veřejných partnerských vztahů Azure
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.openlocfilehash: 477145619e1b4d8b41c422389b57a46615597478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202544"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Vytvoření a správa veřejného partnerského vztahu ExpressRoute

> [!div class="op_single_selector"]
> * [Článek – veřejný partnerský vztah](about-public-peering.md)
> * [Video – veřejný partnerský vztah](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Článek – partnerský vztah Microsoftu](expressroute-circuit-peerings.md#microsoftpeering)
>

Tento článek vám pomůže vytvořit a spravovat konfiguraci směrování veřejných partnerských vztahů pro okruh ExpressRoute. Můžete také kontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení partnerských vztahů. Tento článek se týká Správce prostředkůch okruhů, které byly vytvořené před veřejným partnerským vztahem, se zastaraly. Pokud máte dříve existující okruh (vytvořený před veřejným partnerským vztahem), můžete spravovat/konfigurovat veřejné partnerské vztahy pomocí [Azure PowerShell](#powershell), [Azure CLI](#cli)a [Azure Portal](#portal).

>[!NOTE]
>Veřejný partnerský vztah je zastaralý. Pro nové okruhy ExpressRoute nemůžete vytvořit veřejný partnerský vztah. Pokud máte nový okruh ExpressRoute, místo toho použijte pro své služby Azure [partnerský vztah Microsoftu](expressroute-circuit-peerings.md#microsoftpeering) .
>

## <a name="connectivity"></a>Připojení

Připojení se vždycky zahajuje z vaší sítě WAN až po Microsoft Azure služby. Služby Microsoft Azure Services nebudou moct iniciovat připojení k síti přes tuto doménu směrování. Pokud je pro veřejný partnerský vztah Azure povolen okruh ExpressRoute, můžete k [rozsahům veřejných IP adres, které se používají v Azure](../virtual-network/public-ip-addresses.md#public-ip-addresses) , přistupovat přes okruh.

Po povolení veřejného partnerského vztahu se můžete připojit k většině služeb Azure. Neumožňujeme selektivně vybrat služby, pro které budeme inzerovat trasy.

* Služby, jako jsou Azure Storage, databáze SQL a weby, jsou nabízeny na veřejných IP adresách.
* Prostřednictvím domény směrování veřejného partnerského vztahu se můžete soukromě připojit ke službám hostovaným na veřejných IP adresách, včetně VIP cloudových služeb.
* Doménu veřejného partnerského vztahu můžete připojit k vašemu DMZ a připojit se ke všem službám Azure na svých veřejných IP adresách z vaší sítě WAN bez nutnosti připojení přes Internet.

## <a name="services"></a><a name="services"></a>Služby

V této části jsou uvedené služby, které jsou dostupné přes veřejné partnerské vztahy. Vzhledem k tomu, že veřejný partnerský vztah je zastaralý, není k dispozici žádný plán pro přidání nových nebo dalších služeb do veřejného partnerského vztahu. Pokud používáte veřejný partnerský vztah a služba, kterou chcete použít, je podporovaná jenom přes partnerský vztah Microsoftu, musíte přepnout na partnerský vztah Microsoftu. Seznam podporovaných služeb najdete v tématu [partnerský vztah Microsoftu](expressroute-faqs.md#microsoft-peering) .

**Doložen**

* Power BI
* Většina služeb Azure je podporovaná. Zkontrolujte přímo se službou, kterou chcete použít k ověření podpory.

**Nepodporováno:**
  * CDN
  * Azure Front Door
  * Multi-Factor Authentication Server (starší verze)
  * Traffic Manager

Pokud chcete ověřit dostupnost konkrétní služby, můžete si prohlédnout dokumentaci k příslušné službě a zjistit, jestli je pro danou službu publikovaný rezervovaný rozsah. Pak můžete vyhledat rozsahy IP adres cílové služby a porovnat je s rozsahy uvedenými v části [rozsahy IP adres Azure a značky služby – Public Cloud. XML soubor](https://www.microsoft.com/download/details.aspx?id=56519). Případně můžete pro vyjasnění otevřít lístek podpory pro příslušnou službu.

## <a name="peering-comparison"></a><a name="compare"></a>Porovnání partnerských vztahů

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Veřejný partnerský vztah Azure má 1 IP adresu NAT přidruženou ke každé relaci protokolu BGP. V případě více než 2 IP adres NAT přejděte na partnerský vztah Microsoftu. Partnerský vztah Microsoftu umožňuje nakonfigurovat vlastní přidělení NAT a také použít filtry tras pro inzerování selektivních předpon. Další informace najdete v tématu [Přesun na partnerský vztah Microsoftu](./how-to-move-peering.md).
>

## <a name="custom-route-filters"></a>Vlastní filtry tras

V rámci sítě můžete definovat vlastní filtry tras, které budou využívat pouze trasy, které potřebujete. Podrobné informace o konfiguraci směrování najdete na stránce [Směrování](expressroute-routing.md) .

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell kroky


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Vzhledem k tomu, že veřejný partnerský vztah je zastaralý, nemůžete konfigurovat veřejný partnerský vztah na novém okruhu ExpressRoute.

1. Ověřte, že máte okruh ExpressRoute, který je zřízený a také povolený. Použijte následující příklad:

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
2. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Než budete pokračovat, ujistěte se, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * Volitelné:
   * Hodnota hash MD5, pokud se ji rozhodnete použít.

   Spuštěním následujícího příkladu nakonfigurujte veřejný partnerský vztah Azure pro váš okruh.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Získání podrobností veřejného partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následující rutiny:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Libovolnou část konfigurace můžete aktualizovat pomocí následujícího příkladu. V tomto příkladu je ID sítě VLAN okruhu Aktualizováno z 200 na 600.

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

## <a name="azure-cli-steps"></a><a name="cli"></a>Kroky pro rozhraní příkazového řádku Azure


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Zkontrolujte okruh ExpressRoute a ujistěte se, že je zajištěný a také povolený. Použijte následující příklad:

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

2. Nakonfigurujte veřejný partnerský vztah Azure pro okruh. Než budete pokračovat, ujistěte se, že máte k dispozici následující informace.

   * Podsíť /30 pro primární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Podsíť /30 pro sekundární propojení. Musí se jednat o platnou předponu veřejné IPv4 adresy.
   * Platné ID sítě VLAN, na kterém se má partnerský vztah vytvořit. Zajistěte, aby žádný jiný partnerský vztah v okruhu nepoužíval stejné ID sítě VLAN.
   * Číslo AS pro partnerský vztah. Můžete použít 2bajtová i 4bajtová čísla AS.
   * **Volitelné –** Hodnota hash MD5, pokud se rozhodnete ji použít.

   Spuštěním následujícího příkladu nakonfigurujte veřejný partnerský vztah Azure pro váš okruh:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Pokud se rozhodnete použít hodnotu hash MD5, použijte následující příklad:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Ujistěte se, že své číslo AS zadáváte jako partnerské číslo ASN, ne zákaznické číslo ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Podrobnosti o konfiguraci můžete získat pomocí následujícího příkladu:

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

Libovolnou část konfigurace můžete aktualizovat pomocí následujícího příkladu. V tomto příkladu je ID sítě VLAN okruhu Aktualizováno z 200 na 600.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Odstranění veřejného partnerského vztahu Azure

Konfiguraci partnerského vztahu můžete odebrat spuštěním následujícího příkladu:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure Portal kroky

Pro konfiguraci partnerského vztahu použijte kroky pro PowerShell nebo CLI obsažené v tomto článku. Pro správu partnerského vztahu můžete použít následující části. U těchto kroků se tyto kroky podobají tomu, jak spravovat [partnerský vztah Microsoftu na portálu](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Zobrazení podrobností veřejného partnerského vztahu Azure

Výběrem partnerského vztahu na portálu zobrazte vlastnosti veřejného partnerského vztahu Azure.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Aktualizace konfigurace veřejného partnerského vztahu Azure

Vyberte řádek pro partnerský vztah a pak upravte vlastnosti partnerského vztahu.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Odstranění veřejného partnerského vztahu Azure

Odeberte konfiguraci partnerského vztahu tak, že vyberete ikonu Odstranit.

## <a name="next-steps"></a>Další kroky

Dalším krokem je [propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Další informace o pracovních postupech ExpressRoute najdete v tématu [Pracovní postupy ExpressRoute](expressroute-workflows.md).
* Další informace o partnerském vztahu okruhu najdete v tématu [Okruhy ExpressRoute a domény směrování](expressroute-circuit-peerings.md).
* Další informace o práci s virtuálními sítěmi najdete v článku [Přehled virtuálních sítí](../virtual-network/virtual-networks-overview.md).