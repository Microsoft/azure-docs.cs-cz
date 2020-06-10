---
title: Ingestování telemetrie z IoT Hub
titleSuffix: Azure Digital Twins
description: Podívejte se, jak ingestovat zprávy telemetrie zařízení z IoT Hub.
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 351f7ed131d545d2aa83df753cac3f26e76e4ccb
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612814"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingestování IoT Hub telemetrie do digitálních vláken Azure

Digitální vlákna Azure se řídí daty ze zařízení IoT a dalších zdrojů. Běžný zdroj dat zařízení, která se mají používat v rámci digitálních vláken Azure, je [IoT Hub](../iot-hub/about-iot-hub.md).

Během období Preview je proces pro ingestování dat do digitálních vláken Azure nastavený na externí výpočetní prostředek, jako je třeba [funkce Azure](../azure-functions/functions-overview.md), která přijímá data a používá [rozhraní API DigitalTwins](how-to-use-apis-sdks.md) k nastavení vlastností nebo událostí telemetrie na [digitální vlákna](concepts-twins-graph.md) . 

Tento postup popisuje, jak dokumentovat pomocí procesu vytváření funkce Azure, která může ingestovat telemetrii od IoT Hub.

## <a name="example-telemetry-scenario"></a>Ukázkový scénář telemetrie

Tento postup popisuje, jak odesílat zprávy z IoT Hub do digitálních vláken Azure pomocí funkce Azure Functions. Existuje mnoho možných konfigurací a vyhovujících strategií, které můžete použít, ale příklad tohoto článku obsahuje následující části:
* Zařízení teploměr v IoT Hub se známým ID zařízení.
* Digitální vlákna představující zařízení s ID odpovídajícího
* Digitální dvojitá reprezentace místnosti

> [!NOTE]
> V tomto příkladu se používá jednoznačné ID mezi ID zařízení a odpovídajícím ID digitálního vlákna, ale je možné poskytnout propracovanější mapování ze zařízení na jeho dvojitou hodnotu (například s tabulkou mapování).

Pokaždé, když zařízení teploměru posílá událost telemetrie teploty, musí se aktualizovat vlastnost *teploty* vlákna v *místnosti* . Pokud to chcete udělat, namapujete z události telemetrie na zařízení na vlastnost setter u digitálního vlákna. Pomocí informací o topologii v [grafu s dvojitou](concepts-twins-graph.md) polohou vyhledáte dvojitou *místnost* a pak můžete nastavit vlastnost vlákna. V jiných scénářích může uživatel chtít nastavit vlastnost na párovém vlákna (v tomto příkladu je to dvojitě s ID *123*). Digitální vlákna Azure vám dává značnou flexibilitu při rozhodování o tom, jak se data telemetrie mapují na vlákna. 

Tento scénář je popsaný v diagramu níže:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Zařízení IoT Hub odesílá do funkce Azure funkci telemetrii teploty prostřednictvím IoT Hub, Event Grid nebo systémových témat, což aktualizuje vlastnost teploty u vláken v digitálních událostech Azure." border="false":::

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v tomto příkladu, musíte splnit následující požadavky.
1. Vytvořte centrum IoT. Pokyny najdete v části *vytvoření IoT Hub* v [tomto IoT Hub rychlém](../iot-hub/quickstart-send-telemetry-cli.md) startu.
2. Vytvořte alespoň jednu funkci Azure pro zpracování událostí z IoT Hub. Přečtěte si téma [Postupy: nastavení funkce Azure pro zpracování dat](how-to-create-azure-function.md) a vytvoření základní funkce Azure, která se může připojit k digitálním podprocesům Azure a volat funkce rozhraní API digitálních vláken Azure. Zbytek tohoto postupu bude sestaven na této funkci.
3. Nastavte cíl události pro data centra. V [Azure Portal](https://portal.azure.com/)přejděte k instanci IoT Hub. V části *události*Vytvořte předplatné pro funkci Azure Functions. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure Portal: přidání odběru události":::

4. Na stránce *vytvořit odběr události* vyplňte pole následujícím způsobem:
   * V části *Podrobnosti odběru události*pojmenujte předplatné tak, co byste chtěli.
   * V části *typy událostí*vyberte *telemetrie zařízení* jako typ události, která se má filtrovat.
      - Přidejte filtry k ostatním typům událostí, pokud chcete.
   * V části *Podrobnosti o koncovém bodu*vyberte Azure Function jako koncový bod.

## <a name="create-an-azure-function-in-visual-studio"></a>Vytvoření funkce Azure v aplikaci Visual Studio

V této části se používají stejné kroky při spuštění sady Visual Studio a Osnova funkcí Azure Functions v tématu [Postupy: nastavení funkce Azure pro zpracování dat](how-to-create-azure-function.md). Kostra zpracovává ověřování a vytváří klienta služby, který je připravený na zpracování dat a volání rozhraní API digitálních vláken Azure v reakci. 

Jádrem funkce kostra je:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

V následujících krocích přidáte konkrétní kód pro zpracování událostí telemetrie IoT z IoT Hub.  

## <a name="add-telemetry-processing"></a>Přidat zpracování telemetrie

Události telemetrie přicházejí do formy zpráv ze zařízení. Prvním krokem při přidávání kódu pro zpracování telemetrie je extrakce příslušné části této zprávy zařízení z události Event Grid. 

Různá zařízení mohou strukturovat své zprávy různě, takže kód pro tento krok závisí na připojeném zařízení. 

Následující kód ukazuje příklad jednoduchého zařízení, které odesílá telemetrii jako JSON. Ukázka extrahuje ID zařízení, které zprávu odeslalo, a také hodnotu teploty.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Pokud je účelem tohoto cvičení, je potřeba aktualizovat teplotu *místnosti* v grafu s dvojitou přesností. To znamená, že náš cíl zprávy není digitální, který je spojený s tímto zařízením, ale *místnost* vlákna, která je jeho nadřazenou. Nadřazené vlákna můžete najít pomocí hodnoty ID zařízení, kterou jste extrahovali ze zprávy telemetrie pomocí výše uvedeného kódu.

Pokud to chcete provést, použijte rozhraní API pro digitální vlákna Azure pro přístup k příchozím relacím na zařízení, které představuje zdvojené (v tomto případě má stejné ID jako zařízení). Z příchozího vztahu můžete najít ID nadřazeného prvku s následujícím fragmentem kódu.

Následující fragment kódu ukazuje, jak načíst příchozí relace vlákna:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

Nadřazená položka vlákna je ve vlastnosti *SourceId* vztahu.

Je poměrně běžné pro model vláken, který představuje zařízení, aby měl jenom jeden příchozí vztah. V takovém případě můžete vybrat první (a pouze) vrácenou relaci. Pokud vaše modely umožňují více typů vztahů k tomuto typu vlákna, možná budete muset zadat další pro výběr z více příchozích vztahů. Běžným způsobem, jak to provést, je vybrat relaci podle `RelationshipName` . 

Jakmile budete mít ID nadřazeného vlákna představujícího *místnost*, můžete "opravit" (provést aktualizace), které jsou vytvářené. K tomu použijte následující kód:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Úplný kód funkce Azure

Pomocí kódu z předchozích ukázek je zde celá funkce Azure v kontextu:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

Funkce nástroje pro vyhledání příchozích vztahů:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

A funkce Utility pro opravu vlákna:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Teď máte funkci Azure, která je vybavená ke čtení a interpretaci dat scénáře přicházejících z IoT Hub.

## <a name="debug-azure-function-apps-locally"></a>Místní ladění aplikací funkcí Azure Functions

Službu Azure Functions je možné ladit pomocí Event Grid triggeru místně. Další informace najdete v tématu [ladění Event Grid triggeru místně](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si o příchozím a odchozím přenosu dat pomocí digitálních vláken Azure:
* [Koncepty: integrace s jinými službami](concepts-integration.md)
