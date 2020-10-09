---
title: Kurz – vývoj modulu C# pro Windows pomocí Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem jazyka C# a nasadit ho do zařízení se systémem Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: b9451bcf6f67eb6afc6d06f4aa0cbec83ce75d6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855240"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Kurz: vývoj modulu C# IoT Edge pro zařízení s Windows

Použijte Visual Studio pro vývoj kódu C# a nasaďte ho do zařízení s Windows, na kterém běží Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí sady Visual Studio vytvořte modul IoT Edge založený na sadě C# SDK.
> * Pomocí sady Visual Studio a Docker vytvoříte image Docker a publikujete ji do svého registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvíjet modul v **jazyce C#** pomocí sady **Visual Studio 2019** a jak ho nasadit na **zařízení s Windows**. Pokud vyvíjíte moduly pro zařízení se systémem Linux, přečtěte si místo toho [vývoj modulu C# IoT Edge modul pro zařízení se systémem Linux](tutorial-csharp-module.md) .

Následující tabulka vám pomůže pochopit možnosti vývoje a nasazení modulů C# do zařízení s Windows:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Vývoj pro Windows AMD64** | ![Vývoj modulů C# pro WinAMD64 v VS Code](./media/tutorial-c-module/green-check.png) | ![Vývoj modulů C# pro WinAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Ladění systému Windows AMD64** |   | ![Ladění modulů C# pro WinAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz pro nastavení vývojového prostředí a [vytvořit modul IoT Edge pro zařízení s Windows](tutorial-develop-for-windows.md). Po dokončení tohoto kurzu už byste měli mít následující požadavky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Windows, na kterém běží Azure IoT Edge](quickstart.md).
* Registr kontejneru, například [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) nakonfigurovaný s rozšířením [nástrojů Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) nakonfigurovaný pro spouštění kontejnerů Windows.

> [!TIP]
> Pokud používáte Visual Studio 2017 (verze 15,7 nebo novější), plrease si stáhněte a nainstalujte [nástroje Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro vs 2017 z webu Visual Studio Marketplace.

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

Následující postup slouží k vytvoření projektu IoT Edge modulu pomocí sady Visual Studio a rozšíření Azure IoT Edge Tools. Jakmile máte vytvořenou šablonu projektu, přidejte nový kód tak, aby modul vyfiltroval zprávy na základě jejich hlášených vlastností.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Nástroje Azure IoT Edge poskytují šablony projektů pro všechny podporované jazyky IoT Edge modulů v aplikaci Visual Studio. Tyto šablony obsahují všechny soubory a kód, které potřebujete k nasazení pracovního modulu k testování IoT Edge, nebo vám poskytne výchozí bod pro přizpůsobení šablony s vlastní obchodní logikou.

1. Spusťte Visual Studio 2019 a vyberte **vytvořit nový projekt**.

2. Vyhledejte **IoT Edge** a vyberte projekt **Azure IoT Edge (Windows amd64)** . Klikněte na **Next** (Další).

   ![Vytvoření nového projektu Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Přejmenujte projekt a řešení na něco popisného, jako je **CSharpTutorialApp**. Kliknutím na **vytvořit** vytvořte projekt.

   ![Konfigurace nového projektu Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Nakonfigurujte projekt s následujícími hodnotami:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat šablonu | Vyberte **modul C#**. |
   | Název projektu modulu | Dejte modulu název **CSharpModule**. |
   | Úložiště imagí Docker | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru je předem vyplněná z hodnoty název projektu modulu. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. <br><br> Konečné úložiště imagí vypadá jako \<registry name\> . azurecr.IO/csharpmodule. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a registr kontejnerů](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Vyberte **Přidat** a vytvořte projekt.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí přihlašovací údaje pro váš registr kontejneru s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z oddílu **přístupové klíče** ve službě Azure Container Registry.

1. V Průzkumníku řešení sady Visual Studio otevřete **deployment.template.jsv** souboru.

2. V $edgeAgent požadovaných vlastnostech Najděte vlastnost **registryCredentials** . Měla by mít vaše adresa registru vytvořená z informací, které jste zadali při vytváření projektu, a pole s uživatelským jménem a heslem by měla obsahovat názvy proměnných. Příklad:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otevřete soubor **. env** v řešení modulu. (Ve výchozím nastavení je to ve Průzkumník řešení skryté, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory** a zobrazit ho.) Soubor. env by měl obsahovat stejné proměnné uživatelského jména a hesla, které jste viděli v deployment.template.jssouboru.

4. Přidejte hodnoty **uživatelského jména** a **hesla** z Azure Container Registry.

5. Uložte změny do souboru. env.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je spolu s výstupní frontou. Pojďme přidat nějaký další kód, aby modul zpracoval zprávy na hranici před jejich přesměrováním na IoT Hub. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě, a pošle zprávu jenom IoT Hub, pokud teplota překročí určitou prahovou hodnotu.

1. V aplikaci Visual Studio otevřete **CSharpModule**  >  **program.cs**.

2. Nahoře v oboru názvů **CSharpModule** přidejte tři příkazy **using** pro typy, které se použijí později:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Přidejte proměnnou **temperatureThreshold** do třídy **program** za proměnnou čítače. Proměnná temperatureThreshold nastaví hodnotu, kterou musí měřená teplota překročit, aby se data odesílala do služby IoT Hub.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Po deklaracích proměnných přidejte do třídy **program** třídy **MessageBody**, **Machine**a **Ambient** . Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

5. Vyhledejte metodu **init** . Tato metoda vytvoří a nakonfiguruje objekt **ModuleClient** , který umožňuje modulu připojit se k místnímu modulu Azure IoT Edge runtime pro odesílání a příjem zpráv. Kód také zaregistruje zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím koncového bodu **input1** .

   Celý inicializační metodu nahraďte následujícím kódem:

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

   Tato aktualizovaná metoda init pořád nastaví připojení k modulu runtime IoT Edge pomocí ModuleClient, ale také přidá nové funkce. Načte požadované vlastnosti nevláken modulu, aby získal hodnotu **temperatureThreshold** . Potom vytvoří zpětné volání, které naslouchá jakýmkoli budoucím aktualizacím požadovaných vlastností modulu. V rámci tohoto zpětného volání můžete aktualizovat prahovou hodnotu teploty v modulu, který je na dálku, a změny budou začleněny do modulu.

   Aktualizovaná metoda init také změní existující metodu **SetInputMessageHandlerAsync** . V ukázkovém kódu se příchozí zprávy na *input1* zpracovávají pomocí funkce *PipeMessage* , ale chceme změnit, aby používala funkci *FilterMessages* , kterou vytvoříme v následujících krocích.

6. Přidejte do třídy **program** novou metodu **onDesiredPropertiesUpdate** . Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

7. Odeberte ukázkovou metodu **PipeMessage** a nahraďte ji novou metodou **FilterMessages** . Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu. Do zprávy také přidá vlastnost **MessageType**, která má nastavenou hodnotu **Alert**.

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
                using(var filteredMessage = new Message(messageBytes))
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

9. Otevřete **deployment.template.js** v souboru v řešení IoT Edge. Tento soubor informuje agenta IoT Edge, který moduly mají nasadit, v tomto případě **SimulatedTemperatureSensor** a **CSharpModule**a oznamuje IoT Edgemu centru, jak směrovat zprávy mezi nimi.

10. Přidejte do manifestu nasazení dvojče modulu **CSharpModule**. Vložte následující obsah JSON do dolní části oddílu **modulesContent** za dvojče modulu **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Přidat nevlákenný modul do šablony nasazení](./media/tutorial-csharp-module-windows/module-twin.png)

11. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT a do modulu **CSharpModule** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Pomocí následujícího příkazu se přihlaste k Docker na svém vývojovém počítači. Použijte uživatelské jméno, heslo a přihlašovací server ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

2. V Průzkumníku řešení sady Visual Studio klikněte pravým tlačítkem myši na název projektu, který chcete sestavit. Výchozí název je **AzureIotEdgeApp1** a vzhledem k tomu, že vytváříte modul systému Windows, musí být přípona **Windows. amd64**.

3. Vyberte **sestavení a moduly nabízených IoT Edge**.

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Visual Studio Cloud Explorer a rozšíření Azure IoT Edge Tools. Už máte připravený manifest nasazení pro váš scénář, **deployment.windows-amd64.js** v souboru ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníku cloudu sady Visual Studio rozbalte prostředky, abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem myši na název zařízení IoT Edge, pro které chcete nasazení přijmout.

3. Vyberte **vytvořit nasazení**.

4. V Průzkumníku souborů vyberte **deployment.windows-amd64.js** v souboru ve složce config vašeho řešení.

5. Aktualizujte Průzkumníka cloudu, aby se zobrazily nasazené moduly uvedené v rámci vašeho zařízení.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

K zobrazení zpráv při jejich doručování do IoT Hub můžete použít rozšíření IoT Edge Tools.

1. V Průzkumníku cloudu sady Visual Studio vyberte název zařízení IoT Edge.

2. V seznamu **Akce** vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

3. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat, protože změny, které jsme provedli v kódu CSharpModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících na IoT Hub](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

Pro nastavení prahové hodnoty teploty v 25 stupních jsme použili dvojitou CSharpModule modul. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V aplikaci Visual Studio otevřete **deployment.windows-amd64.jsv** souboru. (Ne soubor Deployment. template. Pokud se v konfiguračním souboru v Průzkumníkovi řešení nezobrazuje manifest nasazení, vyberte na panelu nástrojů Průzkumníka ikonu **Zobrazit všechny soubory** .)

2. Najděte CSharpModule dvojitou hodnotu a změňte hodnotu parametru **temperatureThreshold** na novou teplotu 5 stupňů na 10 stupňů vyšší než Poslední hlášená teplota.

3. Uložte **deployment.windows-amd64.jsdo** souboru.

4. Podle kroků nasazení znovu použijte aktualizovaný manifest nasazení na vaše zařízení.

5. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge.

V dalších kurzech můžete pokračovat a zjistit, jak vám Azure IoT Edge může pomáhat s nasazením cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md) 
>  [Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Custom Vision Service](tutorial-deploy-custom-vision.md)
