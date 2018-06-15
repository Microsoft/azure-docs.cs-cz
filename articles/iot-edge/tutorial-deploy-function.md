---
title: Nasazení funkce Azure pomocí služby Azure IoT Edge | Microsoft Docs
description: Nasazení funkce Azure jako modulu do hraničního zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6102a28ec92f841fe32652e4dac36848d69e389c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631696"
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Nasazení funkce Azure jako modulu IoT Edge – Preview
Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provedete vytvořením a nasazením funkce Azure, která filtruje data senzorů na simulovaném zařízení IoT Edge, které jste vytvořili v kurzu Nasazení služby Azure IoT Edge na simulovaném zařízení ve [Windows][lnk-tutorial1-win] nebo v [Linuxu][lnk-tutorial1-lin]. V tomto kurzu se naučíte:     

> [!div class="checklist"]
> * Použití Visual Studio Code k vytvoření funkce Azure
> * Použití VS Code a Dockeru k vytvoření image Dockeru a jejímu publikování do registru 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Funkce Azure, kterou v tomto kurzu vytvoříte, filtruje data o teplotě generovaná vaším zařízením a odesílá zprávy dále do služby Azure IoT Hub pouze v případě, že teplota přesáhne zadanou prahovou hodnotu. 

## <a name="prerequisites"></a>Požadavky

* Zařízení Azure IoT Edge, které jste vytvořili v rychlém startu nebo předchozím kurzu.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Docker](https://docs.docker.com/engine/installation/). Pro účely tohoto kurzu je dostačující edice Community Edition (CE) pro vaši platformu. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu. 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce
Následující kroky ukazují, jak vytvořit funkci IoT Edge pomocí Visual Studio Code a rozšíření Azure IoT Edge.
1. Otevřete Visual Studio Code.
2. Pokud chcete otevřít integrovaný terminál VS Code, vyberte **Zobrazit** > **Integrovaný terminál**.
3. Pokud chcete nainstalovat (nebo aktualizovat) šablonu **AzureIoTEdgeFunction** v .NET, spusťte v integrovaném terminálu následující příkaz:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. Vytvořte projekt pro nový modul. Následující příkaz vytvoří složku projektu **FilterFunction** s vaším úložištěm kontejnerů. Pokud používáte registr kontejnerů Azure, druhý parametr by měl mít formát `<your container registry name>.azurecr.io`. V aktuální pracovní složce zadejte následující příkaz:

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. Vyberte **Soubor** > **Otevřít složku**, přejděte k složce **FilterFunction** a otevřete projekt ve VS Code.
4. V průzkumníku VS Code rozbalte složku **EdgeHubTrigger-Csharp** a otevřete soubor **run.csx**.
5. Obsah souboru nahraďte následujícím kódem:

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

11. Uložte soubor.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření image Dockeru a její publikování do registru

1. Přihlaste se k Dockeru zadáním následujícího příkazu v integrovaném terminálu VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Pokud chcete zjistit uživatelské jméno, heslo a přihlašovací server, které máte v tomto příkazu použít, přejděte na web [Azure portal] (https://portal.azure.com). V části **Všechny prostředky** kliknutím na dlaždici vašeho registru kontejneru otevřete jeho vlastnosti a pak klikněte na **Přístupové klíče**. Zkopírujte hodnoty v polích **Uživatelské jméno**, **Heslo** a **Přihlašovací server**. 

2. Otevřete soubor **module.json**. Volitelně můžete aktualizovat hodnotu `"version"` například na **1.0**. Zobrazí se také název úložiště, který jste zadali v parametru `-r` příkazu `dotnet new aziotedgefunction`.

3. Uložte soubor **module.json**.

4. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **module.json** a klikněte na **Sestavit a odeslat image Dockeru s modulem IoT Edge**. V místním rozevíracím seznamu v horní části okna VS Code vyberte platformu vašeho kontejneru – **amd64** pro kontejner Linuxu **windows-amd64** pro kontejner Windows. VS Code pak vytvoří kontejner s kódy vaší funkce a odešle jho do zadaného registru kontejnerů.

5. Úplnou adresu image kontejneru můžete získat pomocí značky v integrovaném terminálu VS Code. Další informace o definici sestavení a odeslání najdete v souboru `module.json`.

## <a name="add-registry-credentials-to-your-edge-device"></a>Přidání přihlašovacích údajů registru do hraničního zařízení
Přidejte přihlašovací údaje k vašemu registru do modulu runtime Edge na počítači, na kterém spouštíte službu Edge. Modul runtime tím získá přístup k vyžádání kontejneru. 

- V případě Windows spusťte následující příkaz:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- V případě Linuxu spusťte následující příkaz:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Spuštění řešení

1. Na webu **Azure Portal** přejděte do svého centra IoT.
2. Přejděte na **IoT Edge (preview)** a vyberte zařízení IoT Edge.
1. Vyberte **Nastavit moduly**. 
2. Pokud už jste do tohoto zařízení modul **tempSensor** nasadili, může už být automaticky vyplněný. Pokud ne, přidejte ho podle těchto pokynů:
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Další nastavení ponechte beze změny a klikněte na **Uložit**.
1. Přidejte modul **filterFunction**.
    1. Znovu vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `filterFunction`.
    3. Do pole **Identifikátor URI image** zadejte adresu své image, například `<your container registry address>/filterfunction:0.0.1-amd64`. Úplnou adresu image najdete v předchozí části.
    74. Klikněte na **Uložit**.
2. Klikněte na **Další**.
3. V kroku **Určení tras** zkopírujte do textového pole následující JSON. První trasa přenáší zprávy ze senzoru teploty do modulu filtru přes koncový bod input1. Druhá trasa přenáší zprávy z modulu filtru do služby IoT Hub. V této trase je `$upstream` speciální cíl, který centru Edge říká, že má odesílat zprávy do služby IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. Klikněte na **Další**.
5. V kroku **Kontrola šablony** klikněte na **Odeslat**. 
6. Vraťte se na stránku podrobností o zařízení IoT Edge a klikněte na **Aktualizovat**. Měl by se zobrazit spuštěný nový modul **filterFunction** společně s modulem **tempSensor** a **modulem runtime IoT Edge**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Monitorování zpráv typu zařízení-cloud odesílaných ze zařízení IoT Edge do centra IoT:
1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro vaše centrum IoT: 
    1. Na webu Azure Portal přejděte do svého centra IoT a vyberte **Zásady sdíleného přístupu**. 
    2. Vyberte **iothubowner** a zkopírujte hodnotu **Připojovací řetězec – primární klíč**.
    3. V průzkumníku VS Code klikněte na **ZAŘÍZENÍ SLUŽBY IOT HUB** a pak klikněte na **...**. 
    4. Vyberte **Nastavit připojovací řetězec služby IoT Hub** a do automaticky otevíraného okna zadejte připojovací řetězec služby IoT Hub. 

2. Pokud chcete monitorovat data přicházející do centra IoT, vyberte **Zobrazit** > **Paleta příkazů...** a vyhledejte **IoT: Spustit monitorování zpráv typu zařízení-cloud**. 
3. Pokud chcete monitorování dat zastavit, použijte příkaz **IoT: Zastavit monitorování zpráv typu zařízení-cloud** na paletě příkazů. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili funkci Azure obsahující kód pro filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Pokud chcete pokračovat v prozkoumávání služby Azure IoT Edge, přečtěte si, jak použít zařízení IoT Edge jako bránu. 

> [!div class="nextstepaction"]
> [Vytvoření zařízení brány služby IoT Edge](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
