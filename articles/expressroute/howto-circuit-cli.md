---
title: 'Vytvoření a úprava okruhu Azure ExpressRoute: rozhraní příkazového řádku | Microsoft Docs'
description: Tento článek popisuje, jak vytvořit, zřízení, ověřte, aktualizovat, odstranit a zrušit jejich zřízení okruhu ExpressRoute pomocí rozhraní příkazového řádku.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "23933246"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Vytvoření a úprava okruhu ExpressRoute pomocí rozhraní příkazového řádku


Tento článek popisuje postup vytvoření okruhu Azure ExpressRoute pomocí rozhraní příkazového řádku (CLI). Tento článek také ukazuje, jak zkontrolovat stav, aktualizace nebo odstranění a zrušení zřízení okruh. Pokud chcete použít jinou metodu pro práci s okruhy ExpressRoute, můžete vybrat článek z následujícího seznamu:

> [!div class="op_single_selector"]
> * [portál Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Než začnete

* Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).
* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

## <a name="create"></a>Vytvořit a zřídit okruhu ExpressRoute

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Přihlaste se k účtu Azure a vybrat své předplatné

Chcete-li zahájit konfiguraci, přihlaste se k účtu Azure. Pomůže vám pomůže s připojením v následujících příkladech:

```azurecli
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli
az account list
```

Vyberte předplatné, pro který chcete vytvořit okruh ExpressRoute.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Získejte seznam podporovaných zprostředkovatelů, umístění a šířek pásma

Než vytvoříte okruh ExpressRoute, musíte seznam poskytovatelů podporovaných připojení, umístění a možnosti šířky pásma. Rozhraní příkazového řádku příkaz 'az sítě express route seznamu--poskytovatelé služeb, vrátí tato informace, které budete používat v dalších krocích:

```azurecli
az network express-route list-service-providers
```

Odpověď je stejný jako v následujícím příkladu:

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

Zkontrolujte odpovědi, které chcete zobrazit, pokud je uvedený svého poskytovatele připojení. Poznamenejte si následující informace, které budete potřebovat při vytvoření okruhu:

* Název
* PeeringLocations
* BandwidthsOffered

Nyní jste připraveni vytvořit okruh ExpressRoute.

### <a name="3-create-an-expressroute-circuit"></a>3. Vytvoření okruhu ExpressRoute

> [!IMPORTANT]
> Váš okruh ExpressRoute je účtován od okamžiku, kdy se objeví klíč služby. Tuto operaci proveďte, pokud poskytovatel připojení je připraven ke zřízení okruhu.
> 
> 

Pokud ještě nemáte skupinu prostředků, je musíte vytvořit před vytvořením váš okruh ExpressRoute. Můžete vytvořit skupinu prostředků spuštěním následujícího příkazu:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

Následující příklad ukazuje, jak vytvořit 200 MB/s okruh ExpressRoute prostřednictvím Equinix ze Silicon Valley. Pokud používáte k jinému zprostředkovateli a různá nastavení, dosaďte tyto informace při zkontrolujte vaši žádost. 

Ujistěte se, že jste zadali správné úrovně SKU a řada SKU:

* Úroveň SKU Určuje, zda je povolen ExpressRoute standard nebo doplněk ExpressRoute premium. Můžete zadat "Standardní" získat standardní SKU nebo 'Premium' pro doplněk premium.
* Řada SKU Určuje typ fakturace. Pro plán neomezená data na úrovni můžete zadat 'Metereddata' pro plán měření podle objemu dat a 'Unlimiteddata'. Můžete změnit fakturace typu z 'Metereddata' do 'Unlimiteddata', ale nemůžete změnit typ z 'Unlimiteddata' do 'Metereddata'.


Váš okruh ExpressRoute je účtován od okamžiku, kdy se objeví klíč služby. V následujícím příkladu je žádost o nový klíč služby:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpověď obsahuje klíč služby.

### <a name="4-list-all-expressroute-circuits"></a>4. Zobrazí seznam všech okruhy ExpressRoute

Pokud chcete získat seznam všech okruhy ExpressRoute, které jste vytvořili, spusťte příkaz "az sítě express route seznam". Tyto informace kdykoli můžete načíst pomocí tohoto příkazu. Pro zobrazení seznamu všech okruhů, volání bez parametrů.

```azurecli
az network express-route list
```

Klíč služby, je uvedena ve *klíč ServiceKey* pole odpovědi.

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

Podrobný popis všech parametrů získáte spuštěním příkazu pomocí '-h "parametr.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Klíč služby poslat svého poskytovatele připojení pro zřizování

'ServiceProviderProvisioningState' poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stavu poskytuje stav na straně společnosti Microsoft. Další informace najdete v tématu [pracovních článku](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Když vytvoříte nový okruh ExpressRoute, okruh je v následujícím stavu:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Okruh změní na následující stav po probíhá povolení můžete poskytovatele připojení:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Abyste mohli používat okruh ExpressRoute musí být v následujícím stavu:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Pravidelně kontrolovat stav a stav okruhu klíče

Kontroluje stav a stav okruhu klíče umožňuje vědět, když má poskytovatel povoleno váš okruh. Po nakonfiguroval okruh, se zobrazí 'ServiceProviderProvisioningState' jako 'Zajištěno', jak je znázorněno v následujícím příkladu:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Odpověď je stejný jako v následujícím příkladu:

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

Podrobné pokyny najdete v tématu [konfigurace směrování pro okruh ExpressRoute](howto-routing-cli.md) článek k vytvoření a úpravám partnerských vztahů pro okruh.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené poskytovateli služeb, které nabízejí vrstvy 2 připojení služby. Pokud používáte poskytovatele služeb, který nabízí spravované vrstvy 3 služby (obvykle virtuální privátní síť IP, např. MPLS), poskytovatel připojení konfiguruje a spravuje směrování.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Propojení virtuální sítě k okruhu ExpressRoute

V dalším kroku propojení virtuální sítě k okruhu ExpressRoute. Použití [propojování virtuálních sítí pro okruhy ExpressRoute](howto-linkvnet-cli.md) článku.

## <a name="modify"></a>Úprava okruhu ExpressRoute

Bez dopadu na připojení můžete upravit některé vlastnosti okruhu ExpressRoute. Můžete provádět následující změny bez výpadků:

* Můžete povolit nebo zakázat doplněk ExpressRoute premium pro váš okruh ExpressRoute.
* Zadaný na tomto portu je dostupná kapacita můžete zvětšit šířku pásma okruhu ExpressRoute. Však šířku pásma okruhu přechod na starší verzi není podporován. 
* Měření plán můžete změnit z – měření podle objemu dat na neomezená Data na úrovni. Ale změna měření plán z neomezená Data – měření podle objemu dat není podporována.
* Můžete povolit nebo zakázat *povolit klasické operace*.

Další informace o omezení a omezení, najdete v článku [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Chcete-li povolit doplněk ExpressRoute premium

Doplněk ExpressRoute premium pro váš okruh. existující můžete povolit pomocí následujícího příkazu:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Okruh má nyní funkce doplněk ExpressRoute premium povolena. Můžeme začít hned, jak byl úspěšně spuštěn příkaz fakturace pro funkci rozšíření premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Chcete-li zakázat doplněk ExpressRoute premium

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší než co je povoleno pro standardní okruh.
> 
> 

Před zakázáním doplněk ExpressRoute premium, pochopit následující kritéria:

* Předtím, než na standard se downgradovat z úrovně premium, musí se ujistěte, že máte méně než 10 virtuální sítě propojené ke okruhu. Pokud máte více než 10, vaše žádost o aktualizaci nezdaří a budeme vám účtovat za zvýhodněné sazby.
* Je nutné zrušit všechny virtuální sítě v jiných geopolitické oblasti. Pokud nemáte propojení virtuálních sítí, vaše žádost o aktualizaci nezdaří a budeme vám účtovat za zvýhodněné sazby.
* Směrovací tabulka musí být menší než 4 000 tras pro soukromý partnerský vztah. Pokud vaše velikost tabulky trasy je větší než 4 000 tras, zahodí se relace protokolu BGP. Opětovně relace nebudou povolena, dokud počet předpon inzerovaných je menší než 4 000.

Doplněk ExpressRoute premium u existujícího okruhu můžete zakázat pomocí v následujícím příkladu:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Chcete-li aktualizovat šířku pásma okruhu ExpressRoute

Možnosti podporované šířky pásma pro zprostředkovatele, zkontrolujte [ExpressRoute – nejčastější dotazy](expressroute-faqs.md). Můžete vybrat libovolnou velikost, která je větší než velikost existujícím okruhem.

> [!IMPORTANT]
> Pokud je na existující port nedostatečné kapacity, budete muset znovu vytvořit okruh ExpressRoute. Pokud v tomto umístění není k dispozici žádné další kapacitu, nelze upgradovat okruh.
>
> Nejde snížit šířku pásma okruhu ExpressRoute bez přerušení. Přechod na starší verzi šířky pásma vyžaduje zrušení zřízení okruh ExpressRoute a pak znova nezajistíte nové okruh ExpressRoute.
>

Ke změně velikosti váš okruh, až se rozhodnete velikost, které potřebujete, použijte následující příkaz:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Váš okruh je velikost na straně společnosti Microsoft. V dalším kroku musí kontaktujte svého poskytovatele připojení k aktualizaci konfigurace na jejich straně tak, aby odpovídaly tuto změnu. Když provedete toto oznámení, začneme fakturace můžete pro možnost aktualizované šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Chcete-li přesunout SKU z měřeného na neomezený

SKU okruhu ExpressRoute můžete změnit pomocí v následujícím příkladu:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>K řízení přístupu k classic i Resource Manager prostředí

Přečtěte si pokyny v [okruhy ExpressRoute přesunout z klasického modelu nasazení Resource Manager](expressroute-howto-move-arm.md).

## <a name="delete"></a>Zrušení zřízení a odstraňování okruhu ExpressRoute

Pokud chcete zrušit jejich zřízení a odstranit okruh ExpressRoute, ujistěte se, že rozumíte následující kritéria:

* Je nutné zrušit všechny virtuální sítě od okruhu ExpressRoute. Pokud se tato operace nezdaří, zkontrolujte, pokud jsou všechny virtuální sítě propojené ke okruh.
* Pokud je poskytovatel služby okruh ExpressRoute Stav zřizování **zřizování** nebo **zajištěno**, musíte pracovat se svým poskytovatelem služeb pro zrušení zřízení okruhu na jejich straně. Abychom mohli pokračovat a rezervovat prostředky vám účtovat, dokud poskytovatele služeb dokončení zrušení okruhu a upozorní nás.
* Pokud má poskytovatel služeb zrušit okruh, můžete odstranit okruh. Pokud je zrušit okruh, poskytovatele služeb Stav zřizování je nastavena na **není zajišťováno**. To zastaví fakturace pro okruh.

Váš okruh ExpressRoute můžete odstranit tak, že spustíte následující příkaz:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu, ujistěte se, že můžete provádět následující úlohy:

* [Vytvoření a úprava směrování pro okruhu ExpressRoute](howto-routing-cli.md)
* [Propojení virtuální sítě k okruhu ExpressRoute](howto-linkvnet-cli.md)