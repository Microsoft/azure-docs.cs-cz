---
title: 'Azure ExpressRoute: propojení virtuální sítě s okruhem: Classic'
description: Tento dokument poskytuje přehled, jak propojit virtuální sítě (virtuální sítě) se ExpressRoute okruhy pomocí modelu nasazení Classic a PowerShellu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a731962f22985268093c547b09a8cd77c5b92660
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89395804"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí prostředí PowerShell (Classic)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojit virtuální sítě (virtuální sítě) se ExpressRoute okruhy Azure pomocí PowerShellu. Jedna virtuální síť může být propojená s až čtyřmi okruhy ExpressRoute. Pomocí kroků v tomto článku vytvoříte nový odkaz na každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute můžou být ve stejném předplatném, různých předplatných nebo kombinaci obou. Tento článek se týká virtuálních sítí vytvořených pomocí modelu nasazení Classic.

K okruhu ExpressRoute můžete propojit až 10 virtuálních sítí. Všechny virtuální sítě musí být ve stejné geopolitické oblasti. Můžete propojit větší počet virtuálních sítí s okruhem ExpressRoute nebo propojit virtuální sítě, které jsou v jiných geopolitických oblastech, pokud povolíte doplněk ExpressRoute Premium. Další podrobnosti o doplňku Premium najdete v [nejčastějších dotazech](expressroute-faqs.md) .

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Než začnete s konfigurací, Projděte si [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .
* Musí mít aktivní okruh ExpressRoute.
   * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-classic.md) a umožněte vašemu poskytovateli připojení okruh.
   * Ujistěte se, že máte pro váš okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny k směrování najdete v článku věnovaném [konfiguraci směrování](expressroute-howto-routing-classic.md) .
   * Ujistěte se, že je nakonfigurovaný privátní partnerský vztah Azure a že partnerský vztah protokolu BGP mezi vaší sítí a Microsoftem je tak, aby bylo možné povolit kompletní připojení.
   * Musíte mít virtuální síť a bránu virtuální sítě vytvořenou a plně zřízenou. Podle pokynů [nakonfigurujte virtuální síť pro ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Stáhnout nejnovější rutiny PowerShellu

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě ve stejném předplatném k okruhu
Virtuální síť můžete propojit s okruhem ExpressRoute pomocí následující rutiny. Ujistěte se, že je brána virtuální sítě vytvořená a je připravená k propojení před spuštěním rutiny.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Odebrání odkazu virtuální sítě na okruh
Propojení virtuální sítě s okruhem ExpressRoute můžete odebrat pomocí následující rutiny. Ujistěte se, že je vybráno aktuální předplatné pro danou virtuální síť. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet mezi několika předplatnými. Následující obrázek ukazuje jednoduché schéma, jak sdílení funguje pro okruhy ExpressRoute napříč několika předplatnými.

Každý z menších cloudů ve velkém cloudu se používá k reprezentaci předplatných, která patří do různých oddělení v rámci organizace. Každé oddělení v rámci organizace může používat vlastní předplatné pro nasazení svých služeb – ale oddělení můžou sdílet jeden okruh ExpressRoute pro připojení zpátky k místní síti. Jeden oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Další předplatná v rámci organizace můžou používat okruh ExpressRoute.

> [!NOTE]
> Pro vlastníka okruhu ExpressRoute se použijí poplatky za připojení a šířku pásma pro vyhrazený okruh. Všechny virtuální sítě mají stejnou šířku pásma.
> 
> 

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Správa
*Vlastníkem okruhu* je správce nebo spolusprávce předplatného, ve kterém se vytvoří okruh ExpressRoute. Vlastník okruhu může autorizovat správce nebo spolusprávce jiných předplatných, označovaných jako *Uživatelé okruhu*, aby mohli používat vyhrazený okruh, který vlastní. Uživatelé, kteří mají oprávnění používat okruh ExpressRoute organizace, můžou po autorizaci propojit virtuální síť ve svém předplatném s okruhem ExpressRoute.

Vlastník okruhu má kdykoliv oprávnění upravovat a odvolat. Odvolání autorizace způsobí odstranění všech odkazů z předplatného, jejichž přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytváření autorizace**

Vlastník okruhu autorizuje správce jiných předplatných k používání zadaného okruhu. V následujícím příkladu správce okruhu (Contoso IT) umožňuje správci jiného předplatného (vývoj-test) propojit až dvě virtuální sítě s okruhem. Správce IT společnosti Contoso to umožňuje zadáním ID Dev-Test společnosti Microsoft. Rutina nepošle e-mail na zadané ID Microsoftu. Vlastník okruhu musí explicitně informovat jiného vlastníka předplatného, že se autorizace dokončila.

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

Vlastník okruhu může zkontrolovat všechna oprávnění vydaná určitým okruhem spuštěním následující rutiny:

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

Vlastník okruhu může upravit autorizaci pomocí následující rutiny:

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

**Odstraňují se autorizace.**

Vlastník okruhu může pro uživatele odvolat nebo odstranit autorizaci spuštěním následující rutiny:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operace uživatele obvodu

**Kontrola autorizací**

Uživatel okruhu může ověřit autorizaci pomocí následující rutiny:

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

**Uplatnění autorizace odkazů**

Uživatel okruhu může spustit následující rutinu pro uplatnění autorizace propojení:

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

Další informace o ExpressRoute najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).
