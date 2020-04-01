---
title: Výuka – vývoj modulu C# pro Windows pomocí Azure IoT Edge
description: Tento kurz ukazuje, jak vytvořit modul IoT Edge s kódem C# a nasadit ho do zařízení Windows IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4fa3fb17f4eace8d389738fb46267a097610f175
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760415"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>Kurz: Vývoj modulu C# IoT Edge pro zařízení se systémem Windows

Pomocí Visual Studia můžete vyvinout kód C# a nasadit ho do zařízení s Windows se systémem Azure IoT Edge.

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Visual Studio slouží k vytvoření modulu IoT Edge, který je založen na sadě C# SDK.
> * Pomocí Visual Studia a Dockeru vytvořte bitovou kopii Dockeru a publikujte ji do registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvinout modul v **jazyce C#** pomocí **Visual Studia 2019** a nasadit ho do **zařízení s Windows**. Pokud vyvíjíte moduly pro linuxová zařízení, přejděte na [vývoj modulu C# IoT Edge pro zařízení s Linuxem.](tutorial-csharp-module.md)

V následující tabulce můžete pochopit možnosti vývoje a nasazování modulů Jazyka C# do zařízení s Windows:

| C# | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64 rozvíjet** | ![Vývoj modulů Jazyka C# pro WinAMD64 v kódu VS](./media/tutorial-c-module/green-check.png) | ![Vývoj modulů Jazyka C# pro WinAMD64 v sadě Visual Studio](./media/tutorial-c-module/green-check.png) |
| **Ladění systému Windows AMD64** |   | ![Ladění modulů Jazyka C# pro WinAMD64 v sadě Visual Studio](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí, [vývoj modulu IoT Edge pro zařízení se systémem Windows](tutorial-develop-for-windows.md). Po dokončení tohoto kurzu byste již měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení s Windows se systémem Azure IoT Edge](quickstart.md).
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) nakonfigurované s rozšířením [Azure IoT Edge Tools.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) nakonfigurovaný pro spouštění kontejnerů windows.

> [!TIP]
> Pokud používáte Visual Studio 2017 (verze 15.7 nebo vyšší), stáhněte a nainstalujte [nástroje Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro VS 2017 z webu Visual Studio Marketplace

## <a name="create-a-module-project"></a>Vytvoření projektu modulu

Následující kroky vytvoří projekt modulu IoT Edge pomocí Visual Studia a rozšíření Azure IoT Edge Tools. Po vytvoření šablony projektu přidejte nový kód tak, aby modul odfiltrovat zprávy na základě jejich hlášené vlastnosti.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Nástroje Azure IoT Edge poskytují šablony projektů pro všechny podporované jazyky modulu IoT Edge v sadě Visual Studio. Tyto šablony mají všechny soubory a kód, které potřebujete k nasazení pracovní modul pro testování IoT Edge, nebo vám výchozí bod pro přizpůsobení šablony s vlastní obchodní logiku.

1. Spusťte Visual Studio 2019 a vyberte **Vytvořit nový projekt**.

2. Vyhledejte **IoT Edge** a zvolte projekt **Azure IoT Edge (Windows amd64).** Klikněte na **Další**.

   ![Vytvoření nového projektu Azure IoT Edge](./media/tutorial-csharp-module-windows/new-project.png)

3. Přejmenujte projekt a řešení na něco popisného, jako je **CSharpTutorialApp**. Chcete-li vytvořit projekt, klepněte na **tlačítko Vytvořit.**

   ![Konfigurace nového projektu Azure IoT Edge](./media/tutorial-csharp-module-windows/configure-project.png)

4. Nakonfigurujte projekt s následujícími hodnotami:

   | Pole | Hodnota |
   | ----- | ----- |
   | Výběr šablony | Vyberte **modul Jazyka C#**. |
   | Název projektu modulu | Dejte modulu název **CSharpModule**. |
   | Úložiště bitových obrázků dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z hodnoty názvu projektu modulu. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br> Konečné úložiště bitových \<obrázků\>vypadá jako název registru .azurecr.io/csharpmodule. |

   ![Konfigurace projektu pro cílové zařízení, typ modulu a registr kontejneru](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. Chcete-li vytvořit projekt, vyberte **přidat.**

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

Manifest nasazení sdílí pověření pro registr kontejnerů s runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. Použijte přihlašovací údaje z části **Přístupové klíče** v registru kontejnerů Azure.

1. V průzkumníku řešení Visual Studio otevřete soubor **deployment.template.json.**

2. Najděte vlastnost **registryCredentials** v $edgeAgent požadované vlastnosti. Adresa registru by měla být automaticky vyplněna z informací, které jste zadali při vytváření projektu, a pole uživatelského jména a hesla by měla obsahovat názvy proměnných. Například:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. Otevřete soubor **ENV** v modulovém řešení. (Ve výchozím nastavení je v Průzkumníku řešení skrytá, takže možná budete muset vybrat tlačítko **Zobrazit všechny soubory,** abyste ho zobrazili.) Soubor .env by měl obsahovat stejné proměnné uživatelského jména a hesla, které jste viděli v souboru deployment.template.json.

4. Přidejte hodnoty **Uživatelské jméno** a **heslo** z registru kontejnerů Azure.

5. Uložte změny do souboru ENV.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Výchozí kód modulu přijímá zprávy ve vstupní frontě a předává je prostřednictvím výstupní fronty. Přidáme nějaký další kód, aby modul zpracuje zprávy na hraničním okraji před jejich předáním do ioT hubu. Aktualizujte modul tak, aby analyzoval data o teplotě v každé zprávě a zprávu odešle do služby IoT Hub pouze v případě, že teplota překročí určitou prahovou hodnotu.

1. V sadě Visual Studio otevřete**Program.cs** **CSharpModule** > .

2. Nahoře v oboru názvů **CSharpModule** přidejte tři příkazy **using** pro typy, které se použijí později:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. Přidejte **proměnnou temperatureThreshold** do třídy **Program** za proměnnou čítače. Proměnná temperatureThreshold nastaví hodnotu, kterou musí naměřená teplota překročit pro data, která mají být odeslána do centra IoT.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. Přidejte třídy **MessageBody**, **Machine**a **Ambient** do třídy **Program** za deklarace proměnných. Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

5. Najděte metodu **Init.** Tato metoda vytvoří a nakonfiguruje objekt **ModuleClient,** který umožňuje modulu připojit se k místní modulu Azure IoT Edge runtime pro odesílání a přijímání zpráv. Kód také registruje zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím koncového bodu **input1.**

   Nahraďte celou metodu Init následujícím kódem:

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

   Tato aktualizovaná metoda Init stále nastavuje připojení k modulu Runtime IoT Edge s ModuleClient, ale také přidává nové funkce. Přečte požadované vlastnosti dvojčete modulu pro načtení hodnoty **temperatureThreshold.** Potom vytvoří zpětné volání, které naslouchá jakékoli budoucí aktualizace modulu dvojče požadované vlastnosti. Pomocí tohoto zpětného volání můžete vzdáleně aktualizovat prahovou hodnotu teploty v dvojčeti modulu a změny budou začleněny do modulu.

   Aktualizovaná metoda Init také změní existující metodu **SetInputMessageHandlerAsync.** V ukázkovém kódu příchozí zprávy na *input1* jsou zpracovány s *PipeMessage* funkce, ale chceme změnit, že použít *FilterMessages* funkce, které vytvoříme v následujících krocích.

6. Přidejte novou metodu **onDesiredPropertiesUpdate** do třídy **Program.** Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

7. Odeberte ukázkovou metodu **PipeMessage** a nahraďte ji novou metodou **FilterMessages.** Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu. Do zprávy také přidá vlastnost **MessageType**, která má nastavenou hodnotu **Alert**.

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

9. Otevřete soubor **deployment.template.json** v řešení IoT Edge. Tento soubor informuje agenta IoT Edge, které moduly nasadit, v tomto případě **SimulatedTemperatureSensor** a **CSharpModule**, a řekne centru IoT Edge, jak směrovat zprávy mezi nimi.

10. Přidejte do manifestu nasazení dvojče modulu **CSharpModule**. Vložte následující obsah JSON do dolní části oddílu **modulesContent** za dvojče modulu **$edgeHub**:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Přidání dvojčete modulu do šablony nasazení](./media/tutorial-csharp-module-windows/module-twin.png)

11. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavte a tlačte svůj modul

V předchozí části jste vytvořili řešení IoT a do modulu **CSharpModule** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Pomocí následujícího příkazu se přihlaste k Dockeru ve vývojovém počítači. Použijte uživatelské jméno, heslo a přihlašovací server z registru kontejnerů Azure. Tyto hodnoty můžete načíst z části **Přístupové klíče** v registru na webu Azure Portal.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

2. V průzkumníku řešení Visual Studio klikněte pravým tlačítkem myši na název projektu, který chcete sestavit. Výchozí název je **AzureIotEdgeApp1** a vzhledem k tomu, že vytváříte modul Windows, rozšíření by mělo být **Windows.Amd64**.

3. Vyberte **sestavení a nabízení modulů IoT Edge**.

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Pomocí průzkumníka cloudu Visual Studio a rozšíření Azure IoT Edge Tools nasadit projekt modulu do zařízení IoT Edge. Již máte manifest nasazení připravený pro váš scénář, soubor **deployment.json** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku cloudu Visual Studia rozbalte prostředky a podívejte se na seznam zařízení IoT.

2. Klikněte pravým tlačítkem myši na název zařízení IoT Edge, které chcete přijmout nasazení.

3. Vyberte **možnost Vytvořit nasazení**.

4. V průzkumníku souborů vyberte soubor **deployment.windows-amd64** v konfigurační složce vašeho řešení.

5. Aktualizujte průzkumníka cloudu, abyste viděli nasazené moduly uvedené pod vaším zařízením.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Pomocí rozšíření Nástroje pro edge IoT můžete zobrazit zprávy při jejich doručení do centra IoT Hub.

1. V průzkumníku cloudu Visual Studia vyberte název zařízení IoT Edge.

2. V seznamu **Akce** vyberte **Spustit sledování předdefinovaného koncového bodu událostí**.

3. Zobrazení zpráv přicházejících do vašeho IoT Hubu. Může chvíli trvat, než zprávy dorazí, protože změny, které jsme provedli v kódu CSharpModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také typ zprávy **Výstraha** všechny zprávy, které dosáhnou této prahové hodnoty teploty.

   ![Zobrazení zpráv přicházejících do ioT hubu](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Úprava dvojčete modulu

Použili jsme dvojče modulu CSharpModule k nastavení teplotního prahu na 25 stupňů. Dvojče modulu můžete použít ke změně funkce bez nutnosti aktualizace kódu modulu.

1. V sadě Visual Studio otevřete soubor **deployment.windows-amd64.json.** (Není soubor deployment.template. Pokud manifest nasazení v konfiguračním souboru v průzkumníku řešení nevidíte, vyberte na panelu nástrojů průzkumníka ikonu **Zobrazit všechny soubory.)**

2. Najděte dvojče CSharpModule a změňte hodnotu parametru **temperatureThreshold** na novou teplotu o 5 až 10 stupňů vyšší než poslední hlášená teplota.

3. Uložte soubor **deployment.windows-amd64.json.**

4. Podle pokynů pro nasazení znovu použít aktualizovaný manifest nasazení na vašem zařízení.

5. Sledujte příchozí zprávy mezi zařízeními a cloudy. Měli byste vidět zprávy zastavit, dokud není dosaženo nového prahu teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete se dozvědět více o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů v sadě Visual Studio](how-to-visual-studio-develop-module.md). Příklady modulů IoT Edge, včetně modulu simulované teploty, viz [ukázky modulu IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Můžete pokračovat na další kurzy se dozvíte, jak Azure IoT Edge vám může pomoci nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Vlastní Vision Service](tutorial-deploy-custom-vision.md)
