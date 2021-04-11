---
title: Kurz – použití Azure Time Series Insights k ukládání a analýze telemetrie vašich zařízení technologie Plug and Play Azure IoT
description: Kurz – nastavení Time Series Insights prostředí a připojení centra IoT pro zobrazení a analýzu telemetrie ze zařízení technologie Plug and Play IoT.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28cda9fb6997500f6cd7c4c4349635e7b7a36398
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504273"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Kurz: vytvoření a konfigurace prostředí Time Series Insights Gen2

V tomto kurzu se naučíte, jak vytvořit a nakonfigurovat prostředí [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) pro integraci s řešením technologie Plug and Play IoT. Pomocí Time Series Insights můžete shromažďovat, zpracovávat, ukládat, dotazovat a vizualizovat data časových řad v rozsahu Internet věcí (IoT).

V tomto kurzu jste:

> [!div class="checklist"]
> * Zřízení Time Series Insights prostředí a připojení služby IoT Hub jako zdroje událostí streamování.
> * Pracujte pomocí synchronizace modelů a vytvořte [model časových řad](../time-series-insights/concepts-model-overview.md).
> * Použijte ukázkové soubory modelu [DTDL (Digital Neal Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) , které jste použili pro kontrolku teploty a termostatem zařízení.

> [!NOTE]
> Tato integrace mezi Time Series Insights a IoT technologie Plug and Play je ve verzi Preview. Způsob, jakým se DTDL modely zařízení mapují na model časové řady Time Series Insights se může změnit.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

V tomto okamžiku máte následující:

* Azure IoT Hub.
* Instance služby Device Provisioning Service (DPS) propojená se službou IoT Hub. Instance DPS by měla mít individuální registraci zařízení technologie Plug and Play vaše zařízení IoT.
* Připojení ke službě IoT Hub ze zařízení s jednou komponentou nebo ze zařízení s více komponentami, které streamuje Simulovaná data.

Abyste se vyhnuli nutnosti nainstalovat rozhraní příkazového řádku Azure CLI místně, můžete k nastavení Cloud Services použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Příprava zdroje událostí

Služby IoT Hub, které jste vytvořili dříve, budou [zdrojem událostí](../time-series-insights/concepts-streaming-ingestion-event-sources.md)vašeho prostředí Time Series Insights.

> [!IMPORTANT]
> Zakažte všechny existující trasy IoT Hub. Došlo k známému problému s použitím služby IoT Hub s nakonfigurovaným [směrováním](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) . Dočasně zakažte všechny koncové body směrování. Když je služba IoT Hub připojená k Time Series Insights, můžete znovu povolit koncové body směrování.

Ve službě IoT Hub Vytvořte jedinečnou skupinu uživatelů, pro kterou Time Series Insights využívat. V následujícím příkladu nahraďte `my-pnp-hub` názvem IoT Hub, který jste použili dříve.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Volba ID služby Time Series

Při zřizování Time Series Insights prostředí musíte vybrat *ID časové řady*. Je důležité vybrat příslušné ID časové řady. Tato vlastnost je neměnná a po jejím nastavení ji nelze změnit. ID časové řady je jako klíč oddílu databáze. ID časové řady funguje jako primární klíč pro model časových řad. Další informace najdete v tématu [osvědčené postupy pro výběr ID časové řady](../time-series-insights/how-to-select-tsid.md).

Jako uživatel IoT technologie Plug and Play pro ID časové řady zadejte _složený klíč_ , který se skládá z `iothub-connection-device-id` a `dt-subject` . IoT Hub přidá tyto vlastnosti systému, které obsahují vaše ID zařízení technologie Plug and Play IoT a název vaší komponenty zařízení, v uvedeném pořadí.

I když vaše modely zařízení technologie Plug and Play IoT aktuálně nepoužívají komponenty, měli byste zahrnout `dt-subject` jako součást složeného klíče, abyste mohli komponenty v budoucnu používat. Vzhledem k tomu, že vaše ID časové řady není proměnlivé, Microsoft doporučuje tuto možnost Povolit pro případ, že ji budete potřebovat v budoucnu.

> [!NOTE]
> Příklady v tomto článku jsou pro zařízení s více komponentami `TemperatureController` . Ale koncepty jsou pro zařízení bez součásti stejné `Thermostat` .

## <a name="provision-your-time-series-insights-environment"></a>Zřízení Time Series Insightsho prostředí

Tato část popisuje, jak zřídit Azure Time Series Insights Gen2 prostředí.

Spusťte následující příkaz pro:

* Vytvořte účet úložiště Azure pro [chladírenský sklad](../time-series-insights/concepts-storage.md#cold-store)vašeho prostředí. Tento účet je určený pro dlouhodobé uchovávání a analýzu historických dat.
  * V kódu nahraďte `mytsicoldstore` jedinečným názvem pro účet studeného úložiště.
* Vytvořte prostředí Azure Time Series Insights Gen2. Prostředí se vytvoří s teplé úložiště, které má dobu uchování sedm dní. Účet studeného úložiště bude připojen k nekonečnému uchovávání.
  * V kódu nahraďte `my-tsi-env` jedinečným názvem pro vaše Time Series Insights prostředí.
  * V kódu nahraďte `my-pnp-resourcegroup` názvem skupiny prostředků, kterou jste použili při instalaci.
  * Vaše vlastnost ID časové řady je `iothub-connection-device-id, dt-subject` .

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

Připojte zdroj události IoT Hub. Nahraďte `my-pnp-resourcegroup` , `my-pnp-hub` a `my-tsi-env` hodnotami, které jste zvolili. Následující příkaz odkazuje na skupinu příjemců pro Time Series Insights, kterou jste vytvořili dříve:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

V [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků a pak vyberete své nové Time Series Insights prostředí. Přejít na **adresu URL aplikace Time Series Insights Explorer** zobrazená v přehledu instance:

![Snímek obrazovky stránky s přehledem portálu](./media/tutorial-configure-tsi/view-environment.png)

V Průzkumníkovi vidíte tři instance:

* &lt;ID zařízení PnP &gt; , thermostat1
* &lt;ID zařízení PnP &gt; , thermostat2
* &lt;ID zařízení PnP &gt; , `null`

> [!NOTE]
> Třetí značka představuje telemetrii od `TemperatureController` sebe sama, například pracovní sadu paměti zařízení. Vzhledem k tomu, že se jedná o vlastnost nejvyšší úrovně, hodnota pro název komponenty je null. V pozdějším kroku uděláte tento název uživatelsky přívětivější.

![Snímek obrazovky zobrazující tři instance v Průzkumníkovi](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Konfigurace překladu modelu

Dále přeložíte model zařízení DTDL na model Asset Service v Azure Time Series Insights. V Time Series Insights model časové řady je nástroj pro sémantiku modelování pro kontext dat. Model má tři základní komponenty:

* [Instance modelu časové řady](../time-series-insights/concepts-model-overview.md#time-series-model-instances) představují virtuální reprezentace časových řad. Instance se jednoznačně identifikují pomocí ID vaší časové řady.
* [Hierarchie modelů časových řad](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) organizují instance zadáním názvů vlastností a jejich vztahů.
* [Typy modelů časových řad](../time-series-insights/concepts-model-overview.md#time-series-model-types) vám pomůžou definovat [proměnné](../time-series-insights/concepts-variables.md) nebo vzorce pro výpočty. Typy jsou přidruženy k určité instanci.

### <a name="define-your-types"></a>Definování typů

Můžete začít ingestovat data do Azure Time Series Insights Gen2 bez předdefinovaného modelu. Když telemetrie dorazí, Time Series Insights se pokusí automaticky vyřešit instance časových řad na základě hodnot vlastností ID časových řad. Všem instancím je přiřazen *výchozí typ*. Je nutné ručně vytvořit nový typ pro správné kategorizaci instancí.

Následující podrobnosti popisují nejjednodušší způsob synchronizace DTDL modelů zařízení s typy modelu časové řady:

* Váš identifikátor digitálního zdvojeného modelu se bude vaším ID typu.
* Název typu může být buď název modelu, nebo zobrazovaný název.
* Popis modelu se stal popisem typu.
* Pro každou telemetrii, která má číselné schéma, je vytvořena alespoň jedna proměnná typu.
  * Pro proměnné lze použít pouze číselné datové typy, ale pokud je hodnota odeslána jako jiný typ, který lze převést, `"0"` například můžete použít funkci [převodu](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) , jako je například `toDouble` .
* Název proměnné může být buď název telemetrie, nebo zobrazovaný název.
* Při definování proměnné výrazu časové řady se podívejte na název telemetrie na lince a na datový typ telemetrie.

| DTDL JSON | JSON typu modelu časové řady | Příklad hodnoty |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |
|`contents` skupin| `variables` předmětů  | Prohlédněte si následující příklad.

![Snímek obrazovky znázorňující typ modelu časové řady D T D L.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Tento příklad ukazuje tři proměnné, ale každý typ může mít až 100 proměnných. Různé proměnné mohou odkazovat na stejnou hodnotu telemetrie za účelem provádění různých výpočtů podle potřeby. Úplný seznam filtrů, agregace a skalárních funkcí najdete v tématu [Time Series Insights syntaxi výrazu Gen2 Time Series](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Otevřete textový editor a uložte následující JSON na místní disk.

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

V Průzkumníku Time Series Insights vyberte ikonu modelu na levé straně a otevřete kartu **model** . Vyberte **typy** a pak vyberte **nahrát JSON**:

![Snímek obrazovky ukazující, jak nahrát JSON](./media/tutorial-configure-tsi/upload-type.png)

Vyberte **zvolit soubor**, vyberte JSON, který jste předtím uložili, a pak vyberte **nahrát**.

Zobrazí se nově definovaný typ **teplotního adaptéru** .

### <a name="create-a-hierarchy"></a>Vytvoření hierarchie

Vytvořte hierarchii pro uspořádání značek pod jejich `TemperatureController` nadřazeným prvkem. Následující jednoduchý příklad má jedinou úroveň, ale řešení IoT obvykle mají mnoho úrovní vnoření do dát značek v rámci své fyzické a sémantické polohy v rámci organizace.

Vyberte **hierarchie** a pak vyberte **Přidat hierarchii**. Jako název zadejte *loďstvo zařízení*. Vytvořte jednu úroveň nazvanou *název zařízení*. Pak vyberte **Uložit**.

![Snímek obrazovky ukazující, jak přidat hierarchii](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Přiřadit instance ke správnému typu

Dále změníte typ instancí a umístíte je do hierarchie.

Vyberte kartu **instance** . Najděte instanci, která představuje pracovní sadu zařízení, a pak vyberte ikonu **Upravit** na pravé straně.

![Snímek obrazovky znázorňující úpravu instance](./media/tutorial-configure-tsi/edit-instance.png)

Otevřete rozevírací nabídku **typ** a pak vyberte možnost **kontroler teploty**. Pokud chcete aktualizovat název instance, která představuje všechny značky nejvyšší úrovně přidružené k vašemu zařízení *, <your device name> Zadejte defaultComponent* .

![Snímek obrazovky znázorňující změnu typu instance](./media/tutorial-configure-tsi/change-type.png)

Než vyberete **Save (Uložit**), nejdřív vyberte kartu **pole instance** a pak vyberte **flotila zařízení**. Pokud chcete telemetrii seskupit společně, zadejte *\<your device name> -dočasná kontroler*. Pak vyberte **Uložit**.

![Snímek obrazovky ukazující, jak přiřadit instanci k hierarchii](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Zopakováním předchozích kroků přiřaďte značky termostatu správnému typu a hierarchii.

## <a name="view-your-data"></a>Zobrazení dat

Vraťte se do podokna pro vytváření grafů a rozbalte položku **loďstva zařízení** > vaše zařízení. Vyberte **thermostat1**, vyberte proměnnou **teploty** a pak vyberte **Přidat** , aby se hodnota grafu vybrala. Proveďte totéž pro **thermostat2** a hodnotu workingSet **defaultComponent**  .

![Snímek obrazovky znázorňující změnu typu instance pro thermostat2.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> Další informace o různých možnostech grafů, včetně velikosti intervalu a ovládacích prvků osy y, najdete v tématu [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).
