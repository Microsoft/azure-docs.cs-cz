---
title: 'Vytvoření a úprava okruhu ExpressRoute – prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: Vytvoření, zřízení, ověřte, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: ff86c87690f5dd4a919929f0deef4af739cbe4d3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104998"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Vytvoření a úprava okruhu ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek vám pomůže vytvořit okruh ExpressRoute pomocí modelu nasazení Azure Resource Manageru a rutin prostředí PowerShell. Můžete také zkontrolovat stav, aktualizovat, odstranit nebo zrušit zřízení okruhu.

## <a name="before-you-begin"></a>Před zahájením

Než začnete, projděte si [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se ke svému účtu Azure a vyberte své předplatné
Můžete začít s vaší konfigurací, přihlaste se ke svému účtu Azure. Připojení vám usnadní použijte následující příklady:

Pokud používáte Azure Cloud shell, nemusíte spouštět Connect-AzureRmAccount, protože se automaticky připojí.

```azurepowershell
Connect-AzureRmAccount
```

Zkontrolujte předplatná pro tento účet:

```azurepowershell-interactive
Get-AzureRmSubscription
```

Vyberte předplatné, pro kterou chcete vytvořit okruh ExpressRoute pro:

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získání seznamu podporovaných zprostředkovatelů, umístění a šířky pásma
Než začnete vytvářet okruhu ExpressRoute, budete potřebovat seznam poskytovatelů podporovaných připojení, umístění a možnosti šířky pásma.

Rutiny Powershellu **Get-AzureRmExpressRouteServiceProvider** vrátí tyto informace, které budete používat v dalších krocích:

```azurepowershell-interactive
Get-AzureRmExpressRouteServiceProvider
```

Zaškrtněte, pokud chcete zobrazit, pokud poskytovatel připojení je tu. Poznamenejte si následující informace, které budete potřebovat později při vytvoření okruhu:

* Název
* PeeringLocations
* BandwidthsOffered

Nyní jste připraveni vytvořit okruh ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. Vytvoření okruhu ExpressRoute
Pokud ještě nemáte skupinu prostředků, můžete musí vytvořit před vytvořením váš okruh ExpressRoute. Je to tak, že spustíte následující příkaz:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


Následující příklad ukazuje, jak vytvořit okruh ExpressRoute prostřednictvím Equinix 200 MB/s v Silicon Valley. Pokud používáte jiného poskytovatele a jiné nastavení, dosaďte tyto informace při podat žádost. Požádat o nový klíč služby použijte následující příklad:

```azurepowershell-interactive
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Ujistěte se, že jste zadali správnou úroveň skladové položky a skladová položka rodina:

* Úroveň skladové položky určuje, zda je povolen ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat *standardní* získat standardní SKU nebo *Premium* pořídit si doplněk premium.
* Skladová položka rodina Určuje typ fakturace. Můžete zadat *Metereddata* pro tarif podle objemu dat a *Unlimiteddata* pro tarif s neomezenými daty. Můžete změnit typ fakturační z *Metereddata* k *Unlimiteddata*, ale nemůže změnit typ z *Unlimiteddata* k *Metereddata*.

> [!IMPORTANT]
> Váš okruh ExpressRoute se účtují od okamžiku, kdy vydáním klíče služby. Ujistěte se provést tuto operaci, pokud poskytovatel připojení je připraveno ke zřízení okruhu.
> 
> 

Odpověď obsahuje klíč služby. Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Vypsat všechny okruhy ExpressRoute
Pokud chcete získat seznam všechny okruhy ExpressRoute, které jste vytvořili, spusťte **Get-AzureRmExpressRouteCircuit** příkaz:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Odpověď bude vypadat podobně jako v následujícím příkladu:

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

Tyto informace kdykoli můžete načíst pomocí `Get-AzureRmExpressRouteCircuit` rutiny. Volání bez parametrů jsou uvedeny všechny okruhy. Klíč služby je uveden v *klíč ServiceKey* pole:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit
```


Odpověď bude vypadat podobně jako v následujícím příkladu:

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


Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Poslat klíči služby svého poskytovatele připojení pro zřizování
*Serviceproviderprovisioningstate vzájemného propojení* poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stavu poskytuje stav na straně Microsoftu. Další informace o zřizování stavy okruhu najdete v tématu [pracovních postupů](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Při vytváření nového okruhu ExpressRoute, je okruh v následujícím stavu:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Když probíhá proces jeho povolení pro vás poskytovatel připojení okruhu změní na následující stav:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Abyste mohli použít okruhu ExpressRoute musí být v následujícím stavu:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Pravidelně kontrolovat stav a stav okruhu klávesy
Kontroluje se stav a stav okruhu klíč vám umožňuje vědět, kdy váš poskytovatel povolil váš okruh. Po dokončení konfigurace okruh, *serviceproviderprovisioningstate vzájemného propojení* se zobrazí jako *zřízená*, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpověď bude vypadat podobně jako v následujícím příkladu:

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

### <a name="7-create-your-routing-configuration"></a>7. Vytvořte vlastní konfiguraci směrování
Podrobné pokyny najdete v tématu [konfigurace směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md) článek k vytvoření a úprava okruhu partnerských vztahů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, které nabízejí vrstvy 2 připojení služby. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IP sítě VPN, např. MPLS), svého poskytovatele připojení, konfiguruje a spravuje směrování za vás.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Propojení virtuální sítě k okruhu ExpressRoute
V dalším kroku propojení virtuální sítě pro váš okruh ExpressRoute. Použití [propojení virtuálních sítí s okruhy ExpressRoute](expressroute-howto-linkvnet-arm.md) článek při práci s modelem nasazení Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Při získávání stavu okruhu ExpressRoute
Tyto informace kdykoli můžete načíst pomocí **Get-AzureRmExpressRouteCircuit** rutiny. Volání bez parametrů jsou uvedeny všechny okruhy.

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit
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


Název skupiny prostředků a názvu okruhu předá jako parametr volání, můžete získat informace o konkrétní okruh ExpressRoute:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Odpověď bude vypadat podobně jako v následujícím příkladu:

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


Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Úprava okruhu ExpressRoute
Můžete upravit některé vlastnosti okruhu ExpressRoute bez dopadu na připojení.

Můžete provádět následující úlohy došlo k výpadku:

* Povolit nebo zakázat doplněk ExpressRoute premium pro váš okruh ExpressRoute.
* Zvětšete šířku pásma váš okruh ExpressRoute zadaný na portu je k dispozici kapacita. Šířku pásma okruhu downgradu není podporován. 
* Změňte plán monitorování míry využití měření podle objemu dat na neomezená Data. Změna plánu měření z neomezená Data na měření podle objemu dat se nepodporuje.
* Můžete povolit nebo zakázat *povolit klasické operace*.

Další informace o omezení a omezení, najdete v článku [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Chcete-li povolit doplněk ExpressRoute premium
Doplněk ExpressRoute premium pro existující okruh můžete povolit pomocí následujícího fragmentu kódu Powershellu:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Okruh teď má funkce doplněk ExpressRoute premium povolené. Začneme, jakmile příkaz úspěšně spuštěn fakturace pro funkci doplněk premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Chcete-li zakázat doplněk ExpressRoute premium
> [!IMPORTANT]
> Pokud používáte prostředky, které jsou větší než co je povolený pro standardní okruh, tato operace může selhat.
> 
> 

Všimněte si následujících informací:

* Předtím, než spustíte downgrade z úrovně premium na úroveň standard, ujistěte se, že počet virtuálních sítí, které jsou propojené s okruhem je menší než 10. Pokud to neuděláte, selže požadavek na aktualizaci a budeme vám fakturovat sazby úrovně premium.
* Je nutné zrušit všechny virtuální sítě v dalších geopolitických oblastí. Pokud to neuděláte, selže požadavek na aktualizaci a budeme vám fakturovat sazby úrovně premium.
* Směrovací tabulky musí být menší než 4 000 tras pro soukromý partnerský vztah. Pokud se velikost vašeho směrovací tabulku je větší než 4 000 tras, relace protokolu BGP zahodí a nebude možné opětovně povolena dokud počet předpon inzerovaných klesne pod 4000.

Doplněk ExpressRoute premium pro existující okruh můžete zakázat pomocí následující rutiny Powershellu:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Chcete-li aktualizovat šířku pásma okruhu ExpressRoute
Pro možnosti podporované šířky pásma pro vašeho poskytovatele, zkontrolujte, [ExpressRoute – nejčastější dotazy](expressroute-faqs.md). Můžete si vybrat libovolné velikosti větší než velikost existující okruh.

> [!IMPORTANT]
> Bude pravděpodobně nutné znovu vytvořit okruh ExpressRoute, pokud je nedostatečné kapacity na existující port. Pokud v tomto umístění není k dispozici žádné další kapacitu, nemůže upgradovat okruh.
>
> Nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrade šířky pásma je potřeba zrušit zřízení okruhu ExpressRoute a pak znova nezajistíte nového okruhu ExpressRoute.
> 

Až se rozhodnete, jaké velikosti budete potřebovat, použijte následující příkaz pro změnu velikosti okruhů:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Váš okruh bude mít velikost na straně Microsoftu. Potom musí kontaktovat svého poskytovatele připojení aktualizovat konfiguraci na své straně tak, aby odpovídaly tuto změnu. Když provedete toto oznámení, začneme fakturace je pro možnost aktualizované šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Přesunout SKU z měřeného na neomezený počet
SKU okruhu ExpressRoute můžete změnit pomocí následujícího fragmentu kódu Powershellu:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Řízení přístupu k classic a Resource Manageru prostředí
Přečtěte si pokyny v [okruhy ExpressRoute přesunout z klasického modelu nasazení Resource Manageru](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute
Všimněte si následujících informací:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud se tato operace se nezdaří, zkontrolujte, pokud jsou propojeny žádné virtuální sítě k okruhu.
* Pokud je stav zřizování poskytovatele služeb okruh ExpressRoute **zřizování** nebo **zřízená** , musíte pracovat se svým poskytovatelem služeb zrušit zřízení okruhu na své straně. Pokračujeme v rezervovat prostředky a účtovat až do dokončení zrušení zřízení okruhu a informuje nás poskytovatelem služeb.
* Pokud poskytovatel služeb okruh zruší (poskytovatel služeb Stav zřizování je nastavena na **nezřízeno**), můžete odstranit okruh. Tím se zastaví účtování okruhu.

Váš okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Další postup

Po vytvoření váš okruh, ujistěte se, že provedete následující další kroky:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě pro váš okruh ExpressRoute](expressroute-howto-linkvnet-arm.md)
