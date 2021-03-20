---
title: 'Rychlý Start: vytvoření a úprava okruhu pomocí ExpressRoute-Azure PowerShell'
description: V tomto rychlém startu se dozvíte, jak vytvořit, zřídit, ověřit, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 5397dd2745a0d4e61804cf631014846ae15ec4e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91971536"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Rychlý Start: vytvoření a úprava okruhu ExpressRoute pomocí Azure PowerShell

V tomto rychlém startu se dozvíte, jak vytvořit okruh ExpressRoute pomocí rutin prostředí PowerShell a modelu nasazení Azure Resource Manager. Můžete také kontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.

## <a name="prerequisites"></a>Předpoklady

* Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .
* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Získat seznam podporovaných zprostředkovatelů, umístění a šířky pásma
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

### <a name="create-an-expressroute-circuit"></a>Vytvoření okruhu ExpressRoute
Pokud ještě nemáte skupinu prostředků, musíte ji před vytvořením okruhu ExpressRoute vytvořit. Můžete to udělat spuštěním následujícího příkazu:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Následující příklad ukazuje, jak vytvořit okruh ExpressRoute 200 MB/s až Equinix v karbidu sedlu. Pokud používáte jiného poskytovatele a různá nastavení, nahraďte tyto informace při vytváření žádosti. Pro vyžádání nového klíče služby použijte následující příklad:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Ujistěte se, že zadáte správnou úroveň SKU a řadu SKU:

* Úroveň SKU určuje, jestli je okruh ExpressRoute [místní](expressroute-faqs.md#expressroute-local), Standard nebo [Premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *místní*, * Standard nebo *Premium*. SKU nemůžete změnit z *úrovně Standard/Premium* na *místní*.
* Rodina SKU určuje typ fakturace. Můžete zadat *MeteredData* pro měřený datový tarif a *UnlimitedData* pro neomezený datový tarif. Typ fakturace můžete změnit z *MeteredData* na *UnlimitedData*, ale nemůžete změnit typ z *UnlimitedData* na *MeteredData*. *Místní* okruh je vždy *UnlimitedData*.

> [!IMPORTANT]
> Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. Ujistěte se, že tuto operaci provedete, když je poskytovatel připojení připravený k zřízení okruhu.
>

Odpověď obsahuje klíč služby. Podrobnější popis všech parametrů získáte spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Vypsat všechny okruhy ExpressRoute
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

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Poslat klíč služby poskytovateli připojení pro zřizování
*ServiceProviderProvisioningState* poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stav poskytuje stav na straně Microsoftu. Další informace o stavech zřizování okruhů najdete v tématu [pracovní postupy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Při vytváření nového okruhu ExpressRoute je okruh v následujícím stavu:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Když poskytovatel připojení v tuto chvíli povoluje poskytovatel připojení, změní se na následující stav:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Chcete-li použít okruh ExpressRoute, musí být v následujícím stavu:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Pravidelně kontrolovat stav a stav klíče okruhu
Kontrola stavu a stav klíče služby vám poskytne informace o tom, kdy poskytovatel zřídí váš okruh. Po nakonfigurování okruhu se *ServiceProviderProvisioningState* zobrazí jako *zřízené*, jak je znázorněno v následujícím příkladu:

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

### <a name="create-your-routing-configuration"></a>Vytvoření konfigurace směrování
Podrobné pokyny najdete v článku věnovaném [konfiguraci směrování okruhu ExpressRoute](expressroute-howto-routing-arm.md) k vytváření a úpravám partnerských vztahů okruhů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, kteří nabízejí služby pro připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Propojení virtuální sítě k okruhu ExpressRoute
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

* Povolí nebo zakáže doplněk ExpressRoute Premium pro okruh ExpressRoute. Změna SKU z *úrovně Standard/Premium* na *místní* není podporovaná.
* Zvyšte šířku pásma okruhu ExpressRoute, na kterém je dostupná kapacita na portu. Downgrade šířky pásma okruhu není podporována.
* Změňte plán měření z měřených dat na neomezená data. Změna plánu měření z neomezených dat na měřená data není podporovaná.
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

Všimněte si následujících informací:

* Než začnete downgradovat z úrovně Premium na standard, je nutné zajistit, aby počet virtuálních sítí, které jsou propojeny s okruhem, byl menší než 10. Pokud to neuděláte, váš požadavek na aktualizaci se nezdařil a účtujeme vám sazby za Premium.
* Všechny virtuální sítě v jiných geopolitických oblastech musí být nejprve odpojování. Pokud odkaz neodeberete, vaše žádost o aktualizaci nebude úspěšná a budeme vám účtovat poplatky za Premium.
* Směrovací tabulka musí mít méně než 4 000 tras pro privátní partnerské vztahy. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace protokolu BGP se vynechá. Relace protokolu BGP nebude znovu povolena, dokud nebude počet inzerovaných předpon pod 4 000.

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

Váš okruh bude upgradován na straně Microsoftu. Pak musíte kontaktovat poskytovatele připojení a aktualizovat konfigurace na jejich straně tak, aby odpovídaly této změně. Po vytvoření tohoto oznámení začneme účtovat na aktualizovanou možnost šířky pásma.

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

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení okruhu ExpressRoute
Všimněte si následujících informací:

* Všechny virtuální sítě musí být z okruhu ExpressRoute odpojování. Pokud tato operace neproběhne úspěšně, zkontrolujte, zda jsou k okruhu připojeny nějaké virtuální sítě.
* Pokud je stav zřizování poskytovatele služby okruhu ExpressRoute **zřizování** nebo **zřízené** , musíte s vaším poskytovatelem služeb spolupracovat a zrušit zřízení okruhu na jejich straně. I nadále vyhradíme prostředky a účtujeme vám, dokud poskytovatel služeb nedokončí zrušení zřízení okruhu a pošle nám upozornění.
* Pokud poskytovatel služby zrušil zřízení okruhu, což znamená, že stav zřizování poskytovatele služeb se nastaví na **není zřízené**, můžete okruh odstranit. Faktura za okruh se pak zastaví.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu a jeho zřízení s vaším poskytovatelem pokračujte k dalšímu kroku konfigurace partnerského vztahu:

> [!div class="nextstepaction"]
> [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md)
