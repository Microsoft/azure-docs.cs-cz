---
title: 'Azure ExpressRoute: Úprava okruhu: PowerShell:classic'
description: Tento článek vás provede kroky ke kontrole stavu, aktualizace nebo odstranění a zrušení zřízení okruhu modelu klasického nasazení ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931966"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Úprava okruhu ExpressRoute pomocí prostředí PowerShell (klasický)

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vás provede kroky ke kontrole stavu, aktualizace nebo odstranění a zrušení zřízení okruhu modelu klasického nasazení ExpressRoute. Tento článek se týká modelu nasazení Classic.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**O modelech nasazení Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Získání stavu obvodu

Tyto informace můžete kdykoli načíst `Get-AzureCircuit` pomocí rutiny. Volání bez parametrů obsahuje seznam všech obvodů.

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

Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkladu:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Úprava okruhu

Můžete upravit určité vlastnosti okruhu ExpressRoute bez ovlivnění připojení.

Bez prostoje můžete provést následující úkoly:

* Povolte nebo zakažte doplněk premium ExpressRoute pro okruh ExpressRoute.
* Zvyšte šířku pásma okruhu ExpressRoute za předpokladu, že je na portu k dispozici kapacita. Snížení šířky pásma okruhu není podporováno.
* Změňte plán měření z měřených dat na neomezená data. Změna plánu měření z neomezených dat na data účtovaná nejsou podporována.
* Můžete povolit a zakázat *povolit klasické operace*.

Další informace o limitech a omezeních najdete v [nejčastějších dotazech](expressroute-faqs.md) k expresní trase.

### <a name="enable-the-expressroute-premium-add-on"></a>Povolení doplňku Premium ExpressRoute

Doplněk Premium ExpressRoute pro váš stávající okruh můžete povolit pomocí následující rutiny prostředí PowerShell:

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

Váš okruh bude mít nyní povolené prémiové doplňkové funkce ExpressRoute. Jakmile byl příkaz úspěšně spuštěn, začne fakturace pro funkci doplňku premium.

### <a name="disable-the-expressroute-premium-add-on"></a>Zakázání doplňku premium ExpressRoute

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh.
>
>

#### <a name="considerations"></a>Požadavky

* Ujistěte se, že počet virtuálních sítí propojených s okruhem je menší než 10, než přejdete z prémiového na standardní. Pokud tak neučiníte, váš požadavek na aktualizaci se nezdaří a budou vám účtovány prémiové sazby.
* Je nutné odpojit všechny virtuální sítě v jiných geopolitických oblastech. Pokud tak neučiníte, váš požadavek na aktualizaci se nezdaří a budou vám účtovány prémiové sazby.
* Trasový tabulkový spojbyl menší než 4 000 tras pro soukromý partnerský vztah. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace Protokolu BGP klesne a nebude znovu povolena, dokud počet inzerovaných předpon klesne pod 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Zakázání doplňku premium

Doplněk Premium ExpressRoute pro stávající okruh můžete zakázat pomocí následující rutiny prostředí PowerShell:

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

V [nejčastějších dotazech k expresní trase](expressroute-faqs.md) naleznete podporované možnosti šířky pásma pro vašeho poskytovatele. Můžete vybrat libovolnou velikost, která je větší než velikost existujícího obvodu, pokud to umožňuje fyzický port (na kterém je vytvořen okruh).

> [!IMPORTANT]
> Bude pravděpodobně muset znovu vytvořit okruh ExpressRoute, pokud je nedostatečná kapacita na existující port. Okruh nelze upgradovat, pokud v tomto umístění není k dispozici žádná další kapacita.
>
> Šířku pásma okruhu ExpressRoute nelze snížit bez přerušení. Snížení šířky pásma vyžaduje zrušení zřízení okruhu ExpressRoute a následné opětovné zřízení nového okruhu ExpressRoute.
>
>

#### <a name="resize-a-circuit"></a>Změna velikosti obvodu

Po rozhodnutí, jakou velikost potřebujete, můžete ke změně velikosti okruhu použít následující příkaz:

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

Jakmile je velikost okruhu na straně společnosti Microsoft, musíte kontaktovat poskytovatele připojení a aktualizovat konfigurace na jejich straně tak, aby odpovídaly této změně. Fakturace začíná pro možnost aktualizované šířky pásma od tohoto okamžiku.

Pokud se při zvyšování šířky pásma okruhu zobrazí následující chyba, znamená to, že na fyzickém portu, kde je vytvořen existující okruh, nezbývá dostatečná šířka pásma. Je nutné odstranit tento okruh a vytvořit nový okruh velikosti, kterou potřebujete.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Zrušení zřízení a odstranění obvodu

### <a name="considerations"></a>Požadavky

* Chcete-li, aby tato operace byla úspěšná, je nutné odpojit všechny virtuální sítě od okruhu ExpressRoute. Zkontrolujte, zda máte nějaké virtuální sítě, které jsou propojeny s okruhem, pokud se tato operace nezdaří.
* Pokud je stav zřizování zprostředkovatele okruhů ExpressRoute **Provisioning** nebo **Provisioned,** musíte spolupracovat s poskytovatelem služeb, abyste zrušili zřízení okruhu na jejich straně. Budeme i nadále rezervovat zdroje a účtovat vám, dokud poskytovatel služeb dokončí zrušení zřízení okruhu a upozorní nás.
* Pokud poskytovatel služeb zrušil okruh (stav zřizování poskytovatele služeb je nastaven na **Není zřízeno**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

#### <a name="delete-a-circuit"></a>Odstranění obvodu

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
