---
title: 'Rychlý Start: vytvoření a úprava okruhu ExpressRoute: Azure CLI'
description: V tomto rychlém startu se dozvíte, jak vytvořit, zřídit, ověřit, aktualizovat, odstranit a zrušit zřízení okruhu ExpressRoute pomocí Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: eebb2693d3bc0f65059c6c3c377f1afb7ae7eccd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757567"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-cli"></a>Rychlý Start: vytvoření a úprava okruhu ExpressRoute pomocí Azure CLI

V tomto rychlém startu se dozvíte, jak vytvořit okruh Azure ExpressRoute pomocí rozhraní příkazového řádku (CLI). Tento článek také ukazuje, jak kontrolovat stav, aktualizovat nebo odstranit a zrušit zřízení okruhu.

## <a name="prerequisites"></a>Předpoklady

* Než začnete s konfigurací, Projděte si [požadavky](expressroute-prerequisites.md) a [pracovní postupy](expressroute-workflows.md) .
* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte nejnovější verzi příkazů rozhraní příkazového řádku (2,0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Vytvoření a zřízení okruhu ExpressRoute

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné.

Pokud chcete zahájit konfiguraci, přihlaste se ke svému účtu Azure. Pokud použijete Cloud Shell "vyzkoušet ho", jste přihlášeni automaticky. Následující příklady vám pomůžou s připojením:

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

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>Získat seznam podporovaných zprostředkovatelů, umístění a šířky pásma

Před vytvořením okruhu ExpressRoute potřebujete seznam podporovaných zprostředkovatelů připojení, umístění a možností šířky pásma. Příkaz CLI `az network express-route list-service-providers` vrátí tyto informace, které použijete v pozdějších krocích:

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

Pokud chcete zjistit, jestli je váš poskytovatel připojení uvedený, zkontrolujte odpověď. Poznamenejte si následující informace, které budete potřebovat při vytváření okruhu:

* Název
* PeeringLocations
* BandwidthsOffered

Nyní jste připraveni vytvořit okruh ExpressRoute.

### <a name="create-an-expressroute-circuit"></a>Vytvoření okruhu ExpressRoute

> [!IMPORTANT]
> Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. Tuto operaci proveďte, když je poskytovatel připojení připravený k zřízení okruhu.
>
>

Pokud ještě nemáte skupinu prostředků, musíte ji před vytvořením okruhu ExpressRoute vytvořit. Skupinu prostředků můžete vytvořit spuštěním následujícího příkazu:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Následující příklad ukazuje, jak vytvořit okruh ExpressRoute 200 MB/s až Equinix v karbidu sedlu. Pokud používáte jiného poskytovatele a různá nastavení, nahraďte tyto informace při vytváření žádosti.

Ujistěte se, že zadáte správnou úroveň SKU a řadu SKU:

* Úroveň SKU určuje, jestli je okruh ExpressRoute [místní](expressroute-faqs.md#expressroute-local), Standard nebo [Premium](expressroute-faqs.md#expressroute-premium). Můžete zadat *místní*, * Standard nebo *Premium*. SKU nemůžete změnit z *úrovně Standard/Premium* na *místní*.
* Rodina SKU určuje typ fakturace. Můžete zadat *MeteredData* pro měřený datový tarif a *UnlimitedData* pro neomezený datový tarif. Typ fakturace můžete změnit z *MeteredData* na *UnlimitedData*, ale nemůžete změnit typ z *UnlimitedData* na *MeteredData*. *Místní* okruh je jenom *UnlimitedData* .


Váš okruh ExpressRoute se účtuje okamžikem, kdy je vydaný klíč služby. V následujícím příkladu je žádost o nový klíč služby:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Odpověď obsahuje klíč služby.

### <a name="list-all-expressroute-circuits"></a>Vypsat všechny okruhy ExpressRoute

Pokud chcete získat seznam všech okruhů ExpressRoute, které jste vytvořili, spusťte `az network express-route list` příkaz. Tyto informace můžete získat kdykoli pomocí tohoto příkazu. Chcete-li vypsat všechny okruhy, proveďte volání bez parametrů.

```azurecli-interactive
az network express-route list
```

Váš klíč služby je uvedený v poli *serviceKey* odpovědi.

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

Podrobné popisy všech parametrů získáte spuštěním příkazu s použitím parametru-h.

```azurecli-interactive
az network express-route list -h
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Poslat klíč služby poskytovateli připojení pro zřizování

' ServiceProviderProvisioningState ' poskytuje informace o aktuálním stavu zřizování na straně poskytovatele služeb. Stav poskytuje stav na straně Microsoftu. Další informace najdete v [článku věnovaném pracovním](expressroute-workflows.md#expressroute-circuit-provisioning-states)postupům.

Při vytváření nového okruhu ExpressRoute je okruh v následujícím stavu:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Když poskytovatel připojení v tuto chvíli povoluje poskytovatel připojení, změní se na následující stav:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Chcete-li použít okruh ExpressRoute, musí být v následujícím stavu:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Pravidelně kontrolovat stav a stav klíče okruhu

Kontrola stavu a stav klíče služby vám poskytne informace o tom, kdy poskytovatel zřídí váš okruh. Po nakonfigurování okruhu se *ServiceProviderProvisioningState* zobrazí jako *zřízené*, jak je znázorněno v následujícím příkladu:

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

### <a name="create-your-routing-configuration"></a>Vytvoření konfigurace směrování

Podrobné pokyny najdete v článku věnovaném [konfiguraci směrování okruhu ExpressRoute](howto-routing-cli.md) k vytváření a úpravám partnerských vztahů okruhů.

> [!IMPORTANT]
> Tyto pokyny platí jenom pro okruhy vytvořené s poskytovateli služeb, kteří nabízejí služby pro připojení vrstvy 2. Pokud používáte poskytovatele služeb, který nabízí spravované služby vrstvy 3 (obvykle IP VPN, jako je MPLS), váš poskytovatel připojení nakonfiguruje a spravuje směrování za vás.
>
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Propojení virtuální sítě k okruhu ExpressRoute

Potom propojte virtuální síť se svým okruhem ExpressRoute. Použijte [propojení virtuální sítě s okruhy ExpressRoute](howto-linkvnet-cli.md) .

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Úprava okruhu ExpressRoute

Můžete upravit některé vlastnosti okruhu ExpressRoute, aniž by to mělo vliv na připojení. Můžete provést následující změny bez výpadku:

* Pro okruh ExpressRoute můžete povolit nebo zakázat doplněk ExpressRoute Premium. Změna SKU z *úrovně Standard/Premium* na *místní* není podporovaná.
* Šířku pásma okruhu ExpressRoute můžete zvýšit, pokud je na portu k dispozici kapacita. Nicméně downgrading šířky pásma okruhu není podporován.
* Plán měření z měřených dat můžete změnit na neomezená data. Nicméně Změna plánu měření z neomezených dat na měřená data není podporovaná.
* Můžete povolit nebo zakázat *operace klasických operací*.

Další informace o omezeních a omezeních najdete v tématu [ExpressRoute – Nejčastější dotazy](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Povolení doplňku ExpressRoute Premium

Doplněk ExpressRoute Premium pro stávající okruh můžete povolit pomocí následujícího příkazu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Okruh teď má povolené funkce doplňku ExpressRoute Premium. Až se příkaz úspěšně spustí, začneme vám účtovat možnost doplňku Premium.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Zakázání doplňku ExpressRoute Premium

> [!IMPORTANT]
> Tato operace může selhat, pokud používáte prostředky, které jsou větší, než je povoleno pro standardní okruh.
>
>

Než zakážete doplněk ExpressRoute Premium, pochopte následující kritéria:

* Než začnete downgradovat z úrovně Premium na standard, je nutné zajistit, aby počet virtuálních sítí, které jsou propojeny s okruhem, byl menší než 10. Pokud to neuděláte, váš požadavek na aktualizaci se nezdařil a účtujeme vám sazby za Premium.
* Všechny virtuální sítě v jiných geopolitických oblastech musí být nejprve odpojování. Pokud odkaz neodeberete, vaše žádost o aktualizaci nebude úspěšná a budeme vám účtovat poplatky za Premium.
* Směrovací tabulka musí mít méně než 4 000 tras pro privátní partnerské vztahy. Pokud je velikost směrovací tabulky větší než 4 000 tras, relace protokolu BGP se vynechá. Relace protokolu BGP nebude znovu povolena, dokud nebude počet inzerovaných předpon pod 4 000.

Doplněk ExpressRoute Premium pro stávající okruh můžete zakázat pomocí následujícího příkladu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Aktualizace šířky pásma okruhu ExpressRoute

Možnosti podporované šířky pásma pro poskytovatele najdete v části [Nejčastější dotazy k ExpressRoute](expressroute-faqs.md). Můžete vybrat libovolnou velikost, která je větší než velikost stávajícího okruhu.

> [!IMPORTANT]
> Pokud na stávajícím portu není dostatečná kapacita, bude pravděpodobně nutné znovu vytvořit okruh ExpressRoute. Pokud v daném umístění není k dispozici žádná další kapacita, nemůžete upgradovat okruh.
>
> Nemůžete snížit šířku pásma okruhu ExpressRoute bez přerušení. K downgradům šířky pásma je potřeba zrušit zřízení okruhu ExpressRoute a pak znovu zřídit nový okruh ExpressRoute.
>

Až se rozhodnete, jak velikost potřebujete, změňte velikost okruhu pomocí následujícího příkazu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Váš okruh bude upgradován na straně Microsoftu. V dalším kroku se obraťte na svého poskytovatele připojení a aktualizujte konfiguraci na své straně tak, aby odpovídaly této změně. Po vytvoření tohoto oznámení začneme účtovat aktualizovanou možnost šířky pásma.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Přesun SKU z měření na neomezený

SKU okruhu ExpressRoute můžete změnit pomocí následujícího příkladu:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Řízení přístupu k prostředím Classic a Správce prostředků

Přečtěte si pokyny v tématu [Přesun okruhů ExpressRoute z modelu nasazení Classic na model nasazení Správce prostředků](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>Zrušení zřízení okruhu ExpressRoute

Pokud chcete zrušit zřízení a odstranit okruh ExpressRoute, ujistěte se, že rozumíte následujícím kritériím:

* Všechny virtuální sítě musí být z okruhu ExpressRoute odpojování. Pokud tato operace neproběhne úspěšně, zkontrolujte, zda jsou k okruhu připojeny nějaké virtuální sítě.
* Pokud je stav zřizování poskytovatele služby okruhu ExpressRoute **zřizování** nebo **zřízené** , musíte s vaším poskytovatelem služeb spolupracovat a zrušit zřízení okruhu na jejich straně. I nadále vyhradíme prostředky a účtujeme vám, dokud poskytovatel služeb nedokončí zrušení zřízení okruhu a pošle nám upozornění.
* Pokud poskytovatel služby zrušil zřízení okruhu, což znamená, že stav zřizování poskytovatele služeb se nastaví na **není zřízené**, můžete okruh odstranit. Faktura za okruh se pak zastaví.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Okruh ExpressRoute můžete odstranit spuštěním následujícího příkazu:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Další kroky

Po vytvoření okruhu a jeho zřízení s vaším poskytovatelem pokračujte k dalšímu kroku konfigurace partnerského vztahu:

> [!div class="nextstepaction"]
> [Vytvoření a úprava směrování pro okruh ExpressRoute](howto-routing-cli.md)
