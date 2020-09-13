---
title: 'Azure ExpressRoute: Úprava okruhu: PowerShell'
description: Vytvořit, zřídit, ověřit, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/08/2020
ms.author: duau
ms.openlocfilehash: e9bf9dbe0f4146101513ab9786b298ac6b43b6a3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566293"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Vytvoření a úprava okruhu ExpressRoute pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vám pomůže vytvořit okruh ExpressRoute pomocí rutin prostředí PowerShell a modelu nasazení Azure Resource Manager. Můžete také kontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.

## <a name="before-you-begin"></a>Než začnete

Než začnete, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) před zahájením konfigurace.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se ke svému účtu Azure a vyberte své předplatné.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získejte seznam podporovaných zprostředkovatelů, umístění a šířky pásma.
Před vytvořením okruhu ExpressRoute potřebujete seznam podporovaných zprostředkovatelů připojení, umístění a možností šířky pásma.

Rutina PowerShellu **Get-AzExpressRouteServiceProvider** vrátí tyto informace, které použijete v pozdějších krocích:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Zkontrolujte, jestli je v seznamu uveden poskytovatel připojení. Poznamenejte si následující informace, které později budete potřebovat při vytváření okruhu:

* Name
* PeeringLocations
* BandwidthsOffered

Nyní jste připraveni vytvořit okruh ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. vytvoření okruhu ExpressRoute
Pokud ještě nemáte skupinu prostředků, musíte ji před vytvořením okruhu ExpressRoute vytvořit. Můžete to udělat spuštěním následujícího příkazu:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Následující příklad ukazuje, jak vytvořit okruh ExpressRoute 200 MB/s až Equinix v karbidu sedlu. Pokud používáte jiného poskytovatele a různá nastavení, nahraďte tyto informace při vytváření žádosti. Pro vyžádání nového klíče služby použijte následující příklad:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Ujistěte se, že zadáte správnou úroveň SKU a řadu SKU:

* Úroveň SKU určuje, jestli okruh ExpressRoute je [místní](expressroute-faqs.md#expressroute-local), Standard nebo [Premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *Local*, *Standard* nebo *Premium*. SKU nemůžete změnit z *úrovně Standard/Premium* na *místní*.
* Rodina SKU určuje typ fakturace. Můžete zadat *Metereddata* pro měřený datový tarif a *Unlimiteddata* pro neomezený datový tarif. Typ fakturace můžete změnit z *Metereddata* na *Unlimiteddata*, ale nemůžete změnit typ z *Unlimiteddata* na *Metereddata*. *Místní* okruh je vždy *Unlimiteddata*.

> [!IMPORTANT]
> Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. Ujistěte se, že tuto operaci provedete, když je poskytovatel připojení připravený k zřízení okruhu.
>
>

Odpověď obsahuje klíč služby. Podrobnější popis všech parametrů získáte spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. vypíše všechny okruhy ExpressRoute.
Pokud chcete získat seznam všech okruhů ExpressRoute, které jste vytvořili, spusťte příkaz **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpověď vypadá podobně jako v následujícím příkladu:

```azurepowershell
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
```

Tyto informace můžete získat kdykoli pomocí `Get-AzExpressRouteCircuit` rutiny. Volání bez parametrů vypíše všechny okruhy. Váš klíč služby je uvedený v poli *serviceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Odpověď vypadá podobně jako v následujícím příkladu:

```azurepowershell
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
```


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. zaslat klíč služby poskytovateli připojení pro zřizování
*ServiceProviderProvisioningState* poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stav poskytuje stav na straně Microsoftu. Další informace o stavech zřizování okruhů najdete v tématu [pracovní postupy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Při vytváření nového okruhu ExpressRoute je okruh v následujícím stavu:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```



Když poskytovatel připojení v procesu zapíná za vás, změní se okruh na následující stav:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Aby bylo možné používat okruh ExpressRoute, musí být v následujícím stavu:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. pravidelně kontroluje stav a stav klíče okruhu.
Kontrola stavu a stavu klíče okruhu vám umožní zjistit, jestli váš poskytovatel povolil váš okruh. Po nakonfigurování okruhu se *ServiceProviderProvisioningState* zobrazí jako *zřízené*, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpověď vypadá podobně jako v následujícím příkladu:

```azurepowershell
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
```

### <a name="7-create-your-routing-configuration"></a>7. Vytvořte konfiguraci směrování.
Podrobné pokyny najdete v článku věnovaném [konfiguraci směrování okruhu ExpressRoute](expressroute-howto-routing-arm.md) k vytváření a úpravám partnerských vztahů okruhů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, kteří nabízejí služby pro připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. propojení virtuální sítě k okruhu ExpressRoute
Potom propojte virtuální síť se svým okruhem ExpressRoute. Při práci s modelem nasazení Správce prostředků použijte [propojení virtuálních sítí s okruhy ExpressRoute](expressroute-howto-linkvnet-arm.md) .

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Získání stavu okruhu ExpressRoute
Tyto informace můžete získat kdykoli pomocí rutiny **Get-AzExpressRouteCircuit** . Volání bez parametrů vypíše všechny okruhy.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Odpověď bude podobná jako v následujícím příkladu:

```azurepowershell
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
```


Informace o konkrétním okruhu ExpressRoute můžete získat předáním názvu skupiny prostředků a názvu okruhu jako parametru volání:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpověď vypadá podobně jako v následujícím příkladu:

```azurepowershell
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
```


Podrobnější popis všech parametrů získáte spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Úprava okruhu ExpressRoute
Můžete upravit některé vlastnosti okruhu ExpressRoute, aniž by to mělo vliv na připojení.

Můžete provádět následující úlohy bez výpadku:

* Povolí nebo zakáže doplněk ExpressRoute Premium pro okruh ExpressRoute. Změna SKU z *úrovně Standard/Premium* na *místní* není podporována.
* Zvyšte šířku pásma okruhu ExpressRoute, pokud je na portu k dispozici dostatek kapacity. Downgrade šířky pásma okruhu není podporována.
* Změňte plán měření z měřených dat na neomezená data. Změna plánu měření z neomezených dat na měřená data není podporována.
* Můžete povolit nebo zakázat *operace klasických operací*.

Další informace o omezeních a omezeních najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Povolení doplňku ExpressRoute Premium
Doplněk ExpressRoute Premium pro stávající okruh můžete povolit pomocí následujícího fragmentu kódu prostředí PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Okruh teď má povolené funkce doplňku ExpressRoute Premium. Až se příkaz úspěšně spustí, začneme vám účtovat možnost doplňku Premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Zakázání doplňku ExpressRoute Premium
> [!IMPORTANT]
> Pokud používáte prostředky, které jsou větší, než je povoleno pro standardní okruh, tato operace může selhat.
>
>

Všimněte si následujících informací:

* Než začnete downgradovat z úrovně Premium na standard, je nutné zajistit, aby počet virtuálních sítí, které jsou propojeny s okruhem, byl menší než 10. Pokud to neuděláte, váš požadavek na aktualizaci se nezdařil a účtujeme vám sazby za Premium.
* Je nutné odpojit všechny virtuální sítě v jiných geopolitických oblastech. Pokud to neuděláte, váš požadavek na aktualizaci se nezdařil a účtujeme vám sazby za Premium.
* Směrovací tabulka musí mít méně než 4 000 tras pro privátní partnerské vztahy. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace protokolu BGP se uvolní a nebude znovu povolena, dokud počet inzerovaných předpon nepřekročí hodnotu 4 000.

Doplněk ExpressRoute Premium pro stávající okruh můžete zakázat pomocí následující rutiny prostředí PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aktualizace šířky pásma okruhu ExpressRoute
Možnosti podporované šířky pásma pro poskytovatele najdete na stránce s [nejčastějšími dotazy k ExpressRoute](expressroute-faqs.md). Můžete vybrat libovolnou velikost, která je větší než velikost stávajícího okruhu.

> [!IMPORTANT]
> Je možné, že bude nutné znovu vytvořit okruh ExpressRoute, pokud na stávajícím portu není dostatečná kapacita. Pokud v daném umístění není k dispozici žádná další kapacita, nemůžete upgradovat okruh.
>
> Nemůžete snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrading Bandwidth vyžaduje, abyste zrušili zřízení okruhu ExpressRoute a pak znovu zřídili nový okruh ExpressRoute.
>

Až se rozhodnete, jakou velikost potřebujete, změňte velikost okruhu pomocí následujícího příkazu:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Váš okruh se bude nacházet na straně Microsoftu. Pak musíte kontaktovat poskytovatele připojení a aktualizovat konfigurace na jejich straně tak, aby odpovídaly této změně. Po vytvoření tohoto oznámení začneme účtovat na aktualizovanou možnost šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Přesun SKU z měření na neomezený
SKU okruhu ExpressRoute můžete změnit pomocí následujícího fragmentu kódu prostředí PowerShell:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Řízení přístupu k prostředím Classic a Správce prostředků
Přečtěte si pokyny v tématu [Přesun okruhů ExpressRoute z modelu nasazení Classic na model nasazení Správce prostředků](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute
Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud tato operace neproběhne úspěšně, zkontrolujte, zda jsou k okruhu připojeny nějaké virtuální sítě.
* Pokud je stav zřizování poskytovatele služby okruhu ExpressRoute **zřizování** nebo **zřízené** , musíte s vaším poskytovatelem služeb spolupracovat a zrušit zřízení okruhu na jejich straně. I nadále vyhradíme prostředky a účtujeme vám, dokud poskytovatel služeb nedokončí zrušení zřízení okruhu a pošle nám upozornění.
* Pokud poskytovatel služby zrušil zřízení okruhu (stav zřizování poskytovatele služeb je nastavený na **není zřízený**), můžete okruh odstranit. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu se ujistěte, že provedete následující další kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](expressroute-howto-linkvnet-arm.md)
