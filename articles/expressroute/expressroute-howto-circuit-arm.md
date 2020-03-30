---
title: 'Azure ExpressRoute: Úprava okruhu: PowerShell'
description: Vytvořte, zřiďte, ověřte, aktualizujte, odstraňte a zrušte zřízení okruhu ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/08/2020
ms.author: cherylmc
ms.openlocfilehash: ab44d5358154c17622eef68205ac2326e81377a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770964"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Vytvoření a úprava okruhu ExpressRoute pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vám pomůže vytvořit okruh ExpressRoute pomocí rutin Prostředí PowerShell a modelu nasazení Azure Resource Manager. Můžete také zkontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.

## <a name="before-you-begin"></a>Než začnete

Než začnete, zkontrolujte před zahájením konfigurace [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se ke svému účtu Azure a vyberte předplatné

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získejte seznam podporovaných poskytovatelů, umístění a šířky pásma
Před vytvořením okruhu ExpressRoute potřebujete seznam podporovaných poskytovatelů připojení, umístění a možností šířky pásma.

Rutina prostředí PowerShell **Get-AzExpressRouteServiceProvider** vrátí tyto informace, které použijete v pozdějších krocích:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Zkontrolujte, zda je zde uveden váš poskytovatel připojení. Poznamenejte si následující informace, které budete potřebovat později při vytváření okruhu:

* Name (Název)
* PeeringUmístění
* Nabízené šířky pásma

Nyní jste připraveni vytvořit okruh ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Vytvoření okruhu ExpressRoute
Pokud ještě nemáte skupinu prostředků, musíte ji vytvořit před vytvořením okruhu ExpressRoute. Můžete tak učinit spuštěním následujícího příkazu:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Následující příklad ukazuje, jak vytvořit okruh ExpressRoute o rychlosti 200 Mb/s prostřednictvím equinixu v Silicon Valley. Pokud používáte jiného poskytovatele a různá nastavení, nahraďte tyto informace při žádosti. Pomocí následujícího příkladu můžete požádat o nový klíč služby:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Ujistěte se, že jste určili správnou úroveň skladové položky a rodinu skladových položk:

* Úroveň Skladové položky určuje, zda je okruh ExpressRoute [místní](expressroute-faqs.md#expressroute-local), standardní nebo [premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *místní*, *standardní* nebo *premium*.
* Skladová položka určuje typ fakturace. Můžete zadat *Metereddata* pro datový tarif účtovaný účtárna a *Unlimiteddata* pro neomezený datový tarif. Typ fakturace můžete změnit z *Metereddata* na *Unlimiteddata*, ale nemůžete změnit typ z *Unlimiteddata* na *Metereddata*. Místní *Local* okruh je vždy *Unlimiteddata*.

> [!IMPORTANT]
> Okruh ExpressRoute se účtuje od okamžiku, kdy je vydán servisní klíč. Ujistěte se, že provedete tuto operaci, když je poskytovatel připojení připraven zřídit okruh.
>
>

Odpověď obsahuje klíč služby. Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Seznam všech okruhů ExpressRoute
Chcete-li získat seznam všech okruhů ExpressRoute, které jste vytvořili, spusťte příkaz **Get-AzExpressRouteCircuit:**

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpověď vypadá podobně jako v následujícím příkladu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Tyto informace můžete kdykoli načíst `Get-AzExpressRouteCircuit` pomocí rutiny. Volání bez parametrů obsahuje seznam všech obvodů. Klíč servisu je uveden v poli *ServiceKey:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Odpověď vypadá podobně jako v následujícím příkladu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Odešlete klíč služby svému poskytovateli připojení pro zřizování
*ServiceProviderProvisioningState* poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stav poskytuje stav na straně společnosti Microsoft. Další informace o stavech zřizování okruhů naleznete [v tématu Workflows](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Když vytvoříte nový okruh ExpressRoute, okruh je v následujícím stavu:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Okruh se změní na následující stav, když je poskytovatel připojení v procesu povolení pro vás:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Abyste mohli použít okruh ExpressRoute, musí být v následujícím stavu:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Pravidelně kontrolujte stav a stav klíče obvodu
Kontrola stavu a stavu klíče obvodu vás dozví, kdy váš poskytovatel povolil okruh. Po konfiguraci okruhu *ServiceProviderProvisioningState* se zobrazí jako zřízená , jak je *znázorněno*v následujícím příkladu:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpověď vypadá podobně jako v následujícím příkladu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Vytvoření konfigurace směrování
Podrobné pokyny naleznete v článku konfigurace [směrování okruhů ExpressRoute](expressroute-howto-routing-arm.md) a vytvoření a úprava partnerských stran okruhů.

> [!IMPORTANT]
> Tyto pokyny platí pouze pro okruhy, které jsou vytvořeny s poskytovateli služeb, které nabízejí služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí služby spravované vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení konfiguruje a spravuje směrování za vás.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Propojení virtuální sítě s okruhem ExpressRoute
Dále propojte virtuální síť s okruhem ExpressRoute. Při práci s modelem nasazení Správce prostředků použijte článek [Propojení virtuálních sítí s okruhy ExpressRoute.](expressroute-howto-linkvnet-arm.md)

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Získání stavu okruhu ExpressRoute
Tyto informace můžete kdykoli načíst pomocí rutiny **Get-AzExpressRouteCircuit.** Volání bez parametrů obsahuje seznam všech obvodů.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Odpověď bude podobná jako v následujícím příkladu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Informace o konkrétním okruhu ExpressRoute můžete získat předáním názvu skupiny prostředků a názvu okruhu jako parametru volání:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpověď vypadá podobně jako v následujícím příkladu:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Podrobné popisy všech parametrů můžete získat spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Úprava okruhu ExpressRoute
Můžete upravit určité vlastnosti okruhu ExpressRoute bez ovlivnění připojení.

Bez prostoje můžete provést následující úkoly:

* Povolte nebo zakažte doplněk premium ExpressRoute pro okruh ExpressRoute.
* Zvyšte šířku pásma okruhu ExpressRoute za předpokladu, že je na portu k dispozici kapacita. Snížení šířky pásma okruhu není podporováno.
* Změňte plán měření z měřených dat na neomezená data. Změna plánu měření z neomezených dat na data účtovaná nejsou podporována.
* Můžete povolit a zakázat *povolit klasické operace*.

Další informace o omezeních a omezeních naleznete v [nejčastějších dotazech k expresní trase](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Povolení doplňku ExpressRoute premium
Doplněk Premium ExpressRoute pro váš stávající okruh můžete povolit pomocí následujícího fragmentu prostředí PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Okruh má nyní povolené prémiové doplňkové funkce ExpressRoute. Jakmile příkaz úspěšně spustíte, začneme vám účtovat prémiovou doplňkovou funkci.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Zakázání doplňku premium ExpressRoute
> [!IMPORTANT]
> Pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh, tato operace může selhat.
>
>

Všimněte si následujících informací:

* Před přechodem z prémiového na standardní, musíte zajistit, že počet virtuálních sítí, které jsou propojeny s okruhem je menší než 10. Pokud tak neučiníte, vaše žádost o aktualizaci se nezdaří a my vám účtujeme prémiové sazby.
* Je nutné odpojit všechny virtuální sítě v jiných geopolitických oblastech. Pokud tak neučiníte, vaše žádost o aktualizaci se nezdaří a my vám účtujeme prémiové sazby.
* Trasový tabulkový spojbyl menší než 4 000 tras pro soukromý partnerský vztah. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace Protokolu BGP klesne a nebude znovu povolena, dokud počet inzerovaných předpon klesne pod 4 000.

Doplněk Premium ExpressRoute pro existující okruh můžete zakázat pomocí následující rutiny prostředí PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aktualizace šířky pásma okruhu ExpressRoute
Pokud hledáte podporované možnosti šířky pásma pro vašeho poskytovatele, podívejte se na [nejčastější dotazy k ExpressRoute](expressroute-faqs.md). Můžete vybrat libovolnou velikost větší, než je velikost stávajícího obvodu.

> [!IMPORTANT]
> Bude pravděpodobně muset znovu vytvořit okruh ExpressRoute, pokud je nedostatečná kapacita na existující port. Okruh nelze upgradovat, pokud v tomto umístění není k dispozici žádná další kapacita.
>
> Šířku pásma okruhu ExpressRoute nelze snížit bez přerušení. Snížení šířky pásma vyžaduje zrušení zřízení okruhu ExpressRoute a následné opětovné zřízení nového okruhu ExpressRoute.
>

Po rozhodnutí, jakou velikost potřebujete, použijte následující příkaz pro změna velikosti okruhu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Velikost okruhu bude na straně Microsoftu. Potom musíte kontaktovat svého poskytovatele připojení aktualizovat konfigurace na jejich straně, aby odpovídaly této změně. Po oznámení vám začneme účtovat aktualizovanou možnost šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Přesunutí skladové položky z měřené na neomezenou
Skladovou položku okruhu ExpressRoute můžete změnit pomocí následujícího fragmentu prostředí PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Řízení přístupu ke klasickému prostředí a prostředí Správce prostředků
Projděte si pokyny v [okruhu Move ExpressRoute z klasického modelu nasazení Resource Manageru](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute
Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud se tato operace nezdaří, zkontrolujte, zda jsou nějaké virtuální sítě propojeny s okruhem.
* Pokud je stav zřizování zprostředkovatele okruhů ExpressRoute **Provisioning** nebo **Provisioned,** musíte spolupracovat s poskytovatelem služeb, abyste zrušili zřízení okruhu na jejich straně. Budeme i nadále rezervovat zdroje a účtovat vám, dokud poskytovatel služeb dokončí zrušení zřízení okruhu a upozorní nás.
* Pokud poskytovatel služeb zrušil okruh (stav zřizování poskytovatele služeb je nastaven na **Není zřízeno**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu se ujistěte, že provedete následující kroky:

* [Vytvoření a úprava směrování okruhu ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
