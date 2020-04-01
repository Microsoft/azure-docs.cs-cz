---
title: 'Kurz: Nasazení funkcí Azure jako modulů – Azure IoT Edge'
description: V tomto kurzu můžete vyvinout funkci Azure jako modul IoT Edge a pak ho nasadit do hraničního zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: f909ca12ce080fc5d1241bcc649c041361e405a7
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421166"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Kurz: Nasazení funkcí Azure jako modulů IoT Edge

Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení Azure IoT Edge. Tento kurz vás provedete vytvořením a nasazením funkce Azure Functions, která filtruje data senzorů na simulovaném zařízení IoT Edge. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Použít Visual Studio Code k vytvoření funkce Azure Functions.
> * Použít VS Code a Docker k vytvoření image Dockeru a jejímu publikování do registru kontejneru.
> * Nasadit modul z registru kontejneru do zařízení IoT Edge.
> * Zobrazit filtrovaná data.

<center>

![Diagram – architektura výuky: modul funkce fáze a nasazení](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Funkce Azure Functions, kterou v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Tato funkce pošle zprávy dál do Azure IoT Hubu jen v případě, že teplota překročí zadanou prahovou hodnotu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Dokončením tohoto kurzu byste měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Linuxové zařízení se systémem Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Kód](https://code.visualstudio.com/) nakonfigurovaný pomocí [nástrojů Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Linuxu.

Chcete-li vyvíjet modul IoT Edge v azure functions, nainstalujte do vývojového počítače následující další předpoklady:

* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Nástroje Azure IoT pro kód Visual Studia, který jste nainstalovali v požadavcích, poskytují možnosti správy i některé šablony kódu. V této části pomocí Visual Studio Code vytvoříte řešení IoT Edge obsahující funkci Azure Functions.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení C# Function, kterou můžete přizpůsobit pomocí vlastního kódu.

1. Na vývojovém počítači otevřete Visual Studio Code.

2. Otevřete paletu příkazů VS Kód výběrem možnosti **Zobrazit** > **paletu příkazů**.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení, například **FunctionSolution**, nebo přijměte výchozí. |
   | Vyberte šablonu modulu | Zvolte **funkce Azure – C#**. |
   | Zadejte název modulu | Zadejte název modulu **CSharpFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. Konečný řetězec vypadá \<jako\>název registru .azurecr.io/CSharpFunction. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době visual studio kód můžete vyvíjet moduly C pro Linux AMD64 a Linux ARM32v7 zařízení. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože kontejner je sestaven a spuštěn jinak pro každý typ architektury. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Přidáme nějaký další kód, aby modul zpracuje zprávy na hraničním okraji před jejich předáním do ioT hubu.

1. V kódu Visual Studio otevřete **moduly** > **CSharpFunction** > **CSharpFunction.cs**.

1. Nahraďte obsah **souboru CSharpFunction.cs** následujícím kódem. Tento kód přijímá telemetrii o okolní a počítačteploty a pouze předá zprávu do služby IoT Hub, pokud je teplota počítače je nad definovanou prahovou hodnotu.

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

V předchozí části jste vytvořili řešení IoT Edge a **upravili CSharpFunction** odfiltrovat zprávy s hlášené teploty počítače pod přijatelnou prahovou hodnotu. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

V této části zadáte pověření pro registr kontejnerů podruhé (první byl v souboru **ENV** vašeho řešení IoT Edge) přihlášením místně z vývojového počítače tak, aby Visual Studio Code můžete odesílat obrázky do registru.

1. Otevřete integrovaný terminál VS Code výběrem **možnosti Zobrazit** > **terminál**.

2. Přihlaste se k registru kontejneru zadáním následujícího příkazu v integrovaném terminálu. Použijte uživatelské jméno a přihlašovací server, které jste předtím zkopírovali ze služby Azure Container Registry.

    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    Po zobrazení výzvy k zadání hesla vložte heslo (nebude viditelné v okně terminálu) pro registr kontejnerů a stiskněte **enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

3. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

Když sdělíte kódu sady Visual Studio, aby vytvořil i řešení, nejprve převezme informace v šabloně nasazení a vygeneruje soubor deployment.json v nové složce s názvem **config**. Pak běží dva příkazy v `docker build` integrovaném terminálu: a `docker push`. Příkaz sestavení vytvoří váš kód a kontejnerizuje funkce. Příkaz push pak odešle kód do registru kontejneru, který jste zadali při inicializace řešení.

## <a name="view-your-container-image"></a>Zobrazení image kontejneru

Po odeslání image kontejneru do registru kontejneru zobrazí Visual Studio Code zprávu o úspěchu. Pokud chcete sami ověřit úspěch operace, můžete zobrazit image v registru.

1. Na webu Azure Portal přejděte ke svému registru kontejneru Azure.
2. Vyberte **Úložiště**.
3. Na seznamu byste měli vidět úložiště **csharpfunction**. Výběrem tohoto úložiště zobrazíte další podrobnosti.
4. V části **Značky** by se měla zobrazit značka **0.0.1-amd64**. Tato značka indikuje verzi a platformu image, kterou jste sestavili. Tyto hodnoty jsou nastavené v souboru module.json ve složce CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

K nasazení modulu funkce na zařízení IoT Edge můžete použít web Azure Portal, jako jste to udělali v rychlých startech. Moduly však můžete také nasadit a monitorovat z editoru Visual Studio Code. V následujících částech se používají nástroje Azure IoT pro Kód VS, který byl uveden v požadavcích. Pokud jste to ještě neudělali, nainstalujte toto rozšíření nyní.

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub).

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for single device** (Vytvořit nasazení pro jedno zařízení).

3. Přejděte do složky řešení, která obsahuje modul **CSharpFunction**. Otevřete složku config, vyberte soubor **deployment.json** a pak **zvolte Vybrat manifest nasazení okraje**.

4. Aktualizujte sekci **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). Měli byste vidět nové **CSharpFunction** běží spolu s **Modul SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**. Může chvíli trvat, než se nové moduly zobrazí. Vaše zařízení IoT Edge musí načíst své nové informace o nasazení z ioT hubu, spustit nové kontejnery a pak ohlásit stav zpět do služby IoT Hub.

   ![Zobrazení nasazených modulů ve VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Všechny zprávy, které se do vašeho ioT centra zobrazí, se můžou zobrazit spuštěním **služby Azure IoT Hub: Spuštění matného koncového bodu s integrovaným monitorováním** v paletě příkazů.

Pomocí filtru také můžete zobrazit všechny zprávy přicházející do vašeho IoT Hubu z konkrétního zařízení. Klikněte pravým tlačítkem myši na zařízení v části **Zařízení služby Azure IoT Hub** a vyberte **Spustit monitorování integrovaného koncového bodu událostí**.

Chcete-li zastavit monitorování zpráv, spusťte příkaz **Azure IoT Hub: Stop monitoring Built-in Event Endpoint** v paletě příkazů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul funkce Azure Functions s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni k vytvoření vlastních modulů, přečtěte si další informace o [vývoji pomocí kódu Azure IoT Edge for Visual Studio](how-to-vs-code-develop-module.md)Code .

Pokračujte dalšími kurzy, ve kterých se seznámíte s jinými způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)
