---
title: 'Azure ExpressRoute: Konfigurace ExpressRoute Direct: CLI'
description: Naučte se pomocí Azure CLI nakonfigurovat Azure ExpressRoute Direct pro přímé připojení k globální síti Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: d68011afe044535783dd8a8c56ed5d950c6d06b1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099875"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Konfigurace ExpressRoute Direct pomocí Azure CLI

ExpressRoute Direct vám umožní přímo se připojit k globální síti Microsoftu prostřednictvím umístění partnerských vztahů, které jsou v celém světě strategické distribuce. Další informace najdete v tématu [informace o přímém připojení ExpressRoute](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a>Než začnete

Než začnete používat ExpressRoute Direct, musíte nejdřív zaregistrovat své předplatné. Než začnete používat ExpressRoute Direct, musíte nejdřív zaregistrovat své předplatné. Pokud se chcete zaregistrovat, udělejte to prosím pomocí Azure PowerShell:
1.  Přihlaste se k Azure a vyberte předplatné, které chcete zaregistrovat.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Zaregistrujte své předplatné pro Public Preview pomocí následujícího příkazu:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Po zaregistrování ověřte, zda je poskytovatel prostředků **Microsoft. Network** zaregistrován ve vašem předplatném. Když zaregistrujete poskytovatele prostředků, nakonfigurujete vaše předplatné, aby fungovalo s poskytovatelem prostředků.

## <a name="create-the-resource"></a><a name="resources"></a>Vytvoření prostředku

1. Přihlaste se k Azure a vyberte předplatné, které obsahuje ExpressRoute. Prostředek ExpressRoute Direct a vaše okruhy ExpressRoute musí být ve stejném předplatném. V rozhraní příkazového řádku Azure CLI spusťte následující příkazy:

   ```azurecli
   az login
   ```

   Ověřte odběry účtu: 

   ```azurecli
   az account list 
   ```

   Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Znovu zaregistrujte své předplatné do Microsoftu. Network pro přístup k rozhraním API expressrouteportslocation a expressrouteport.

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. Vypíše všechna umístění, kde se podporuje ExpressRoute Direct:
    
   ```azurecli
   az network express-route port location list
   ```

   **Příklad výstupu**
  
   ```output
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
4. Určete, zda jedno z umístění uvedených v předchozím kroku má dostupnou šířku pásma:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Příklad výstupu**

   ```output
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
5. Vytvořte ExpressRoute přímý prostředek založený na umístění, které jste zvolili v předchozích krocích.

   ExpressRoute Direct podporuje zapouzdření QinQ i Dot1Q. Vyberete-li možnost QinQ, každý okruh ExpressRoute dynamicky přiřadí značku S-a je jedinečný v celém prostředku ExpressRoute Direct. Každé označení C na okruhu musí být v okruhu jedinečné, ale ne napříč ExpressRoute přímým zdrojem.  

   Pokud vyberete zapouzdření Dot1Q, musíte spravovat jedinečnost značky C (VLAN) napříč celým prostředkem ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct může být jenom jeden typ zapouzdření. Typ zapouzdření nemůžete změnit po vytvoření prostředku ExpressRoute Direct.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Můžete také nastavit atribut **zapouzdření** na **Dot1Q**. 
   >

   **Příklad výstupu**

   ```output
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

## <a name="change-adminstate-for-links"></a><a name="state"></a>Změnit AdminState pro odkazy

Tento postup použijte k provedení testu vrstvy 1. Ujistěte se, že každé připojení mezi jednotlivými směrovači v primárních a sekundárních portech je správně opravené.

1. Nastavte odkazy na **povoleno**. Zopakováním tohoto kroku nastavte všechna propojení na **povoleno**.

   Odkazy [0] je primární port a propojení [1] je sekundární port.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Příklad výstupu**

   ```output
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

   Stejný postup použijte pro snížení portu pomocí `AdminState = "Disabled"` .

## <a name="create-a-circuit"></a><a name="circuit"></a>Vytvoření okruhu

Ve výchozím nastavení můžete vytvořit 10 okruhů v rámci předplatného, které obsahuje prostředek ExpressRoute Direct. Podpora Microsoftu může zvýšit výchozí limit. Zodpovídáte za sledování zřízené a využité šířky pásma. Zřízená šířka pásma je součet šířky pásma všech okruhů v prostředku ExpressRoute Direct. Využitá šířka pásma je fyzické využití základních fyzických rozhraní.

Další šířky pásma okruhů v ExpressRoute můžete použít jenom pro podporu scénářů, které jsou zde popsané. Šířky pásma jsou 40 GB/s a 100 GB/s.

**SkuTier** může být Local, Standard nebo Premium.

**SkuFamily** může být MeteredData. V ExpressRoute Direct se nepodporuje neomezený počet.

Vytvořte okruh na prostředku ExpressRoute Direct:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Mezi další šířky pásma patří 5 GB/s, 10 GB/s a 40 GB/s.

  **Příklad výstupu**

  ```output
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

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute Direct najdete v [přehledu](expressroute-erdirect-about.md).
