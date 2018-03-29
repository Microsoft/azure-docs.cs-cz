---
title: Azure IoT Edge C# modulu | Microsoft Docs
description: Vytvořte modul IoT Edge s kód C# a nasadíte ho do hraniční zařízení
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95ca66f34548f86e25c1e7af331fa88797847906
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Vývoj a nasazení modul IoT Edge C# na simulovaného zařízení – náhled

Moduly IoT Edge můžete nasadit kód, který implementuje obchodní logiku přímo do zařízení IoT okraj. Tento kurz vás provede vytváření a nasazování modul IoT okraj, který filtruje data snímačů. Budete používat simulované zařízení IoT okraj, který jste vytvořili v nasazení Azure IoT Edge v simulovaném zařízení v [Windows] [ lnk-tutorial1-win] nebo [Linux] [ lnk-tutorial1-lin]kurzy. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Visual Studio Code použít k vytvoření modul IoT Edge založené na .NET core 2.0
> * Pomocí Visual Studio Code a Docker vytvoření bitové kopie docker a publikujete ho v registru 
> * Nasazení modulu do vašeho zařízení IoT Edge
> * Vygeneruje zobrazení dat


Modul IoT okraj, který vytvoříte v tomto kurzu filtruje data teploty generována zařízení. Pouze odešle zprávy proti proudu pokud teplota překročí zadanou prahovou hodnotu. Tento typ analýzy na hranici je užitečné při snižování množství dat oznamovat a uložit v cloudu. 

## <a name="prerequisites"></a>Požadavky

* Azure IoT hraniční zařízení, který jste vytvořili v prvním kurzu nebo rychlý start.
* Primární připojovací řetězec klíče pro zařízení IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Azure IoT Edge rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [C# pro rozšíření Visual Studio Code (používá technologii OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) na stejném počítači, který má Visual Studio Code. Edice Community (CE) je dostačující pro účely tohoto kurzu. 
* [Základní rozhraní .NET 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu použijete modul sestavení a vytvoření rozšíření Azure IoT Edge pro VS Code **kontejneru image** z souborů. Pak push k této bitové kopie **registru** která uchovává a spravuje vaše Image. Nakonec nasazení bitové kopie z registru ke spuštění na vašem zařízení IoT okraj.  

Žádné kompatibilní Docker registru můžete použít pro tento kurz. Jsou dvě oblíbených Docker registru služby k dispozici v cloudu [registru kontejner Azure](https://docs.microsoft.com/azure/container-registry/) a [úložiště Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Tento kurz používá registru kontejner Azure. 

1. V [portál Azure](https://portal.azure.com), vyberte **vytvořit prostředek** > **kontejnery** > **registru kontejner Azure** .
2. Pojmenujte registr, vyberte předplatné, vybrat skupinu prostředků a nastavte verze SKU **základní**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření kontejneru registr, přejděte k němu a vyberte **přístupové klíče**. 
5. Přepnutí **uživatel s oprávněními správce** k **povolit**.
6. Zkopírujte hodnoty pro **přihlášení na server**, **uživatelské jméno**, a **heslo**. Tyto hodnoty později v tomto kurzu budete používat při publikování bitovou kopii Docker v registru a přidejte tato pověření registru do modulu runtime okraj. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následující kroky zobrazení můžete jak vytvořit modul IoT Edge založené na rozhraní .NET základní 2.0 pomocí kódu v jazyce Visual Studio a rozšíření Azure IoT okraj.
1. V sadě Visual Studio Code vyberte **zobrazení** > **integrované terminálu** otevřete integrované terminálu VS Code.
2. Integrované terminálu, zadejte následující příkaz pro instalaci (nebo aktualizujte) **AzureIoTEdgeModule** šablony v dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Vytvoření projektu pro nového modulu. Následující příkaz vytvoří složce projektu **FilterModule**, s kontejner úložiště. Druhý parametr by měl být ve tvaru `<your container registry name>.azurecr.io` Pokud používáte Azure kontejneru registru. Zadejte následující příkaz v aktuální pracovní složku:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Vyberte **soubor** > **otevřete složku**.
5. Vyhledejte **FilterModule** složky a klikněte na tlačítko **vyberte složku** otevřete projekt v produktu VS Code.
6. V Průzkumníku VS Code, klikněte na tlačítko **Program.cs** ho otevřete.

   ![Open Program.cs][1]

7. V horní části **FilterModule** obor názvů, přidejte tři `using` příkazy pro typy používané později na:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Přidat `temperatureThreshold` proměnnou **Program** třídy. Tato proměnná nastaví hodnotu, která nesmí být větší než teplota měřená v pořadí pro data k odeslání do služby IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Přidat `MessageBody`, `Machine`, a `Ambient` třídy k **Program** třídy. Tyto třídy definují očekávané schéma pro tělo příchozí zprávy.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

10. V **Init** metoda, kód vytvoří a nakonfiguruje **DeviceClient** objektu. Tento objekt umožňuje modul pro připojení k místní Azure IoT Edge modulu runtime odesílat a přijímat zprávy. Připojovací řetězec použitý v **Init** metoda poskytl modulu runtime IoT okraj. Po vytvoření **DeviceClient**, kód čte TemperatureThreshold z modulu Twin požadované vlastnosti a zaregistruje zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím **input1**koncový bod. Nahraďte `SetInputMessageHandlerAsync` metoda s novou a přidejte `SetDesiredPropertyUpdateCallbackAsync` metoda aktualizace požadované vlastnosti. Chcete-li tuto změnu, nahraďte poslední řádek **Init** metoda následujícím kódem:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    if (moduleTwinCollection["TemperatureThreshold"] != null)
    {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Přidat `onDesiredPropertiesUpdate` metodu **Program** třídy. Tato metoda přijímá aktualizace na požadované vlastnosti z modulu twin a aktualizací **temperatureThreshold** proměnné tak, aby odpovídaly. Všechny moduly mají vlastní twin modul, který vám umožní nakonfigurovat kód běžících v rámci modul přímo z cloudu.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

12. Nahraďte `PipeMessage` metoda s `FilterMessages` metoda. Tato metoda je volána, když modul přijímá zprávy z centra IoT okraj. Filtruje zprávy, které podávají teploty pod prahovou hodnotou teploty nastavených prostřednictvím twin modulu. Přidává také **typ zprávy** vlastnost na zprávu nastavená na hodnotu **výstrahy**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

13. Uložte tento soubor.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření bitové kopie Docker a publikujete ho v registru

1. Přihlaste se k Docker zadáním následujícího příkazu v terminálu integrované VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

2. V Průzkumníku VS Code, klikněte pravým tlačítkem myši **module.json** souboru a klikněte na tlačítko **sestavení a Push IoT Edge modulu Docker image**. Do pole automaticky otevírané okno rozevírací seznam v horní části okna VS Code vyberte kontejner platformu, buď **amd64** pro kontejner Linux nebo **windows amd64** kontejneru systému Windows. VS Code pak sestavení kódu, containerize `FilterModule.dll` a poslat ho do kontejneru registru, který jste zadali.


3. Můžete získat adresu úplné kontejneru bitové kopie s značkou v produktu VS Code integrované terminálu. Další informace o definici sestavení a posílejte nabízená oznámení najdete `module.json` souboru.

## <a name="add-registry-credentials-to-edge-runtime"></a>Přidejte pověření registru do hraniční runtime
Přidáte přihlašovací údaje pro vaše registru do hraniční runtime v počítači, na kterém je spuštěn hraniční zařízení. Tyto přihlašovací údaje poskytnout přístup runtime vyžádání kontejneru. 

- Pro systém Windows spusťte následující příkaz:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Pro Linux spusťte následující příkaz:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Spuštění řešení

1. V [portál Azure](https://portal.azure.com), přejděte do služby IoT hub.
2. Přejděte na **IoT Edge (preview)** a vyberte zařízení IoT Edge.
3. Vyberte **nastavit moduly**. 
4. Zkontrolujte, zda **tempSensor** modul se automaticky vyplní. Pokud není, přidejte ho pomocí následující kroky:
    1. Vyberte **přidání okraj IoT modulu**.
    2. V **název** zadejte `tempSensor`.
    3. V **Image URI** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Další nastavení nezměníte a klikněte na tlačítko **Uložit**.
5. Přidat **filterModule** modul, který jste vytvořili v předchozí části. 
    1. Vyberte **přidání okraj IoT modulu**.
    2. V **název** zadejte `filterModule`.
    3. V **Image URI** pole, zadejte adresu bitové kopie, třeba `<your container registry address>/filtermodule:0.0.1-amd64`. Adresu úplnou bitovou kopii lze nalézt z předchozí části.
    4. Zkontrolujte **povolit** pole tak, aby bylo možné upravit dvojici modulu. 
    5. Nahraďte kód JSON do textového pole pro dvojici modul s následujícím kódu JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klikněte na **Uložit**.
6. Klikněte na **Další**.
7. V **zadejte trasy** kroku, zkopírujte do textového pole JSON níže. Moduly publikovat všechny zprávy do hraniční runtime. Deklarativní pravidel v modulu runtime definovat, kde toku zpráv. V tomto kurzu musíte dvě trasy. První trasa je určena k přenosu zpráv z teploty senzoru modulu filtru prostřednictvím koncového bodu "input1", což je koncový bod, který jste nakonfigurovali s **FilterMessages** obslužné rutiny. Druhá trasa je určena k přenosu zpráv z modulu filtru do služby IoT Hub. V této trase `upstream` je speciální cílového umístění, které informuje Edge rozbočovače k odesílání zpráv do služby IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Klikněte na **Další**.
9. V **šablona kontrolní** krok, klikněte na tlačítko **odeslání**. 
10. Návrat na stránku Podrobnosti o zařízení IoT okraj a klikněte na tlačítko **aktualizovat**. Byste měli vidět, že se vám nové **filtermodule** společně s **tempSensor** modulu a **IoT Edge runtime**. 

## <a name="view-generated-data"></a>Vygeneruje zobrazení dat

K monitorování zařízení na cloud zprávy odeslané ze zařízení IoT okraj do služby IoT hub:
1. Rozšíření Azure IoT Toolkit nakonfigurujte připojovací řetězec služby IoT hub: 
    1. Otevřete Průzkumníka VS Code výběrem **zobrazení** > **Explorer**. 
    2. V Průzkumníku, klikněte na tlačítko **zařízení IOT HUB** a pak klikněte na **...** . Klikněte na tlačítko **nastavit připojovací řetězec centra IoT** a zadejte připojovací řetězec pro službu IoT hub, která zařízení IoT Edge připojuje v místním okně. 

        Chcete-li najít připojovací řetězec, klikněte na dlaždici služby IoT hub v portálu Azure a pak klikněte na **zásady sdíleného přístupu**. V **zásady sdíleného přístupu**, klikněte **iothubowner** zásad a zkopírujte IoT Hub připojovací řetězec v **iothubowner** okno.   

2. Ke sledování dat odesílaných do služby IoT hub, vyberte **zobrazení** > **příkaz palety** a vyhledejte **IoT: spuštění monitorování D2C zpráva** příkazu v nabídce. 
3. Chcete-li zastavit monitorování dat, použijte **IoT: zastavení monitorování D2C zpráva** příkazu nabídky. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili hraniční IoT modul, který obsahuje kód pro filtrování nezpracovaná data generována zařízení IoT okraj. Můžete pokračovat na některý z následujících kurzech informace o další způsoby, které mohou pomoci Azure IoT Edge, že zapněte data do podnikových statistik na hranici.

> [!div class="nextstepaction"]
> [Nasazení funkce Azure jako modul](tutorial-deploy-function.md)
> [nasazení Azure Stream Analytics jako modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
