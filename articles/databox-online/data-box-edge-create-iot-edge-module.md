---
title: Modul C# IoT Edge pro Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Zjistěte, jak vyvinout modul C# IoT Edge, který se dá nasadit na okraji datové schránky.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942545"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Vývoj modulu C# IoT Edge pro přesun souborů na okraji datové schránky

Tento článek vás provede, jak vytvořit modul IoT Edge pro nasazení se zařízením Data Box Edge. Azure Data Box Edge je řešení úložiště, které umožňuje zpracovat data a odeslat je přes síť do Azure.

Pomocí modulů Azure IoT Edge s okrajem datové schránky můžete transformovat data při přesunu do Azure. Modul použitý v tomto článku implementuje logiku pro kopírování souboru z místní sdílené složky do sdílené složky cloudu na zařízení Data Box Edge.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte registr kontejnerů pro ukládání a správu modulů (iimages Dockeru).
> * Vytvořte modul IoT Edge, který se nasadíte na zařízení Data Box Edge.


## <a name="about-the-iot-edge-module"></a>O modulu IoT Edge

Vaše zařízení Data Box Edge může nasadit a spustit moduly IoT Edge. Hraniční moduly jsou v podstatě kontejnery Dockeru, které provádějí určitou úlohu, jako je například ingestování zprávy ze zařízení, transformace zprávy nebo odeslání zprávy do služby IoT Hub. V tomto článku vytvoříte modul, který zkopíruje soubory z místní sdílené složky do sdílené složky cloudu na zařízení Data Box Edge.

1. Soubory se zapisují do místní sdílené složky na zařízení Data Box Edge.
2. Generátor událostí souboru vytvoří událost souboru pro každý soubor zapsaný do místní sdílené složky. Události souboru jsou také generovány při změně souboru. Události souboru se pak odešlou do ioT Edge Hub (v runtime IoT Edge).
3. Vlastní modul IoT Edge zpracuje událost souboru a vytvoří objekt události souboru, který také obsahuje relativní cestu k souboru. Modul generuje absolutní cestu pomocí relativní cesty k souboru a zkopíruje soubor z místní sdílené složky do sdílené složky cloudu. Modul pak odstraní soubor z místní sdílené složky.

![Jak funguje modul Azure IoT Edge na okraji datové schránky](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Jakmile je soubor ve sdílené složce cloudu, automaticky se nahraje do vašeho účtu Azure Storage.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- Zařízení Data Box Edge, které je spuštěno.

    - Zařízení má také přidružený prostředek služby IoT Hub.
    - Zařízení má nakonfigurovanou výpočetní roli Edge.
    Další informace najdete v [najetí výpočetních prostředků](data-box-edge-deploy-configure-compute.md#configure-compute) pro váš datový schránka Edge.

- Následující zdroje vývoje:

    - [Visual Studio kód](https://code.visualstudio.com/).
    - [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Rozšíření Azure IoT Edge pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Možná budete muset vytvořit účet ke stažení a instalaci softwaru.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Registr kontejnerů Azure je privátním registrem Dockeru v Azure, kde můžete ukládat a spravovat privátní image kontejnerů Dockeru. Dvě oblíbené služby registru Dockeru dostupné v cloudu jsou Azure Container Registry a Docker Hub. Tento článek používá registr kontejnerů.

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Vyberte **možnost Vytvořit prostředek > kontejnery > registru kontejnerů**. Klikněte na **Vytvořit**.
3. Poskytnout:

   1. Jedinečný **název registru** v rámci Azure, který obsahuje 5 až 50 alfanumerických znaků.
   2. Zvolte **předplatné**.
   3. Vytvořte nové nebo zvolte existující **skupinu prostředků**.
   4. Vyberte **umístění**. Doporučujeme, aby toto umístění bylo stejné jako umístění přidružené k prostředku Data Box Edge.
   5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
   6. Nastavte skladovou položku na **základní**.

      ![Vytvoření registru kontejneru](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Vyberte **Vytvořit**.
5. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**.

    ![Získat přístupové klíče](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty později použít k publikování image Dockeru do registru a přidat pověření registru do runtime Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge

Následující kroky vytvoří projekt modulu IoT Edge na základě sady .NET Core 2.1 SDK. Projekt používá Visual Studio Code a rozšíření Azure IoT Edge.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Vytvořte šablonu řešení v jazyce C#, kterou můžete přizpůsobit pomocí vlastního kódu.

1. V kódu Visual Studia vyberte **Zobrazit > paletu příkazů** a otevřete paletu příkazů VS Code.
2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.
3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení:

    1. Vyberte složku, ve které chcete vytvořit řešení.
    2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
    
        ![Vytvořit nové řešení 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Jako šablonu modulu zvolte **C# Module**.
    4. Nahraďte výchozí název modulu názvem, který chcete přiřadit, v tomto případě je **filecopymodule**.
    
        ![Vytvořit nové řešení 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Zadejte registr kontejneru, který jste vytvořili v předchozí části jako úložiště bitových obrázků pro váš první modul. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali.

        Poslední řetězec vypadá `<Login server name>/<Module name>`jako . V tomto příkladu je `mycontreg2.azurecr.io/filecopymodule`řetězec: .

        ![Vytvořit nové řešení 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Přejděte do **souboru > otevřít složku**.

    ![Vytvořit nové řešení 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Projděte a přejděte na složku **EdgeSolution,** kterou jste vytvořili dříve. Okno VS Code načte pracovní plochu řešení IoT Edge pomocí pěti komponent nejvyšší úrovně. V tomto článku nebudete upravovat složku **.vscode,** soubor **Gitignore,** soubor **ENV** a soubor **deployment.template.json.**
    
    Jedinou součástí, kterou upravíte, je složka modulů. Tato složka má kód C# pro váš modul a soubory Dockeru k vytvoření modulu jako image kontejneru.

    ![Vytvořit nové řešení 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V průzkumníku kódu VS otevřete **moduly > FileCopyModule > Program.cs**.
2. V horní části **oboru názvů FileCopyModule**přidejte následující příkazy using pro typy, které se použijí později. **Microsoft.Azure.Devices.Client.Transport.Mqtt** je protokol pro odesílání zpráv do služby IoT Edge Hub.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Přidejte proměnnou **InputFolderPath** a **OutputFolderPath** do třídy Program.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Ihned po předchozím kroku přidejte třídu **FileEvent** a definujte text zprávy.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. V **init metoda**, kód vytvoří a nakonfiguruje **Objekt ModuleClient.** Tento objekt umožňuje modulu připojit se k místnímu modulu Azure IoT Edge pomocí protokolu MQTT k odesílání a přijímání zpráv. Modul runtime IoT Edge poskytne modulu připojovací řetězec používaný metodou Init. Kód registruje zpětné volání FileCopy pro příjem zpráv z centra IoT Edge prostřednictvím koncového bodu **input1.** Nahraďte **metodu Init** následujícím kódem.

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

6. Odeberte kód pro **metodu PipeMessage** a na jejím místě vložte kód pro **FileCopy**.

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
    ```

7. Soubor uložte.
8. Můžete [také stáhnout existující ukázku kódu](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) pro tento projekt. Potom můžete ověřit soubor, který jste uložili proti **souboru program.cs** v této ukázce.

## <a name="build-your-iot-edge-solution"></a>Vytvoření řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a přidali kód do FileCopyModule pro kopírování souborů z místní sdílené složky do sdílené složky cloudu. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Ve VSCode přejděte na Terminál > Nový terminál a otevřete nový integrovaný terminál Visual Studio Code.
2. Přihlaste se k Dockeru zadáním následujícího příkazu do integrovaného terminálu.

    `docker login <ACR login server> -u <ACR username>`

    Použijte přihlašovací server a uživatelské jméno, které jste zkopírovali z registru kontejnerů.

    ![Sestavení a nabízení řešení IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po zobrazení výzvy k zadání hesla zadej heslo. Hodnoty přihlašovacího serveru, uživatelského jména a hesla můžete také načíst z **přístupových klíčů** v registru kontejnerů na webu Azure Portal.
 
3. Po dodání přihlašovacích údajů můžete nabízenou image modulu do registru kontejnerů Azure. V Průzkumníku kódů VS klikněte pravým tlačítkem myši na soubor **module.json** a vyberte **možnost Sestavit a push řešení IoT Edge**.

    ![Sestavení a nabízení řešení IoT Edge](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Když řeknete Visual Studio Kód k sestavení řešení, spustí dva příkazy v integrovaném terminálu: docker sestavení a docker push. Tyto dva příkazy sestaví kód, provedou kontejnerizaci vaší knihovny CSharpModule.dll a odešlou ji do registru kontejneru, který jste zadali při inicializaci řešení.

    Budete vyzváni k výběru platformy modulu. Vyberte *amd64* odpovídající Linuxu.

    ![Vybrat platformu](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Podporovány jsou pouze linuxové moduly.

    Může se zobrazit následující upozornění, které můžete ignorovat:

    *Program.cs(77,44): upozornění CS1998: Tato asynchronní metoda postrádá operátory 'await' a bude spuštěna synchronně. Zvažte použití operátoru 'await' k čekání na neblokující volání rozhraní API nebo "await Task.Run(...)" k práci vázané na procesor na vlákně na pozadí.*

4. Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa obrázku je sestavena z informací, které jsou v `<repository>:<version>-<platform>`souboru module.json s formátem . Pro tento článek by `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`měl vypadat .

## <a name="next-steps"></a>Další kroky

Pokud chcete nasadit a spustit tento modul v datové schránce Edge, přečtěte si postup přidání [modulu](data-box-edge-deploy-configure-compute.md#add-a-module).
