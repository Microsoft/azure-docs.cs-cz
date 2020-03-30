---
title: 'Azure ExpressRoute: Propojení virtuální sítě s okruhem: klasické'
description: Tento dokument poskytuje přehled o tom, jak propojit virtuální sítě (Virtuální sítě) s okruhy ExpressRoute pomocí klasického modelu nasazení a prostředí PowerShell.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930040"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí PowerShellu (klasického)
> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojit virtuální sítě (Virtuální sítě) s okruhy Azure ExpressRoute pomocí PowerShellu. Jedna virtuální síť může být propojena až ke čtyřem okruhům ExpressRoute. Pomocí kroků v tomto článku vytvořte nový odkaz na každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute může být ve stejném předplatném, různých předplatných nebo kombinací obou. Tento článek se vztahuje na virtuální sítě vytvořené pomocí klasického modelu nasazení.

K okruhu ExpressRoute můžete propojit až 10 virtuálních sítí. Všechny virtuální sítě musí být ve stejné geopolitické oblasti. Můžete propojit větší počet virtuálních sítí s okruhem ExpressRoute nebo propojit virtuální sítě, které jsou v jiných geopolitických oblastech, pokud povolíte doplněk ExpressRoute premium. Další podrobnosti o prémiovém doplňku najdete v [nejčastějších](expressroute-faqs.md) dotazech.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md)na směrování , [požadavky na směrování](expressroute-routing.md)a pracovní [postupy.](expressroute-workflows.md)
* Musí mít aktivní okruh ExpressRoute.
   * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-classic.md) a povolte poskytovatele připojení okruh.
   * Ujistěte se, že máte azure privátní partnerský vztah nakonfigurovaný pro váš okruh. Pokyny pro směrování naleznete v článku [Konfigurace směrování.](expressroute-howto-routing-classic.md)
   * Ujistěte se, že je nakonfigurovaný soukromý partnerský vztah Azure a partnerský vztah Protokolu BGP mezi vaší sítí a Microsoftem je nahoře, abyste mohli povolit připojení od konce do konce.
   * Musíte mít vytvořenou a plně zřízené virtuální síť a bránu virtuální sítě. Podle pokynů [nakonfigurujte virtuální síť pro ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Stažení nejnovějších rutin prostředí PowerShell

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě ve stejném předplatném k okruhu
Virtuální síť můžete propojit s okruhem ExpressRoute pomocí následující rutiny. Ujistěte se, že brána virtuální sítě je vytvořen a je připraven k propojení před spuštěním rutiny.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Odebrání propojení virtuální sítě s okruhem
Propojení virtuální sítě s okruhem ExpressRoute můžete odebrat pomocí následující rutiny. Ujistěte se, že aktuální předplatné je vybránpro danou virtuální síť. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet mezi více předplatnými. Následující obrázek znázorňuje jednoduché schéma fungování sdílení pro okruhy ExpressRoute napříč více předplatnými.

Každý z menších cloudů v rámci velkého cloudu se používá k reprezentaci předplatných, které patří do různých oddělení v rámci organizace. Každé oddělení v rámci organizace můžete použít své vlastní předplatné pro nasazení svých služeb – ale oddělení můžete sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Okruh ExpressRoute může vlastnit jedno oddělení (v tomto příkladu: IT). Ostatní předplatná v rámci organizace můžete použít okruh ExpressRoute.

> [!NOTE]
> Poplatky za připojení a šířku pásma vyhrazeného okruhu budou použity na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojení napříč předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Správa
*Vlastník okruhu* je správce/spolusprávce předplatného, ve kterém je vytvořen okruh ExpressRoute. Vlastník okruhu může povolit správcům/spolusprávcům jiných předplatných označovaných jako *uživatelé okruhu,* aby používali vyhrazený okruh, který vlastní. Uživatelé okruhu, kteří jsou oprávněni používat okruh ExpressRoute organizace, mohou propojit virtuální síť ve svém předplatném s okruhem ExpressRoute poté, co jsou autorizováni.

Vlastník obvodu má pravomoc kdykoli upravit a odvolat autorizace. Odvolání autorizace bude mít za následek odstranění všech odkazů z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace**

Vlastník okruhu autorizuje správcům jiných předplatných používat zadaný okruh. V následujícím příkladu správce okruhu (Contoso IT) umožňuje správci jiného předplatného (Dev-Test) propojit až dvě virtuální sítě s okruhem. Správce IT contoso umožňuje zadáním Dev-Test Microsoft ID. Rutina neodesílá e-maily na zadané Microsoft ID. Vlastník okruhu musí explicitně upozornit ostatní vlastníky předplatného, že autorizace je dokončena.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Vrací:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Kontrola autorizací**

Vlastník okruhu může zkontrolovat všechna oprávnění, která jsou vydána na určitém okruhu spuštěním následující rutiny:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Vrací:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Aktualizace autorizací**

Vlastník okruhu můžete upravit autorizace pomocí následující rutiny:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Vrací:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Odstranění autorizací**

Vlastník okruhu může odvolat nebo odstranit autorizace pro uživatele spuštěním následující rutiny:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operace uživatele okruhu

**Kontrola autorizací**

Uživatel okruhu může zkontrolovat autorizace pomocí následující rutiny:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Vrací:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Uplatnění autorizací odkazů**

Uživatel okruhu může spustit následující rutinu pro uplatnění autorizace odkazu:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Vrací:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Spusťte tento příkaz v nově propojeném předplatném pro virtuální síť:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Další kroky

Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
