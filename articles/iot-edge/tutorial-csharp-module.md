---
title: Kurz – vývoj modulu C# pro Linux pomocí Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem jazyka C# a nasadit ho do zařízení IoT Edge se systémem Linux.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 01b30fed23b33719f08e93907075eee757343b1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461735"
---
# <a name="tutorial-develop-a-c-iot-edge-module-using-linux-containers"></a>Kurz: vývoj modulu IoT Edge C# pomocí kontejnerů Linux

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Pomocí Visual Studio Code můžete vyvíjet kód v jazyce C# a nasadit ho do zařízení se systémem Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí Visual Studio Code můžete vytvořit modul IoT Edge založený na sadě .NET Core 2,1 SDK.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento kurz ukazuje, jak vytvořit modul v **jazyce C#** pomocí **Visual Studio Code** a nasadit ho do IoT Edgeho zařízení. Pokud vyvíjíte moduly pomocí kontejnerů Windows, použijte místo toho [vývoj modulu C# IoT Edge pomocí kontejnerů Windows](tutorial-csharp-module-windows.md) .

Následující tabulka vám pomůže pochopit možnosti vývoje a nasazení modulů C# pomocí kontejnerů Linux:

| C# | Visual Studio Code | Visual Studio |
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![Moduly C# pro LinuxAMD64 v VS Code](./media/tutorial-c-module/green-check.png) | ![Moduly C# pro LinuxAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![Moduly C# pro LinuxARM32 v VS Code](./media/tutorial-c-module/green-check.png) | ![Moduly C# pro LinuxARM64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>Podpora pro zařízení se systémem Linux ARM64 je k dispozici ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace najdete v tématu [vývoj a ladění ARM64 IoT Edgech modulů v Visual Studio Code (Preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

Před zahájením tohoto kurzu byste si měli projít předchozí kurz nastavení vývojového prostředí a vývoj [modulu IoT Edge pomocí kontejnerů Linux](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu už byste měli mít následující požadavky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Zařízení se systémem Azure IoT Edge. Pomocí rychlých startů můžete nastavit zařízení se systémem [Linux](quickstart-linux.md) nebo [zařízení s Windows](quickstart.md).
* Registr kontejneru, například [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

Pokud chcete tyto kurzy dokončit, připravte na svém vývojovém počítači následující další požadavky:

* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

Následující postup slouží k vytvoření projektu IoT Edge modulu pro jazyk C# pomocí Visual Studio Code a rozšíření Azure IoT Tools. Jakmile máte vytvořenou šablonu projektu, přidejte nový kód tak, aby modul vyfiltroval zprávy na základě jejich hlášených vlastností.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v jazyce C#, kterou můžete přizpůsobit pomocí vlastního kódu.

1. V Visual Studio Code výběrem **Zobrazit**  >  **paleta příkazů** otevřete paletu příkazů vs Code.

2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **modul C#**. |
   | Zadejte název modulu | Dejte modulu název **CSharpModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru se předem vyplní názvem, který jste zadali v posledním kroku. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. <br><br>Konečné úložiště imagí vypadá jako \<registry name\> . azurecr.IO/csharpmodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-csharp-module/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z oddílu **přístupové klíče** ve službě Azure Container Registry.

Rozšíření IoT Edge se pokusí načíst přihlašovací údaje registru kontejneru z Azure a naplnit je do souboru prostředí. Zkontrolujte, jestli jsou vaše přihlašovací údaje už zahrnuté. Pokud ne, přidejte je nyní:

1. V Průzkumníku VS Code otevřete soubor **. env** .
2. Aktualizujte pole hodnotami **uživatelského jména** a **hesla** z Azure Container Registry.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly C# pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení Edge** nebo vyberte ikonu zástupce na bočním panelu v dolní části okna.

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku vs Code otevřete **modules**  >  **CSharpModule**  >  **program. cs**.

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

5. Vyhledejte funkci **init** . Tato funkce vytvoří a nakonfiguruje objekt **ModuleClient** , který umožňuje modulu připojit se k místnímu modulu Azure IoT Edge runtime pro odesílání a příjem zpráv. Po vytvoření objektu **ModuleClient** kód přečte hodnotu **TemperatureThreshold** z požadovaných vlastností dvojčete modulu. a zaznamená zpětné volání přijatých zpráv z IoT Edge Hubu prostřednictvím koncového bodu **input1**. Nahraďte metodu **SetInputMessageHandlerAsync** novou metodou a přidejte metodu **SetDesiredPropertyUpdateCallbackAsync** pro aktualizace požadovaných vlastností. Pokud chcete tuto změnu provést, nahraďte poslední řádek metody **Init** následujícím kódem:

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

    ![Přidat nevlákenný modul do šablony nasazení](./media/tutorial-csharp-module/module-twin.png)

11. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste kód do CSharpModule. Nový kód filtruje zprávy, kde teplota hlášeného počítače spadá do přípustných limitů. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Otevřete vs Code Integrated Terminal výběrem možnosti **Zobrazit**  >  **terminál**.

1. Přihlaste se k Docker zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. V Průzkumníku VS Code klikněte pravým tlačítkem na **deployment.template.jsna** soubor a vyberte **sestavení a nabízené IoT Edge řešení**.

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

K nasazení projektu modulu do zařízení IoT Edge použijte Průzkumníka Visual Studio Code a rozšíření Azure IoT Tools. Už máte připravený manifest nasazení pro váš scénář, **deployment.amd64.js** v souboru ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníkovi Visual Studio Code v části **Azure IoT Hub** rozbalte **zařízení** , abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte **deployment.amd64.js** v souboru ve složce **config** a pak klikněte na **Vybrat manifest nasazení Edge**. Nepoužívejte soubor deployment.template.json.

4. V části zařízení rozbalte **moduly** a zobrazte seznam nasazených a spuštěných modulů. Klikněte na tlačítko pro obnovení. Měl by se zobrazit nový **CSharpModule** spuštěný spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

    Spuštění modulů může trvat několik minut. Modul runtime IoT Edge musí přijmout nový manifest nasazení, Stáhnout image modulu z modulu runtime kontejneru a pak začít každý nový modul.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

2. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu CModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících na IoT Hub](./media/tutorial-csharp-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

V manifestu nasazení jsme použili vláken Module CSharpModule, které nastaví prahovou hodnotu teploty na 25 stupních. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V Visual Studio Code rozbalte podrobnosti pod zařízením IoT Edge a podívejte se na běžící moduly.

2. Klikněte pravým tlačítkem na **CSharpModule** a vyberte **Upravit modul s dvojitou** čárkou.

3. V požadovaných vlastnostech vyhledejte **TemperatureThreshold** . Změňte jeho hodnotu na novou teplotu 5 stupňů na 10 stupňů vyšší než při nejnovější hlášené teplotě.

4. Uložte modul s dvojitým pracovním souborem.

5. Klikněte pravým tlačítkem na libovolné místo v podoknì s dvojitou úpravou modulu a vyberte **aktualizovat modul vlákna**.

6. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge.

V dalších kurzech můžete pokračovat a zjistit, jak vám Azure IoT Edge může pomáhat s nasazením cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)