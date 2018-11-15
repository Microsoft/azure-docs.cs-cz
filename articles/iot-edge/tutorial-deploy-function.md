---
title: Nasazení funkcí Azure Functions pomocí služby Azure IoT Edge | Microsoft Docs
description: V tomto kurzu nasadíte funkci Azure Functions jako modul na hraniční zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 27aac9431c3f4cd801d090ddf11114c98edab405
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567311"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Kurz: Nasazení Azure funguje jako moduly IoT Edge

Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení Azure IoT Edge. Tento kurz vás provedete vytvořením a nasazením funkce Azure Functions, která filtruje data senzorů na simulovaném zařízení IoT Edge. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:     

> [!div class="checklist"]
> * Použít Visual Studio Code k vytvoření funkce Azure Functions.
> * Použít VS Code a Docker k vytvoření image Dockeru a jejímu publikování do registru kontejneru.
> * Nasadit modul z registru kontejneru do zařízení IoT Edge.
> * Zobrazit filtrovaná data.

<center>
![Diagram architektury v tomto kurzu](./media/tutorial-deploy-function/FunctionsTutDiagram.png)
</center>

>[!NOTE]
>Moduly funkce Azure Functions v Azure IoT Edge jsou ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Funkce Azure Functions, kterou v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Tato funkce pošle zprávy dál do Azure IoT Hubu jen v případě, že teplota překročí zadanou prahovou hodnotu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code. 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

    ![Vytvoření registru kontejnerů](./media/tutorial-deploy-function/create-container-registry.png)

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota | 
   | ----- | ----- |
   | Název registru | Zadejte jedinečný název. |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová jednotka (SKU) | Vyberte **Basic**. | 

5. Vyberte **Vytvořit**.

6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 

7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu. 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Rozšíření Azure IoT Edge pro Visual Studio Code, které jste nainstalovali jako součást požadavků, poskytuje možnosti správy a také několik šablon kódu. V této části pomocí Visual Studio Code vytvoříte řešení IoT Edge obsahující funkci Azure Functions. 

1. Na vývojovém počítači otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se). Podle pokynů se přihlaste ke svému účtu Azure.

4. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   1. Vyberte složku, ve které chcete vytvořit řešení. 
   2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
   3. Jako šablonu modulu zvolte **Azure Functions – C#**. 
   4. Zadejte název modulu **CSharpFunction**. 
   5. Jako úložiště imagí pro první modul určete registr kontejneru Azure, který jste vytvořili v předchozí části. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali. Ujistěte se, že součástí řetězce je nezměněný název modulu (například /csharpfunction). Konečný řetězec vypadá takto: \<název_registru\>.azurecr.io/csharpfunction.

   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-function/repository.png)

4. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge: složka \.vscode, složka s moduly a soubor šablony manifestu nasazení. a soubor \.env. V průzkumníku VS Code otevřete **modules** > **CSharpFunction** > **CSharpFunction.cs**.

5. Obsah souboru **CSharpFunction.cs** nahraďte následujícím kódem:

   ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;
    using Newtonsoft.Json;

    namespace Functions.Samples
    {
        public static class CSharpFunction
        {
            [FunctionName("CSharpFunction")]
            public static async Task FilterMessageAndSendMessage(
                        [EdgeHubTrigger("input1")] Message messageReceived,
                        [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
                        ILogger logger)
            {
                const int temperatureThreshold = 20;
                byte[] messageBytes = messageReceived.GetBytes();
                var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

                if (!string.IsNullOrEmpty(messageString))
                {
                    logger.LogInformation("Info: Received one non-empty message");
                    // Get the body of the message and deserialize it.
                    var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                    if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                    {
                        // Send the message to the output as the temperature value is greater than the threashold.
                        var filteredMessage = new Message(messageBytes);
                        // Copy the properties of the original message into the new Message object.
                        foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                        {
                            filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                        // Add a new property to the message to indicate it is an alert.
                        filteredMessage.Properties.Add("MessageType", "Alert");
                        // Send the message.       
                        await output.AddAsync(filteredMessage);
                        logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                    }
                }
            }
        }
        //Define the expected schema for the body of incoming messages.
        class MessageBody
        {
            public Machine machine {get; set;}
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
    }
   ```

6. Uložte soubor.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a do modulu **CSharpFunction** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

V této části dvakrát zadáte přihlašovací údaje pro váš registr kontejneru. Jako první je zadáte pro místní přihlášení z vývojového počítače, aby editor Visual Studio Code mohl odesílat image do vašeho registru. Pak je zadáte v souboru **.env** vašeho řešení IoT Edge a tím udělíte vašemu zařízení IoT Edge oprávnění ke stahování imagí z registru. 

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code. 

1. Přihlaste se k registru kontejneru zadáním následujícího příkazu v integrovaném terminálu. Potom můžete odeslat image modulu do služby Azure Container Registry: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Použijte uživatelské jméno a přihlašovací server, které jste předtím zkopírovali ze služby Azure Container Registry. Po zobrazení výzvy k zadání hesla vložte heslo pro váš registr kontejneru a stiskněte **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. Tento soubor informuje modul runtime IoT Edge, které moduly se mají nasadit do zařízení. Všimněte si, že soubor obsahuje váš modul funkce **CSharpFunction** a také modul **tempSensor**, který poskytuje testovací data. Další informace o manifestech nasazení najdete ve [vysvětlení, jak lze moduly IoT Edge používat, konfigurovat a opětovně používat](module-composition.md).

   ![Zobrazení modulu v manifestu nasazení](./media/tutorial-deploy-function/deployment-template.png)

3. V pracovním prostoru vašeho řešení IoT Edge otevřete soubor **.env**. V tomto souboru, který Git ignoruje, jsou uložené přihlašovací údaje vašeho registru kontejneru, abyste je nemuseli vkládat do šablony manifestu nasazení. Zadejte **uživatelské jméno** a **heslo** pro váš registr kontejneru. 

5. Soubor uložte.

6. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci funkcí a kód odešlou do kontejneru registru, který jste zadali při inicializaci řešení. 

## <a name="view-your-container-image"></a>Zobrazení image kontejneru

Po odeslání image kontejneru do registru kontejneru zobrazí Visual Studio Code zprávu o úspěchu. Pokud chcete sami ověřit úspěch operace, můžete zobrazit image v registru. 

1. Na webu Azure Portal přejděte ke svému registru kontejneru Azure. 
2. Vyberte **Úložiště**.
3. Na seznamu byste měli vidět úložiště **csharpfunction**. Výběrem tohoto úložiště zobrazíte další podrobnosti.
4. V části **Značky** by se měla zobrazit značka **0.0.1-amd64**. Tato značka indikuje verzi a platformu image, kterou jste sestavili. Tyto hodnoty jsou nastavené v souboru module.json ve složce CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

K nasazení modulu funkce na zařízení IoT Edge můžete použít web Azure Portal, jako jste to udělali v rychlých startech. Moduly však můžete také nasadit a monitorovat z editoru Visual Studio Code. Následující sekce používají rozšíření Azure IoT Edge pro VS Code, které bylo uvedené v požadavcích. Pokud jste to ještě neudělali, nainstalujte toto rozšíření nyní. 

1. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

2. Vyhledejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se). Podle pokynů se přihlaste ke svému účtu Azure. 

3. Na paletě příkazů vyhledejte a spusťte příkaz **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub). 

4. Vyberte předplatné, které obsahuje váš IoT Hub, a pak vyberte IoT Hub, ke kterému chcete získat přístup.

5. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

6. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

7. Přejděte do složky řešení, která obsahuje modul **CSharpFunction**. Otevřete složku config, vyberte soubor deployment.json a pak zvolte **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge).

8. Aktualizujte sekci **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). Měl by se zobrazit spuštěný nový modul **CSharpFunction** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. 

   ![Zobrazení nasazených modulů ve VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Všechny zprávy přicházející do vašeho IoT Hubu můžete zobrazit tak, že na paletě příkazů spustíte příkaz **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: Začít monitorovat zprávy D2C).

Pomocí filtru také můžete zobrazit všechny zprávy přicházející do vašeho IoT Hubu z konkrétního zařízení. Klikněte pravým tlačítkem na **Azure IoT Hub Devices** (Zařízení Azure IoT Hub) a vyberte **Start Monitoring D2C Messages** (Začít monitorovat zprávy D2C).

Pokud chcete monitorování zpráv zastavit, spusťte na paletě příkazů příkaz **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Zastavit monitorování zpráv D2C). 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul funkce Azure Functions s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md). 

Pokračujte dalšími kurzy, ve kterých se seznámíte s jinými způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)

