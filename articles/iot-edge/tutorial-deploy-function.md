---
title: Tutorial deploy an Azure function to a device - Azure IoT Edge | Microsoft Docs
description: In this tutorial, you develop an Azure function as an IoT Edge module, then deploy it to an edge device.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1db64c2cc68dc9c47d4f2cf5f63eb0667907d737
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456714"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Tutorial: Deploy Azure functions as IoT Edge modules

Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení Azure IoT Edge. Tento kurz vás provedete vytvořením a nasazením funkce Azure Functions, která filtruje data senzorů na simulovaném zařízení IoT Edge. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použít Visual Studio Code k vytvoření funkce Azure Functions.
> * Použít VS Code a Docker k vytvoření image Dockeru a jejímu publikování do registru kontejneru.
> * Nasadit modul z registru kontejneru do zařízení IoT Edge.
> * Zobrazit filtrovaná data.

<center>

![Diagram - Tutorial architecture, stage and deploy function module](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Moduly funkce Azure Functions v Azure IoT Edge jsou ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Funkce Azure Functions, kterou v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Tato funkce pošle zprávy dál do Azure IoT Hubu jen v případě, že teplota překročí zadanou prahovou hodnotu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in with Azure Functions, install the following additional prerequisites on your development machine: 

* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

The Azure IoT Tools for Visual Studio Code that you installed in the prerequisites provides management capabilities as well as some code templates. V této části pomocí Visual Studio Code vytvoříte řešení IoT Edge obsahující funkci Azure Functions. 

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Create a C# Function solution template that you can customize with your own code.

1. Na vývojovém počítači otevřete Visual Studio Code.

2. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Enter a descriptive name for your solution, like **FunctionSolution**, or accept the default. |
   | Vyberte šablonu modulu | Choose **Azure Functions - C#** . |
   | Zadejte název modulu | Zadejte název modulu **CSharpFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. The final string looks like \<registry name\>.azurecr.io/CSharpFunction. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop C modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Let's add some additional code so that the module processes the messages at the edge before forwarding them to IoT Hub.

1. In Visual Studio Code, open **modules** > **CSharpFunction** > **CSharpFunction.cs**.

1. Replace the contents of the **CSharpFunction.cs** file with the following code. This code receives telemetry about ambient and machine temperature, and only forwards the message on to IoT Hub if the machine temperature is above a defined threshold.

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
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
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

## <a name="build-your-iot-edge-solution"></a>Vytvoření řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a do modulu **CSharpFunction** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba sestavit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

In this section, you provide the credentials for your container registry for the second time (the first was in the **.env** file of your IoT Edge solution) by signing in locally from your development machine so that Visual Studio Code can push images to your registry.

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code. 

2. Přihlaste se k registru kontejneru zadáním následujícího příkazu v integrovaném terminálu. Použijte uživatelské jméno a přihlašovací server, které jste předtím zkopírovali ze služby Azure Container Registry.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    When you're prompted for the password, paste the password (it won't be visible in the terminal window) for your container registry and press **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

When you tell Visual Studio Code to build your solution, it first takes the information in the deployment template and generates a deployment.json file in a new folder named **config**. Then it runs two commands in the integrated terminal: `docker build` and `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci funkcí a kód odešlou do kontejneru registru, který jste zadali při inicializaci řešení. 

## <a name="view-your-container-image"></a>Zobrazení image kontejneru

Po odeslání image kontejneru do registru kontejneru zobrazí Visual Studio Code zprávu o úspěchu. Pokud chcete sami ověřit úspěch operace, můžete zobrazit image v registru. 

1. Na webu Azure Portal přejděte ke svému registru kontejneru Azure. 
2. Vyberte **Úložiště**.
3. Na seznamu byste měli vidět úložiště **csharpfunction**. Výběrem tohoto úložiště zobrazíte další podrobnosti.
4. V části **Značky** by se měla zobrazit značka **0.0.1-amd64**. Tato značka indikuje verzi a platformu image, kterou jste sestavili. Tyto hodnoty jsou nastavené v souboru module.json ve složce CSharpFunction. 

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

K nasazení modulu funkce na zařízení IoT Edge můžete použít web Azure Portal, jako jste to udělali v rychlých startech. Moduly však můžete také nasadit a monitorovat z editoru Visual Studio Code. The following sections use the Azure IoT Tools for VS Code that was listed in the prerequisites. Pokud jste to ještě neudělali, nainstalujte toto rozšíření nyní. 

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for single device** (Vytvořit nasazení pro jedno zařízení). 

3. Přejděte do složky řešení, která obsahuje modul **CSharpFunction**. Open the config folder, select the **deployment.json** file, and then choose **Select Edge Deployment Manifest**.

4. Aktualizujte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). You should see the new **CSharpFunction** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**. It may take a few moments for the new modules to show up. Your IoT Edge device has to retrieve its new deployment information from IoT Hub, start the new containers, and then report the status back to IoT Hub. 

   ![Zobrazení nasazených modulů ve VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

You can see all of the messages that arrive at your IoT hub by running **Azure IoT Hub: Start Monitoring Built-in Event Endpoint** in the command palette.

Pomocí filtru také můžete zobrazit všechny zprávy přicházející do vašeho IoT Hubu z konkrétního zařízení. Right-click the device in the **Azure IoT Hub Devices** section and select **Start Monitoring Built-in Event Endpoint**.

To stop monitoring messages, run the command **Azure IoT Hub: Stop Monitoring Built-in Event Endpoint** in the command palette. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul funkce Azure Functions s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. When you're ready to build your own modules, you can learn more about how to [Develop with Azure IoT Edge for Visual Studio Code](how-to-vs-code-develop-module.md). 

Pokračujte dalšími kurzy, ve kterých se seznámíte s jinými způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)
