---
title: Integrace s Azure Maps
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak vytvořit funkci Azure, která může použít oznámení o vytvářené grafice a digitální vlákna Azure k aktualizaci Azure Maps vnitřní mapy.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: f6c6c1cfdfef864be17adfed2d115150c4fbede0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045121"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Použití digitálních vláken Azure k aktualizaci mapy vnitřních Azure Maps

Tento článek vás provede kroky potřebnými k aktualizaci dat, která se zobrazují na *mapě interiéru* pomocí nástroje [Azure Maps](../azure-maps/about-azure-maps.md), pomocí dat digitálních vláken Azure. Služba Azure Digital autoformaes ukládá graf vašich vztahů zařízení IoT a směruje telemetrii do různých koncových bodů a díky tomu představuje ideální službu pro aktualizaci informačních překryvů na mapách.

Tento postup se zabývá těmito postupy:

1. Konfigurace instance digitálního vlákna Azure pro posílání dvojitě aktualizovaných událostí do funkce v [Azure Functions](../azure-functions/functions-overview.md).
2. Vytvoření funkce Azure, která aktualizuje Azure Maps funkce vnitřních map stateset
3. Jak ukládat ID map a ID stateset funkcí do grafu digitálních vláken Azure

### <a name="prerequisites"></a>Požadované součásti

* Postupujte podle kurzu digitálních vláken Azure [*: připojení kompletního řešení*](./tutorial-end-to-end.md).
    * Tuto dvojitou cestu rozšíříte pomocí dalšího koncového bodu a trasy. Z tohoto kurzu taky přidáte další funkci do aplikace Function App. 
* Postupujte podle Azure Maps [*kurzu: pomocí nástroje Azure Maps Creator Vytvořte vnitřní*](../azure-maps/tutorial-creator-indoor-maps.md) mapy a vytvořte Azure Mapsou vnitřní mapu s *funkcí stateset*.
    * [Funkce statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) jsou kolekce dynamických vlastností (stavů) přiřazených k funkcím datové sady, jako jsou místnosti nebo vybavení. V tomto kurzu Azure Maps ukládá funkce stateset stav místnosti, který budete zobrazovat na mapě.
    * Budete potřebovat *STATESET ID* vaší funkce Azure Maps a *ID předplatného*.

### <a name="topology"></a>Topologie

Následující obrázek znázorňuje, kde se prvky integrace vnitřních map v tomto kurzu vejdou do většího, uceleného scénáře digitálních vláken Azure.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Zobrazení služeb Azure v rámci uceleného scénáře – zvýraznění části pro integraci vnitřních map" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Vytvoření funkce pro aktualizaci mapy při aktualizaci dvojitých vláken

Nejdřív vytvoříte trasu v části digitální vlákna Azure, která bude předávat všechny události s dvojitou aktualizací do tématu Event Grid. Pak použijete funkci Azure ke čtení těchto zpráv aktualizace a aktualizujete stateset funkcí v Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Vytvoření trasy a filtru pro dvojitá oznámení o aktualizacích

Instance digitálních vláken Azure mohou generovat události s dvojitou aktualizací pokaždé, když se aktualizuje stav vlákna. Kurz k digitálním vazbám Azure [*: připojení kompletního řešení*](./tutorial-end-to-end.md) propojeného výše projde scénářem, kdy se k aktualizaci atributu teploty připojeného ke zdvojení místnosti používá teploměr. Toto řešení rozšíříte tak, že se přihlásíte k odběru oznámení o aktualizacích pro vlákna a tyto informace použijete k aktualizaci vašich map.

Tento model čte přímo z místnosti místo zařízení IoT, což vám dává flexibilitu při změně podkladového zdroje dat na teplotu, aniž by bylo potřeba aktualizovat logiku mapování. Můžete například přidat více teploměrů nebo nastavit tuto místnost pro sdílení teploměru s jinou místností, a to vše bez nutnosti aktualizovat logiku mapy.

1. Vytvořte téma Event gridu, které bude přijímat události z instance digitálního vlákna Azure.
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Vytvořte koncový bod, který propojí téma Event Grid s digitálními podsítěmi Azure.
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Vytvořte trasu v rámci digitálních vláken Azure, která odešle do koncového bodu události s dvojitou aktualizací.

    >[!NOTE]
    >V současnosti se jedná o **známý problém** v Cloud Shell ovlivňují tyto skupiny příkazů: `az dt route` , `az dt model` , `az dt twin` .
    >
    >Chcete-li tento problém vyřešit, buď spusťte `az login` v Cloud Shell před spuštěním příkazu, nebo použijte [místní CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) místo Cloud Shell. Další podrobnosti najdete v tématu [*řešení potíží: známé problémy v Azure Digital revláken*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>Vytvoření funkce Azure pro aktualizaci map

Chystáte se vytvořit funkci aktivovanou Event Grid v rámci aplikace Function App z kompletního kurzu ([*kurz: připojení kompletního řešení*](./tutorial-end-to-end.md)). Tato funkce rozbalí tato oznámení a pošle aktualizace Azure Maps funkcím stateset k aktualizaci teploty jedné místnosti. 

V následujícím dokumentu najdete referenční informace: [*Azure Event Grid Trigger pro Azure Functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Kód funkce nahraďte následujícím kódem. Odfiltruje pouze aktualizace vláken na prostor, přečte aktualizovanou teplotu a odešle tyto informace do Azure Maps.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in your map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

Ve své aplikaci Function App budete muset nastavit dvě proměnné prostředí. Jedním z nich je váš [Azure Maps primární klíč předplatného](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)a jedna je vaše [Azure Maps ID stateset](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>Zobrazit živé aktualizace na mapě

Pokud chcete vidět živou aktualizaci, postupujte podle následujících kroků:

1. Začněte posílat Simulovaná data IoT spuštěním projektu **DeviceSimulator** z kurzu digitálních vláken Azure [*: Připojte ucelené řešení*](tutorial-end-to-end.md). Pokyny k tomuto postupu najdete v části [*Configure and run the simulace*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Pomocí [modulu **Azure Maps interiéru** ](../azure-maps/how-to-use-indoor-module.md) můžete vykreslit vaše vnitřní mapy vytvořené v programu Azure Maps Creator.
    1. Zkopírujte kód HTML z [*příkladu: použijte modul vnitřních map*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) v kurzu pro mapy vnitřních souborů [*: použijte modul Azure Mapsch vnitřních map*](../azure-maps/how-to-use-indoor-module.md) k místnímu souboru.
    1. Nahraďte *tilesetId* a *statesetID* v místním souboru HTML hodnotami.
    1. Otevřete tento soubor v prohlížeči.

Oba vzorky posílají teplotu v kompatibilním rozsahu, takže by se měla na mapě zobrazit barva místnosti 121 aktualizace každých 30 sekund.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Zobrazení služeb Azure v rámci uceleného scénáře – zvýraznění části pro integraci vnitřních map":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Ukládání informací o mapách do digitálních vláken Azure

Teď, když máte pevně zakódované řešení pro aktualizaci informací o mapách, můžete použít graf digitálních vláken Azure k uložení všech informací potřebných pro aktualizaci vaší vnitřní mapy. To by zahrnovalo ID stateset, mapování ID předplatného a ID funkcí jednotlivých map a umístění. 

Řešení pro tento konkrétní příklad by zahrnovalo aktualizaci každého prostoru na nejvyšší úrovni tak, aby měl ID stateset a mapování ID předplatného, a aktualizace každé místnosti tak, aby měla ID funkce. Tyto hodnoty byste museli nastavit jednou při inicializaci vyzdvojeného grafu a pak tyto hodnoty dotazovat pro každou událost s dvojitou aktualizací.

V závislosti na konfiguraci topologie budete moct uložit tyto tři atributy na různých úrovních, které korelují s členitosti mapy.

## <a name="next-steps"></a>Další kroky

Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících odkazech:

* [*Postupy: Správa digitálních vláken*](./how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](./how-to-query-graph.md)