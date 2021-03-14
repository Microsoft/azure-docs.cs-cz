---
title: Kurz – vývoj modulů C# pro Windows pomocí Azure IoT Edge
description: V tomto kurzu se dozvíte, jak vytvořit moduly IoT Edge s kódem v jazyce C# a jak je nasadit na zařízení s Windows, na kterých běží IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
ms.openlocfilehash: 4e01b1ca9a3858ff31ad9b5da1d1159209c44330
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464053"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Kurz: vývoj modulů C# IoT Edge pomocí kontejnerů Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

V tomto článku se dozvíte, jak pomocí sady Visual Studio vyvíjet kód v jazyce C# a nasadit ho do zařízení s Windows, které běží Azure IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS je poslední kanál verze, který bude podporovat kontejnery Windows. Od verze 1,2 nejsou kontejnery Windows podporované. Při spouštění IoT Edge na zařízeních s Windows zvažte použití nebo přesunutí [IoT Edge pro Linux ve Windows](iot-edge-for-linux-on-windows.md) .

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje vaši obchodní logiku přímo v zařízeních IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí sady Visual Studio vytvořte modul IoT Edge založený na sadě C# SDK.
> * Pomocí sady Visual Studio a Docker vytvoříte image Docker a publikujete ji do svého registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Modul odesílá zprávy pouze v případě, že teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Tento kurz ukazuje, jak vytvořit modul v jazyce C# pomocí sady Visual Studio 2019 a jak ho nasadit na zařízení s Windows. Pokud vyvíjíte moduly pomocí kontejnerů pro Linux, použijte místo toho [modul pro vývoj C# IoT Edge pomocí kontejnerů Linux](tutorial-csharp-module.md) .

Chcete-li pochopit možnosti vývoje a nasazení modulů C# pomocí kontejnerů systému Windows, přečtěte si následující tabulku:

| C# | Visual &nbsp; Studio &nbsp; Code | Visual Studio 2017 &nbsp; a &nbsp; 2019 |
| -- | :------------------: | :------------------: |
| Vývoj pro Windows AMD64 | ![Vývoj modulů C# pro WinAMD64 v Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Vývoj modulů C# pro WinAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |
| Ladění systému Windows AMD64 |   | ![Ladění modulů C# pro WinAMD64 v aplikaci Visual Studio](./media/tutorial-c-module/green-check.png) |

Než začnete s tímto kurzem, nastavte vývojové prostředí podle pokynů uvedených v kurzu [vývoj IoT Edgech modulů pomocí kontejnerů Windows](tutorial-develop-for-windows.md) . Po dokončení bude vaše prostředí obsahovat následující požadavky:

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) na bezplatné nebo standardní úrovni v Azure.
* [Zařízení s Windows, na kterém běží Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md).
* Registr kontejnerů, například [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio 2019](/visualstudio/install/install-visual-studio)nakonfigurovaný s rozšířením [nástrojů Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) .
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)nakonfigurovaný pro spouštění kontejnerů Windows.

> [!TIP]
> Pokud používáte Visual Studio 2017 (verze 15,7 nebo novější), Stáhněte a nainstalujte Azure IoT Edge nástroje pro Visual Studio 2017 z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="create-a-module-project"></a>Vytvořit projekt modulu

V této části vytvoříte projekt modulu IoT Edge pomocí sady Visual Studio a rozšíření Azure IoT Edge Tools. Po vytvoření šablony projektu přidáte nový kód tak, aby modul vyfiltroval zprávy na základě jejich hlášených vlastností.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Azure IoT Edge nástroje poskytují šablony projektů pro všechny podporované jazyky IoT Edge modulů v aplikaci Visual Studio. Tyto šablony obsahují všechny soubory a kód, které potřebujete k nasazení pracovního modulu pro účely testování IoT Edge. Můžou vám taky poskytnout výchozí bod pro přizpůsobení s vlastní obchodní logikou.

1. Otevřete Visual Studio 2019 a pak vyberte **vytvořit nový projekt**.

1. V podokně **vytvořit nový projekt** vyhledejte **IoT Edge** a potom v seznamu výsledků vyberte projekt **Azure IoT Edge (Windows amd64)** .

   ![Snímek obrazovky s podoknem IoT Edge vytvořit nový projekt](./media/tutorial-csharp-module-windows/new-project.png)

1. Vyberte **Další**.

    Otevře se podokno **Konfigurovat nový projekt** .

   ![Snímek obrazovky s podoknem konfigurovat nový projekt](./media/tutorial-csharp-module-windows/configure-project.png)

1. V podokně **Konfigurovat nový projekt** přejmenujte projekt a řešení na výstižnější, například **CSharpTutorialApp**. 

1. Vyberte **Vytvořit** a vytvořte projekt.

   Otevře se podokno **Přidat modul** .

   ![Snímek obrazovky s podoknem přidat modul pro konfiguraci projektu](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Na stránce **Konfigurovat nový projekt** proveďte následující:

   a. V levém podokně vyberte šablonu **modulu C#** .  
   b. Do pole **název modulu** zadejte **CSharpModule**.  
   c. V poli **Adresa URL úložiště** nahraďte **localhost: 5000** hodnotou **přihlašovacího serveru** z služby Azure Container Registry, a to v následujícím formátu: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru je předem vyplněná z modulu hodnota název projektu.  Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal.

1. Vyberte **Přidat** a vytvořte projekt.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí přihlašovací údaje pro váš registr kontejneru s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z oddílu **přístupové klíče** ve službě Azure Container Registry.

1. V aplikaci Visual Studio Průzkumník řešení otevřete *deployment.template.jsv* souboru.

1. V $edgeAgent požadovaných vlastnostech vyhledejte vlastnost **registryCredentials** . Adresa registru vlastnosti by měla být vyplněna informacemi, které jste zadali při vytváření projektu. Pole uživatelského jména a hesla by měla obsahovat názvy proměnných. Například:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Otevřete soubor prostředí (ENV) ve vašem řešení modulu. Ve výchozím nastavení je soubor v Průzkumník řešení skrytý, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory** a zobrazit ho. Soubor ENV by měl obsahovat stejné proměnné uživatelského jména a hesla, které jste viděli v *deployment.template.js* souboru.

1. Přidejte hodnoty **uživatelského jména** a **hesla** z Azure Container Registry.

1. Uložte změny do souboru ENV.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je spolu s výstupní frontou. Pojďme přidat nějaký další kód, aby modul zpracoval zprávy na hraničních zařízeních před jejich přesměrováním do služby IoT Hub. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě a odeslal zprávu do centra IoT pouze v případě, že teplota překročí určitou prahovou hodnotu.

1. V aplikaci Visual Studio vyberte **CSharpModule**  >  **program.cs**.

1. Nahoře v oboru názvů **CSharpModule** přidejte tři příkazy **using** pro typy, které se použijí později:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Přidejte proměnnou **temperatureThreshold** do třídy **program** za proměnnou čítače. Proměnná temperatureThreshold nastaví hodnotu, kterou musí měřená teplota překročit, aby se data odesílala do služby IoT Hub.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Po deklaracích proměnných přidejte do třídy **program** třídy **MessageBody**, **Machine** a **Ambient** . Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

1. Vyhledejte metodu **init** . Tato metoda vytvoří a nakonfiguruje objekt **ModuleClient** , který umožňuje modulu připojit se k místnímu modulu Azure IoT Edge runtime pro odesílání a příjem zpráv. Kód také zaregistruje zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím koncového bodu **input1** .

   Celý inicializační metodu nahraďte následujícím kódem:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
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

1. Přidejte do třídy **program** novou metodu **onDesiredPropertiesUpdate** . Tato metoda přijímá aktualizace požadovaných vlastností z modulu jako nevlákenný a aktualizuje proměnnou **temperatureThreshold** tak, aby odpovídala. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

1. Odeberte ukázkovou metodu **PipeMessage** a nahraďte ji novou metodou **FilterMessages** . Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Filtruje zprávy, které vykazují teploty pod prahovou hodnotou teploty, která je nastavena přes modul s dvojitou hodnotou. Metoda také přidá vlastnost **MessageType** do zprávy s hodnotou nastavenou na hodnotu **Alert**.

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

1. Uložte soubor *Program.cs*.

1. Otevřete *deployment.template.js* v souboru v řešení IoT Edge. Tento soubor oznamuje agentovi IoT Edge, které moduly mají nasadit, a oznamuje IoT Edgemu centru, jak směrovat zprávy mezi nimi. Zde jsou moduly, které se mají nasadit, **SimulatedTemperatureSensor** a **CSharpModule**.

1. Přidejte do manifestu nasazení dvojče modulu **CSharpModule**. `modulesContent`Po vyzdvojení **$edgeHub** modulu vložte následující obsah JSON do dolní části části.

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Snímek obrazovky znázorňující, že se modul přidávají do šablony nasazení.](./media/tutorial-csharp-module-windows/module-twin.png)

1. Uložte soubor *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste kód pro odfiltrování zpráv **, u kterých** se teplota hlášeného počítače nachází pod přijatelnou prahovou hodnotou. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

### <a name="sign-in-to-docker"></a>Přihlásit se k Docker

1. Pomocí následujícího příkazu se přihlaste k Docker na svém vývojovém počítači. Použijte uživatelské jméno, heslo a přihlašovací server ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když doporučujeme tento postup jako osvědčený postup pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace najdete v tématu [přihlašovací Reference k Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Sestavení a vložení

1. V aplikaci Visual Studio Průzkumník řešení klikněte pravým tlačítkem myši na název projektu, který chcete sestavit. Výchozí název je **AzureIotEdgeApp1** a, protože vytváříte modul systému Windows, přípona by měla být **Windows. amd64**.

1. Vyberte **sestavení a moduly nabízených IoT Edge**.

   Příkaz Build a push spustí tři operace:
   * Nejprve vytvoří novou složku v řešení s názvem *config*, která obsahuje úplný manifest nasazení. Je sestaven z informací v šabloně nasazení a v dalších souborech řešení. 
   * Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. 
   * Nakonec se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů bude rychlejší.

## <a name="deploy-modules-to-the-device"></a>Nasazení modulů do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Visual Studio Cloud Explorer a rozšíření Azure IoT Edge Tools. Již jste připravili manifest nasazení pro váš scénář, *deployment.windows-amd64.js* v souboru ve složce *config* . Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníku cloudu sady Visual Studio rozbalte prostředky, abyste si zobrazili seznam zařízení IoT.

1. Klikněte pravým tlačítkem myši na název zařízení IoT Edge, pro které chcete nasazení přijmout.

1. Vyberte **vytvořit nasazení**.

1. V Průzkumníku souborů sady Visual Studio vyberte *deployment.windows-amd64.js* v souboru ve složce *config* vašeho řešení.

1. Aktualizujte Průzkumníka cloudu, aby se zobrazily nasazené moduly, které jsou uvedené v rámci vašeho zařízení.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

Po použití manifestu nasazení na zařízení IoT Edge bude modul runtime IoT Edge na zařízení shromažďovat nové informace o nasazení a spustí se na něm. Všechny moduly, které jsou spuštěny v zařízení, ale nejsou součástí manifestu nasazení, jsou zastaveny. Všechny moduly, které v zařízení chybí, jsou spuštěné.

Pomocí rozšíření IoT Edge Tools můžete zobrazovat zprávy při jejich doručování do služby IoT Hub.

1. V Průzkumníku cloudu sady Visual Studio vyberte název zařízení IoT Edge.

1. V seznamu **Akce** vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

1. Podívejte se na zprávy, které přicházejí do služby IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny kódu CSharpModule musí počkat, dokud teplota počítače nedosáhne 25 stupňů předtím, než bude možné zprávy odeslat. Kód také přidá **výstrahu** typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty.

   ![Snímek obrazovky okna výstup zobrazující zprávy, které přicházejí do centra IoT Hub.](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

Pro nastavení prahové hodnoty teploty na 25 stupňů jste použili modul CSharpModule s dvojitou hodnotou. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V aplikaci Visual Studio otevřete *deployment.windows-amd64.jsv* souboru. 

   Neotevírejte *soubor* *Deployment. template* . Pokud se v *konfiguračním* souboru v Průzkumník řešení nezobrazuje manifest nasazení, vyberte na panelu nástrojů Průzkumník řešení ikonu **Zobrazit všechny soubory** .

1. Vyhledejte CSharpModule s dvojitou hodnotou a změňte hodnotu parametru **temperatureThreshold** na novou teplotu, která je 5 až 10 stupňů vyšší než Poslední hlášená teplota.

1. Uložte *deployment.windows-amd64.jsdo* souboru.

1. Podle pokynů pro nasazení znovu použijte aktualizovaný manifest nasazení na vaše zařízení.

1. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Zprávy by měly skončit až do dosažení nové prahové hodnoty teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat k dalšímu doporučenému článku, můžete zachovat a znovu použít prostředky a konfigurace, které jste vytvořili v tomto kurzu. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete k tomu, abyste se vyhnuli poplatkům, mohli odstranit místní konfigurace a prostředky Azure, které jste zde použili.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge.

Další informace o tom, jak vám Azure IoT Edge může pomáhat při nasazení cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních, najdete v dalších kurzech.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md) 
>  [Azure Stream Analytics](tutorial-deploy-stream-analytics.md) 
>  [Azure Machine Learning](tutorial-deploy-machine-learning.md) 
>  [Služba Custom Vision](tutorial-deploy-custom-vision.md)
