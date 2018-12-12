---
title: 'Vytvoření a úprava okruhu ExpressRoute: Azure CLI | Dokumentace Microsoftu'
description: Tento článek ukazuje, jak vytvořit, zřízení, ověřte, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute pomocí rozhraní příkazového řádku.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman;cherylmc
ms.openlocfilehash: 2013b3b96fddd32f01245655c1feb600bc426e2a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084137"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Vytvoření a úprava okruhu ExpressRoute pomocí rozhraní příkazového řádku


Tento článek popisuje, jak vytvořit okruh Azure ExpressRoute pomocí rozhraní příkazového řádku (CLI). Tento článek také ukazuje, jak zkontrolovat stav, update nebo delete a zrušit zřízení okruhu. Pokud chcete použít jinou metodu pro práci s okruhy ExpressRoute, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Před zahájením

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).
* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

## <a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se ke svému účtu Azure a vyberte své předplatné

Můžete začít s vaší konfigurací, přihlaste se ke svému účtu Azure. Pokud používáte cloud shell, "Vyzkoušet", jste přihlášení automaticky. Připojení vám usnadní použijte následující příklady:

```azurecli
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli-interactive
az account list
```

Vyberte předplatné, pro kterou chcete vytvořit okruh ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získání seznamu podporovaných zprostředkovatelů, umístění a šířky pásma

Než začnete vytvářet okruhu ExpressRoute, budete potřebovat seznam poskytovatelů podporovaných připojení, umístění a možnosti šířky pásma. Rozhraní příkazového řádku příkaz "az express-route seznamu – poskytovatelé síťových služeb-" vrátí tyto informace, které budete používat v dalších krocích:

```azurecli-interactive
az network express-route list-service-providers
```

Odpověď bude podobná jako v následujícím příkladu:

```azurecli
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

Zkontrolujte odpověď zobrazíte, pokud je uveden váš poskytovatel připojení. Poznamenejte si následující informace, které budete potřebovat při vytváření okruhu:

* Název
* PeeringLocations
* BandwidthsOffered

Nyní jste připraveni vytvořit okruh ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Vytvoření okruhu ExpressRoute

> [!IMPORTANT]
> Váš okruh ExpressRoute se účtují od okamžiku, kdy vydáním klíče služby. Tuto operaci proveďte, pokud poskytovatel připojení je připraveno ke zřízení okruhu.
> 
> 

Pokud ještě nemáte skupinu prostředků, můžete musí vytvořit před vytvořením váš okruh ExpressRoute. Vytvořte skupinu prostředků spuštěním následujícího příkazu:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Následující příklad ukazuje, jak vytvořit okruh ExpressRoute prostřednictvím Equinix 200 MB/s v Silicon Valley. Pokud používáte jiného poskytovatele a jiné nastavení, dosaďte tyto informace při podat žádost. 

Ujistěte se, že jste zadali správnou úroveň skladové položky a skladová položka rodina:

* Úroveň skladové položky určuje, zda je povolen ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat "Standard". Pokud chcete získat standardní SKU nebo "Premium" pořídit si doplněk premium.
* Skladová položka rodina Určuje typ fakturace. Můžete určit "Metereddata" tarif podle objemu dat a "Unlimiteddata" pro tarif s neomezenými daty. Můžete změnit fakturační typ z "Metereddata" na "Unlimiteddata", ale nemůže změnit typ z "Unlimiteddata" na "Metereddata".


Váš okruh ExpressRoute se účtují od okamžiku, kdy vydáním klíče služby. V následujícím příkladu je žádost o nový klíč služby:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpověď obsahuje klíč služby.

### <a name="4-list-all-expressroute-circuits"></a>4. Vypsat všechny okruhy ExpressRoute

Pokud chcete získat seznam všech okruhy ExpressRoute, které jste vytvořili, spusťte příkaz "az network express-route list". Tyto informace kdykoli můžete načíst pomocí tohoto příkazu. Chcete-li vypsat všechny okruhy volání bez parametrů.

```azurecli-interactive
az network express-route list
```

Klíč služby je uveden v *klíč ServiceKey* pole odpovědi.

```azurecli
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

Podrobný popis všech parametrů můžete získat spuštěním tohoto příkazu s "-h" parametr.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Poslat klíči služby svého poskytovatele připojení pro zřizování

"Serviceproviderprovisioningstate vzájemného propojení" poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stavu poskytuje stav na straně Microsoftu. Další informace najdete v tématu [pracovních postupů článku](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Při vytváření nového okruhu ExpressRoute, je okruh v následujícím stavu:

```azurecli-interactive
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Když probíhá proces jeho povolení pro vás poskytovatel připojení okruhu změní na následující stav:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Abyste mohli použít okruhu ExpressRoute musí být v následujícím stavu:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Pravidelně kontrolovat stav a stav okruhu klávesy

Kontroluje se stav a stav okruhu klíč vám umožňuje vědět, kdy váš poskytovatel povolil váš okruh. Po dokončení konfigurace okruh, se zobrazí 'Serviceproviderprovisioningstate vzájemného propojení' jako "Zřízená", jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Odpověď bude podobná jako v následujícím příkladu:

```azurecli
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

### <a name="7-create-your-routing-configuration"></a>7. Vytvořte vlastní konfiguraci směrování

Podrobné pokyny najdete v tématu [konfigurace směrování pro okruh ExpressRoute](howto-routing-cli.md) článek k vytvoření a úprava okruhu partnerských vztahů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, které nabízejí vrstvy 2 připojení služby. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle IP sítě VPN, např. MPLS), svého poskytovatele připojení, konfiguruje a spravuje směrování za vás.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Propojení virtuální sítě k okruhu ExpressRoute

V dalším kroku propojení virtuální sítě pro váš okruh ExpressRoute. Použití [propojení virtuálních sítí s okruhy ExpressRoute](howto-linkvnet-cli.md) článku.

## <a name="modify"></a>Úprava okruhu ExpressRoute

Můžete upravit některé vlastnosti okruhu ExpressRoute bez dopadu na připojení. Můžete provést následující změny bez jakéhokoli výpadku:

* Můžete povolit nebo zakázat doplněk ExpressRoute premium pro váš okruh ExpressRoute.
* Zadaný port je k dispozici kapacity můžete zvýšit šířku pásma váš okruh ExpressRoute. Ale není podporované downgradu šířku pásma okruhu. 
* Měření podle objemu dat můžete změnit měření plán pro neomezená Data. Však změna měření plánu z neomezená Data na měřený objem dat se nepodporuje.
* Můžete povolit nebo zakázat *povolit klasické operace*.

Další informace o omezení a omezení, najdete v článku [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Chcete-li povolit doplněk ExpressRoute premium

Doplněk ExpressRoute premium pro existující okruh můžete povolit pomocí následujícího příkazu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Okruh teď má funkce doplněk ExpressRoute premium povolené. Začneme, jakmile příkaz úspěšně spuštěn fakturace pro funkci doplněk premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Chcete-li zakázat doplněk ExpressRoute premium

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší než co je povolený pro standardní okruh.
> 
> 

Před zakázáním doplněk ExpressRoute premium, seznamte s následujícími kritérii:

* Než spustíte downgrade z úrovně premium na úroveň standard, je třeba mít míň než 10 virtuálních sítí propojených s okruhem. Pokud máte více než 10, požadavek na aktualizaci selže a budeme vám fakturovat sazby úrovně premium.
* Je nutné zrušit všechny virtuální sítě v dalších geopolitických oblastí. Pokud nemáte propojení virtuálních sítí, vaši žádost o aktualizaci se nezdaří a budeme vám fakturovat sazby úrovně premium.
* Směrovací tabulky musí být menší než 4 000 tras pro soukromý partnerský vztah. Pokud se velikost vašeho směrovací tabulku je větší než 4 000 tras, relace BGP se zahodí. Opětovně relace nebudou povolena, dokud počet předpon inzerovaných je nižší než 4 000.

Doplněk ExpressRoute premium pro existující okruh můžete zakázat s použitím v následujícím příkladu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Chcete-li aktualizovat šířku pásma okruhu ExpressRoute

Možnosti podporované šířky pásma pro vašeho poskytovatele, zkontrolujte, [ExpressRoute – nejčastější dotazy](expressroute-faqs.md). Můžete si vybrat libovolné velikosti větší než velikost existující okruh.

> [!IMPORTANT]
> Pokud je na existující port nedostatečné kapacity, budete muset znovu vytvořit okruh ExpressRoute. Pokud v tomto umístění není k dispozici žádné další kapacitu, nemůže upgradovat okruh.
>
> Nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Downgrade šířky pásma je potřeba zrušit zřízení okruhu ExpressRoute a pak znova nezajistíte nového okruhu ExpressRoute.
>

Až se rozhodnete velikost, které potřebujete, použijte následující příkaz pro změnu velikosti okruhů:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Váš okruh je velikost na straně Microsoftu. V dalším kroku musí kontaktovat svého poskytovatele připojení aktualizovat konfiguraci na své straně tak, aby odpovídaly tuto změnu. Když provedete toto oznámení, začneme fakturace je pro možnost aktualizované šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Přesunout SKU z měřeného na neomezený počet

SKU okruhu ExpressRoute můžete změnit pomocí následujícího příkladu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Řízení přístupu k classic a Resource Manageru prostředí

Přečtěte si pokyny v [okruhy ExpressRoute přesunout z klasického modelu nasazení Resource Manageru](expressroute-howto-move-arm.md).

## <a name="delete"></a>Zrušení zřízení a odstranění okruhu ExpressRoute

Zrušení zřízení a odstranění okruhu ExpressRoute, ujistěte se, že se že můžete seznámit s následujícími kritérii:

* Od okruhu ExpressRoute je potřeba odpojit všechny virtuální sítě. Pokud se tato operace se nezdaří, zkontrolujte, pokud jsou propojeny žádné virtuální sítě k okruhu.
* Pokud je stav zřizování poskytovatele služeb okruh ExpressRoute **zřizování** nebo **zřízená**, musíte pracovat se svým poskytovatelem služeb zrušit zřízení okruhu na své straně. Pokračujeme v rezervovat prostředky a účtovat až do dokončení zrušení zřízení okruhu a informuje nás poskytovatelem služeb.
* Můžete odstranit okruh, pokud poskytovatel služeb okruh zruší. Při zrušení zřízení okruhu poskytovatele služeb, Stav zřizování je nastaven na **nezřízeno**. Tím se zastaví účtování okruhu.

Váš okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Další postup

Po vytvoření váš okruh, ujistěte se, že můžete provádět následující úlohy:

* [Vytvoření a úprava směrování pro okruh ExpressRoute](howto-routing-cli.md)
* [Propojení virtuální sítě pro váš okruh ExpressRoute](howto-linkvnet-cli.md)
