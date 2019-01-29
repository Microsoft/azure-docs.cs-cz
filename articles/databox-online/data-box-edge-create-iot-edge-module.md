---
title: Modul IoT Edge C# pro Azure Data Box Edge | Dokumentace Microsoftu
description: Zjistěte, jak vývoj modulu jazyka C# IoT Edge, které se dají nasadit na hranici vaší Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/16/2018
ms.author: alkohli
ms.openlocfilehash: 86eec87d0c466b9172834fa9dbe7dfcb3702ea55
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094100"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge-preview"></a>Vývoj modulu jazyka C# IoT Edge pro přesun souborů na okraji pole dat (Preview)

Tento článek vás provede jednotlivými kroky k vytvoření modul IoT Edge pro nasazení s vaším zařízením Data Box Edge. Azure Data Box Edge je řešení úložiště, které umožňuje zpracovat data a odeslat je přes síť do Azure.

Moduly Azure IoT Edge s hranici pole Data slouží k transformaci dat, jako je přesunuta do Azure. Modul používaný v tomto článku implementuje logika se zkopírovat soubor z místní sdílené složky do cloudové sdílené složky na vašem zařízení Data Box Edge.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření registru kontejnerů k ukládání a správě modulů (imagí Dockeru).
> * Vytvoření modulu IoT Edge pro nasazení v zařízení Data Box Edge.

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="about-the-iot-edge-module"></a>Informace o modulu IoT Edge

Vaše zařízení Data Box Edge můžete nasadit a spustit moduly IoT Edge. Moduly Edge jsou v podstatě kontejnery Dockeru, které provedení určitého úkolu, jako například příjem zpráv ze zařízení, transformujte zprávy nebo odeslání zprávy do služby IoT Hub. V tomto článku vytvoříte modul, který zkopíruje soubory z místní sdílené složky do cloudové sdílené složky na vašem zařízení Data Box Edge.

1. Soubory jsou zapsány do místní sdílené složky na vašem zařízení Data Box Edge.
2. Generátor souboru události vytvoří událost souboru pro každý soubor zapsán do místní sdílené složky. Soubor událostí se pak odesílají do služby IoT Edge Hub (v modul runtime IoT Edge).

   > [!IMPORTANT]
   > Soubor události se generují jenom pro nově vytvořené soubory. Úprava existující soubory negeneruje žádné události souboru.

3. Vlastní modul IoT Edge zpracovává událost souboru k vytvoření souboru událostí objektu, který také obsahuje relativní cestu k souboru. Modul generuje absolutní cesta pomocí relativní cesta k souboru a zkopíruje soubor z místní sdílené složky ke sdílené složce cloudu. V modulu pak odstraní soubor z místní sdílené složky.

![Jak funguje Azure IoT Edge module na hraničních zařízeních Data Box](./media/data-box-edge-create-iot-edge-module/how-module-works.png)

Jakmile je soubor ve sdílené složce cloudu, automaticky získá nahrát ho do svého účtu Azure Storage.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- Data Box hraniční zařízení, na kterém běží.

    - Zařízení má taky přidružený prostředek služby IoT Hub. Další informace najdete v části [vytvoří prostředek služby IoT Hub](data-box-edge-deploy-configure-compute.md#create-an-iot-hub-resource) pro hranici Data Box.
    - Zařízení má Edge nakonfigurovaná rolí služby compute. Další informace najdete v části [nastavení výpočetní roli](data-box-edge-deploy-configure-compute.md#set-up-compute-role) na hranici vaší Data Box.

- Následující prostředky pro vývoj:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Budete muset vytvořit účet, který chcete stáhnout a nainstalovat software.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Registr kontejnerů Azure je privátním registrem Dockeru v Azure, kde můžete ukládat a spravovat privátní image kontejnerů Dockeru. Dvě oblíbené služby registrů Dockeru k dispozici v cloudu se Azure Container Registry a Docker Hubu. Tento článek používá Container Registry.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **vytvořit prostředek > kontejnery > Container Registry**. Klikněte na možnost **Vytvořit**.
3. Zadejte:

    1. Jedinečné **název registru** v Azure, která obsahuje 5 až 50 alfanumerických znaků.
    2. Zvolte **předplatné**.
    3. Vytvořit novou nebo vybrat existující **skupiny prostředků**.
    4. Vyberte **Umístění**. Doporučujeme vám, že toto umístění být stejná jako, který je přidružen okraj pole Data prostředků.
    5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
    6. Nastavte skladovou Položku **základní**.

    ![Vytvoření registru kontejneru](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Vyberte **Vytvořit**.
5. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**.

    ![Získání přístupových klíčů](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete později k publikování image Dockeru do registru a přidání přihlašovacích údajů registru do modulu runtime Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge

Následujícím postupem se vytvoří projekt modul IoT Edge založené na sadě SDK .NET Core 2.1. Projekt používá Visual Studio Code a rozšíření Azure IoT Edge.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Vytvořte šablonu řešení v jazyce C#, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Ve Visual Studio Code, vyberte **zobrazení > paletu příkazů** otevřete paletu příkazů VS Code.
2. V paletu příkazů zadejte a spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů k přihlášení účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.
3. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Na paletě příkazů zadejte následující informace k vytvoření řešení:

    1. Vyberte složku, ve které chcete vytvořit řešení.
    2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
    
        ![Vytvoření nového řešení 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Jako šablonu modulu zvolte **C# Module**.
    4. Nahraďte názvem, kterou chcete přiřadit výchozí název modulu, v tomto případě je **FileCopyModule**.
    
        ![Vytvoření nového řešení 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Zadejte registru kontejneru, který jste vytvořili v předchozí části jako úložiště imagí pro první modul. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali.

        Konečný řetězec vypadá jako `<Login server name>/<Module name>`. V tomto příkladu je řetězec: `mycontreg2.azurecr.io/filecopymodule`.

        ![Vytvoření nového řešení 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Přejděte na **soubor > Otevřít složku**.

    ![Vytvoření nového řešení 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Procházení a přejděte **EdgeSolution** složky, kterou jste vytvořili dříve. Okna nástroje VS Code načte pracovní prostor řešení IoT Edge s komponentami pět nejvyšší úrovně. Nebude upravovat **.vscode** složce **.gitignore** souboru, **.env** souboru a **deployment.template.json** v tomto článku.
    
    Jedinou komponentou, který můžete upravit je složka moduly. Tato složka obsahuje kód jazyka C# pro modul a souborů Dockeru k sestavení modulu jako image kontejneru.

    ![Vytvoření nového řešení 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku VS Code, Otevřít **moduly > CSharpModule > Program.cs**.
2. V horní části **obor názvů FileCopyModule**, přidejte následující příkazy using pro typy, které se později použijí. **Microsoft.Azure.Devices.Client.Transport.Mqtt** je protokol pro odesílání zpráv do IoT Edge Hub.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Přidat **InputFolderPath** a **OutputFolderPath** proměnné do třídy Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/LocalShare";
            private const string OutputFolderPath = "/home/CloudShare";
    ```

    > [!IMPORTANT]
    > Poznamenejte si, `InputFolderPath` a `OutputFolderPath`. Je potřeba zadat tyto cesty při nasazení tohoto modulu.

4. Přidat **MessageBody** třídy do třídy Program. Tyto třídy definují očekávané schéma textu příchozích zpráv.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. V metodě **Init** kódem vytvoříte a nakonfigurujete objekt **ModuleClient**. Tento objekt umožňuje modulu pro připojení k místní modul runtime Azure IoT Edge k odesílání a příjem zpráv pomocí protokolu MQTT. Připojovací řetězec, který se používá v metodě inicializace poskytuje modulu runtime IoT Edge. Tento kód zaregistruje FileCopy zpětné volání pro příjem zpráv z centra IoT Edge prostřednictvím **vstup1** koncového bodu.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Vložit kód pro **FileCopy**.

    ```
            /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }

    }
    ```

7. Soubor uložte.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili hraničních zařízeních IoT řešení a přidat kód do FileCopyModule kopírování souborů z místní sdílené složky ke sdílené složce cloudu. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Zadáním následujícího příkazu v integrovaném terminálu editoru Visual Studio Code se přihlaste k Dockeru.

    `docker login <ACR login server> -u <ACR username>`

    Použijte přihlášení na server a uživatelské jméno, které jste zkopírovali ze svého registru kontejneru. 

    ![Vytváření a nasdílení změn řešení IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po výzvě k zadání hesla, zadejte heslo. Můžete také načíst hodnoty pro přihlášení na server, uživatelské jméno a heslo **přístupové klíče** do vašeho registru kontejneru na webu Azure Portal.
 
3. Jakmile jsou zadané přihlašovací údaje, můžete nabízet bitové kopie modulu do služby Azure container registry. V Průzkumníku VS Code klikněte pravým tlačítkem myši **module.json** a vyberte možnost **řešení pro sestavení a Push IoT Edge**.

    ![Vytváření a nasdílení změn řešení IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Pokud dáte Visual Studio Code pro vytvoření řešení, běží v integrovaném terminálu dvou příkazů: sestavení dockeru a docker push. Tyto dva příkazy sestaví kód, provedou kontejnerizaci vaší knihovny CSharpModule.dll a odešlou ji do registru kontejneru, který jste zadali při inicializaci řešení.

    Mohou se zobrazit následující upozornění, které můžete ignorovat:

    *Program.cs(77,44): upozornění CS1998: Této asynchronní metodě chybí operátory 'operátor await' a spustí se synchronně. Zvažte možnost použít operátor 'await' await pro čekání neblokující volání rozhraní API nebo 'operátor await Task.Run(...)' k provedení práce vázané na procesor ve vlákně na pozadí.*

4. Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresu image je sestaven z informací, které jsou v souboru module.json s formátem `<repository>:<version>-<platform>`. Pro účely tohoto článku by mělo vypadat jako `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Další postup

K nasazení a spuštění tohoto modulu na okraji pole Data, podívejte se na postup v [přidat vlastní modul](data-box-edge-deploy-configure-compute.md#add-a-custom-module).
