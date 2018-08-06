---
title: Kurz k Azure IoT Edge C# | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce C# a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c94479ca523f0097c8fbf94729f3a255ffc0c2bf
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413217"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>Kurz: Vývoj modulu IoT Edge v jazyce C# a jeho nasazení na simulované zařízení

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows][lnk-tutorial1-win] nebo [Linuxu][lnk-tutorial1-lin]. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge, který je založený na sadě .NET Core 2.0 SDK.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/).


## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v dalších částech kurzu k publikování image Dockeru do registru a k přidání přihlašovacích údajů registru do modulu runtime Azure IoT Edge. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
V následujících krocích vytvoříte projekt modulu IoT Edge, který je založený na sadě .NET Core 2.0 SDK. Použijete k tomu Visual Studio Code a rozšíření Azure IoT Edge.

1. V nástroji Visual Studio Code zvolte **View** (Zobrazit)  > **Command Palette** (Paleta příkazů). Otevře se paleta příkazů VS Code. 
2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.
3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   1. Vyberte složku, ve které chcete vytvořit řešení. 
   2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
   3. Jako šablonu modulu zvolte **C# Module**. 
   4. Nahraďte výchozí název modulu názvem **CSharpModule**. 
   5. Jako úložiště imagí pro první modul určete registr kontejneru Azure, který jste vytvořili v předchozí části. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali. Konečný řetězec vypadá takto: \<název_registru\>.azurecr.io/csharpmodule.

4.  V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge: složka s moduly, složka \.vscode, soubor šablony manifestu nasazení a soubor \.env. V průzkumníku VS Code otevřete **modules** > **CSharpModule** > **Program.cs**.

5. Nahoře v oboru názvů **CSharpModule** přidejte tři příkazy **using** pro typy, které se použijí později:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

6. Do třídy **Program** přidejte proměnnou **temperatureThreshold**. Tato proměnná nastaví hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. Do třídy **Program** přidejte třídy **MessageBody**, **Machine** a **Ambient**. Tyto třídy definují očekávané schéma textu příchozích zpráv.

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

8. V metodě **Init** kódem vytvoříte a nakonfigurujete objekt **ModuleClient**. Tento objekt modulu umožňuje připojit se k místnímu modulu runtime IoT Edge kvůli odesílání a přijímání zpráv. Modul runtime IoT Edge poskytne modulu připojovací řetězec používaný metodou **Init**. Po vytvoření objektu **ModuleClient** kód přečte hodnotu **TemperatureThreshold** z požadovaných vlastností dvojčete modulu. a zaznamená zpětné volání přijatých zpráv z IoT Edge Hubu prostřednictvím koncového bodu **input1**. Nahraďte metodu **SetInputMessageHandlerAsync** novou metodou a přidejte metodu **SetDesiredPropertyUpdateCallbackAsync** pro aktualizace požadovaných vlastností. Pokud chcete tuto změnu provést, nahraďte poslední řádek metody **Init** následujícím kódem:

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. Do třídy **Program** přidejte metodu **onDesiredPropertiesUpdate**. Tato metoda přijímá aktualizace požadovaných vlastností dvojčete modulu a aktualizuje proměnnou **temperatureThreshold** na stejnou hodnotu. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

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

10. Nahraďte metodu **PipeMessage** metodou **FilterMessages**. Tato metoda se volá pokaždé, když modul dostane zprávu z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu. Do zprávy také přidá vlastnost **MessageType**, která má nastavenou hodnotu **Alert**. 

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

11. Soubor uložte.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT a do modulu **CSharpModule** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru. 

1. Zadáním následujícího příkazu v integrovaném terminálu editoru Visual Studio Code se přihlaste k Dockeru. Potom můžete odeslat image modulu do služby Azure Container Registry.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Použijte uživatelské jméno, heslo a přihlašovací server zkopírované ze služby Azure Container Registry v první části. Tyto hodnoty můžete také načíst z oddílu **Přístupové klíče** v registru na webu Azure Portal.

2. V průzkumníku VS Code otevřete soubor deployment.template.json v pracovním prostoru řešení IoT Edge. Tento soubor sděluje modulu **$edgeAgent**, že má nasadit dva moduly: **tempSensor** a **CSharpModule**. Hodnota **CSharpModule.image** je nastavená na verzi image Linux amd64. 

   Zkontrolujte, jestli má šablona správný název modulu, ne výchozí název **SampleModule**, který jste změnili při vytváření řešení IoT Edge.

   Další informace o manifestech nasazení najdete ve [vysvětlení, jak lze moduly IoT Edge používat, konfigurovat a opětovně používat](module-composition.md).

3. V souboru deployment.template.json se nachází oddíl **registryCredentials**, do kterého se ukládají přihlašovací údaje registru Dockeru. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje.  

4. Přidejte do manifestu nasazení dvojče modulu **CSharpModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**: 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. Soubor uložte.

5. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build IoT Edge solution** (Vytvořit řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci vaší knihovny CSharpModule.dll a odešlou ji do registru kontejneru, který jste zadali při inicializaci řešení. 

Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa image je sestavená z informací v souboru module.json a má formát \<úložiště\>:\<verze\>-\<platforma\>. V tomto kurzu by měla vypadat takto: název_registru.azurecr.io/csharpmodule:0.0.1-amd64.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro váš IoT Hub: 

    1. Otevřete průzkumník VS Code tím, že vyberete **Zobrazení** > **Průzkumník**.

    1. V průzkumníku vyberte **Azure IoT Hub Devices** (Zařízení Azure IoT Hub), pak vyberte tři tečky (**...**) a nakonec zvolte **Select IoT Hub** (Vybrat IoT Hub). Postupujte podle pokynů, přihlaste se k účtu Azure a zvolte svůj IoT Hub. 

       > [!Note]
       > Nastavení můžete taky dokončit výběrem možnosti **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu). V místním okně zadejte připojovací řetězec pro IoT Hub, ke kterému se vaše zařízení IoT Edge připojí.

2. V průzkumníku zařízení Azure IoT Hub klikněte pravým tlačítkem na vaše zařízení IoT Edge a pak vyberte možnost **Create Deployment for IoT Edge device** (Vytvořit nasazení pro zařízení IoT Edge). Ve složce config vyberte soubor deployment.json a potom vyberte možnost **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge).

3. Aktualizujte sekci **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). Měl by se zobrazit spuštěný nový modul **CSharpModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte tři tečky (**...**) a potom vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
2. Pokud chcete monitorovat zprávy D2C pro konkrétní zařízení, klikněte pravým tlačítkem na příslušné zařízení v seznamu a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
3. Když chcete monitorování dat zastavit, spusťte na paletě příkazů příkaz **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Zastavit monitorování zpráv D2C). 
4. Pokud chcete zobrazit nebo aktualizovat dvojče modulu, klikněte pravým tlačítkem na příslušný modul v seznamu a vyberte **Edit module twin** (Upravit dvojče modulu). Pokud chcete aktualizovat dvojče modulu, uložte soubor JSON dvojčete, klikněte pravým tlačítkem na oblast editoru a vyberte **Update Module Twin** (Aktualizovat dvojče modulu).
5. Pokud chcete zobrazit protokoly Dockeru, nainstalujte [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pro VS Code. Spuštěné moduly můžete vyhledat místně v průzkumníku Dockeru. Výběrem možnosti **Show Logs** (Zobrazit protokoly) v místní nabídce je zobrazíte protokoly v integrovaném terminálu.
 
## <a name="clean-up-resources"></a>Vyčištění prostředků 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

> [!IMPORTANT]
> Odstranění prostředků Azure a skupin prostředků je nevratná akce. V případě odstranění se skupina prostředků i všechny prostředky, které obsahuje, trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. Pokud jste službu IoT Hub vytvořili uvnitř existující skupiny prostředků obsahující prostředky, které chcete zachovat, odstraňte místo skupiny prostředků pouze samotný prostředek služby IoT Hub.
>

Pokud chcete odstranit jenom IoT Hub, spusťte následující příkaz s názvem vaší služby Hub a názvem skupiny prostředků:

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


Odstranění celé skupiny prostředků podle názvu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a potom vyberte **Skupiny prostředků**.

2. Do textového pole **Filtrovat podle názvu** zadejte název skupiny prostředků obsahující vaši službu IoT Hub. 

3. Vpravo vedle skupiny prostředků ve výsledcích hledání vyberte tři tečky (**...**) a pak vyberte **Odstranit skupinu prostředků**.

4. Zobrazí se výzva k potvrzení odstranění skupiny prostředků. Potvrďte odstranění opětovným zadáním názvu vaší skupiny prostředků a vyberte **Odstranit**. Po chvíli bude skupina prostředků včetně všech obsažených prostředků odstraněná.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, získáte další informace na stránce o [vývoji modulu jazyka C# pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-module.md). Pokračujte dalšími kurzy, ve kterých se naučíte další způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Uložení dat na hraničních zařízeních s využitím databází SQL Serveru](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
