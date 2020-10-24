---
title: Integrace se službou Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit směrování událostí z digitálních vláken Azure na Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 58d101bb93b4635e362c5ec78a03a659b71b63da
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495279"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrace digitálních vláken Azure s Azure Time Series Insights

V tomto článku se dozvíte, jak integrovat digitální vlákna Azure pomocí [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md).

Řešení popsané v tomto článku vám umožní shromáždit a analyzovat historické údaje o řešení IoT. Digitální vlákna Azure je skvělým způsobem, který umožňuje zasílat data do Time Series Insights, protože umožňuje korelovat více datových proudů a standardizovat informace před jejich odesláním do Time Series Insights. 

## <a name="prerequisites"></a>Předpoklady

Než budete moct nastavit relaci s Time Series Insights, musíte mít **instanci digitálního vlákna Azure**. Tato instance by měla být nastavená na možnost aktualizovat digitální informace na základě dat, protože je potřeba aktualizovat zdvojené informace několikrát, aby se zobrazila data sledovaná v Time Series Insights. 

Pokud toto nastavení ještě nemáte, můžete ho vytvořit pomocí kurzu digitálních vláken Azure [*: Připojte ucelené řešení*](./tutorial-end-to-end.md). Tento kurz vás provede nastavením instance digitálního vlákna Azure, která bude fungovat s virtuálním zařízením IoT, aby aktivovala aktualizace digitálních vláken.

## <a name="solution-architecture"></a>Architektura řešení

Pomocí níže uvedené cesty budete připojeni Time Series Insights k digitálním Vlákenám Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Vytvoření trasy a filtru pro dvojitá oznámení o aktualizacích

Instance digitálních vláken Azure mohou generovat [události s dvojitou aktualizací](how-to-interpret-event-data.md) pokaždé, když se aktualizuje stav vlákna. V této části vytvoříte [**trasu událostí**](concepts-route-events.md) digitálních vláken Azure, která bude tyto události aktualizace směrovat na Azure [Event Hubs](../event-hubs/event-hubs-about.md) pro další zpracování.

Kurz digitálních vláken Azure [*: připojení uceleného řešení*](./tutorial-end-to-end.md) projde scénářem, kde se k aktualizaci atributu teploty na digitálním vlákna představujícím místnost používá teploměr. Tento model spoléhá na nepřesné aktualizace a nepředávací telemetrii ze zařízení IoT, což vám poskytne flexibilitu při změně podkladového zdroje dat, aniž by bylo potřeba aktualizovat logiku Time Series Insights.

1. Nejdřív vytvořte obor názvů centra událostí, který bude přijímat události z instance digitálního vlákna Azure. Můžete buď použít níže uvedené pokyny pro Azure CLI, nebo použít Azure Portal: [*rychlý Start: vytvoření centra událostí pomocí Azure Portal*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Vytvořte centrum událostí v rámci oboru názvů.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Vytvořte [autorizační pravidlo](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) s oprávněními Odeslat a přijmout.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Vytvořte [koncový bod](concepts-route-events.md#create-an-endpoint) digitálních vláken Azure, který propojuje centrum událostí s instancí digitálních vláken Azure.

    ```azurecli-interactive
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Vytvořte [trasu](concepts-route-events.md#create-an-event-route) v rámci digitálních vláken Azure, která odešle do koncového bodu události s dvojitou aktualizací. Filtr v této trase umožní předat do koncového bodu pouze zprávy s dvojitou aktualizací.

    >[!NOTE]
    >V současnosti se jedná o **známý problém** v Cloud Shell ovlivňují tyto skupiny příkazů: `az dt route` , `az dt model` , `az dt twin` .
    >
    >Chcete-li tento problém vyřešit, buď spusťte `az login` v Cloud Shell před spuštěním příkazu, nebo použijte [místní CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) místo Cloud Shell. Další podrobnosti najdete v tématu [*řešení potíží: známé problémy v Azure Digital revláken*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Než začnete pracovat, poznamenejte si *obor názvů Event Hubs* a *skupinu prostředků*, protože je budete později používat k vytvoření dalšího centra událostí dále v tomto článku.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure 

V dalším kroku vytvoříte funkci aktivovanou Event Hubs v rámci aplikace Function App. Můžete použít aplikaci Function App vytvořenou v rámci kompletního kurzu ([*kurz: připojení k*](./tutorial-end-to-end.md)kompletnímu řešení) nebo vlastní. 

Tato funkce převede tyto události s dvojitou aktualizací z původního formátu jako dokumenty JSON patch na objekty JSON obsahující pouze aktualizované a přidané hodnoty z vašich vláken.

Další informace o použití Event Hubs s Azure Functions najdete v tématu [*Trigger Azure Event Hubs pro Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

V rámci vaší publikované aplikace Function App nahraďte kód funkce následujícím kódem.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                {
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
                }
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

Odsud pak funkce pošle objekty JSON, které vytvoří, do druhého centra událostí, ke kterým se připojíte Time Series Insights.

Později nanastavíte také některé proměnné prostředí, které tato funkce použije pro připojení k vlastním centrům událostí.

## <a name="send-telemetry-to-an-event-hub"></a>Odeslání telemetrie do centra událostí

Nyní vytvoříte druhé centrum událostí a nakonfigurujete funkci pro streamování výstupu do tohoto centra událostí. Toto centrum událostí pak bude připojené k Time Series Insights.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Pokud chcete vytvořit druhé centrum událostí, můžete použít níže uvedené pokyny k rozhraní příkazového řádku Azure CLI nebo použít Azure Portal: [*rychlý Start: vytvoření centra událostí pomocí Azure Portal*](../event-hubs/event-hubs-create.md).

1. Příprava *oboru názvů Event Hubs* a názvu *skupiny prostředků* z výše uvedeného článku

2. Vytvořit nové centrum událostí
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Vytvoření [autorizačního pravidla](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) s oprávněními Odeslat a přijmout
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Konfigurace funkce

V dalším kroku budete muset ve své aplikaci Function App nastavovat proměnné prostředí z dříve, které obsahují připojovací řetězce pro centra událostí, která jste vytvořili.

### <a name="set-the-twins-event-hub-connection-string"></a>Nastavení připojovacího řetězce centra událostí s dvojitými událostmi

1. Pomocí autorizačních pravidel, která jste vytvořili výše pro centrum vláken, Získejte [připojovací řetězec centra událostí](../event-hubs/event-hubs-get-connection-string.md).

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Použijte připojovací řetězec, který získáte jako výsledek vytvoření nastavení aplikace ve vaší aplikaci Function App, které obsahuje připojovací řetězec:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Nastavit připojovací řetězec centra událostí Time Series Insights

1. Pomocí autorizačních pravidel, která jste vytvořili výše pro centrum Time Series Insights, Získejte [připojovací řetězec centra událostí](../event-hubs/event-hubs-get-connection-string.md):

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Ve vaší aplikaci Function App vytvořte nastavení aplikace, které obsahuje připojovací řetězec:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Vytvoření a připojení instance Time Series Insights

V dalším kroku nastavíte instanci Time Series Insights pro příjem dat z druhého centra událostí. Postupujte podle následujících kroků a podrobnější informace o tomto procesu najdete v tématu [*kurz: nastavení Azure Time Series Insights Gen2 PAYG Environment*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. V Azure Portal Začněte vytvářet prostředek Time Series Insights. 
    1. Vyberte cenovou úroveň **PAYG (Preview)** .
    2. Pro toto prostředí budete muset zvolit **ID časové řady** . Vaše ID časové řady může mít až tři hodnoty, které použijete k hledání vašich dat v Time Series Insights. Pro tento kurz můžete použít **$dtId**. Přečtěte si další informace o výběru hodnoty ID v tématu [*osvědčené postupy pro výběr ID časové řady*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights":::

2. Vyberte **Další: zdroj události** a vyberte Event Hubs informace výše. Budete také muset vytvořit novou Event Hubs skupinu uživatelů.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Zahájení odesílání dat IoT do digitálních vláken Azure

Pokud chcete začít odesílat data do Time Series Insights, budete muset začít aktualizovat digitální vlastnosti v digitálních provlastnostech Azure pomocí změny hodnot dat. Použijte příkaz [AZ DT s dvojitou aktualizací](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) .

Pokud používáte kompletní kurz ([*kurz: připojení kompletního řešení*](tutorial-end-to-end.md)), které vám pomůže s nastavením prostředí, můžete začít odesílat Simulovaná data IoT spuštěním projektu *DeviceSimulator* z ukázky. Pokyny najdete v části [*Konfigurace a spuštění simulace*](tutorial-end-to-end.md#configure-and-run-the-simulation) v tomto kurzu.

## <a name="visualize-your-data-in-time-series-insights"></a>Vizualizace dat v Time Series Insights

Nyní by data měla být předávána do instance Time Series Insights, která je připravena k analýze. Pomocí následujících kroků můžete prozkoumat data přicházející v.

1. Otevřete instanci Time Series Insights v [Azure Portal](https://portal.azure.com) (můžete vyhledat název instance na panelu hledání na portálu). Přejděte na *adresu URL aplikace Time Series Insights Explorer* zobrazená v přehledu instance.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights":::

2. V Průzkumníkovi se zobrazí vaše tři vlákna z digitálních vláken Azure, která se zobrazují vlevo. Vyberte _**thermostat67**_, vyberte **teplotu**a stiskněte **Přidat**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights":::

3. Nyní byste měli vidět počáteční čtení teploty z termostatu, jak je znázorněno níže. Tato stejná teplota se aktualizuje pro *room21* a *floor1*a tyto datové proudy můžete vizualizovat společně.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights":::

4. Pokud povolíte, aby simulace běžela mnohem déle, vizualizace bude vypadat přibližně takto:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights":::

## <a name="next-steps"></a>Další kroky

Digitální vlákna jsou ve výchozím nastavení ukládána jako Plochá hierarchie v Time Series Insights, ale lze je rozšířit pomocí informací o modelu a víceúrovňové hierarchie pro organizaci. Další informace o tomto procesu najdete v článku: 

* [*Kurz: definování a použití modelu*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Můžete napsat vlastní logiku, která automaticky poskytne tyto informace pomocí modelu a dat grafu, která jsou již uložena v rámci digitálních vláken Azure. Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících odkazech:

* [*Postupy: Správa digitálního vlákna*](./how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](./how-to-query-graph.md)