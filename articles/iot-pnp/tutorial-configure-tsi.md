---
title: Použití Time Series Insights k ukládání a analýze telemetrie zařízení technologie Plug and Play Azure IoT | Microsoft Docs
description: Nastavením Time Series Insights prostředí a připojením centra IoT můžete zobrazit a analyzovat telemetrii ze zařízení technologie Plug and Play IoT.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ca2319a78fb4c0c720a21e97944d5b75ada9d008
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014986"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Kurz verze Preview: vytvoření a připojení k Time Series Insights Gen2 k ukládání, vizualizaci a analýze telemetrie technologie Plug and Play zařízení IoT

V tomto kurzu se naučíte, jak vytvořit a správně nakonfigurovat prostředí [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) pro integraci s řešením technologie Plug and Play IoT. Pomocí TSI můžete shromažďovat, zpracovávat, ukládat, dotazovat a vizualizovat data časových řad ve Internet věcí (IoT) škálování.

Nejdřív zřizujete prostředí TSI a připojíte své centrum IoT jako zdroj událostí streamování. Pak provedete synchronizaci modelů a vytvořte [model časových řad](../time-series-insights/concepts-model-overview.md) založený na ukázkových souborech modelů [DTDL (Digital-The Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) , které jste použili pro kontrolku teploty a termostatem.

> [!NOTE]
> Tato integrace je ve verzi Preview. Způsob mapování modelů zařízení DTDL na model časové řady se může změnit.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

V tomto okamžiku máte následující:

* Azure IoT Hub.
* Instance DPS propojená se službou IoT Hub s jednotlivými registracemi zařízení pro vaše zařízení technologie Plug and Play IoT.
* Připojení ke službě IoT Hub ze zařízení s jedním nebo více součástmi, což je simulované streamovaná data.

Abyste se vyhnuli nutnosti nainstalovat rozhraní příkazového řádku Azure CLI místně, můžete k nastavení Cloud Services použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Příprava zdroje událostí

Služby IoT Hub, které jste vytvořili dříve, budou [zdrojem událostí](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)vašeho prostředí TSI.

> [!IMPORTANT]
> Zakažte všechny existující trasy IoT Hub. Při použití služby IoT Hub jako zdroje událostí TSI s nakonfigurovaným [směrováním](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) se jedná o známý problém. Dočasně zakažte všechny koncové body směrování a když je vaše služba IoT Hub připojená k TSI, můžete je znovu povolit.

Vytvořte ve službě IoT Hub jedinečnou skupinu uživatelů, kterou chcete využít pro TSI. Nahraďte `my-pnp-hub` názvem centra IoT, které jste použili dříve:

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Zvolit ID časové řady

Při zřizování prostředí TSI musíte vybrat *ID časové řady*. Je důležité vybrat příslušné ID časové řady, protože tato vlastnost je neměnná a po jejím nastavení ji nejde změnit. ID časové řady je jako klíč oddílu databáze. ID časové řady funguje jako primární klíč pro model časových řad. Další informace najdete v tématu [osvědčené postupy pro výběr ID časové řady](../time-series-insights/how-to-select-tsid.md).

Jako uživatel IoT technologie Plug and Play zadejte _složený klíč_ , který se skládá z `iothub-connection-device-id` a `dt-subject` jako ID vaší časové řady. IoT Hub přidá tyto vlastnosti systému, které obsahují vaše ID zařízení technologie Plug and Play IoT, a názvy součástí zařízení, v uvedeném pořadí.

I když vaše modely zařízení technologie Plug and Play IoT aktuálně nepoužívají komponenty, měli byste zahrnout `dt-subject` jako součást složeného klíče, abyste je mohli použít v budoucnu. Vzhledem k tomu, že vaše ID časové řady není proměnlivé, Microsoft doporučuje tuto možnost Povolit pro případ, že ji budete potřebovat v budoucnu.

> [!NOTE]
> Níže uvedené příklady jsou určené pro **TemperatureController** zařízení s více komponentami, ale koncepty jsou pro zařízení **termostatu** bez komponent stejné.

## <a name="provision-your-tsi-environment"></a>Zřízení prostředí TSI

Tato část popisuje, jak zřídit Azure Time Series Insights Gen2 prostředí.

Následující příkaz:

* Vytvoří účet služby Azure Storage pro [chladírenský sklad](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)vašeho prostředí, který je určený pro dlouhodobé uchovávání a analýzu prostřednictvím historických dat.
  * Nahraďte `mytsicoldstore` jedinečným názvem pro účet studeného úložiště.
* Vytvoří prostředí Azure Time Series Insights Gen2, včetně teplého úložiště s dobou uchování 7 dní, a studeným úložištěm pro nekonečné uchovávání.
  * Nahraďte `my-tsi-env` jedinečným názvem pro prostředí TSI.
  * Nahraďte `my-pnp-resourcegroup` názvem skupiny prostředků, kterou jste použili během nastavování.
  * `iothub-connection-device-id, dt-subject` je vaše vlastnost ID časové řady.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Připojte zdroj události IoT Hub. Nahraďte `my-pnp-resourcegroup` , `my-pnp-hub` a `my-tsi-env` hodnotami, které jste zvolili. Následující příkaz odkazuje na skupinu uživatelů pro TSI, kterou jste vytvořili dříve:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Přejděte do skupiny prostředků v [Azure Portal](https://portal.azure.com) a vyberte své nové Time Series Insights prostředí. Přejděte na *adresu URL aplikace Time Series Insights Explorer* zobrazená v přehledu instance:

![Stránka s přehledem portálu](./media/tutorial-configure-tsi/view-environment.png)

V Průzkumníkovi vidíte tři instance:

* &lt;ID zařízení PnP &gt; , thermostat1
* &lt;ID zařízení PnP &gt; , thermostat2
* &lt;ID zařízení PnP &gt; , `null`

> [!NOTE]
> Třetí značka představuje telemetrii ze samotné **TemperatureController** , jako je například pracovní sada paměti zařízení. Vzhledem k tomu, že se jedná o vlastnost nejvyšší úrovně, hodnota pro název komponenty je null. V pozdějším kroku tuto hodnotu aktualizujete na více uživatelsky přívětivého názvu.

![Průzkumník – zobrazení 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurace překladu modelu

Dále přeložíte model zařízení DTDL na model Asset Service v Azure Time Series Insights (TSI). Model časové řady TSI je sémantický nástroj modelování pro kontexty dat v rámci TSI. Model časové řady má tři základní komponenty:

* [Instance modelu časové řady](../time-series-insights/concepts-model-overview.md#time-series-model-instances). Instance jsou virtuální reprezentace samotné časové řady. Instance se jednoznačně identifikují pomocí ID vaší časové řady.
* [Hierarchie modelů časových řad](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). Hierarchie organizují instance zadáním názvů vlastností a jejich vztahů.
* [Typy modelů časových řad](../time-series-insights/concepts-model-overview.md#time-series-model-types). Typy umožňují definovat [proměnné](../time-series-insights/concepts-variables.md) nebo vzorce pro výpočty. Typy jsou přidruženy k určité instanci.

### <a name="define-your-types"></a>Definování typů

Můžete začít ingestovat data do Azure Time Series Insights Gen2 bez předdefinovaného modelu. Když telemetrie dorazí, pokusí se TSI automaticky vyřešit instance časových řad na základě vašich hodnot vlastností ID časové řady. Všem instancím je přiřazen *výchozí typ*. Je nutné ručně vytvořit nový typ pro správné kategorizaci instancí. Následující podrobnosti znázorňují nejjednodušší způsob, jak synchronizovat modely DTDL zařízení s typy modelu časové řady:

* Váš identifikátor digitálního zdvojeného modelu se bude vaším ID typu.
* Název typu může být buď název modelu, nebo zobrazovaný název.
* Popis modelu se stal popisem typu.
* Pro každou telemetrii s číselným schématem je vytvořena alespoň jedna proměnná typu.
  * Pro proměnné lze použít pouze číselné datové typy, ale pokud je hodnota odeslána jako jiný typ, který lze převést, `"0"` například můžete použít funkci [převodu](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions) , jako je například `toDouble` .
* Název proměnné může být buď název telemetrie, nebo zobrazovaný název.
* Při definování výrazu proměnné časové řady se podívejte na název telemetrie na lince a datový typ.

| DTDL JSON | JSON typu modelu časové řady | Příklad hodnoty |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` skupin| `variables` předmětů  | Zobrazit níže uvedený příklad

![Typ modelu DTDL na časové řady](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Tento příklad ukazuje tři proměnné, ale každý typ může mít až 100. Různé proměnné mohou odkazovat na stejnou hodnotu telemetrie za účelem provádění různých výpočtů podle potřeby. Úplný seznam filtrů, agregace a skalárních funkcí najdete v tématu [Time Series Insights syntaxi výrazu Gen2 Time Series](/rest/api/time-series-insights/reference-time-series-expression-syntax.md).

Otevřete textový editor a uložte následující JSON na místní disk:

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

V Průzkumníku Time Series Insights přejděte na kartu **model** tak, že na levé straně vyberete ikonu modelu. Vyberte **typy** a pak vyberte **nahrát JSON**:

![Nahrávání](./media/tutorial-configure-tsi/upload-type.png)

Vyberte **zvolit soubor**, vyberte JSON, který jste předtím uložili, a pak vyberte **nahrát** .

Zobrazí se nově definovaný typ **teplotního adaptéru** .

### <a name="create-a-hierarchy"></a>Vytvoření hierarchie

Vytvořte hierarchii pro uspořádání značek pod jejich **TemperatureController** nadřazeným objektem. Následující jednoduchý příklad má jedinou úroveň, ale řešení IoT obvykle mají mnoho úrovní vnoření do dát značek v rámci své fyzické a sémantické polohy v rámci organizace.

Vyberte **hierarchie** a vyberte **Přidat hierarchii**. Jako název zadejte *loďstvo zařízení* a vytvořte jednu úroveň s názvem *zařízení*. Pak vyberte **Uložit**.

![Přidat hierarchii](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Přiřadit instance ke správnému typu

Dále změníte typ instancí a umístíte je do hierarchie.

Vyberte kartu **instance** , najděte instanci, která představuje pracovní sadu zařízení, a vyberte ikonu **Upravit** na pravé straně:

![Upravit instance](./media/tutorial-configure-tsi/edit-instance.png)

Vyberte rozevírací seznam **typ** a vyberte možnost **kontroler teploty**. Pokud chcete aktualizovat název instance, která představuje všechny značky nejvyšší úrovně přidružené k vašemu zařízení *, <your device name> Zadejte defaultComponent* .

![Změnit typ instance](./media/tutorial-configure-tsi/change-type.png)

Než vyberete Save (Uložit), vyberte kartu **pole instance** a zaškrtněte pole **loďstvo zařízení** . Zadejte `<your device name> - Temp Controller` pro seskupení telemetrie dohromady a pak vyberte **Uložit**.

![Přiřadit k hierarchii](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Zopakováním předchozích kroků přiřaďte značky termostatu správnému typu a hierarchii.

## <a name="view-your-data"></a>Zobrazení dat

Přejděte zpátky do podokna pro vytváření grafů a rozbalte **> zařízení do loďstva zařízení**. Vyberte **thermostat1**, vyberte proměnnou **teploty** a pak vyberte **Přidat** , aby se hodnota grafu vybrala. Proveďte totéž pro **thermostat2** a hodnotu workingSet **defaultComponent** **workingSet** .

![Změnit typ instance pro thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Další kroky

* Další informace o různých možnostech grafů, včetně velikosti intervalu a ovládacích prvků osy Y, najdete v tématu [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).

* Podrobný přehled modelu časových řad vašeho prostředí najdete v tématu [model služby Time Series v článku Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md) .

* Pokud se chcete podrobně do rozhraní API pro dotazy a syntaxí výrazu časové řady, přečtěte si téma [rozhraní API pro dotazy na Azure Time Series Insights Gen2](/rest/api/time-series-insights/reference-query-apis.md).
