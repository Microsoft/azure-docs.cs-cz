---
title: Použití kódu v jazyce Visual Studio k vývoji modulu jazyka C# s hranou Azure IoT | Microsoft Docs
description: Vývoj a nasazení modul C# s Azure IoT hrany v sadě Visual Studio Code, aniž by se přepnula kontextu.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 48c6cacebdeb7505c8dc2bcaed099c33862589ac
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-develop-a-c-module-with-azure-iot-edge"></a>Použití kódu v jazyce Visual Studio k vývoji modulu jazyka C# s hranou Azure IoT
Tento článek obsahuje podrobné pokyny pro používání [Visual Studio Code](https://code.visualstudio.com/) jako hlavní vývojový nástroj k vývoji a nasazení moduly Azure IoT okraj. 

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows nebo Linux jako vývojovém počítači. Může být zařízení IoT Edge jiného fyzického zařízení, nebo můžete simulovat zařízení IoT Edge na vývojovém počítači.

Než začnete v tomto návodu, proveďte následující kurzy:
- Nasazení v simulovaném zařízení v Azure IoT Edge [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) nebo [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)
- [Vývoj a nasazení modul IoT Edge C# do simulovaného zařízení](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

Tady je kontrolní seznam, který obsahuje položky že byste měli mít po dokončení předchozí kurzy:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Azure IoT Edge rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# pro rozšíření Visual Studio Code (používá technologii OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [Základní rozhraní .NET 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- [Python 2.7](https://www.python.org/downloads/)
- [Skript IoT okraj ovládacího prvku](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- Šablona AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Aktivní Centrum IoT se alespoň IoT hraniční zařízení

Je také užitečné k instalaci [Docker podporu pro VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) modulu bitové kopie a kontejnery správy.

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>Nasazení modul Azure IoT Edge v produktu VS Code

### <a name="list-your-iot-hub-devices"></a>Zobrazí seznam zařízení IoT hub
Seznam zařízení IoT hub v produktu VS Code dvěma způsoby. Můžete buď způsob, jak pokračovat.

#### <a name="sign-in-to-your-azure-account-in-vs-code-and-choose-your-iot-hub"></a>Přihlaste se k účtu Azure v produktu VS Code a zvolte služby IoT hub
1. V příkazu palety (F1 nebo Ctrl + Shift + P), zadejte a vyberte **Azure: Přihlaste**. Potom vyberte **zkopírujte & Otevřít**. Vložte (Ctrl + V) kód v prohlížeči a vyberte **pokračovat**. Pak se přihlaste pomocí účtu Azure. Můžete zobrazit informace o účtu ve stavovém řádku VS Code.
2. Příkaz palety, typ a vyberte **IoT: Vyberte IoT Hub**. Nejdřív vyberte předplatné, které jste vytvořili službu IoT hub v předchozí kurzu. Zvolte Centrum IoT, který obsahuje IoT hraniční zařízení.

    ![Snímek obrazovky seznamu zařízení](./media/how-to-vscode-develop-csharp-module/device-list.png)

#### <a name="set-the-iot-hub-connection-string"></a>Nastavení připojovacího řetězce centra IoT
Příkaz palety, typ a vyberte **IoT: nastavit připojovací řetězec centra IoT**. Ujistěte se, vložte připojovací řetězec v rámci zásad **iothubowner**. (Najdete ji v zásady sdíleného přístupu služby IoT hub v portálu Azure.)
 
Zobrazí seznam zařízení v Průzkumníku zařízení IoT Hub na straně panelu na levé straně.

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>Spuštění vaší hraniční IoT runtime a nasazení modulu
Instalace a spuštění modulu runtime Azure IoT Edge ve vašem zařízení. Nasaďte simulované senzor modul, který odesílá telemetrická data do Azure IoT Hub.
1. V příkazu palety, vyberte **Edge: Instalační program Edge** a zvolte vaší hraniční IoT ID zařízení. Případně, klikněte pravým tlačítkem na ID zařízení IoT Edge v **seznam zařízení**a vyberte **instalace Edge**.

    ![Snímek obrazovky instalační program Edge runtime](./media/how-to-vscode-develop-csharp-module/setup-edge.png)

2. Příkaz palety, vyberte **Edge: spuštění Edge** spuštění vaší hraniční IoT runtime. Zobrazí odpovídající výstupy v integrovaném terminálu.

    ![Snímek obrazovky Start Edge runtime](./media/how-to-vscode-develop-csharp-module/start-edge.png)

3. Zkontrolujte stav modulu runtime IoT Edge v Průzkumníku Docker. Zelená znamená běží, a vaší hraniční IoT runtime byla úspěšně spuštěna. Váš počítač nyní simuluje IoT hraniční zařízení.

    ![Snímek obrazovky Edge běhový stav](./media/how-to-vscode-develop-csharp-module/edge-runtime.png)

4. Simuluje snímač, který udržuje odesílání zpráv do vašeho zařízení IoT okraj. Příkaz palety, typ a vyberte **Edge: generování Edge konfigurační soubor**. Vyberte složku k vytvoření tohoto souboru. V souboru deployment.json, který se vygeneruje, nahradí obsah `<registry>/<image>:<tag>` s `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`a soubor uložte.

    ![Snímek obrazovky senzor modulu](./media/how-to-vscode-develop-csharp-module/sensor-module.png)

5. Vyberte **Edge: vytvoření nasazení pro hraniční zařízení**a vyberte hranici IoT ID zařízení k vytvoření nového nasazení. Alternativně můžete klikněte pravým tlačítkem na ID zařízení IoT Edge v seznamu zařízení a vybrat **vytvořit nasazení pro hraniční zařízení**. 

6. Měli byste vidět vaší hraniční IoT spustit v Průzkumníku Docker s simulované senzoru. Klikněte pravým tlačítkem na kontejner v Průzkumníku Docker. Podívejte se na Docker protokolů pro každý modul. Navíc můžete zobrazit seznam modulů v seznamu zařízení.

    ![Snímek obrazovky seznam modulů](./media/how-to-vscode-develop-csharp-module/module-list.png)

7. Klikněte pravým tlačítkem na ID zařízení IoT okraj a také můžete monitorovat D2C zprávy v produktu VS Code.
8. K zastavení modulu runtime vaší hraniční IoT a modul senzor, typ a vyberte **Edge: Zastavit Edge** v palety příkaz.

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>Vývoj a nasazení modul C# v produktu VS Code
V tomto kurzu [vyvíjet modulu jazyka C#](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module), aktualizovat, vytvářet a publikovat bitové kopie modulu v produktu VS Code. Potom přejděte na portál Azure k nasazení modulu jazyka C#. Tato část představuje způsob použití VS Code k nasazení a monitorování modulu jazyka C#.

### <a name="start-a-local-docker-registry"></a>Spustit místní registru Docker
Žádné kompatibilní Docker registru můžete použít pro tento článek. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Tato část používá [místním registru Docker](https://docs.docker.com/registry/deploying/), což je snazší pro testování během vaší časná vývoje.
V produktu VS Code **integrované Terminálové** (Ctrl + '), spusťte následující příkaz spusťte místní registru:  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> Tento příklad ukazuje konfigurace registru, které jsou vhodné pro testování. Produkční prostředí registru musí být chráněny pomocí protokolu TLS a v ideálním případě by měl použít mechanismus řízení přístupu. Doporučujeme použít [registru kontejner Azure](https://docs.microsoft.com/azure/container-registry/) nebo [úložiště Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) nasazení moduly IoT Edge produkční prostředí.

### <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následující kroky ukazují, jak vytvořit modul IoT Edge založené na rozhraní .NET 2.0 jádra, pomocí kódu v jazyce Visual Studio a rozšíření Azure IoT okraj. Pokud jste v předchozím kurz dokončili v této části, můžete tuto část bezpečně přeskočit.
1. V sadě Visual Studio Code vyberte **zobrazení** > **integrované terminálu** otevřete integrované terminálu VS Code.
3. Integrované terminálu, zadejte následující příkaz pro instalaci (nebo aktualizujte) **AzureIoTEdgeModule** šablony v dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. Vytvořte projekt pro nový modul. Následující příkaz vytvoří složce projektu **FilterModule**, v aktuální pracovní složku:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. Vyberte **soubor** > **otevřete složku**.
4. Vyhledejte **FilterModule** složky a klikněte na **vyberte složku** otevřete projekt v produktu VS Code.
5. V Průzkumníku kód VS vyberte **Program.cs** ho otevřete. V horní části **program.cs**, zahrnout následujících oborů názvů:
   ```csharp
   using Microsoft.Azure.Devices.Shared;
   using System.Collections.Generic;  
   using Newtonsoft.Json;
   ```

6. Přidat `temperatureThreshold` proměnnou **Program** třídy. Tato proměnná nastaví hodnotu, která nesmí být větší než teplota měřená v pořadí pro data k odeslání do služby IoT Hub. 

   ```csharp
   static int temperatureThreshold { get; set; } = 25;
   ```

7. Přidat `MessageBody`, `Machine`, a `Ambient` třídy k **Program** třídy. Tyto třídy definují očekávané schéma pro tělo příchozí zprávy.

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

8. V **Init** metoda, kód vytvoří a nakonfiguruje **DeviceClient** objektu. Tento objekt umožňuje modul pro připojení k místní IoT Edge modulu runtime odesílat a přijímat zprávy. Modul runtime IoT Edge poskytuje modul připojovací řetězec používaný v **Init** metoda. Po vytvoření **DeviceClient** objektu kód zaregistruje zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím **input1** koncový bod. Nahraďte `SetInputMessageHandlerAsync` metoda s novou a přidejte `SetDesiredPropertyUpdateCallbackAsync` metoda aktualizace požadované vlastnosti. Chcete-li tuto změnu, nahraďte poslední řádek **Init** metoda následujícím kódem:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Přidat `onDesiredPropertiesUpdate` metodu **Program** třídy. Tato metoda přijímá aktualizace na požadované vlastnosti z modulu twin a aktualizací **temperatureThreshold** proměnné tak, aby odpovídaly. Všechny moduly mají vlastní twin modul, který vám umožní nakonfigurovat kód běžících v rámci modul přímo z cloudu.

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

10. Nahraďte `PipeMessage` metoda s `FilterMessages` metoda. Tato metoda je volána, když modul přijímá zprávy z centra IoT okraj. Filtruje zprávy, které podávají teploty pod prahovou hodnotou teploty nastavených prostřednictvím twin modulu. Přidává také **typ zprávy** vlastnost na zprávu nastavená na hodnotu **výstrahy**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
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
            DeviceClient deviceClient = (DeviceClient)userContext;
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

11. Pro sestavení projektu, klikněte pravým tlačítkem myši **FilterModule.csproj** v Průzkumníku souborů a vyberte **modul sestavení IoT Edge**. Tento proces kompilovaný modul a exportuje binárního souboru a jeho závislosti do složky, která se používá k vytvoření bitové kopie Docker. 

    ![Snímek obrazovky Průzkumník kódu VS](./media/how-to-vscode-develop-csharp-module/build-module.png)

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření bitové kopie Docker a publikujete ho v registru

1. V Průzkumníku kód VS rozbalte **Docker** složky. Pak rozbalte složku pro vaši kontejnerovou platformu – **linux-x64** nebo **windows-nano**.
2. Klikněte pravým tlačítkem myši **soubor Docker** soubor a vyberte **sestavení IoT Edge modulu Docker image**. 

    ![Snímek obrazovky Průzkumník kódu VS](./media/how-to-vscode-develop-csharp-module/build-docker-image.png)

3. V **vyberte složku** okně vyhledejte nebo zadejte `./bin/Debug/netcoreapp2.0/publish`. Vyberte **vyberte složku EXE_DIR**.
4. Do místního textového pole v horní části okna VS Code zadejte název image. Například: `<your container registry address>/filtermodule:latest`. Když nasazujete do místního registru, měla by být `localhost:5000/filtermodule:latest`.
5. Nasdílejte image do svého úložiště Dockeru. Použití **Edge: Push IoT Edge modulu Docker image** příkaz a zadejte adresu URL bitové kopie do místní textového pole v horní části okna VS Code. Použijte stejnou adresu URL obrázku jste použili v předchozím kroku. Zkontrolujte protokol konzoly a ujistěte se, že bylo úspěšně posunuto bitovou kopii.

    ![Snímek obrazovky vkládání bitovou kopii Docker](./media/how-to-vscode-develop-csharp-module/push-image.png) ![snímek obrazovky protokolu console](./media/how-to-vscode-develop-csharp-module/pushed-image.png)

### <a name="deploy-your-iot-edge-modules"></a>Nasazení moduly IoT Edge

1. Otevřete `deployment.json` souboru a nahraďte **moduly** oddíl následujícím kódem:
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. Nahraďte **trasy** oddíl následujícím kódem:
    ```json
    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
    ```
   > [!NOTE]
   > Deklarativní pravidel v modulu runtime definovat, kde se tyto zprávy toku. V tomto článku je nutné dvě trasy. První trasa přenáší zprávy ze senzoru teploty do modulu filtru přes koncový bod input1. Toto je koncový bod, který jste nakonfigurovali s FilterMessages obslužnou rutinou. Druhá trasa přenáší zprávy z modulu filtru do služby IoT Hub. V této trase je nadřazený speciální cíl, která říká službě IoT Hub Edge k odesílání zpráv do služby IoT Hub.

3. Uložte tento soubor.
4. Příkaz palety, vyberte **Edge: vytvoření nasazení pro hraniční zařízení**. Pak vyberte ID zařízení IoT Edge k vytvoření nasazení. Nebo, klikněte pravým tlačítkem na ID zařízení v seznamu zařízení a vyberte **vytvořit nasazení pro hraniční zařízení**.

    ![Snímek obrazovky vytvoření možnost nasazení](./media/how-to-vscode-develop-csharp-module/create-deployment.png)

5. Vyberte `deployment.json` vás informovat. V okně výstupu uvidíte odpovídající výstupy pro vaše nasazení.

    ![Snímek obrazovky výstup – okno](./media/how-to-vscode-develop-csharp-module/deployment-succeeded.png)

6. Spuštění vaší hraniční IoT runtime v příkazu palety (vyberte **Edge: spuštění Edge**).
7. Můžete zobrazit vaše IoT Edge runtime spustit v Průzkumníku Docker simulované senzor a modul filtru.

    ![Snímek obrazovky Průzkumníka Docker](./media/how-to-vscode-develop-csharp-module/solution-running.png)

8. Klikněte pravým tlačítkem na ID zařízení IoT okraj a také můžete monitorovat D2C zprávy v produktu VS Code.


## <a name="next-steps"></a>Další postup

[Ladění jazyka C# modulu v produktu VS Code](how-to-vscode-debug-csharp-module.md)
