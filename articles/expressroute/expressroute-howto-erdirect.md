---
title: Konfigurace ExpressRoute přímo – Azure | Dokumentace Microsoftu
description: Tato stránka vám pomůže nakonfigurovat přímé ExpressRoute (Preview).
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 9dadd61c8c4c2d1cd2305aa852e4528bfb9a1421
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076606"
---
# <a name="how-to-configure-expressroute-direct-preview"></a>Konfigurace ExpressRoute Direct (Preview)

Přímé ExpressRoute poskytuje možnost připojení přímo do globální síti Microsoftu na umístění partnerského vztahu strategicky distribuovaných po celém světě. Další informace najdete v tématu [informace o ExpressRoute přímé připojení](expressroute-erdirect-about.md).

> [!IMPORTANT]
> Přímé ExpressRoute je aktuálně ve verzi Preview.
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="resources"></a>Vytvoření prostředku

1. Přihlaste se k Azure a vyberte předplatné. Prostředek přímo ExpressRoute a okruhy ExpressRoute musí být ve stejném předplatném.

  ```powershell
  Connect-AzureRMAccount 

  Select-AzureRMSubscription -Subscription “<SubscriptionID or SubscriptionName>”
  ```
2. Výpis všech umístění, kde se podporuje přímý ExpressRoute.
  
  ```powershell
  Get-AzureRmExpressRoutePortsLocation
  ```

  **Příklad výstupu**
  
  ```powershell
  Name                : Equinix-Ashburn-DC2
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
  ProvisioningState   : Succeeded
  Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-Dallas-DA3
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
  ProvisioningState   : Succeeded
  Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : []
  ```
3. Pokud výše uvedené umístění určila dostupnou šířku pásma

  ```powershell
  Get-AzureRMExpressRoutePortsLocations -Name "Equinix-San-Jose-SV1"
  ```

  **Příklad výstupu**

  ```powershell
  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
  ```
4. Vytvoření prostředku ExpressRoute přímo na základě umístění zvoleno výše

  Přímé ExpressRoute podporuje QinQ a Dot1Q zapouzdření. Vybrali QinQ každý okruh ExpressRoute se dynamicky přiřadí značku S a bude jedinečný v rámci prostředku ExpressRoute přímo. Každá značka C na okruh musí být jedinečný v okruhu, ale ne přes ExpressRoute přímo.  

  Vybrali Dot1Q zapouzdření musíte spravovat jedinečnost C – značka (VLAN) přes celý zdroj přímo ExpressRoute.  

  > [!IMPORTANT]
  > Přímé ExpressRoute může být pouze jeden typ zapouzdření. Zapouzdření nelze změnit po vytvoření přímé ExpressRoute.
  > 
 
  ```powershell 
  $ERDirect = New-AzureRMExpressRoutePort -Name $Name -ResourceGroupName -$RGName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
  ```

  > [!NOTE]
  > Atribut zapouzdření může být také nastaven na Dot1Q. 
  >

  **Příklad výstupu:**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
  Circuits                   : []
  ```

## <a name="state"></a>Změnit stav správce odkazů

  Tento proces by měla sloužit k provedení testu vrstvy 1, zajistit, aby každý křížové připojení správně opravený do směrovače pro primární a sekundární.
1. Získáte přímé ExpressRoute podrobnosti.

  ```powershell
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  ```
2. Nastavit odkaz na povoleno. Opakujte tento krok nastavit každý odkaz na povoleno.

  Odkazy [0] je primární port a odkazy [1] je sekundární port.

  ```powershell
  $ERDirect.Links[0].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  $ERDirect.Links[1].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  ```
  **Příklad výstupu:**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
  Circuits                   : []
  ```

  Použijte stejný postup s `AdminState = “Disabled”` Chcete-li snížit porty.

## <a name="circuit"></a>Vytvoření okruhu

Ve výchozím nastavení můžete vytvořit v rámci předplatného, ve kterém je prostředek přímo ExpressRoute 10 okruhy. To jde navýšit o podporu. Které nesou odpovědnost za sledování zřízená a využité šířky pásma. Přenosového pásma je součet šířky pásma všechny okruhy ExpressRoute přímo prostředku a využívaných šířky pásma fyzického využití základní fyzické rozhraní.

Existují další okruh šířky pásma, které můžete využít na ExpressRoute přímo pouze do podpory scénářů uvedených výše. Jedná se o: 40Gbps a 100Gbps.

Je možné vytvářet okruhy úrovně Standard nebo premium. Standardní okruhy jsou zahrnutá v ceně, zatímco okruhy premium mají svou cenu na základě šířky pásma vybrané. Okruhy lze vytvořit pouze jako měřená, jako neomezená nepodporuje přímé ExpressRoute.

Vytvoření okruhu ExpressRoute přímo prostředku.

  ```powershell
  New-AzureRmExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Zahrnout další šířek pásma: 5.0, 10.0 a 40.0

  **Příklad výstupu:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>Další postup

Další informace o ExpressRoute Direct, najdete v článku [přehled](expressroute-erdirect-about.md).
