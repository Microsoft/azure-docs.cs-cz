---
title: 'Azure ExpressRoute: Úprava okruhu: PowerShell: Classic'
description: Tento článek vás provede kroky pro kontrolu stavu, aktualizaci nebo odstranění a zrušení zřízení okruhu modelu nasazení ExpressRoute Classic.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be45d49d3f445810c7ac6a38e3e12abe178a4bed
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396280"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Úprava okruhu ExpressRoute pomocí prostředí PowerShell (Classic)

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vás provede kroky pro kontrolu stavu, aktualizaci nebo odstranění a zrušení zřízení okruhu modelu nasazení ExpressRoute Classic. Tento článek se týká modelu nasazení Classic.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Získání stavu okruhu

Tyto informace můžete získat kdykoli pomocí `Get-AzureCircuit` rutiny. Volání bez parametrů vypíše všechny okruhy.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Informace o konkrétním okruhu ExpressRoute můžete získat předáním klíče služby jako parametru volání.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Podrobné popisy všech parametrů získáte spuštěním následujícího příkladu:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Úprava okruhu

Můžete upravit některé vlastnosti okruhu ExpressRoute, aniž by to mělo vliv na připojení.

Můžete provádět následující úlohy bez výpadku:

* Povolí nebo zakáže doplněk ExpressRoute Premium pro okruh ExpressRoute.
* Zvyšte šířku pásma okruhu ExpressRoute, pokud je na portu k dispozici dostatek kapacity. Downgrade šířky pásma okruhu není podporována.
* Změňte plán měření z měřených dat na neomezená data. Změna plánu měření z neomezených dat na měřená data není podporována.
* Můžete povolit nebo zakázat *operace klasických operací*.

Další informace o omezeních a omezeních najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md) .

### <a name="enable-the-expressroute-premium-add-on"></a>Povolit doplněk ExpressRoute Premium

Doplněk ExpressRoute Premium pro stávající okruh můžete povolit pomocí následující rutiny PowerShellu:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Váš okruh teď bude mít povolené funkce doplňku ExpressRoute Premium. Jakmile se příkaz úspěšně spustí, zahájí se fakturace pro funkci doplňku Premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Zakázat doplněk ExpressRoute Premium

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší, než je povoleno pro standardní okruh.
>
>

#### <a name="considerations"></a>Požadavky

* Před přechodem na úroveň Premium na Standard se ujistěte, že je počet virtuálních sítí propojených s okruhem menší než 10. Pokud to neuděláte, žádost o aktualizaci se nezdařila a účtují se poplatky za prémii.
* Je nutné odpojit všechny virtuální sítě v jiných geopolitických oblastech. Pokud to neuděláte, vaše žádost o aktualizaci se nezdařila a účtují se poplatky za prémii.
* Směrovací tabulka musí mít méně než 4 000 tras pro privátní partnerské vztahy. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace protokolu BGP se uvolní a nebude znovu povolena, dokud počet inzerovaných předpon nepřekročí hodnotu 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Zakázání doplňku Premium

Doplněk ExpressRoute Premium pro stávající okruh můžete zakázat pomocí následující rutiny PowerShellu:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualizace šířky pásma okruhu ExpressRoute

Přečtěte si [Nejčastější dotazy k ExpressRoute](expressroute-faqs.md) pro podporované možnosti šířky pásma pro vašeho poskytovatele. Můžete vybrat libovolnou velikost, která je větší než velikost stávajícího okruhu, pokud je fyzický port (na kterém je vytvořen okruh) povoleno.

> [!IMPORTANT]
> Je možné, že bude nutné znovu vytvořit okruh ExpressRoute, pokud na stávajícím portu není dostatečná kapacita. Pokud v daném umístění není k dispozici žádná další kapacita, nemůžete upgradovat okruh.
>
> Nemůžete snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrading Bandwidth vyžaduje, abyste zrušili zřízení okruhu ExpressRoute a pak znovu zřídili nový okruh ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Změna velikosti okruhu

Až se rozhodnete, jakou velikost potřebujete, můžete změnit velikost okruhu pomocí následujícího příkazu:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Jakmile se váš okruh naplní na straně Microsoftu, musíte se obrátit na svého poskytovatele připojení a aktualizovat konfigurace na jejich straně tak, aby odpovídaly této změně. Fakturace začíná u aktualizované možnosti šířky pásma od tohoto okamžiku.

Pokud se při zvýšení šířky pásma okruhu zobrazí následující chyba, znamená to, že na fyzickém portu, na kterém je vytvořený stávající okruh, není dost dostupné šířky pásma. Je nutné odstranit tento okruh a vytvořit nový okruh velikosti, kterou potřebujete.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Zrušení zřízení a odstranění okruhu

### <a name="considerations"></a>Požadavky

* Aby tato operace proběhla úspěšně, je nutné odpojit všechny virtuální sítě od okruhu ExpressRoute. Zkontrolujte, zda máte žádné virtuální sítě, které jsou propojeny se okruhem, pokud tato operace není úspěšná.
* Pokud je stav zřizování poskytovatele služby okruhu ExpressRoute **zřizování** nebo **zřízené** , musíte s vaším poskytovatelem služeb spolupracovat a zrušit zřízení okruhu na jejich straně. I nadále vyhradíme prostředky a účtujeme vám, dokud poskytovatel služeb nedokončí zrušení zřízení okruhu a pošle nám upozornění.
* Pokud poskytovatel služby zrušil zřízení okruhu (stav zřizování poskytovatele služeb je nastavený na **není**zřízený), můžete okruh odstranit. Tím se zastaví účtování okruhu.

#### <a name="delete-a-circuit"></a>Odstranit okruh

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
