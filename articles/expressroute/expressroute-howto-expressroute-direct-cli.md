---
title: Konfigurace ExpressRoute přímo – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Tento článek vám pomůže s konfigurací ExpressRoute přímo pomocí rozhraní příkazového řádku Azure (preview)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 285b429f565f8a2c7f8c20756f076e631223b10f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076708"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli-preview"></a>Konfigurace ExpressRoute přímo pomocí rozhraní příkazového řádku Azure (preview)

Azure ExpressRoute Direct můžete použít pro připojení přímo k globální síti Microsoftu na umístění partnerského vztahu strategicky distribuovaných po celém světě. Další informace najdete v tématu [informace o ExpressRoute přímé připojení](expressroute-erdirect-about.md).

> [!IMPORTANT]
> Přímé ExpressRoute je aktuálně ve verzi preview.
>
> Přímá ExpressRoute ve verzi public preview se poskytuje bez smlouvu o úrovni služeb. ExpressRoute s přímým přístupem ve verzi Preview byste neměli používat pro produkční úlohy. Některé funkce nemusí být podporované, některé funkce můžou mít omezené možnosti a některé funkce nemusí být k dispozici ve všech umístěních Azure. Podrobnosti najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="resources"></a>Vytvoření prostředku

1. Přihlaste se k Azure a vyberte předplatné, které obsahuje ExpressRoute. Prostředek přímo ExpressRoute a okruhů ExpressRoute musí být ve stejném předplatném. V Azure CLI spusťte následující příkazy:

  ```azurecli
  az login
  ```

  Zkontrolujte předplatná pro tento účet: 

  ```azurecli
  az account list 
  ```

  Vyberte předplatné, pro kterou chcete vytvořit okruh ExpressRoute:

  ```azurecli
  az account set --subscription "<subscription ID>"
  ```

2. Výpis všech umístění, kde se podporuje přímý ExpressRoute:
    
  ```azurecli
  az network express-route port location list
  ```

  **Příklad výstupu**
  
  ```azurecli
  [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
  ]
  ```
3. Určení, zda jeden z míst uvedených v předchozím kroku dostupnou šířku pásma:

  ```azurecli
  az network express-route port location show -l "Equinix-Ashburn-DC2"
  ```

  **Příklad výstupu**

  ```azurecli
  {
  "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
  "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
  ],
  "contact": "support@equinix.com",
  "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
  "location": null,
  "name": "Equinix-Ashburn-DC2",
  "provisioningState": "Succeeded",
  "tags": null,
  "type": "Microsoft.Network/expressRoutePortsLocations"
  }
  ```
4. Vytvoření prostředku ExpressRoute Direct, která je založena na umístění, které jste zvolili v předchozích krocích.

  Přímé ExpressRoute podporuje QinQ a Dot1Q zapouzdření. Pokud vyberete QinQ, každý okruh ExpressRoute se dynamicky přiřadí značku S a je jedinečný v rámci prostředku ExpressRoute přímo. Každá značka C na okruh musí být jedinečný v okruhu, ale ne přes ExpressRoute přímo prostředku.  

  Pokud vyberete Dot1Q zapouzdření, musíte spravovat jedinečnost C – značka (VLAN) přes celý zdroj přímo ExpressRoute.  

  > [!IMPORTANT]
  > Přímé ExpressRoute může být pouze jeden typ zapouzdření. Typ zapouzdření nelze změnit po vytvoření prostředku ExpressRoute přímo.
  > 
 
  ```azurecli
  az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
  ```

  > [!NOTE]
  > Můžete také nastavit **zapouzdření** atribut **Dot1Q**. 
  >

  **Příklad výstupu**

  ```azurecli
  {
  "allocationDate": "Wednesday, October 17, 2018",
  "bandwidthInGbps": 100,
  "circuits": null,
  "encapsulation": "Dot1Q",
  "etag": "W/\"<etagnumber>\"",
  "etherType": "0x8100",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
  "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
  ],
  "location": "westus",
  "mtu": "1500",
  "name": "Contoso-Direct",
  "peeringLocation": "Equinix-Ashburn-DC2",
  "provisionedBandwidthInGbps": 0.0,
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
  "tags": null,
  "type": "Microsoft.Network/expressRoutePorts"
  }  
  ```

## <a name="state"></a>Změna AdminState pro odkazy

Tento proces můžete provést test vrstvy 1. Ujistěte se, že každý křížové připojení správně nainstalované opravy do směrovače v primární a sekundární porty.

1. Nastavte odkazy na **povoleno**. Opakováním tohoto kroku můžete nastavit každý odkaz na **povoleno**.

  Odkazy [0] je primární port a odkazy [1] je sekundární port.

  ```azurecli
  az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
  ```
  ```azurecli
  az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
  ```
  **Příklad výstupu**

  ```azurecli
  {
  "allocationDate": "Wednesday, October 17, 2018",
  "bandwidthInGbps": 100,
  "circuits": null,
  "encapsulation": "Dot1Q",
  "etag": "W/\"<etagnumber>\"",
  "etherType": "0x8100",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
  "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
  ],
  "location": "westus",
  "mtu": "1500",
  "name": "Contoso-Direct",
  "peeringLocation": "Equinix-Ashburn-DC2",
  "provisionedBandwidthInGbps": 0.0,
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "resourceGuid": "<resourceGUID>",
  "tags": null,
  "type": "Microsoft.Network/expressRoutePorts"
  }
  ```

  Stejný postup použijte k dolů porty pomocí `AdminState = “Disabled”`.

## <a name="circuit"></a>Vytvoření okruhu

Ve výchozím nastavení můžete vytvořit 10 okruhů v rámci předplatného, který obsahuje prostředek přímo ExpressRoute. Microsoft Support můžete zvýšit výchozí omezení. Zodpovídáte za sledování zřízené a využívaných šířku pásma. Přenosového pásma je součet šířky pásma všechny okruhy ExpressRoute přímo prostředku. Využívaných šířka pásma je fyzický využití základní fyzické rozhraní.

Můžete použít další okruh šířek pásma na ExpressRoute přímo pouze za účelem podpory scénářů podle zde uvedeného. Šířky pásma se 40 GB/s a 100 GB/s.

Můžete vytvořit okruhy Standard nebo Premium. Ceny za službu jsou součástí standardní okruhy. Náklady na Premium okruhů je založen na šířku pásma, kterou jste vybrali. Okruhy můžete vytvořit jen měří. Neomezený počet okruhů ExpressRoute přímo nepodporuje.

Vytvoření okruhu ExpressRoute přímo prostředku:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Další šířek pásma zahrnují 40 GB/s, 5 GB/s a 10 GB/s.

  **Příklad výstupu**

  ```azurecli
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Další postup

Další informace o ExpressRoute Direct, najdete v článku [přehled](expressroute-erdirect-about.md).
