---
title: 'ExpressRoute: propojení virtuální sítě s okruhem: Classic'
description: Tento dokument poskytuje přehled, jak propojit virtuální sítě (virtuální sítě) se ExpressRoute okruhy pomocí modelu nasazení Classic a PowerShellu.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: e02073e777c62be00b5c25c2242294e54795a0d4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031609"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí prostředí PowerShell (Classic)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojit virtuální sítě (virtuální sítě) se ExpressRoute okruhy Azure pomocí PowerShellu. Jedné virtuální sítě nesmí být propojení až čtyři okruhy ExpressRoute. Pomocí kroků v tomto článku vytvoříte nový odkaz na každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute může být ve stejném předplatném, různá předplatná nebo kombinaci obou. Tento článek se týká virtuálních sítí vytvořených pomocí modelu nasazení Classic.

K okruhu ExpressRoute můžete propojit až 10 virtuálních sítí. Všechny virtuální sítě musí být ve stejné geopolitické oblasti. Můžete propojit větší počet virtuálních sítí s okruhem ExpressRoute nebo propojit virtuální sítě, které jsou v jiných geopolitických oblastech, pokud povolíte doplněk ExpressRoute Premium. Další podrobnosti o doplňku Premium najdete v [nejčastějších dotazech](expressroute-faqs.md) .

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky směrování](expressroute-routing.md), a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Musí mít aktivní okruh ExpressRoute.
   * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-classic.md) a umožněte vašemu poskytovateli připojení okruh.
   * Ujistěte se, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Zobrazit [konfigurace směrování](expressroute-howto-routing-classic.md) najdete pokyny pro směrování.
   * Ověřte, že je nakonfigurovaný soukromého partnerského vztahu Azure a partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem nahoru tak, že povolíte připojení k začátku do konce.
   * Musíte mít virtuální síť a bránu virtuální sítě vytvořenou a plně zřízenou. Podle pokynů [nakonfigurujte virtuální síť pro ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Stáhnout nejnovější rutiny PowerShellu

Nainstalujte nejnovější verze modulů Azure Service Management Powershellu a modul ExpressRoute. Při použití v následujícím příkladu, mějte na paměti, že číslo verze (v tomto příkladu 5.1.1) se změní vydané novější verze rutin.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Pokud potřebujete další informace o Azure Powershellu, přečtěte si [Začínáme s rutinami Azure Powershellu](/powershell/azure/overview) podrobné pokyny o tom, jak nakonfigurovat počítač pomocí modulů Azure Powershellu.

### <a name="sign-in"></a>Přihlášení

Pokud se chcete přihlásit ke svému účtu Azure, použijte následující příklady:

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu.

   ```powershell
   Connect-AzAccount
   ```
2. Zkontrolujte předplatná pro příslušný účet.

   ```powershell
   Get-AzSubscription
   ```
3. Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. V dalším kroku použijte následující rutinu k vašemu předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

   ```powershell
   Add-AzureAccount
   ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě v rámci stejného předplatného k okruhu
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
Okruh ExpressRoute můžete sdílet napříč několika předplatnými. Následující obrázek znázorňuje jednoduchý způsob sdílení prací pro okruhy ExpressRoute schéma napříč několika předplatnými.

Každá z menších cloudy v rámci velkých cloudových se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Každé oddělení v rámci organizace může používat vlastní předplatné pro nasazení svých služeb – ale oddělení můžou sdílet jeden okruh ExpressRoute pro připojení zpátky k místní síti. Jednoho oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Okruh ExpressRoute můžete použít další předplatná v rámci organizace.

> [!NOTE]
> Poplatky za připojení a šířku pásma pro vyhrazeného okruhu uplatní se na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojením mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Správa
*Vlastníkem okruhu* je správce nebo spolusprávce předplatného, ve kterém se vytvoří okruh ExpressRoute. Vlastník okruhu může autorizovat správce nebo spolusprávce jiných předplatných, označovaných jako *Uživatelé okruhu*, aby mohli používat vyhrazený okruh, který vlastní. Uživatelé, kteří mají oprávnění používat okruh ExpressRoute organizace, můžou po autorizaci propojit virtuální síť ve svém předplatném s okruhem ExpressRoute.

Vlastníka okruhu má schopnost upravovat dokumentů a odvolání přístupu autorizace kdykoli. Odvolání autorizace způsobí odstranění všech odkazů z předplatného, jejichž přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytváření autorizace**

Vlastník okruhu autorizuje správce jiných předplatných k používání zadaného okruhu. V následujícím příkladu správce okruhu (Contoso IT) umožňuje správci jiného předplatného (vývoj-test) propojit až dvě virtuální sítě s okruhem. Správce IT společnosti Contoso to umožňuje zadáním ID pro vývoj a testování společnosti Microsoft. Rutina nepošle e-mail na zadané ID Microsoftu. Vlastník okruhu musí explicitně informovat jiného vlastníka předplatného, že se autorizace dokončila.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Vrátit

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Kontrola autorizací**

Vlastníka okruhu můžete zkontrolovat všechny autorizace, které jsou vydány na konkrétní okruhu spuštěním následující rutiny:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Vrátit

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

  Vrátit

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Odstraňují se autorizace.**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací uživateli spuštěním následující rutiny:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operace pro uživatele okruhu

**Kontrola autorizací**

Uživatel okruhu může ověřit autorizaci pomocí následující rutiny:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Vrátit

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

Uživatele okruhu spuštěním následující rutiny můžete uplatnit autorizaci odkaz:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Vrátit

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

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
