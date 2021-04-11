---
title: 'Azure ExpressRoute: Konfigurace ExpressRoute Direct'
description: Naučte se, jak pomocí Azure PowerShell nakonfigurovat Azure ExpressRoute Direct pro přímé připojení k globální síti Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 2c49fe65cf97207a495f6c0cc78585489a0db6ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228244"
---
# <a name="how-to-configure-expressroute-direct"></a>Jak nakonfigurovat ExpressRoute Direct

ExpressRoute Direct vám umožní přímo se připojit k globální síti Microsoftu prostřednictvím umístění partnerských vztahů, které jsou v celém světě strategické distribuce. Další informace najdete v článku o [ExpressRoute Direct](expressroute-erdirect-about.md).

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

1. Přihlaste se k Azure a vyberte předplatné. Okruhy prostředků ExpressRoute Direct a ExpressRoute musí být ve stejném předplatném.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Opětovně zaregistrujete předplatné do Microsoftu. Network pro přístup k rozhraním API expressrouteportslocation a expressrouteport.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Vypíše všechna umístění, kde se podporuje ExpressRoute Direct.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
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
4. Zjistit, jestli výše uvedená umístění má dostupnou šířku pásma

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
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
5. Vytvoření prostředku ExpressRoute Direct na základě výše zvoleného umístění

   ExpressRoute Direct podporuje zapouzdření QinQ i Dot1Q. Pokud je vybraná možnost QinQ, bude se každému okruhu ExpressRoute dynamicky přiřazovat značka S-a bude jedinečný v celém prostředku ExpressRoute Direct. Každé označení C na okruhu musí být v okruhu jedinečné, ale ne přes ExpressRoute Direct.  

   Pokud je vybraná možnost zapouzdření Dot1Q, musíte spravovat jedinečnost značky C (VLAN) napříč celým prostředkem ExpressRoute Direct.  

   > [!IMPORTANT]
   > ExpressRoute Direct může být jenom jeden typ zapouzdření. Po přímém vytvoření ExpressRoute se zapouzdření nedá změnit.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
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

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Vygenerovat písmeno autorizace (LOA)

Odkažte na nedávno vytvořený ExpressRoute přímý prostředek, zadejte název zákazníka pro zápis LOA do a (volitelně) definujte umístění souboru pro uložení dokumentu. Pokud na cestu k souboru neodkazuje, dokument se stáhne do aktuálního adresáře.

  ```powershell 
   New-AzExpressRoutePortLOA -ExpressRoutePort $ERDirect -CustomerName TestCustomerName -Destination "C:\Users\SampleUser\Downloads" 
   ```
 **Příklad výstupu**

   ```powershell
   Written Letter of Authorization To: C:\Users\SampleUser\Downloads\LOA.pdf
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>Změnit stav Správce odkazů
   
Tento proces by měl být použit k provedení testu vrstvy 1, čímž se zajistí, že každé připojení mezi jednotlivými směrovači je u primárních a sekundárních zařízení správně opraveno.
1. Získat podrobnosti o ExpressRoute

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Nastavte odkaz na povoleno. Zopakováním tohoto kroku nastavte všechna propojení na povoleno.

   Odkazy [0] je primární port a propojení [1] je sekundární port.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
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

   `AdminState = "Disabled"`Pro vypnutí portů použijte stejný postup.

## <a name="create-a-circuit"></a><a name="circuit"></a>Vytvoření okruhu

Ve výchozím nastavení můžete vytvořit 10 okruhů v rámci předplatného, kde je prostředek ExpressRoute Direct. Toto omezení se dá zvýšit díky podpoře. Zodpovídáte za sledování zřízené i využité šířky pásma. Zřízená šířka pásma je celková šířka šířky pásma všech okruhů v prostředku ExpressRoute Direct a využité šířky pásma je fyzické využití základních fyzických rozhraní.

K dispozici jsou další šířky pásma okruhů, které je možné využít na ExpressRoute přímo k podpoře pouze těch scénářů uvedených výše. Tyto šířky pásma jsou 40 GB/s a 100 GB/s.

**SkuTier** může být Local, Standard nebo Premium.

**SkuFamily** může být MeteredData. V ExpressRoute Direct se nepodporuje neomezený počet.

Vytvořte okruh na prostředku ExpressRoute Direct.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Mezi další šířky pásma patří: 5,0, 10,0 a 40,0.

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
## <a name="delete-the-resource"></a>Odstranit prostředek
Před odstraněním prostředku ExpressRoute Direct musíte nejprve odstranit všechny okruhy ExpressRoute vytvořené na páru portů ExpressRoute Direct.
Přímý prostředek ExpressRoute můžete odstranit spuštěním následujícího příkazu:
 ```powershell
   Remove-azexpressrouteport -Name $Name -Resourcegroupname -$ResourceGroupName
   ```
## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute Direct najdete v [přehledu](expressroute-erdirect-about.md).
