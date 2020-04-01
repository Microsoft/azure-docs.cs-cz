---
title: Výuka – vývoj modulu C# pro Linux pomocí Azure IoT Edge
description: Tento kurz ukazuje, jak vytvořit modul IoT Edge s kódem C# a nasadit ho do zařízení IoT Edge s Linuxem.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 53e1863f6f3421a6d8df9112f463f16443cff93e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943049"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj modulu C# IoT Edge pro linuxová zařízení

Pomocí kódu Visual Studia můžete vyvinout kód C# a nasadit ho do linuxového zařízení se systémem Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí kódu Visual Studio vytvořte modul IoT Edge, který je založen na sadě .NET Core 2.1 SDK.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvinout modul v **jazyce C#** pomocí **kódu sady Visual Studio** a nasadit jej do zařízení S **IP**. Pokud vyvíjíte moduly pro zařízení s Windows, přejděte na [vývoj modulu C# IoT Edge pro zařízení s Windows.](tutorial-csharp-module-windows.md)

V následující tabulce můžete pochopit možnosti vývoje a nasazování modulů Jazyka C# do Linuxu:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Moduly C# pro LinuxAMD64 v kódu VS](./media/tutorial-c-module/green-check.png) | ![Moduly Jazyka C# pro LinuxAMD64 ve Visual Studiu](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Moduly C# pro LinuxARM32 v Kódu VS](./media/tutorial-c-module/green-check.png) | ![Moduly C# pro LinuxARM64 ve Visual Studiu](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Podpora pro zařízení Linux ARM64 je k dispozici ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace naleznete v [tématu Vývoj a ladění modulů ARM64 IoT Edge v kódu Visual Studio (preview).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz pro nastavení vývojového prostředí, [vývoj modulu IoT Edge pro zařízení S Linuxem](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu byste již měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Linuxové zařízení se systémem Azure IoT Edge](quickstart-linux.md).
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Kód](https://code.visualstudio.com/) nakonfigurovaný pomocí [nástrojů Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Linuxu.

Chcete-li dokončit tyto kurzy, připravte následující další předpoklady na vývojovém počítači:

* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Vytvoření projektu modulu

Následující kroky vytvořit projekt modulu IoT Edge pro C# pomocí Visual Studio Code a rozšíření Nástroje Azure IoT. Po vytvoření šablony projektu přidejte nový kód tak, aby modul odfiltrovat zprávy na základě jejich hlášené vlastnosti.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v jazyce C#, kterou můžete přizpůsobit pomocí vlastního kódu.

1. V kódu Visual Studia vyberte **Zobrazit** > **paletu příkazů** a otevřete paletu příkazů VS Code.

2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení nebo přijměte výchozí **edgesolution**. |
   | Vyberte šablonu modulu | Zvolte **modul Jazyka C#**. |
   | Zadejte název modulu | Dejte modulu název **CSharpModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z názvu, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Konečné úložiště bitových \<obrázků\>vypadá jako název registru .azurecr.io/csharpmodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z části **Přístupové klíče** v registru kontejnerů Azure.

1. V průzkumníku kódu VS otevřete soubor **ENV.**
2. Aktualizujte pole pomocí hodnot **uživatelského jména** a **hesla** z registru kontejnerů Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době Visual Studio Code můžete vyvíjet moduly C# pro Linux AMD64 a Linux ARM32v7 zařízení. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože kontejner je sestaven a spuštěn jinak pro každý typ architektury. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V průzkumníku kódu VS otevřete **moduly** > **CSharpModule** > **Program.cs**.

2. Nahoře v oboru názvů **CSharpModule** přidejte tři příkazy **using** pro typy, které se použijí později:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Do třídy **Program** přidejte proměnnou **temperatureThreshold**. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Do třídy **Program** přidejte třídy **MessageBody**, **Machine** a **Ambient**. Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

5. Najděte funkci **Init.** Tato funkce vytvoří a nakonfiguruje objekt **ModuleClient,** který umožňuje modulu připojit se k místnímu modulu runtime Azure IoT Edge k odesílání a přijímání zpráv. Po vytvoření objektu **ModuleClient** kód přečte hodnotu **TemperatureThreshold** z požadovaných vlastností dvojčete modulu. a zaznamená zpětné volání přijatých zpráv z IoT Edge Hubu prostřednictvím koncového bodu **input1**. Nahraďte metodu **SetInputMessageHandlerAsync** novou metodou a přidejte metodu **SetDesiredPropertyUpdateCallbackAsync** pro aktualizace požadovaných vlastností. Pokud chcete tuto změnu provést, nahraďte poslední řádek metody **Init** následujícím kódem:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. Do třídy **Program** přidejte metodu **onDesiredPropertiesUpdate**. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

7. Nahraďte metodu **PipeMessage** metodou **FilterMessages**. Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu. Do zprávy také přidá vlastnost **MessageType**, která má nastavenou hodnotu **Alert**.

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
                using (var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
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

9. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

10. Přidejte do manifestu nasazení dvojče modulu **CSharpModule**. Vložte následující obsah JSON do dolní části oddílu **modulesContent** za dvojče modulu **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Přidání dvojčete modulu do šablony nasazení](./media/tutorial-csharp-module/module-twin.png)

11. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavte a tlačte svůj modul

V předchozí části jste vytvořili řešení IoT Edge a přidali kód do CSharpModule. Nový kód filtruje zprávy, kde je hlášená teplota stroje v přijatelných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Otevřete integrovaný terminál VS Code výběrem **možnosti Zobrazit** > **terminál**.

1. Přihlaste se k Dockeru zadáním následujícího příkazu do terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru z registru kontejnerů Azure. Tyto hodnoty můžete načíst z části **Přístupové klíče** v registru na webu Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. V průzkumníku kódu VS klikněte pravým tlačítkem myši na soubor **deployment.template.json** a vyberte **možnost Sestavit a push řešení IoT Edge**.

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

Pomocí průzkumníka kódu Visual Studio a rozšíření Nástroje Azure IoT k nasazení projektu modulu do zařízení IoT Edge. Již máte manifest nasazení připravený pro váš scénář, soubor **deployment.json** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku kódu Visual Studia rozbalte část **Zařízení služby Azure IoT Hub,** abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měli byste vidět nový **CSharpModule** běží spolu s **Modul SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.  

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V průzkumníku kódu Visual Studia klikněte pravým tlačítkem myši na název zařízení IoT Edge a vyberte **spustit sledování integrovaného koncového bodu událostí**.

2. Zobrazení zpráv přicházejících do vašeho IoT Hubu. Může chvíli trvat, než zprávy dorazí, protože zařízení IoT Edge musí přijímat nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu CModule, pak před odesláním zpráv počkejte, dokud teplota stroje nedosáhne 25 stupňů. Přidá také typ zprávy **Výstraha** všechny zprávy, které dosáhnou této prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících do ioT hubu](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Úprava dvojčete modulu

Použili jsme dvojče modulu CSharpModule v manifestu nasazení k nastavení prahové hodnoty teploty na 25 stupňů. Dvojče modulu můžete použít ke změně funkce bez nutnosti aktualizace kódu modulu.

1. V kódu Visual Studio rozbalte podrobnosti v zařízení IoT Edge a podívejte se na spuštěné moduly.

2. Klepněte pravým tlačítkem myši na **příkaz CSharpModule** a vyberte **příkaz Upravit dvojče modulu**.

3. Najít **TemperatureThreshold** v požadovaných vlastnostech. Změňte jeho hodnotu na novou teplotu o 5 stupňů až 10 stupňů vyšší než poslední hlášená teplota.

4. Uložte soubor dvojčete modulu.

5. Klepněte pravým tlačítkem myši na libovolné místo v podokně úprav dvojčete modulu a vyberte **možnost Aktualizovat dvojče modulu**.

6. Sledujte příchozí zprávy mezi zařízeními a cloudy. Měli byste vidět zprávy zastavit, dokud není dosaženo nového prahu teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete se dozvědět více o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů s visual studio code](how-to-vs-code-develop-module.md). Příklady modulů IoT Edge, včetně modulu simulované teploty, viz [ukázky modulu IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Můžete pokračovat na další kurzy se dozvíte, jak Azure IoT Edge vám může pomoci nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Vlastní Vision Service](tutorial-deploy-custom-vision.md)
