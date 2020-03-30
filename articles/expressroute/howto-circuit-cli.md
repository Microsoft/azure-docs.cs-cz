---
title: 'Vytvoření a úprava okruhu ExpressRoute: Azure CLI'
description: Tento článek ukazuje, jak vytvořit, zřídit, ověřit, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute pomocí rozhraní příkazového příkazu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: b967e1d8751a9c6a5214fef5241d57e954ad9f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476147"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Vytvoření a úprava okruhu ExpressRoute pomocí rozhraní příkazového řádku


Tento článek popisuje, jak vytvořit okruh Azure ExpressRoute pomocí rozhraní příkazového řádku (CLI). Tento článek také ukazuje, jak zkontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení okruhu. Pokud chcete pro práci s okruhy ExpressRoute použít jinou metodu, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Šablona Azure Resource Manageru](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Než začnete

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).
* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovní postupy.](expressroute-workflows.md)

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se ke svému účtu Azure a vyberte předplatné

Chcete-li zahájit konfiguraci, přihlaste se ke svému účtu Azure. Pokud používáte CloudShell "Try It", jste přihlášeni automaticky. Pomocí následujících příkladů, které vám pomohou připojit se:

```azurecli-interactive
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli-interactive
az account list
```

Vyberte předplatné, pro které chcete vytvořit okruh ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získejte seznam podporovaných poskytovatelů, umístění a šířky pásma

Před vytvořením okruhu ExpressRoute potřebujete seznam podporovaných poskytovatelů připojení, umístění a možností šířky pásma. Příkaz PŘÍKAZ `az network express-route list-service-providers` K PŘÍKAZOVÉ KONto vrátí tyto informace, které použijete v pozdějších krocích:

```azurecli-interactive
az network express-route list-service-providers
```

Odpověď bude podobná jako v následujícím příkladu:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Zkontrolujte odpověď a zjistěte, zda je uveden váš poskytovatel připojení. Poznamenejte si následující informace, které budete potřebovat při vytváření okruhu:

* Name (Název)
* PeeringUmístění
* Nabízené šířky pásma

Nyní jste připraveni vytvořit okruh ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Vytvoření okruhu ExpressRoute

> [!IMPORTANT]
> Okruh ExpressRoute se účtuje od okamžiku, kdy je vydán servisní klíč. Tuto operaci proveďte, pokud je poskytovatel připojení připraven zřídit okruh.
>
>

Pokud ještě nemáte skupinu prostředků, musíte ji vytvořit před vytvořením okruhu ExpressRoute. Skupinu prostředků můžete vytvořit spuštěním následujícího příkazu:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Následující příklad ukazuje, jak vytvořit okruh ExpressRoute o rychlosti 200 Mb/s prostřednictvím equinixu v Silicon Valley. Pokud používáte jiného poskytovatele a různá nastavení, nahraďte tyto informace při žádosti.

Ujistěte se, že jste určili správnou úroveň skladové položky a rodinu skladových položk:

* Úroveň Skladové položky určuje, zda je okruh ExpressRoute [místní](expressroute-faqs.md#expressroute-local), standardní nebo [premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *místní*, *standardní* nebo *premium*.
* Skladová položka určuje typ fakturace. Můžete zadat *Metereddata* pro datový tarif účtovaný účtárna a *Unlimiteddata* pro neomezený datový tarif. Typ fakturace můžete změnit z *Metereddata* na *Unlimiteddata*, ale nemůžete změnit typ z *Unlimiteddata* na *Metereddata*. Místní *Local* okruh je pouze *Unlimiteddata.*


Okruh ExpressRoute se účtuje od okamžiku, kdy je vydán servisní klíč. Následující příklad je požadavek na nový klíč služby:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpověď obsahuje klíč služby.

### <a name="4-list-all-expressroute-circuits"></a>4. Seznam všech okruhů ExpressRoute

Chcete-li získat seznam všech okruhů ExpressRoute, `az network express-route list` které jste vytvořili, spusťte příkaz. Tyto informace můžete kdykoli načíst pomocí tohoto příkazu. Chcete-li vypsat všechny obvody, proveďte volání bez parametrů.

```azurecli-interactive
az network express-route list
```

Klíč služby je uveden v poli *ServiceKey* odpovědi.

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Podrobný popis všech parametrů můžete získat spuštěním příkazu pomocí parametru '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Odešlete klíč služby svému poskytovateli připojení pro zřizování

"ServiceProviderProvisioningState" poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stav poskytuje stav na straně společnosti Microsoft. Další informace naleznete v [článku Pracovní postupy](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Když vytvoříte nový okruh ExpressRoute, okruh je v následujícím stavu:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Okruh se změní na následující stav, když je poskytovatel připojení v procesu povolení pro vás:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Abyste mohli použít okruh ExpressRoute, musí být v následujícím stavu:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Pravidelně kontrolujte stav a stav klíče obvodu

Kontrola stavu a stavu klíče obvodu vás dozví, kdy váš poskytovatel povolil okruh. Po nakonfigurování okruhu se "ServiceProviderProvisioningState" zobrazí jako "Provisioned", jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Odpověď bude podobná jako v následujícím příkladu:

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Vytvoření konfigurace směrování

Podrobné pokyny naleznete v článku konfigurace [směrování okruhů ExpressRoute](howto-routing-cli.md) a vytvoření a úprava partnerských stran okruhů.

> [!IMPORTANT]
> Tyto pokyny platí pouze pro okruhy, které jsou vytvořeny s poskytovateli služeb, které nabízejí služby připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí služby spravované vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení konfiguruje a spravuje směrování za vás.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Propojení virtuální sítě s okruhem ExpressRoute

Dále propojte virtuální síť s okruhem ExpressRoute. Použijte článek [Propojení virtuálních sítí s okruhy ExpressRoute.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Úprava okruhu ExpressRoute

Můžete upravit určité vlastnosti okruhu ExpressRoute bez ovlivnění připojení. Můžete provést následující změny bez prostoje:

* Můžete povolit nebo zakázat doplněk premium ExpressRoute pro okruh ExpressRoute.
* Šířku pásma okruhu ExpressRoute můžete zvýšit za předpokladu, že je na portu k dispozici kapacita. Snížení šířky pásma okruhu však není podporováno.
* Plán měření můžete změnit z měřených dat na neomezená data. Změna plánu měření z neomezených dat na data účtované podle objemu dat však není podporována.
* Můžete povolit a zakázat *povolit klasické operace*.

Další informace o omezeních a omezeních naleznete v [nejčastějších dotazech k expresní trase](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Povolení doplňku ExpressRoute premium

Doplněk Premium ExpressRoute pro existující okruh můžete povolit pomocí následujícího příkazu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Okruh má nyní povolené prémiové doplňkové funkce ExpressRoute. Jakmile příkaz úspěšně spustíte, začneme vám účtovat prémiovou doplňkovou funkci.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Zakázání doplňku premium ExpressRoute

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh.
>
>

Před zakázáním doplňku premium ExpressRoute pochopte následující kritéria:

* Před přechodem z prémiového na standardní, musíte se ujistit, že máte méně než 10 virtuálních sítí propojených s okruhem. Pokud máte více než 10, vaše žádost o aktualizaci se nezdaří a my vám účtujeme prémiové sazby.
* Je nutné odpojit všechny virtuální sítě v jiných geopolitických oblastech. Pokud neodpojíte všechny virtuální sítě, váš požadavek na aktualizaci se nezdaří a my vám účtujeme prémiové sazby.
* Trasový tabulkový spojbyl menší než 4 000 tras pro soukromý partnerský vztah. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace Protokolu BGP klesne. Relace nebude znovu povolena, dokud nebude počet inzerovaných předpon nižší než 4 000.

Doplněk premium ExpressRoute pro existující okruh můžete zakázat pomocí následujícího příkladu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aktualizace šířky pásma okruhu ExpressRoute

Podporované možnosti šířky pásma pro vašeho poskytovatele najdete v [nejčastějších dotazech k aplikaci ExpressRoute](expressroute-faqs.md). Můžete vybrat libovolnou velikost větší, než je velikost stávajícího obvodu.

> [!IMPORTANT]
> Pokud je nedostatečná kapacita na existující port, bude pravděpodobně muset znovu vytvořit okruh ExpressRoute. Okruh nelze upgradovat, pokud v tomto umístění není k dispozici žádná další kapacita.
>
> Šířku pásma okruhu ExpressRoute nelze snížit bez přerušení. Snížení šířky pásma vyžaduje zrušení zřízení okruhu ExpressRoute a následné opětovné zřízení nového okruhu ExpressRoute.
>

Po rozhodnutí o velikosti, kterou potřebujete, použijte následující příkaz pro změna velikosti okruhu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Velikost obvodu na straně Microsoftu. Dále je nutné kontaktovat poskytovatele připojení aktualizovat konfigurace na jejich straně, aby odpovídaly této změny. Po tomto oznámení vám začneme účtovat aktualizovanou možnost šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Přesunutí skladové položky z měřené na neomezenou

Skladovou položku okruhu ExpressRoute můžete změnit pomocí následujícího příkladu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Řízení přístupu ke klasickému prostředí a prostředí Správce prostředků

Projděte si pokyny v [okruhu Move ExpressRoute z klasického modelu nasazení Resource Manageru](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Chcete-li zrušit zřízení a odstranit okruh ExpressRoute, ujistěte se, že rozumíte následujícím kritériím:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud se tato operace nezdaří, zkontrolujte, zda jsou nějaké virtuální sítě propojeny s okruhem.
* Pokud je stav zřizování zprostředkovatele okruhů ExpressRoute **Provisioning** nebo **Provisioned**, musíte spolupracovat s poskytovatelem služeb, abyste zrušili zřízení okruhu na jejich straně. Budeme i nadále rezervovat zdroje a účtovat vám, dokud poskytovatel služeb dokončí zrušení zřízení okruhu a upozorní nás.
* Okruh můžete odstranit, pokud poskytovatel služeb zrušil okruh. Když je zrušen okruh, stav zřizování poskytovatele služeb je nastaven na **Nezřízeno**. Tím se zastaví účtování okruhu.

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu se ujistěte, že provedete následující úkoly:

* [Vytvoření a úprava směrování okruhu ExpressRoute](howto-routing-cli.md)
* [Propojení virtuální sítě s okruhem ExpressRoute](howto-linkvnet-cli.md)
