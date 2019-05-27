---
title: Kurz nasazení funkce Azure do zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu budete vyvíjet Azure fungovat jako modul IoT Edge a pak ji nasadit do hraničního zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 5b7d903c8be74e4c0561bb4a857619c9c62f95a9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239659"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Kurz: Nasazení Azure funguje jako moduly IoT Edge

Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení Azure IoT Edge. Tento kurz vás provedete vytvořením a nasazením funkce Azure Functions, která filtruje data senzorů na simulovaném zařízení IoT Edge. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:     

> [!div class="checklist"]
> * Použít Visual Studio Code k vytvoření funkce Azure Functions.
> * Použít VS Code a Docker k vytvoření image Dockeru a jejímu publikování do registru kontejneru.
> * Nasadit modul z registru kontejneru do zařízení IoT Edge.
> * Zobrazit filtrovaná data.

<center>

![Diagram – kurz architektury, Příprava a nasazení modulu – funkce](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Moduly funkce Azure Functions v Azure IoT Edge jsou ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Funkce Azure Functions, kterou v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Tato funkce pošle zprávy dál do Azure IoT Hubu jen v případě, že teplota překročí zadanou prahovou hodnotu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, by měl prošli předchozího kurzu věnovaného nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu, byste měli mít splněné následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linuxovému zařízení s Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovanou [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurované ke spuštění kontejnerů Linuxu.

K vývoji modul IoT Edge se pomocí Azure Functions, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Nástroje Azure IoT pro Visual Studio Code, který jste nainstalovali v rámci požadavků poskytuje funkce pro správu, jakož i některé šablony kódu. V této části pomocí Visual Studio Code vytvoříte řešení IoT Edge obsahující funkci Azure Functions. 

### <a name="create-a-new-project"></a>Vytvořit nový projekt

Vytvoření C# šablony funkce řešení, kterou můžete přizpůsobit s vlastním kódem.

1. Na vývojovém počítači otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení, jako je třeba **FunctionSolution**, nebo přijměte výchozí nastavení. |
   | Vyberte šablonu modulu | Zvolte **Azure Functions – C#** . |
   | Zadejte název modulu | Zadejte název modulu **CSharpFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. Konečný řetězec vypadá jako \<název registru\>.azurecr.io/CSharpFunction. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte Cílová architektura

Visual Studio Code v současné době můžete vyvíjet C moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Budete muset vybrat, jakou architekturu vývoji cílíte jednotlivých řešení, protože kontejneru je sestaven a jinak spusťte pro každý typ architektury. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavit výchozí Cílová platforma pro řešení**, nebo vyberte ikonu zástupce v bočním panelu v dolní části okna. 

2. Vyberte Cílová architektura v paletu příkazů v seznamu možností. Pro účely tohoto kurzu používáme virtuálního počítače s Ubuntu jako zařízení IoT Edge, budou mít výchozí **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Přidáme další kód tak, aby modul zpracovávat zprávy na hraničních zařízeních před předáním do služby IoT Hub.

1. V sadě Visual Studio Code otevřete **moduly** > **CSharpFunction** > **CSharpFunction.cs**.

1. Nahraďte obsah **CSharpFunction.cs** souboru následujícím kódem. Tento kód přijímá telemetrická data o okolí a počítač teploty a pouze předává zprávy do služby IoT Hub, jestliže je počítač teplota nad definovanou prahovou hodnotu.

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
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
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

1. Uložte soubor.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a do modulu **CSharpFunction** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

V této části dvakrát zadáte přihlašovací údaje pro váš registr kontejneru. Jako první je zadáte pro místní přihlášení z vývojového počítače, aby editor Visual Studio Code mohl odesílat image do vašeho registru. Pak je zadáte v souboru **.env** vašeho řešení IoT Edge a tím udělíte vašemu zařízení IoT Edge oprávnění ke stahování imagí z registru. 

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code. 

2. Přihlaste se k registru kontejneru zadáním následujícího příkazu v integrovaném terminálu. Použijte uživatelské jméno a přihlašovací server, které jste předtím zkopírovali ze služby Azure Container Registry.
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Po zobrazení výzvy k zadání hesla vložte heslo pro váš registr kontejneru a stiskněte **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci funkcí a kód odešlou do kontejneru registru, který jste zadali při inicializaci řešení. 

## <a name="view-your-container-image"></a>Zobrazení image kontejneru

Po odeslání image kontejneru do registru kontejneru zobrazí Visual Studio Code zprávu o úspěchu. Pokud chcete sami ověřit úspěch operace, můžete zobrazit image v registru. 

1. Na webu Azure Portal přejděte ke svému registru kontejneru Azure. 
2. Vyberte **Úložiště**.
3. Na seznamu byste měli vidět úložiště **csharpfunction**. Výběrem tohoto úložiště zobrazíte další podrobnosti.
4. V části **Značky** by se měla zobrazit značka **0.0.1-amd64**. Tato značka indikuje verzi a platformu image, kterou jste sestavili. Tyto hodnoty jsou nastavené v souboru module.json ve složce CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

K nasazení modulu funkce na zařízení IoT Edge můžete použít web Azure Portal, jako jste to udělali v rychlých startech. Moduly však můžete také nasadit a monitorovat z editoru Visual Studio Code. Následující části použijte nástroje Azure IoT pro VS Code, který byl uveden v rámci požadavků. Pokud jste to ještě neudělali, nainstalujte toto rozšíření nyní. 

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

3. Přejděte do složky řešení, která obsahuje modul **CSharpFunction**. Otevřete složku konfigurace, vyberte **deployment.json** souboru a klikněte na tlačítko **vyberte Manifest nasazení Edge**.

4. Aktualizujte sekci **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). Měl by se zobrazit spuštěný nový modul **CSharpFunction** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. Může trvat ještě chvilku na nové moduly zobrazení. Zařízení IoT Edge má k načtení nové informace o nasazení ze služby IoT Hub, spustit nové kontejnery a vykazování stavu zpět do služby IoT Hub. 

   ![Zobrazení nasazených modulů ve VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Zobrazí se všechny zprávy, které přicházejí na váš IoT hub spuštěním **Azure IoT Hub: Spuštění monitorování koncového bodu integrované události** v paletu příkazů.

Pomocí filtru také můžete zobrazit všechny zprávy přicházející do vašeho IoT Hubu z konkrétního zařízení. Klikněte pravým tlačítkem na zařízení do služby **zařízení Azure IoT Hub** a vyberte **spustit monitorování integrovaných událostí koncový bod**.

Pokud chcete zastavit monitorování zpráv, spusťte příkaz **Azure IoT Hub: Zastavit monitorování koncového bodu integrované události** v paletu příkazů. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul funkce Azure Functions s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni k sestavení vlastních modulů, můžete další informace o tom, jak [vývoj s použitím Azure IoT Edge pro Visual Studio Code](how-to-vs-code-develop-module.md). 

Pokračujte dalšími kurzy, ve kterých se seznámíte s jinými způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)
