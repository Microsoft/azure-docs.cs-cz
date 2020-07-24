---
title: Integrace s Time Series Insights
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit směrování událostí z digitálních vláken Azure na Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131595"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Integrujte digitální vlákna pomocí Azure Time Series Insights

V tomto odkazu se dozvíte, jak integrovat digitální vlákna Azure pomocí [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md). Toto řešení vám umožní shromažďovat a analyzovat historické údaje o řešení IoT. Digitální vlákna Azure je skvělým způsobem, který umožňuje zasílat data do Time Series Insights, protože umožňuje korelovat více datových proudů a standardizovat informace před jejich odesláním do Time Series Insights. 

## <a name="solution-architecture"></a>Architektura řešení

Pomocí níže uvedené cesty budete připojovat službu Time Series Insights k digitálním složkám Azure.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Zobrazení služeb Azure v rámci kompletního scénáře, zvýrazňování Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Předpoklady

* Potřebujete instanci digitálních vláken Azure, kterou můžete několikrát aktualizovat, aby se zobrazila data sledovaná v Time Series Insights. 
    * Pokud ho nemáte, postupujte podle kurzu digitálních vláken Azure [*: Připojte si ucelené řešení*](./tutorial-end-to-end.md) , abyste mohli vytvořit instanci digitálních vláken Azure a virtuální zařízení IoT pro generování zdvojených změn.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Vytvoření trasy a filtru pro dvojitá oznámení o aktualizacích

Instance digitálních vláken Azure mohou generovat [události s dvojitou aktualizací](how-to-interpret-event-data.md) pokaždé, když se aktualizuje stav vlákna. Vytvoříme trasu, která bude směrovat tyto události aktualizace do centra událostí pro další zpracování.

Kurz k digitálním [*událostem Azure: připojení uceleného řešení*](./tutorial-end-to-end.md) projde scénářem, kde se k aktualizaci atributu teploty připojeného k dvojitým událostem místnosti používá teploměr. Tento model používá nepřesné aktualizace, místo předávání telemetrie ze zařízení IoT, což vám dává flexibilitu při změně podkladového zdroje dat, aniž by bylo potřeba aktualizovat logiku Time Series Insights.

1. Vytvořte obor názvů centra událostí, který bude přijímat události z instance digitálního vlákna Azure. Můžete buď použít níže uvedené pokyny pro Azure CLI, nebo použít Azure Portal: [*rychlý Start: vytvoření centra událostí pomocí Azure Portal*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Vytvořte centrum událostí.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Vytvořte [autorizační pravidlo](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) s oprávněními Odeslat a přijmout.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Vytvořte koncový bod, který propojí téma Event Grid s digitálními podsítěmi Azure.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Vytvořte trasu v rámci digitálních vláken Azure, která odešle do koncového bodu události s dvojitou aktualizací. Filtr v této trase umožní předat do koncového bodu pouze zprávy s dvojitou aktualizací.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure 

V dalším kroku vytvoříte funkci aktivovanou Event Hubs v nové aplikaci Function App, aplikaci Function App z kompletního kurzu ([*kurz: připojení kompletního řešení*](./tutorial-end-to-end.md)). Tato funkce převede tyto aktualizace z původního formuláře jako dokumenty JSON patch na objekty JSON obsahující pouze aktualizované a přidané hodnoty z vašich vláken.

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
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
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

## <a name="send-telemetry-to-an-event-hub"></a>Odeslání telemetrie do centra událostí

Nyní vytvoříte druhé centrum událostí a nakonfigurujete funkci pro streamování výstupu do tohoto centra událostí. Toto centrum událostí pak bude připojené k Time Series Insights.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Můžete buď použít níže uvedené pokyny pro Azure CLI, nebo použít Azure Portal: [*rychlý Start: vytvoření centra událostí pomocí Azure Portal*](../event-hubs/event-hubs-create.md).

1. Připravte svůj obor názvů centra událostí a název skupiny prostředků z předchozích verzí. 

2. Vytvoření centra událostí
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. Vytvoření [autorizačního pravidla](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) s oprávněními Odeslat a přijmout
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>Konfigurace funkce

Ve své aplikaci Function App budete muset nastavit jednu proměnnou prostředí z dřívějších, která obsahuje připojovací řetězec centra událostí.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Nastavit připojovací řetězec centra událostí Time Series Insights

1. Získejte [připojovací řetězec centra událostí](../event-hubs/event-hubs-get-connection-string.md) pro autorizační pravidla, která jste vytvořili výše pro centrum Time Series Insights:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. Ve vaší aplikaci Function App vytvořte nastavení aplikace, které obsahuje připojovací řetězec:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Nastavení připojovacího řetězce centra událostí s dvojitými událostmi

1. Získejte [připojovací řetězec centra událostí](../event-hubs/event-hubs-get-connection-string.md) pro autorizační pravidla, která jste vytvořili výše pro centrum vláken.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Ve vaší aplikaci Function App vytvořte nastavení aplikace, které obsahuje připojovací řetězec:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Vytvoření a připojení instance Time Series Insights

V dalším kroku nastavíte instanci Time Series Insights pro příjem dat z druhého centra událostí. Další podrobnosti o tomto procesu najdete v tématu [ *kurz: nastavení Azure Time Series Insights Gen2 PAYG Environment.*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. V Azure Portal Začněte vytvářet prostředek Time Series Insights. 
    1. Vyberte cenovou úroveň **PAYG (Preview)** .
    2. Pro toto prostředí budete muset zvolit ID časové řady. Vaše ID časové řady může mít až tři hodnoty, které použijete k hledání vašich dat v Time Series Insights. Pro tento kurz můžete použít **$dtId**. Přečtěte si další informace o výběru hodnoty ID v tématu [*osvědčené postupy pro výběr ID časové řady*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="UŽIVATELSKÉ rozhraní portálu pro vytváření Time Series Insightsho prostředí. Je vybrána cenová úroveň PAYG (Preview) a název vlastnosti časové řady je $dtId":::

2. Vyberte **Další: zdroj události** a vyberte Event Hubs informace výše. Budete také muset vytvořit novou Event Hubs skupinu uživatelů.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="UŽIVATELSKÉ rozhraní portálu pro vytváření Time Series Insightsho zdroje událostí prostředí. Vytváříte zdroj událostí s informacemi z centra událostí výše. Vytváříte také novou skupinu příjemců.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Zahájení odesílání dat IoT do digitálních vláken Azure

Chcete-li začít odesílat data do Time Series Insights, bude nutné začít aktualizovat digitální vlastnosti s hodnotou změny hodnot dat. Použijte příkaz [AZ DT s dvojitou aktualizací](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) .

Pokud používáte kompletní kurz pro pomoc s nastavením prostředí, můžete začít odesílat Simulovaná data IoT spuštěním `DeviceSimulator` projektu z kurzu digitálních vláken Azure [*: Připojte ucelené řešení*](tutorial-end-to-end.md). Pokyny najdete v části [*Konfigurace a spuštění simulace*](tutorial-end-to-end.md#configure-and-run-the-simulation) v tomto kurzu.

## <a name="visualize-your-data-in-time-series-insights"></a>Vizualizace dat v Time Series Insights

Nyní by data měla být předávána do instance Time Series Insights, která je připravena k analýze. Pomocí následujících kroků můžete prozkoumat data přicházející v.

1. Otevřete instanci Time Series Insights v Azure Portal. Přejděte na adresu URL aplikace Time Series Insights Explorer zobrazenou v přehledu.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Na kartě Přehled prostředí Time Series Insights klikněte na adresu URL Time Series Insights Exploreru.":::

2. V Průzkumníkovi se zobrazí vaše tři vlákna na levé straně. Klikněte na **thermostat67**, vyberte **patch_value**a klikněte na **Přidat**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Klikněte na * * thermostat67 * *, vyberte * * teplota * * a klikněte na * * Přidat * *.":::

3. Nyní byste měli vidět počáteční čtení teploty z termostatu, jak je znázorněno níže. Tato stejná teplota se aktualizuje pro room21 a floor1 a tyto datové proudy můžete vizualizovat společně.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Počáteční data o teplotě se graficky v Průzkumníkovi TSI. Jedná se o řádek náhodných hodnot mezi 68 a 85":::

4. Pokud povolíte, aby simulace běžela mnohem déle, vizualizace bude vypadat přibližně takto:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Data o teplotě pro každý z vláken se grafují ve třech paralelních řádcích různých barev.":::

## <a name="next-steps"></a>Další kroky

Digitální vlákna jsou ve výchozím nastavení ukládána jako Plochá hierarchie v Time Series Insights, ale lze je rozšířit pomocí informací o modelu a víceúrovňové hierarchie pro organizaci. Další informace o tomto procesu najdete v článku: 

* [*Kurz: definování a použití modelu*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Můžete napsat vlastní logiku, která automaticky poskytne tyto informace pomocí modelu a dat grafu, která jsou již uložena v rámci digitálních vláken Azure. Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících odkazech:

* [*Postupy: Správa digitálního vlákna*](./how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](./how-to-query-graph.md)