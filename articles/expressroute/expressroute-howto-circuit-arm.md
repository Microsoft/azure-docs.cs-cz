---
title: 'Vytvoření a úprava okruhu ExpressRoute: prostředí PowerShell: Azure Resource Manager | Microsoft Docs'
description: Tento článek popisuje, jak vytvořit, zřízení, ověřte, aktualizovat, odstranit a zrušit jejich zřízení okruhu ExpressRoute.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f997182e-9b25-4a7a-b079-b004221dadcc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 0d45e97cc42826375a99df16a73c9a7b0c359224
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31594033"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Vytvoření a úprava okruhu ExpressRoute pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

Tento článek popisuje postup vytvoření okruhu Azure ExpressRoute pomocí rutin prostředí PowerShell a modelu nasazení Azure Resource Manager. Tento článek také ukazuje, jak zkontrolovat stav okruhu, aktualizovat, nebo odstranit a zrušit jejich zřízení ho.

## <a name="before-you-begin"></a>Než začnete
* Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace najdete v tématu [Přehled prostředí Azure PowerShell](/powershell/azure/overview).
* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.


## <a name="create"></a>Vytvořit a zřídit okruhu ExpressRoute
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se k účtu Azure a vybrat své předplatné
Chcete-li zahájit konfiguraci, přihlaste se k účtu Azure. Pomůže vám pomůže s připojením v následujících příkladech:

```powershell
Connect-AzureRmAccount
```

Zkontrolujte předplatná pro příslušný účet:

```powershell
Get-AzureRmSubscription
```

Vyberte odběr, který chcete vytvořit okruh ExpressRoute pro:

```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získejte seznam podporovaných zprostředkovatelů, umístění a šířek pásma
Než vytvoříte okruh ExpressRoute, musíte seznam poskytovatelů podporovaných připojení, umístění a možnosti šířky pásma.

Rutiny prostředí PowerShell **Get-AzureRmExpressRouteServiceProvider** vrátí tyto informace, které budete používat v dalších krocích:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Zkontrolujte, pokud poskytovatel připojení se nezobrazí. Poznamenejte si následující informace, které budete potřebovat později při vytvoření okruhu:

* Název
* PeeringLocations
* BandwidthsOffered

Nyní jste připraveni vytvořit okruh ExpressRoute.   

### <a name="3-create-an-expressroute-circuit"></a>3. Vytvoření okruhu ExpressRoute
Pokud ještě nemáte skupinu prostředků, je musíte vytvořit před vytvořením váš okruh ExpressRoute. Můžete tak učinit pomocí následujícího příkazu:

```powershell
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


Následující příklad ukazuje, jak vytvořit 200 MB/s okruh ExpressRoute prostřednictvím Equinix ze Silicon Valley. Pokud používáte k jinému zprostředkovateli a různá nastavení, dosaďte tyto informace při zkontrolujte vaši žádost. Pomocí následujícího příkladu požádat o nový klíč služby:

```powershell
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Ujistěte se, že jste zadali správné úrovně SKU a řada SKU:

* Úroveň SKU Určuje, zda je povolen ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat *standardní* získat standardní SKU nebo *Premium* pro doplněk premium.
* Řada SKU Určuje typ fakturace. Můžete zadat *Metereddata* pro plán měření podle objemu dat a *Unlimiteddata* pro plán neomezená data na úrovni. Můžete změnit typ fakturace z *Metereddata* k *Unlimiteddata*, ale nemůžete změnit typ z *Unlimiteddata* k *Metereddata*.

> [!IMPORTANT]
> Váš okruh ExpressRoute je účtován od okamžiku, kdy se objeví klíč služby. Ujistěte se, při provádění této operace, pokud poskytovatel připojení je připraven ke zřízení okruhu.
> 
> 

Odpověď obsahuje klíč služby. Podrobný popis všech parametrů můžete získat spuštěním následujícího příkazu:

```powershell
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Zobrazí seznam všech okruhy ExpressRoute
Chcete-li získat seznam všech okruhy ExpressRoute, které jste vytvořili, spusťte **Get-AzureRmExpressRouteCircuit** příkaz:

```powershell
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

Tyto informace kdykoli můžete načíst pomocí `Get-AzureRmExpressRouteCircuit` rutiny. Provádění volání bez parametrů jsou uvedeny všechny okruhů. Klíč služby, je uvedena ve *klíč ServiceKey* pole:

```powershell
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

```powershell
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Klíč služby poslat svého poskytovatele připojení pro zřizování
*ServiceProviderProvisioningState* poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stavu poskytuje stav na straně společnosti Microsoft. Další informace o zřizování stavy okruhu najdete v tématu [pracovních](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Když vytvoříte nový okruh ExpressRoute, okruh je v následujícím stavu:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Okruh změní na následující stav po probíhá povolení můžete poskytovatele připojení:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Abyste mohli používat okruh ExpressRoute musí být v následujícím stavu:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Pravidelně kontrolovat stav a stav okruhu klíče
Kontroluje stav a stav okruhu klíče umožňuje vědět, když má poskytovatel povoleno váš okruh. Po nakonfiguroval okruhu *ServiceProviderProvisioningState* se zobrazí jako *zajištěno*, jak je znázorněno v následujícím příkladu:

```powershell
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
Podrobné pokyny najdete v tématu [konfigurace směrování pro okruh ExpressRoute](expressroute-howto-routing-arm.md) článek k vytvoření a úpravám partnerských vztahů pro okruh.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb, které nabízejí vrstvy 2 připojení služby. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle virtuální privátní síť IP, např. MPLS), poskytovatel připojení konfiguruje a spravuje směrování.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Propojení virtuální sítě k okruhu ExpressRoute
V dalším kroku propojení virtuální sítě k okruhu ExpressRoute. Použití [propojování virtuálních sítí pro okruhy ExpressRoute](expressroute-howto-linkvnet-arm.md) článek při práci s modelem nasazení Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Získávání stav okruhu ExpressRoute
Tyto informace kdykoli můžete načíst pomocí **Get-AzureRmExpressRouteCircuit** rutiny. Provádění volání bez parametrů jsou uvedeny všechny okruhů.

```powershell
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


Informace o konkrétní okruh ExpressRoute můžete získat pomocí předání název skupiny prostředků a název okruh jako parametr pro volání:

```powershell
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

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Úprava okruhu ExpressRoute
Bez dopadu na připojení můžete upravit některé vlastnosti okruhu ExpressRoute.

Můžete provést následující úlohy bez výpadků:

* Povolit nebo zakázat doplněk ExpressRoute premium pro váš okruh ExpressRoute.
* Zadaný na tomto portu je dostupná kapacita, zvětšete šířku pásma okruhu ExpressRoute. Přechod na starší verzi šířku pásma okruhu není podporována. 
* Změňte plán měření z – měření podle objemu dat na neomezená Data na úrovni. Změna plánu měření z neomezená Data – měření podle objemu dat není podporována.
* Můžete povolit nebo zakázat *povolit klasické operace*.

Další informace o omezení a omezení, najdete v článku [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Chcete-li povolit doplněk ExpressRoute premium
Doplněk ExpressRoute premium pro váš okruh. existující můžete povolit pomocí následující fragment kódu prostředí PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Okruh má nyní funkce doplněk ExpressRoute premium povolena. Můžeme začít hned, jak byl úspěšně spuštěn příkaz fakturace pro funkci rozšíření premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Chcete-li zakázat doplněk ExpressRoute premium
> [!IMPORTANT]
> Pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh, tato operace může selhat.
> 
> 

Vezměte na vědomí následující informace:

* Předtím, než na standard se downgradovat z úrovně premium, je nutné zajistit, že počet virtuálních sítí, které jsou propojeny s okruh je menší než 10. Pokud to neuděláte, selže aktualizaci požadavků a budeme vám účtovat za zvýhodněné sazby.
* Je nutné zrušit všechny virtuální sítě v jiných geopolitické oblasti. Pokud to neuděláte, selže aktualizaci požadavků a budeme vám účtovat za zvýhodněné sazby.
* Směrovací tabulka musí být menší než 4 000 tras pro soukromý partnerský vztah. Pokud vaše velikost tabulky trasy je větší než 4 000 tras, relace BGP zahodí a nebude možné opětovně povolena dokud počet předpon inzerovaných přejde nižší než 4 000.

Doplněk ExpressRoute premium u existujícího okruhu můžete zakázat pomocí následující rutiny prostředí PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Chcete-li aktualizovat šířku pásma okruhu ExpressRoute
Možnosti podporované šířky pásma pro zprostředkovatele, zkontrolujte [ExpressRoute – nejčastější dotazy](expressroute-faqs.md). Můžete vybrat libovolnou velikost, která je větší než velikost existujícím okruhem.

> [!IMPORTANT]
> Možná budete muset znovu vytvořit okruh ExpressRoute, pokud je nedostatečné kapacity na existující port. Pokud v tomto umístění není k dispozici žádné další kapacitu, nelze upgradovat okruh.
>
> Nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Přechod na starší verzi šířky pásma vyžaduje zrušení zřízení okruh ExpressRoute a pak znova nezajistíte nové okruh ExpressRoute.
> 

Jakmile se rozhodnete, jakou velikost, budete potřebovat, ke změně velikosti okruhu použijte následující příkaz:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Váš okruh bude mít velikost na straně společnosti Microsoft. Pak musí kontaktujte svého poskytovatele připojení k aktualizaci konfigurace na jejich straně tak, aby odpovídaly tuto změnu. Když provedete toto oznámení, začneme fakturace můžete pro možnost aktualizované šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Chcete-li přesunout SKU z měřeného na neomezený
SKU okruhu ExpressRoute můžete změnit pomocí následující fragment kódu prostředí PowerShell:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>K řízení přístupu k classic i Resource Manager prostředí
Přečtěte si pokyny v [okruhy ExpressRoute přesunout z klasického modelu nasazení Resource Manager](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Zrušení zřízení a odstraňování okruhu ExpressRoute
Vezměte na vědomí následující informace:

* Je nutné zrušit všechny virtuální sítě od okruhu ExpressRoute. Pokud se tato operace nezdaří, zkontrolujte, pokud jsou všechny virtuální sítě propojené ke okruh.
* Pokud je poskytovatel služby okruh ExpressRoute Stav zřizování **zřizování** nebo **zajištěno** , musíte pracovat se svým poskytovatelem služeb pro zrušení zřízení okruhu na jejich straně. Abychom mohli pokračovat a rezervovat prostředky vám účtovat, dokud poskytovatele služeb dokončení zrušení okruhu a upozorní nás.
* Pokud má poskytovatel služeb zrušit okruhu (poskytovatele služeb Stav zřizování je nastavena na **není zajišťováno**), můžete odstranit okruh. To zastaví fakturace pro okruh.

Váš okruh ExpressRoute můžete odstranit tak, že spustíte následující příkaz:

```powershell
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Další postup

Po vytvoření okruhu, ujistěte se, abyste provedli následující další kroky:

* [Vytvoření a úprava směrování pro okruhu ExpressRoute](expressroute-howto-routing-arm.md)
* [Propojení virtuální sítě k okruhu ExpressRoute](expressroute-howto-linkvnet-arm.md)
