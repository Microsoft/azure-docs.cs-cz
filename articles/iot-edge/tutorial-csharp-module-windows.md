---
title: Kurz vývoj C# modul pro Windows – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s C# kódu a nasadit ho do zařízení s Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 046398af8678e708784614dfdc231778454ed945
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576597"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Kurz: Vývoj C# modul IoT Edge pro zařízení s Windows

Použití sady Visual Studio k vývoji C# kódu a nasadit ho do zařízení s Windows s Azure IoT Edge. 

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Pomocí sady Visual Studio k vytvoření, která je založena na modulu IoT Edge C# SDK.
> * Pomocí sady Visual Studio a Dockeru k vytvoření image Dockeru a její publikování do registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Řešení rozsahu

Tento kurz ukazuje postupy při vývoji modulu v **C#** pomocí **Visual Studio 2017**a jak ji nasadit **zařízení Windows**. Pokud vyvíjíte moduly pro Linux zařízení, přejděte na [vývoje C# modul IoT Edge pro zařízení s Linuxem](tutorial-csharp-module.md) místo. 

Popis možností pro vývoj a nasazení modulů C na zařízení s Windows pomocí následující tabulky: 

| C# | Visual Studio Code | Visual Studio 2017 | 
| -- | ------------------ | ------------------ |
| **Vývoj Windows AMD64** | ![Vývoj C# moduly pro WinAMD64 ve VS Code](./media/tutorial-c-module/green-check.png) | ![Vývoj C# moduly pro WinAMD64 v sadě Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Ladění Windows AMD64** |   | ![Ladění C# moduly pro WinAMD64 v sadě Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu měli jste prošli předchozího kurzu věnovaného nastavení vývojového prostředí [vývoj modul IoT Edge pro zařízení s Windows](tutorial-develop-for-windows.md). Po dokončení tohoto kurzu, byste již měli mít splněné následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [zařízení Windows s Azure IoT Edge](quickstart.md).
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio?view=vs-2017), verze 15.7 nebo novější, nakonfigurují [nástroje Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) rozšíření.
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Windows.

## <a name="create-a-module-project"></a>Vytvoření modulu projektu

Následující kroky vytvoření projektu modul IoT Edge pomocí Visual Studio a rozšíření Azure IoT Edge Tools. Jakmile budete mít vytvořenou šablonu projektu, přidejte nový kód, aby modul odfiltruje zprávy podle jejich ohlášené vlastnosti. 

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Rozšíření nástroje Azure IoT nabízí šablony projektů pro všechny podporované IoT Edge modulu jazyků v sadě Visual Studio 2017. Tyto šablony mají všechny soubory a kód, který musíte nasadit pracovní modul pro testování IoT Edge, nebo získáte výchozí bod pro přizpůsobení šablony s vlastní obchodní logikou. 

1. Spuštění sady Visual Studio jako správce.

2. Vyberte **Soubor** > **Nový** > **Projekt**. 

3. V okně Nový projekt, vyberte **Azure IoT** typ projektu a zvolte **Azure IoT Edge** projektu. Přejmenování projektu a řešení na něco popisného jako **CSharpTutorialApp**. Vyberte **OK** a vytvořte projekt. 

   ![Vytvořte nový projekt Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

4. V okně aplikace IoT Edge a modul konfiguraci projektu s použitím následujících hodnot: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Aplikační platforma | Zrušte zaškrtnutí políčka **Linux Amd64**a zkontrolujte **WindowsAmd64**. |
   | Vybrat šablonu | Vyberte  **C# modulu**. | 
   | Název modulu projektu | Dejte modulu název **CSharpModule**. | 
   | Úložiště imagí dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z hodnoty názvu modulu projektu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/csharpmodule. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a container registry](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Vyberte **OK** změny. 

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí přihlašovací údaje pro svůj registr kontejneru se modul runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z **přístupové klíče** část svůj registr kontejneru Azure. 

1. V Průzkumníku řešení sady Visual Studio, otevřete **deployment.template.json** souboru. 

2. Najít **registryCredentials** vlastnost $edgeAgent požadované vlastnosti. 

3. Aktualizujte vlastnost pomocí svých přihlašovacích údajů, za tento formát: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Update the module with custom code

The default module code receives messages on an input queue and passes them along through an output queue. Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub. Update the module so that it analyzes the temperature data in each message, and only sends the message to IoT Hub if the temperature exceeds a certain threshold. 

1. In Visual Studio, open **CSharpModule** > **Program.cs**.

2. At the top of the **CSharpModule** namespace, add three **using** statements for types that are used later:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Přidat **temperatureThreshold** proměnnou **Program** třídy po proměnná čítače. Proměnná temperatureThreshold nastaví hodnotu, která musí být delší než teplota měřená pro data, která mají být odeslána do služby IoT hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Přidat **MessageBody**, **počítač**, a **vnější** třídy k **Program** třídy po deklarace proměnných. Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

5. Najít **Init** metody. Tato metoda vytvoří a nakonfiguruje **ModuleClient** objektu, který umožňuje modulu pro připojení k místní modul runtime Azure IoT Edge k odesílání a příjem zpráv. Tento kód také zaregistruje zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím **vstup1** koncového bodu.

   Celou metodu Init nahraďte následujícím kódem:
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   Tato aktualizovaná metoda Init připojení k modulu runtime IoT Edge se ModuleClient stále nastaví, ale také přidává nové funkce. Načte požadované vlastnosti dvojčete pro načtení modulu **temperatureThreshold** hodnotu. Potom vytvoří zpětné volání, která naslouchá pro všechny budoucí aktualizace požadované vlastnosti dvojčete modulu. Toto zpětné volání můžete vzdáleně aktualizovat prahová hodnota teploty v dvojčeti modulu a změny budou zahrnuty do modulu. 

   Aktualizované metody Init také změní existující **SetInputMessageHandlerAsync** metody. Ve vzorovém kódu příchozí zprávy *vstup1* se zpracovávají *PipeMessage* funkce, ale chcete změnit, aby používal *FilterMessages* funkce, které vytvoříme v následujících krocích. 

6. Přidat nový **onDesiredPropertiesUpdate** metodu **Program** třídy. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

7. Odebrání vzorku **PipeMessage** metoda a nahraďte ji metodou nový **FilterMessages** metody. Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu. Do zprávy také přidá vlastnost **MessageType**, která má nastavenou hodnotu **Alert**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Uložte soubor Program.cs.

9. Otevřít **deployment.template.json** souboru ve vašem řešení IoT Edge. Tento soubor říká agentovi, IoT Edge které moduly chcete nasadit, v tomto případě **tempSensor** a **CSharpModule**a informuje Centrum IoT Edge, jak můžete směrovat zprávy mezi nimi.

10. Přidejte do manifestu nasazení dvojče modulu **CSharpModule**. Vložte následující obsah JSON do dolní části oddílu **modulesContent** za dvojče modulu **$edgeHub**: 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Dvojče zařízení přidat do šablony nasazení](./media/tutorial-csharp-module-windows/module-twin.png)

11. Uložte soubor deployment.template.json.


## <a name="build-and-push-your-module"></a>Vytváření a nasdílení změn modulu

V předchozí části jste vytvořili řešení IoT a do modulu **CSharpModule** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru. 

1. Použijte následující příkaz pro přihlášení k Docker na svém vývojovém počítači. Použijte uživatelské jméno, heslo a přihlašovací server ze služby Azure container registry. Takové hodnoty můžete načíst **přístupové klíče** části vašeho registru na webu Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití `--password-stdin`. Tento osvědčený postup doporučuje pro produkční scénáře, je mimo rámec tohoto návodu. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odkaz.

2. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem na název projektu, který má být sestaveno. Výchozí název je **AzureIotEdgeApp1** a protože vytváříte modulu Windows, by měl být rozšíření **Windows.Amd64**. 

3. Vyberte **vytváření a nasdílení změn moduly IoT Edge**. 

   Sestavení a odeslání příkaz spustí tři operace. Nejprve vytvoří novou složku řešení, nazývá **config** , který obsahuje soubory manifestu, sestavení si informace při nasazení šablony a dalších řešení úplné nasazení. Za druhé, poběží `docker build` k sestavení image kontejneru, který je založen na příslušný soubor dockerfile pro cílové architektury. Pak spustí `docker push` tak, aby nabízel úložiště imagí do vašeho registru kontejneru. 

## <a name="deploy-modules-to-device"></a>Do zařízení nasadit moduly

Pomocí Průzkumníka cloudu sady Visual Studio a rozšíření Azure IoT Edge nástroje pro nasazení projektu modulu do zařízení IoT Edge. Už máte manifest nasazení připravili pro váš scénář **deployment.json** souboru ve složce Konfigurace. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že zařízení IoT Edge je zprovozněný. 

1. V Průzkumníku cloudu sady Visual Studio rozšíření prostředků k seznamu zařízení IoT. 

2. Klikněte pravým tlačítkem na název zařízení IoT Edge, které chcete dostávat nasazení. 

3. Vyberte **vytvořit nasazení**.

4. V Průzkumníku souborů vyberte **deployment.windows amd64** souboru ve složce Konfigurace vašeho řešení. 

5. Aktualizujte Průzkumníka cloudu zobrazíte nasazené moduly, které jsou uvedeny ve vašem zařízení. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí. 

Chcete-li zobrazit zprávy při jejich doručení na službě IoT Hub můžete použít rozšíření nástroje IoT Edge. 

1. V Průzkumníku cloudu sady Visual Studio vyberte název vašeho zařízení IoT Edge. 

2. V **akce** seznamu vyberte **spustit monitorování zpráv D2C**. 

3. Zobrazení zpráv přicházejících u služby IoT Hub. Může trvat nějakou dobu zprávy dorazí, protože změny, které jsme provedli kód CSharpModule Počkejte, dokud počítač teploty dosáhne 25 stupňů před odesláním zprávy. Také přidá typ zprávy **výstrah** pro všechny zprávy, které dosažení této prahové hodnoty teploty. 

   ![Zobrazení zpráv přicházejících u služby IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit dvojče modulu

Dvojče modulu CSharpModule jsme použili k nastavení prahové hodnoty teploty ve stupních 25. Chcete-li změnit funkci bez nutnosti aktualizovat kód modulu můžete použít dvojče modulu.

1. V sadě Visual Studio, otevřete **deployment.windows amd64.json** souboru. (Ne deployment.template soubor. Pokud se nasazení manifestu v konfiguračním souboru v Průzkumníku řešení, vyberte **zobrazit všechny soubory** ikonu na panelu nástrojů Průzkumník.)

2. Najít dvojče CSharpModule a změňte hodnotu **temperatureThreshold** parametr nové teploty 5 až 10 stupňů vyšší než nejnovější ohlášené teploty. 

3. Uložit **deployment.windows amd64.json** souboru.

4. Postupujte podle kroků nasazení znovu a použijte aktualizovaného manifestu nasazení do vašeho zařízení. 

5. Monitorování příchozích zpráv typu zařízení cloud. Měli byste vidět zprávy zastavit, dokud nebude dosaženo novou teploty prahovou hodnotu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

V opačném případě můžete odstranit místní konfigurací a použité v tomto článku se vyhnout poplatkům za prostředky Azure. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Jakmile budete připraveni k sestavení vlastních modulů, další informace o [vyvíjet vlastní moduly IoT Edge](module-development.md) nebo jak [vývoj modulů pomocí sady Visual Studio](how-to-visual-studio-develop-module.md). Budete moct pokračovat do další kurzy se dozvíte, jak Azure IoT Edge můžete nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [službu Custom Vision Service](tutorial-deploy-custom-vision.md)
