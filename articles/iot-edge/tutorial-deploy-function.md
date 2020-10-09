---
title: 'Kurz: nasazení služby Azure Functions jako modulů – Azure IoT Edge'
description: V tomto kurzu vyvíjíte funkci Azure Function jako modul IoT Edge a pak ho nasadíte do hraničního zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 9526a1569a5d8320dd59272ca97f5b48ec1dac1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999094"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>Kurz: nasazení Azure Functions jako IoT Edgech modulů

Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení Azure IoT Edge. Tento kurz vás provede vytvořením a nasazením funkce Azure, která filtruje data senzorů na simulovaném IoT Edgem zařízení. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Pomocí Visual Studio Code vytvořit funkci Azure Functions.
> * Použít VS Code a Docker k vytvoření image Dockeru a jejímu publikování do registru kontejneru.
> * Nasadit modul z registru kontejneru do zařízení IoT Edge.
> * Zobrazit filtrovaná data.

<center>

![Diagram – architektura kurzu: fáze a nasazení modulu funkcí](./media/tutorial-deploy-function/functions-architecture.png)
</center>

Funkce Azure, kterou vytvoříte v tomto kurzu, filtruje data o teplotě vygenerovaná vaším zařízením. Funkce odesílá zprávy do Azure IoT Hub pouze v případě, že je teplota nad určenou prahovou hodnotou.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste si měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů pro Linux: [vývoj IoT Edgech modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md). Po dokončení tohoto kurzu byste měli mít následující požadavky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Linux se spuštěným Azure IoT Edge](quickstart-linux.md)
* Registr kontejneru, například [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

Pokud chcete vytvořit modul IoT Edge v nástroji s Azure Functions, nainstalujte na svém vývojovém počítači následující další požadavky:

* [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Vytvoření projektu funkce

Nástroje Azure IoT Tools pro Visual Studio Code, které jste nainstalovali v požadavcích, poskytují možnosti správy i některé šablony kódu. V této části použijete Visual Studio Code k vytvoření řešení IoT Edge obsahujícího funkci Azure Functions.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení Functions v jazyce C#, kterou lze přizpůsobit vlastním kódem.

1. Na vývojovém počítači otevřete Visual Studio Code.

2. Otevřete paletu příkazů vs Code výběrem možnosti **Zobrazit**  >  **paletu příkazů**.

3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení, třeba **FunctionSolution**, nebo přijměte výchozí nastavení. |
   | Vyberte šablonu modulu | Vyberte možnost **Azure Functions-C#**. |
   | Zadejte název modulu | Zadejte název modulu **CSharpFunction**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Položku **localhost: 5000** nahraďte hodnotou **přihlašovacího serveru** z služby Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal. Výsledný řetězec vypadá jako \<registry name\> . azurecr.IO/CSharpFunction. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

Rozšíření IoT Edge se pokusí načíst přihlašovací údaje registru kontejneru z Azure a naplnit je do souboru prostředí. Zkontrolujte, jestli jsou vaše přihlašovací údaje už zahrnuté. Pokud ne, přidejte je nyní:

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly C pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení Edge**nebo vyberte ikonu zástupce na bočním panelu v dolní části okna.

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Pojďme přidat nějaký další kód, aby modul zpracoval zprávy na hranici před jejich přesměrováním na IoT Hub.

1. V Visual Studio Code otevřete **moduly**  >  **CSharpFunction**  >  **CSharpFunction.cs**.

1. Obsah souboru **CSharpFunction.cs** nahraďte následujícím kódem. Tento kód obdrží telemetrii o teplotě okolí a počítače a přesměruje zprávu na IoT Hub, pokud je teplota počítače nad stanovenou prahovou hodnotou.

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

## <a name="build-and-push-your-iot-edge-solution"></a>Sestavení a nabízení IoT Edge řešení

V předchozí části jste vytvořili řešení IoT Edge a upravili **CSharpFunction** pro filtrování zpráv s nahlášenými teplotami počítačů pod přijatelnou prahovou hodnotou. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Otevřete vs Code Integrated Terminal výběrem možnosti **Zobrazit**  >  **terminál**.

2. Přihlaste se k Docker zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití nástroje `--password-stdin` . I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu přihlašovací Reference k [Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

3. V Průzkumníku VS Code klikněte pravým tlačítkem na **deployment.template.jsna** soubor a vyberte **sestavení a nabízené IoT Edge řešení**.

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, který vychází z informací v šabloně nasazení a dalších souborů řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a nahraje `docker push` úložiště imagí do registru kontejneru.

   Tento proces může trvat několik minut poprvé, ale při příštím spuštění příkazů je rychlejší.

## <a name="view-your-container-image"></a>Zobrazení image kontejneru

Po odeslání image kontejneru do registru kontejneru zobrazí Visual Studio Code zprávu o úspěchu. Pokud chcete sami ověřit úspěch operace, můžete zobrazit image v registru.

1. Na webu Azure Portal přejděte ke svému registru kontejneru Azure.
2. Vyberte **Úložiště**.
3. Na seznamu byste měli vidět úložiště **csharpfunction**. Výběrem tohoto úložiště zobrazíte další podrobnosti.
4. V části **Značky** by se měla zobrazit značka **0.0.1-amd64**. Tato značka indikuje verzi a platformu image, kterou jste sestavili. Tyto hodnoty jsou nastavené v souboru module.json ve složce CSharpFunction.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

Pomocí Azure Portal můžete nasadit modul Functions do IoT Edge zařízení, jako jste provedli v rychlých startech. Moduly však můžete také nasadit a monitorovat z editoru Visual Studio Code. V následujících částech se používají nástroje Azure IoT pro VS Code, které jsou uvedené v části požadavky. Pokud jste to ještě neudělali, nainstalujte toto rozšíření nyní.

1. V Průzkumníkovi Visual Studio Code v části **Azure IoT Hub** rozbalte **zařízení** , abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název zařízení IoT Edge a pak vyberte **vytvořit nasazení pro jedno zařízení**.

3. Přejděte do složky řešení, která obsahuje modul **CSharpFunction**. Otevřete konfigurační složku, vyberte **deployment.amd64.jsv** souboru a pak zvolte **možnost vybrat manifest nasazení Edge**.

4. V části zařízení rozbalte **moduly** a zobrazte seznam nasazených a spuštěných modulů. Klikněte na tlačítko pro obnovení. Měl by se zobrazit nový **CSharpFunction** spuštěný spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

    Aby se nové moduly zobrazovaly, může chvíli trvat. Vaše zařízení IoT Edge musí načíst nové informace o nasazení z IoT Hub, začít nové kontejnery a pak stav nahlásit zpět do IoT Hub.

   ![Zobrazení nasazených modulů ve VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Pomocí služby Azure IoT Hub můžete zobrazit všechny zprávy, které dorazí do služby IoT Hub: na paletě příkazů **spusťte sledování integrovaného koncového bodu událostí** .

Pomocí filtru také můžete zobrazit všechny zprávy přicházející do vašeho IoT Hubu z konkrétního zařízení. V části **zařízení IoT Hub Azure** klikněte pravým tlačítkem myši na zařízení a vyberte **Spustit sledování integrovaného koncového bodu události**.

Pokud chcete zprávy monitorování zastavit, spusťte příkaz **Azure IoT Hub: v paletě příkazů ukončete monitorování integrovaného koncového bodu události** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul Azure Functions s kódem k filtrování nezpracovaných dat generovaných vaším zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete získat další informace o [vývoji Azure IoT Edge Visual Studio Code](how-to-vs-code-develop-module.md).

Pokračujte dalšími kurzy, ve kterých se seznámíte s jinými způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)
