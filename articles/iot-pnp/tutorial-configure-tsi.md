---
title: Použití Time Series Insights k ukládání a analýze telemetrie zařízení technologie Plug and Play Azure IoT | Microsoft Docs
description: Nastavte Time Series Insights prostředí a připojte IoT Hub, abyste mohli zobrazit a analyzovat telemetrii ze zařízení IoT technologie Plug and Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422259"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Kurz: vytvoření a připojení k Time Series Insights Gen2 k ukládání, vizualizaci a analýze telemetrie technologie Plug and Play zařízení IoT

V tomto kurzu se dozvíte, jak vytvořit a správně nakonfigurovat prostředí [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) pro integraci s řešením technologie Plug and Play IoT. Pomocí TSI můžete shromažďovat, zpracovávat, ukládat, dotazovat a vizualizovat data časových řad ve Internet věcí (IoT) škálování.

Nejdřív zřídíte prostředí TSI a připojíte IoT Hub jako zdroj událostí streamování. Pak budete pracovat pomocí synchronizace modelů a vytvořit model časových řad prostředí TSI založený na ukázkových souborech modelů [DTDL (Digital-The Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) , které jste použili pro řadič teploty a Termostatická zařízení.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Abyste se vyhnuli nutnosti nainstalovat rozhraní příkazového řádku Azure CLI místně, můžete k nastavení Cloud Services použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Výběr ID časové řady

Při zřizování prostředí TSI budete muset vybrat ID časové řady. Výběr vhodného ID časové řady je kritický, protože vlastnost je neměnná a po jejím nastavení ji nelze změnit. Výběr ID časové řady je například volba klíče oddílu pro databázi. Obvykle by mělo být vaše ID TS uzlem vašeho modelu assetu. Jinými slovy, obvykle chcete vybrat vlastnost ID nejpodrobnějšího prostředku nebo senzoru, který vysílá telemetrii.

Jako uživatel IoT technologie Plug and Play, což je příslušná otázka pro výběr vašeho ID TS, je přítomnost [součástí](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) modelů zařízení. 

![Výběr ID TS](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Pokud jste prodělali rychlý Start a vaše zařízení IoT Hub představuje [termostata](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), použijte `iot-hub-connection-device-id` jako své ID TS.

* Pokud jste prodělali některé z kurzů pro [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json)s více komponentami, použijte v níže uvedené části složený klíč, který je napsán jako  `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Zřízení prostředí Azure Time Series Insights Gen2

Následující příkaz provede následující akce:

* Vytvoří účet služby Azure Storage pro [chladírenský sklad](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)vašeho prostředí, který je určený pro dlouhodobé uchovávání a analýzu prostřednictvím historických dat.
  * Nahraďte `mytsicoldstore` jedinečným názvem vašeho účtu.
* Vytvoří prostředí Azure Time Series Insights Gen2, včetně teplého úložiště s dobou uchování 7 dní, a chladírenským skladem pro nekonečné uchovávání. 
  * Nahraďte `my-tsi-env` jedinečným názvem pro prostředí TSI. 
  * Nahraďte `my-pnp-resourcegroup` názvem skupiny prostředků, kterou jste použili během nastavování.
  * Nahraďte `my-ts-id-property` hodnotou vlastnosti TS ID na základě výše uvedených kritérií výběru.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Teď nakonfigurujete IoT Hub, které jste předtím vytvořili jako [zdroj události](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)vašeho prostředí. Když je zdroj události připojený, TSI začne indexovat události z vašeho centra, počínaje první událostí ve frontě.

Nejdřív vytvořte v IoT Hub jedinečnou skupinu příjemců pro prostředí TSI. Nahraďte `my-pnp-hub` názvem IoT Hub, který jste použili dříve.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Připojte IoT Hub. Nahraďte `my-pnp-resourcegroup` , `my-pnp-hub` a `my-tsi-env` s příslušnými hodnotami.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com) a vyberte své nově vytvořené Time Series Insights prostředí. Přejděte na *adresu URL aplikace Time Series Insights Explorer* zobrazená v přehledu instance.

![Stránka s přehledem portálu](./media/tutorial-configure-tsi/view-environment.png)

V Průzkumníkovi byste měli vidět své dva termostaty v části všechny hierarchie. V dalším kroku získáte model časové řady založený na modelu zařízení.

![Průzkumník – zobrazení 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Synchronizace modelů mezi digitálními a Time Series Insightsmi jazyky definice a Gen2

Dále přeložíte model zařízení DTDL na model Asset Service v Azure Time Series Insights (TSI). Model časové řady TSI je sémantický nástroj modelování pro kontexty dat v rámci TSI. Model časové řady má tři základní komponenty:

* [Instance modelu časové řady](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Instance jsou virtuální reprezentace samotné časové řady. Instance budou jedinečně identifikovat vaše ID TS.
* [Hierarchie modelů časových řad](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). Hierarchie organizují instance zadáním názvů vlastností a jejich vztahů.
* [Typy modelů časových řad](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Typy vám pomůžou definovat [proměnné](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) nebo vzorce pro výpočty. Typy jsou přidruženy k určité instanci.

> [!NOTE]
> Níže uvedené příklady jsou určené pro TemperatureController s více komponentami.

### <a name="define-your-types"></a>Definování typů

Můžete začít ingestovat data do Azure Time Series Insights Gen2, aniž byste museli předem definovat model. Když telemetrie dorazí, TSI se pokusí automaticky vyřešit instance časových řad na základě hodnoty vlastnosti TS ID. Všem instancím se přiřadí *výchozí typ*. Budete muset ručně vytvořit nový typ, který bude představovat vaše modely. Následující obrázek znázorňuje jednoduchou metodu synchronizace modelu DTDL a typu TSM:

![DTDL do TSM typu](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* Identifikátor digitálního vlákna (DTMI) se stane vaším ID typu.
* Název typu může být buď název modelu, nebo zobrazovaný název.
* Popis modelu se stal popisem typu.
* Pro každou komponentu telemetrie, která má číselné schéma, je vytvořena alespoň jedna proměnná typu. 
  * Pro proměnné lze použít pouze číselné datové typy, ale pokud je hodnota odeslána jako řetězec, který lze analyzovat, `"0"` například můžete použít funkci [převodu](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) , jako je například `toDouble`
* Název proměnné může být buď název telemetrie, nebo zobrazované jméno.
* Při definování výrazu proměnné časové řady (TSX) se podívejte na název telemetrie na lince a datový typ.

> [!NOTE]
> Tento příklad ukazuje pouze dvě proměnné – agregované a číselné, ale každý typ může mít až 100. Různé proměnné mohou odkazovat na stejnou hodnotu telemetrie, aby v případě potřeby prováděla různé calucaultions. Úplný seznam filtrů, agregace a skalárních funkcí najdete v dokumentaci k [syntaxi výrazů Time Series Insights Gen2 Time Series](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) .

Otevřete textový editor podle vlastního výběru a níže uložte JSON na místní disk:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

V Průzkumníku Time Series Insights přejděte na kartu model kliknutím na ikonu modelu na levé straně. Klikněte na **typy** a klikněte na **nahrát JSON** :

![Nahrávání](./media/tutorial-configure-tsi/upload-type.png)

Vyberte **zvolit soubor** , vyberte JSON, který jste předtím uložili, a klikněte na **nahrát** .

Měl by se zobrazit nově definovaný typ termostatu.

### <a name="optional---create-a-hierarchy"></a>Volitelné – vytvoření hierarchie

Volitelně můžete vytvořit hierarchii pro uspořádání dvou částí termostatu v rámci své TemeraptureController nadřazené položky.

Klikněte na *hierarchie* a vyberte *Přidat hierarchii*. `Device Fleet`Jako název zadejte a vytvořte jednu úroveň s názvem `Device Name` a pak klikněte na *Uložit*.

![Přidat hierarchii](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Přiřadit instance ke správnému typu

Dále změníte typ instancí a případně je situate v hierarchii.

Vyberte kartu *instance* a klikněte na ikonu *úprav* na pravé straně.

![Upravit instance](./media/tutorial-configure-tsi/edit-instance.png)

Klikněte na rozevírací seznam typ a vyberte `Thermostat` . 

![Změnit typ instance](./media/tutorial-configure-tsi/change-type.png)

Pokud jste vytvořili hierarchii, vyberte *pole instance* a zaškrtněte `Device Fleet` políčko. Zadejte `Temperature Controller` hodnotu svého nadřazeného zařízení a pak klikněte na *Uložit*.

![Přiřadit k hierarchii](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Opakujte výše uvedené kroky pro druhý termostat.

### <a name="view-your-data"></a>Zobrazení dat

Přejděte zpátky do podokna pro vytváření grafů a rozbalte položku loďstva a TemperatureController zařízení. Klikněte na thermostat1, vyberte `Temperature` proměnnou a pak klikněte na *Přidat* , aby se vybrala hodnota grafu. Totéž udělejte pro thermostat2.

![Změnit typ instance pro thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Další kroky

* Další informace o různých možnostech vytváření grafů, včetně velikosti intervalu a ovládacích prvků osy Y, najdete v dokumentaci k [aplikaci Azure Time Series Insights Explorer ](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels) .

* Podrobný přehled modelu časových řad vašeho prostředí najdete v [tomto](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview) článku.

* Pokud se chcete podrobně do rozhraní API pro dotazy a do syntaxe výrazu časové řady, [Vyberte](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




