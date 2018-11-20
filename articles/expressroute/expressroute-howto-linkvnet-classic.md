---
title: 'Propojení virtuální sítě k okruhu ExpressRoute: prostředí PowerShell: classic: Azure | Dokumentace Microsoftu'
description: Tento dokument poskytuje přehled o tom, jak propojit virtuální sítě (Vnet) pro okruhy ExpressRoute pomocí modelu nasazení classic a prostředí PowerShell.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: d2108b86aa44710535dbfad49819949a621cc900
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161255"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Připojení virtuální sítě k okruhu ExpressRoute pomocí prostředí PowerShell (classic)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojit virtuální sítě (Vnet) pro okruhy Azure ExpressRoute pomocí prostředí PowerShell. Jedné virtuální sítě nesmí být propojení až čtyři okruhy ExpressRoute. Chcete-li vytvořit nové propojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí kroků v tomto článku. Okruhy ExpressRoute může být ve stejném předplatném, různá předplatná nebo kombinaci obou. Tento článek se týká virtuální sítě vytvořené pomocí modelu nasazení classic.

Až 10 virtuálních sítí můžete propojit s okruhem ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti. Můžete propojit větší počet virtuálních sítí pro váš okruh ExpressRoute nebo propojení virtuálních sítí, které jsou v dalších geopolitických oblastí, je-li povolit doplněk ExpressRoute premium. Zkontrolujte, [– nejčastější dotazy](expressroute-faqs.md) další podrobnosti o doplněk premium.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Předpoklady konfigurace

* Zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky směrování](expressroute-routing.md), a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Musí mít aktivní okruh ExpressRoute.
   * Postupujte podle pokynů a [vytvořit okruh ExpressRoute](expressroute-howto-circuit-classic.md) a mají povolení je okruh poskytovatelem připojení.
   * Ujistěte se, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Zobrazit [konfigurace směrování](expressroute-howto-routing-classic.md) najdete pokyny pro směrování.
   * Ověřte, že je nakonfigurovaný soukromého partnerského vztahu Azure a partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem nahoru tak, že povolíte připojení k začátku do konce.
   * Musí mít virtuální sítě a bránu virtuální sítě vytvořené a plně zřízený. Postupujte podle pokynů a [konfigurace virtuální sítě pro ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Stáhněte si nejnovější rutiny prostředí PowerShell

Nainstalujte nejnovější verze modulů Azure Service Management Powershellu a modul ExpressRoute. Při použití v následujícím příkladu, mějte na paměti, že číslo verze (v tomto příkladu 5.1.1) se změní vydané novější verze rutin.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Pokud potřebujete další informace o Azure Powershellu, přečtěte si [Začínáme s rutinami Azure Powershellu](/powershell/azure/overview) podrobné pokyny o tom, jak nakonfigurovat počítač pomocí modulů Azure Powershellu.

### <a name="sign-in"></a>Přihlášení

Přihlásit se ke svému účtu Azure, použijte následující příklady:

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

4. V dalším kroku použijte následující rutinu k vašemu předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

  ```powershell
  Add-AzureAccount
  ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě v rámci stejného předplatného k okruhu
Pomocí následující rutiny můžete propojit virtuální sítě k okruhu ExpressRoute. Ujistěte se, že brány virtuální sítě je vytvořen a připraven k propojení předtím, než spustíte rutinu.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Odebrat propojení virtuální sítě k okruhu
Propojení virtuální sítě k okruhu ExpressRoute můžete odebrat pomocí následující rutiny. Ujistěte se, že je vybrána aktuální předplatné pro dané virtuální síti. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet napříč několika předplatnými. Následující obrázek znázorňuje jednoduchý způsob sdílení prací pro okruhy ExpressRoute schéma napříč několika předplatnými.

Každá z menších cloudy v rámci velkých cloudových se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Vlastní předplatné každé oddělení v rámci organizace můžete použít k nasazení svých služeb – ale jako vodítko použijte oddělení můžou sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Jednoho oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Okruh ExpressRoute můžete použít další předplatná v rámci organizace.

> [!NOTE]
> Poplatky za připojení a šířku pásma pro vyhrazeného okruhu uplatní se na vlastníka okruhu ExpressRoute. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojením mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Správa
*Vlastníka okruhu* je správce nebo spolusprávce předplatného, ve kterém se vytvoří okruh ExpressRoute. Vlastníka okruhu můžete autorizovat správci/spolusprávci z jiných předplatných, označuje jako *okruh uživatelů*, pro použití vyhrazeného okruhu, které vlastní. Okruh uživatelů, kteří jsou oprávněni používat organizace okruh ExpressRoute můžete propojit virtuální sítě v rámci svého předplatného k okruhu ExpressRoute po jsou ověřeny.

Vlastníka okruhu má schopnost upravovat dokumentů a odvolání přístupu autorizace kdykoli. Odstraňuje se z předplatného, jehož přístup byl odvolán. všechny odkazy způsobí odvolání povolení.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření ověření**

Autorizuje vlastníka okruhu správcům další předplatná používat určený okruh. V následujícím příkladu správce okruhu (Contoso IT) umožňuje správcům z jiného předplatného (Dev-Test) k propojení až dvě virtuální sítě k okruhu. Správce společnosti Contoso IT umožňuje to tak, že zadáte ID vývoj a testování Microsoftu. Rutina nemá odeslání e-mailu pro zadaný účet Microsoft ID. Vlastníka okruhu, musí explicitně upozornit ostatní vlastník předplatného, registrace je dokončena.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Návratová hodnota:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Kontrola povolení**

Vlastníka okruhu můžete zkontrolovat všechny autorizace, které jsou vydány na konkrétní okruhu spuštěním následující rutiny:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Návratová hodnota:

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

**Aktualizuje se autorizace**

Vlastníka okruhu můžete upravit autorizace pomocí následující rutiny:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Návratová hodnota:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Odstraňuje se autorizace**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací uživateli spuštěním následující rutiny:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Operace pro uživatele okruhu

**Kontrola povolení**

Uživatele okruhu můžete zkontrolovat autorizace pomocí následující rutiny:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Návratová hodnota:

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

**Uplatnění autorizací propojení**

Uživatele okruhu spuštěním následující rutiny můžete uplatnit autorizaci odkaz:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Návratová hodnota:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

V nově propojené předplatné virtuální sítě, spusťte tento příkaz:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Další postup

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).