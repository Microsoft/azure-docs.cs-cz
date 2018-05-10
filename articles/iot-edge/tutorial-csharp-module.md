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
ms.openlocfilehash: 1caa887a13453ce2b2b07e83b74f0ed57535b026
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Vývoj a nasazení modul IoT Edge C# na simulovaného zařízení – náhled

Moduly IoT Edge můžete nasadit kód, který implementuje obchodní logiku přímo do zařízení IoT okraj. Tento kurz vás provede vytváření a nasazování modul IoT okraj, který filtruje data snímačů. Budete používat simulované zařízení IoT okraj, který jste vytvořili v nasazení Azure IoT Edge v simulovaném zařízení v [Windows] [ lnk-tutorial1-win] nebo [Linux] [ lnk-tutorial1-lin]kurzy. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Visual Studio Code použít k vytvoření modul IoT Edge založené na .NET core 2.0
> * Pomocí Visual Studio Code a Docker vytvoření bitové kopie docker a publikujete ho v registru 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT okraj, který vytvoříte v tomto kurzu filtruje data teploty generována zařízení. Pouze odešle zprávy proti proudu pokud teplota překročí zadanou prahovou hodnotu. Tento typ analýzy na hranici je užitečné při snižování množství dat oznamovat a uložit v cloudu. 

## <a name="prerequisites"></a>Požadavky

* Azure IoT hraniční zařízení, který jste vytvořili v prvním kurzu nebo rychlý start.
* Primární připojovací řetězec klíče pro zařízení IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) na stejném počítači, který má Visual Studio Code. Edice Community (CE) je dostačující pro účely tohoto kurzu. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty později v tomto kurzu budete používat při publikování bitovou kopii Docker v registru a přidejte tato pověření registru do modulu runtime okraj. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následující kroky zobrazení můžete jak vytvořit modul IoT Edge založené na rozhraní .NET základní 2.0 pomocí kódu v jazyce Visual Studio a rozšíření Azure IoT okraj.
1. V sadě Visual Studio Code vyberte **zobrazení** > **integrované terminálu** otevřete integrované terminálu VS Code.
2. Integrované terminálu, zadejte následující příkaz pro instalaci (nebo aktualizujte) **AzureIoTEdgeModule** šablony v dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Vytvořte projekt pro nový modul. Následující příkaz vytvoří složce projektu **FilterModule**, s kontejner úložiště. Pokud používáte registr kontejnerů Azure, druhý parametr by měl mít formát `<your container registry name>.azurecr.io`. V aktuální pracovní složce zadejte následující příkaz:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Vyberte **soubor** > **otevřete složku**.
5. Vyhledejte **FilterModule** složky a klikněte na tlačítko **vyberte složku** otevřete projekt v produktu VS Code.
6. V Průzkumníku VS Code, klikněte na tlačítko **Program.cs** ho otevřete.

   ![Otevření souboru Program.cs][1]

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
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Property TemperatureThreshold not exist");
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

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření image Dockeru a její publikování do registru

1. Přihlaste se k Dockeru zadáním následujícího příkazu v integrovaném terminálu VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Pokud chcete zjistit uživatelské jméno, heslo a přihlašovací server, které máte v tomto příkazu použít, přejděte na web [Azure portal] (https://portal.azure.com). V části **Všechny prostředky** kliknutím na dlaždici vašeho registru kontejneru otevřete jeho vlastnosti a pak klikněte na **Přístupové klíče**. Zkopírujte hodnoty v polích **Uživatelské jméno**, **Heslo** a **Přihlašovací server**. 

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **module.json** a klikněte na **Sestavit a odeslat image Dockeru s modulem IoT Edge**. V místním rozevíracím seznamu v horní části okna VS Code vyberte platformu vašeho kontejneru – **amd64** pro kontejner Linuxu **windows-amd64** pro kontejner Windows. VS Code pak sestavení kódu, containerize `FilterModule.dll` a poslat ho do kontejneru registru, který jste zadali.


3. Úplnou adresu image kontejneru můžete získat pomocí značky v integrovaném terminálu VS Code. Další informace o definici sestavení a odeslání najdete v souboru `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Přidejte pověření registru do hraniční runtime
Přidejte přihlašovací údaje k vašemu registru do modulu runtime Edge na počítači, na kterém spouštíte službu Edge. Tyto přihlašovací údaje poskytnout přístup runtime vyžádání kontejneru. 

- V případě Windows spusťte následující příkaz:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- V případě Linuxu spusťte následující příkaz:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Spuštění řešení

1. Na webu [Azure Portal](https://portal.azure.com) přejděte do svého centra IoT.
2. Přejděte na **IoT Edge (preview)** a vyberte zařízení IoT Edge.
3. Vyberte **Nastavit moduly**. 
4. Zkontrolujte, zda **tempSensor** modul se automaticky vyplní. Pokud není, přidejte ho pomocí následující kroky:
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Další nastavení ponechte beze změny a klikněte na **Uložit**.
5. Přidat **filterModule** modul, který jste vytvořili v předchozí části. 
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `filterModule`.
    3. Do pole **Identifikátor URI image** zadejte adresu své image, například `<your container registry address>/filtermodule:0.0.1-amd64`. Úplnou adresu image najdete v předchozí části.
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
7. V kroku **Určení tras** zkopírujte do textového pole následující JSON. Moduly publikovat všechny zprávy do hraniční runtime. Deklarativní pravidel v modulu runtime definovat, kde toku zpráv. V tomto kurzu musíte dvě trasy. První trasa je určena k přenosu zpráv z teploty senzoru modulu filtru prostřednictvím koncového bodu "input1", což je koncový bod, který jste nakonfigurovali s **FilterMessages** obslužné rutiny. Druhá trasa přenáší zprávy z modulu filtru do služby IoT Hub. V této trase je `upstream` speciální cíl, který centru Edge říká, že má odesílat zprávy do služby IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Klikněte na **Další**.
9. V kroku **Kontrola šablony** klikněte na **Odeslat**. 
10. Vraťte se na stránku podrobností o zařízení IoT Edge a klikněte na **Aktualizovat**. Byste měli vidět, že se vám nové **filtermodule** společně s **tempSensor** modulu a **IoT Edge runtime**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Monitorování zpráv typu zařízení-cloud odesílaných ze zařízení IoT Edge do centra IoT:
1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro vaše centrum IoT: 
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
