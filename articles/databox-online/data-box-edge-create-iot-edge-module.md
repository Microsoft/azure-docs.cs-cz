---
title: C#IoT Edge modul pro Azure Data Box Edge | Microsoft Docs
description: Naučte se vyvíjet modul C# IoT Edge, který se dá nasadit na data box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942545"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>Vývoj modulu C# IoT Edge pro přesun souborů na data box Edge

Tento článek popisuje, jak vytvořit modul IoT Edge pro nasazení pomocí zařízení Data Box Edge. Azure Data Box Edge je řešení úložiště, které umožňuje zpracovat data a odeslat je přes síť do Azure.

K transformaci dat při jejich přesunu do Azure můžete použít Azure IoT Edge moduly s Data Box Edge. Modul použitý v tomto článku implementuje logiku ke zkopírování souboru z místního sdílení do sdílené složky v cloudu na zařízení Data Box Edge.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořte registr kontejnerů pro ukládání a správu modulů (Image Docker).
> * Vytvořte modul IoT Edge k nasazení na Data Box Edge zařízení.


## <a name="about-the-iot-edge-module"></a>O modulu IoT Edge

Vaše zařízení Data Box Edge může nasazovat a spouštět IoT Edge moduly. Hraniční moduly jsou v podstatě kontejnery Docker, které provádějí konkrétní úlohu, například ingestování zprávy ze zařízení, transformace zprávy nebo odeslání zprávy na IoT Hub. V tomto článku vytvoříte modul, který kopíruje soubory z místní sdílené složky do sdílené složky cloudu na zařízení Data Box Edge.

1. Soubory se zapisují do místního sdílení na zařízení Data Box Edge.
2. Generátor událostí souboru vytvoří událost souboru pro každý soubor zapsaný do místní sdílené složky. Události souboru jsou také generovány při změně souboru. Události souboru se pak odesílají do centra IoT Edge (v IoT Edge Runtime).
3. Vlastní modul IoT Edge zpracovává událost souboru pro vytvoření objektu události souboru, který obsahuje také relativní cestu k souboru. Modul vygeneruje absolutní cestu pomocí relativní cesty k souboru a zkopíruje soubor z místní sdílené složky do sdílené složky cloudu. Modul pak odstraní soubor z místní sdílené složky.

![Jak Azure IoT Edge modul funguje Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Jakmile se soubor nachází ve sdílené složce cloudu, automaticky se nahraje na váš Azure Storage účet.

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že máte následující:

- Data Box Edge zařízení, na kterém je spuštěný.

    - Zařízení má také přidružený prostředek IoT Hub.
    - U zařízení je nakonfigurované role pro výpočetní výkon.
    Další informace najdete v pro [konfiguraci COMPUTE](data-box-edge-deploy-configure-compute.md#configure-compute) pro data box Edge.

- Následující zdroje pro vývoj:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Možná budete muset vytvořit účet ke stažení a instalaci softwaru.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Registr kontejnerů Azure je privátním registrem Dockeru v Azure, kde můžete ukládat a spravovat privátní image kontejnerů Dockeru. K dispozici jsou dvě oblíbené služby Docker Registry v cloudu Azure Container Registry a Docker Hub. Tento článek používá Container Registry.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Vyberte **vytvořit prostředek > kontejnery > Container Registry**. Klikněte na možnost **Vytvořit**.
3. Sdělit

   1. Jedinečný **název registru** v rámci Azure, který obsahuje 5 až 50 alfanumerických znaků.
   2. Vyberte **předplatné**.
   3. Vytvořte novou nebo vyberte existující **skupinu prostředků**.
   4. Vyberte **Umístění**. Doporučujeme, aby toto umístění bylo stejné jako spojené s Data Box Edgem prostředkem.
   5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
   6. Nastavte SKU na **Basic**.

      ![Vytvoření registru kontejneru](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Vyberte **Vytvořit**.
5. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**.

    ![Získání přístupových klíčů](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete později k publikování image Docker do svého registru a k přidání přihlašovacích údajů registru do modulu runtime Azure IoT Edge.


## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge

Následující kroky vytvoří projekt modulu IoT Edge v závislosti na sadě .NET Core 2,1 SDK. Projekt používá Visual Studio Code a rozšíření Azure IoT Edge.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Vytvořte šablonu řešení v jazyce C#, kterou můžete přizpůsobit pomocí vlastního kódu.

1. V Visual Studio Code vyberte **zobrazit > paleta příkazů** pro otevření vs Code paleta příkazů.
2. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.
3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení:

    1. Vyberte složku, ve které chcete vytvořit řešení.
    2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
    
        ![Vytvořit nové řešení 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Jako šablonu modulu zvolte **C# Module**.
    4. Nahraďte výchozí název modulu názvem, který chcete přiřadit. v tomto případě je to **FileCopyModule**.
    
        ![Vytvořit nové řešení 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Zadejte registr kontejneru, který jste vytvořili v předchozí části, jako úložiště imagí pro váš první modul. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali.

        Výsledný řetězec vypadá jako `<Login server name>/<Module name>`. V tomto příkladu je řetězec: `mycontreg2.azurecr.io/filecopymodule`.

        ![Vytvořit nové řešení 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Přejít na **soubor > otevřít složku**.

    ![Vytvořit nové řešení 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Přejděte do složky **EdgeSolution** , kterou jste vytvořili dříve. Okno VS Code načte pracovní prostor řešení IoT Edge s jeho pěti komponentami nejvyšší úrovně. V tomto článku nebudete upravovat složku **. VSCode** , soubor. **gitignore** , soubor **. env** a soubor **Deployment. template. JSON** .
    
    Jedinou komponentou, kterou upravíte, je složka moduly. Tato složka obsahuje C# kód pro modul a soubory Docker pro sestavení modulu jako image kontejneru.

    ![Vytvořit nové řešení 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V Průzkumníku VS Code otevřete **moduly > FileCopyModule > program.cs**.
2. V horní části **oboru názvů FileCopyModule**přidejte následující příkazy using pro typy, které se používají později. **Microsoft. Azure. Devices. Client. Transport. MQTT** je protokol, který slouží k odesílání zpráv do centra IoT Edge.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Do třídy program přidejte proměnnou **InputFolderPath** a **OutputFolderPath** .

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Hned za předchozí krok přidejte třídu **sudý** a definujte text zprávy.

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

5. V **metodě Init**kód vytvoří a nakonfiguruje objekt **ModuleClient** . Tento objekt umožňuje modulu připojit se k místnímu modulu Azure IoT Edge runtime pomocí protokolu MQTT pro odesílání a příjem zpráv. Připojovací řetězec, který je použit v metodě Init, je dodána modulu modulem runtime IoT Edge. Kód zaregistruje zpětné volání kopírovacího kopírování pro příjem zpráv z centra IoT Edge prostřednictvím koncového bodu **input1** . **Metodu init** nahraďte následujícím kódem.

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

6. Odeberte kód pro **metodu PipeMessage** a na jejím místě vložte kód pro **kopii**.

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
8. Můžete si také [Stáhnout existující ukázku kódu](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) pro tento projekt. Pak můžete ověřit soubor, který jste uložili proti souboru **program.cs** v této ukázce.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste kód do FileCopyModule ke zkopírování souborů z místní sdílené složky do sdílené složky cloudu. Teď je potřeba sestavit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. V VSCode přejděte do terminálu > nový terminál a otevřete nové Visual Studio Code integrovaného terminálu.
2. Přihlaste se k Docker zadáním následujícího příkazu v integrovaném terminálu.

    `docker login <ACR login server> -u <ACR username>`

    Použijte přihlašovací server a uživatelské jméno, které jste zkopírovali z registru kontejneru.

    ![Sestavování a nabízených IoT Edge řešení](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Po zobrazení výzvy k zadání hesla zadejte heslo. Můžete také načíst hodnoty pro přihlašovací server, uživatelské jméno a heslo z **přístupových klíčů** v registru kontejnerů v Azure Portal.
 
3. Po zadání přihlašovacích údajů můžete do služby Azure Container Registry odeslat image modulu. V Průzkumníku VS Code klikněte pravým tlačítkem na soubor **Module. JSON** a vyberte **sestavení a nabízené IoT Edge řešení**.

    ![Sestavování a nabízených IoT Edge řešení](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Když Visual Studio Code poznáte, že se má vaše řešení sestavit, spustí se dva příkazy v integrovaném terminálu: sestavení Docker a Docker push. Tyto dva příkazy sestaví kód, provedou kontejnerizaci vaší knihovny CSharpModule.dll a odešlou ji do registru kontejneru, který jste zadali při inicializaci řešení.

    Zobrazí se výzva k výběru platformy modulu. Vyberte *amd64* odpovídající systému Linux.

    ![Vybrat platformu](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Podporují se jenom moduly Linux.

    Může se zobrazit následující upozornění, které můžete ignorovat:

    *Program. cs (77, 44): upozornění CS1998: v této asynchronní metodě chybí operátory await a spustí se synchronně. Zvažte použití operátoru await pro čekání na neblokující volání rozhraní API nebo ' await Task. Run (...) ' k provedení práce vázané na procesor ve vlákně na pozadí.*

4. Úplnou adresu image kontejneru se značkou můžete vidět v integrovaném terminálu VS Code. Adresa obrázku je sestavena z informací, které jsou v souboru Module. JSON ve formátu `<repository>:<version>-<platform>`. V tomto článku by měl vypadat jako `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>Další kroky

Pokud chcete nasadit a spustit tento modul na Data Box Edge, přečtěte si postup v tématu [Přidání modulu](data-box-edge-deploy-configure-compute.md#add-a-module).
