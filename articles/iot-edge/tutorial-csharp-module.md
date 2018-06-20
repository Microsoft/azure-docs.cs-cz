---
title: Modul Azure IoT Edge C# | Microsoft Docs
description: Vytvoření modulu IoT Edge s kódem v jazyce C# a jeho nasazení na zařízení Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1da3a246a2ad33a4563f491058f5d4d115f3954d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631067"
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Vytvoření modulu IoT v jazyce C# a jeho nasazení na simulované zařízení – Preview

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v tématu o nasazení Azure IoT Edge na simulované zařízení ve [Windows][lnk-tutorial1-win] nebo [Linuxu][lnk-tutorial1-lin]. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge založeného na rozhraní .NET Core 2.0.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru. 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

## <a name="prerequisites"></a>Požadavky

* Zařízení Azure IoT Edge, které jste vytvořili v rychlém startu nebo v prvním kurzu.
* Primární připojovací řetězec klíče pro zařízení IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Docker](https://docs.docker.com/engine/installation/) na stejném počítači, jako je Visual Studio Code. Pro tento kurz stačí edice Community Edition (CE). 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Centrum Dockeru](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v dalších částech kurzu k publikování image Dockeru do registru a k přidání přihlašovacích údajů registru do modulu runtime Edge. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
V následujících krocích si ukážeme, jak vytvořit modul IoT Edge, který je založený na rozhraní .NET Core 2.0. Budeme používat Visual Studio Code a rozšíření Azure IoT Edge.
1. V nástroji Visual Studio Code vyberte, že chcete **zobrazit** > **integrovaný terminál**, aby se otevřel integrovaný terminál VS Code.
2. V integrovaném terminálu zadejte následující příkaz, kterým nainstalujete (nebo aktualizujete) šablonu **AzureIoTEdgeModule** v rozhraní .NET:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Vytvořte projekt pro nový modul. Následující příkaz vytvoří složku projektu **FilterModule** s úložištěm kontejnerů. Pokud používáte registr kontejnerů Azure, druhý parametr by měl mít formát `<your container registry name>.azurecr.io`. V aktuální pracovní složce zadejte následující příkaz:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Vyberte **File** > **Open Folder** (Soubor > Otevřít složku).
5. Přejděte ke složce **FilterModule** a klikněte na **Select Folder** (Vybrat složku). Projekt se otevře v průzkumníku VS Code.
6. Když v průzkumníku VS Code kliknete na soubor **Program.cs**, otevře se.

   ![Otevření souboru Program.cs][1]

7. Nahoře v oboru názvů **FilterModule** přidejte tři výrazy `using` pro typy, které použijeme později:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Do třídy **Program** přidejte proměnnou `temperatureThreshold`. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Do třídy **Program** přidejte třídy `MessageBody`, `Machine` a `Ambient`. Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

10. V metodě **Init** kódem vytvoříte a nakonfigurujete objekt **DeviceClient**. Tento objekt umožňuje modulu připojit se k místnímu modulu runtime IoT Edge kvůli odesílání a přijímání zpráv. Modul runtime IoT Edge poskytne modulu připojovací řetězec používaný metodou **Init**. Po vytvoření objektu **DeviceClient** kód přečte hodnotu TemperatureThreshold z požadovaných vlastností dvojčete modulu a zaznamená zpětné volání přijatých zpráv z IoT Edge Hubu prostřednictvím koncového bodu **input1**. Nahraďte metodu `SetInputMessageHandlerAsync` novou metodou a přidejte metodu `SetDesiredPropertyUpdateCallbackAsync` pro aktualizace požadovaných vlastností. Pokud chcete tuto změnu provést, nahraďte poslední řádek metody **Init** následujícím kódem:

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

11. Do třídy **Program** přidejte metodu `onDesiredPropertiesUpdate`. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

12. Nahraďte metodu `PipeMessage` metodou `FilterMessages`. Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu. Do zprávy také přidá vlastnost **MessageType**, která má nastavenou hodnotu **Alert**. 

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

13. Soubor uložte.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření image Dockeru a její publikování do registru

1. Přihlaste se k Dockeru zadáním následujícího příkazu v integrovaném terminálu VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Pokud chcete zjistit uživatelské jméno, heslo a přihlašovací server, které máte v tomto příkazu použít, přejděte na web [Azure Portal] (https://portal.azure.com). V části **Všechny prostředky** kliknutím na dlaždici vašeho registru kontejneru otevřete jeho vlastnosti a pak klikněte na **Přístupové klíče**. Zkopírujte hodnoty v polích **Uživatelské jméno**, **Heslo** a **Přihlašovací server**. 

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **module.json** a klikněte na **Sestavit a odeslat image Dockeru s modulem IoT Edge**. V místním rozevíracím seznamu v horní části okna VS Code vyberte platformu vašeho kontejneru – **amd64** pro kontejner Linuxu **windows-amd64** pro kontejner Windows. Potom VS Code vytvoří kód, vytvoří kontejner `FilterModule.dll` a doručí ho do zadaného registru kontejneru.


3. Úplnou adresu image kontejneru můžete získat pomocí značky v integrovaném terminálu VS Code. Další informace o definici sestavení a odeslání najdete v souboru `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Přidání přihlašovacích údajů registru do modulu runtime Edge
Přidejte přihlašovací údaje k vašemu registru do modulu runtime Edge na počítači, na kterém spouštíte službu Edge. S těmito přihlašovacími údaji může modul runtime načíst kontejner. 

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
4. Zkontrolujte, že je automaticky vyplněný modul **tempSensor**. Pokud tomu tak není, použijte k jeho přidání následující postup:
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Další nastavení ponechte beze změny a klikněte na **Uložit**.
5. Přidejte modul **filterModule**, který jste vytvořili v předchozích částech. 
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `filterModule`.
    3. Do pole **Identifikátor URI image** zadejte adresu své image, například `<your container registry address>/filtermodule:0.0.1-amd64`. Úplnou adresu image najdete v předchozí části.
    4. Zaškrtněte políčko **Povolit**, abyste mohli upravit dvojče modulu. 
    5. Nahraďte kód JSON v textovém poli dvojčete modulu následujícím kódem JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klikněte na **Uložit**.
6. Klikněte na **Další**.
7. V kroku **Určení tras** zkopírujte do textového pole následující JSON. Moduly publikují všechny zprávy v modulu runtime Edge. Tok zpráv definují deklarační pravidla modulu runtime. V tomto kurzu potřebujete dvě trasy: První přenáší zprávy ze senzoru teploty do modulu filtru. Používá k tomu koncový bod „input1“, který jste nakonfigurovali obslužnou rutinou **FilterMessages**. Druhá trasa přenáší zprávy z modulu filtru do služby IoT Hub. V této trase je `upstream` speciální cíl, který centru Edge říká, že má odesílat zprávy do služby IoT Hub. 

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
10. Vraťte se na stránku podrobností o zařízení IoT Edge a klikněte na **Aktualizovat**. Měl by se zobrazit spuštěný nový modul **filtermodule** společně s modulem **tempSensor** a **modulem runtime IoT Edge**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Monitorování zpráv typu zařízení-cloud odesílaných ze zařízení IoT Edge do centra IoT:
1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro vaše centrum IoT: 
    1. Otevřete průzkumník VS Code tím, že vyberete **Zobrazení** > **Průzkumník**. 
    2. V průzkumníku klikněte na **IOT HUB DEVICES** (ZAŘÍZENÍ IOT HUB) a pak klikněte na **...**. Klikněte na **Set IoT Hub Connection String** (Nastavit připojovací řetězec pro IoT Hub) a v místním okně zadejte připojovací řetězec IoT Hubu, se kterým se připojí zařízení IoT Edge. 

        Pokud chcete najít připojovací řetězec, klikněte na dlaždici IoT Hubu na webu Azure Portal a pak klikněte na **Zásady sdíleného přístupu**. V části **Zásady sdíleného přístupu** klikněte na zásadu **iothubowner** a potom si z okna **iothubowner** zkopírujte připojovací řetězec IoT Hubu.   

2. Pokud chcete monitorovat data přicházející do IoT Hubu, vyberte, že chcete **zobrazit** > **paletu příkazů** a vyhledejte v nabídce příkaz **IoT: Start monitoring D2C message** (IoT: Spustit monitorování zpráv typu zařízení-cloud). 
3. Pokud chcete monitorování dat zastavit, vyberte z nabídky příkaz **IoT: Stop monitoring D2C message** (IoT: Zastavit monitorování zpráv typu zařízení-cloud). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Pokračujte některým z následujících kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení funkce Azure Functions jako modulu](tutorial-deploy-function.md)
> [Nasazení služby Azure Stream Analytics jako modulu](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
