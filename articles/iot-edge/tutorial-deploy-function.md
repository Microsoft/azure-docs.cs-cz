---
title: Nasazení funkcí Azure Functions pomocí služby Azure IoT Edge | Microsoft Docs
description: V tomto kurzu nasadíte funkci Azure Functions jako modul na hraniční zařízení.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d37e08f58986a1318e6b379d2efeb71bc58d4583
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413723"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>Kurz: Nasazení funkcí Azure Functions jako modulů IoT Edge (Preview)

Pomocí služby Azure Functions můžete nasadit kód, který implementuje vaši obchodní logiku přímo do zařízení Azure IoT Edge. Tento kurz vás provedete vytvořením a nasazením funkce Azure Functions, která filtruje data senzorů na simulovaném zařízení IoT Edge. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech o nasazení Azure IoT Edge na simulované zařízení ve [Windows][lnk-tutorial1-win] nebo [Linuxu][lnk-tutorial1-lin]. V tomto kurzu se naučíte:     

> [!div class="checklist"]
> * Použít Visual Studio Code k vytvoření funkce Azure Functions.
> * Použít VS Code a Docker k vytvoření image Dockeru a jejímu publikování do registru kontejneru.
> * Nasadit modul z registru kontejneru do zařízení IoT Edge.
> * Zobrazit filtrovaná data.

>[!NOTE]
>Moduly funkce Azure Functions v Azure IoT Edge jsou ve verzi [Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Funkce Azure Functions, kterou v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Tato funkce pošle zprávy dál do Azure IoT Hubu jen v případě, že teplota překročí zadanou prahovou hodnotu. 

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

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

    ![Vytvoření registru kontejnerů](./media/tutorial-deploy-function/create-container-registry.png)

2. Zadejte název registru a zvolte předplatné.
3. U skupiny prostředků se doporučuje použít název skupiny prostředků, která obsahuje službu IoT Hub. Když umístíte všechny prostředky do stejné skupiny, můžete je spravovat společně. Když se například odstraní skupina prostředků, která se používá k testování, odstraní se všechny testovací prostředky ve skupině. 
4. Nastavte skladovou položku na **Basic** a přepněte možnost **Uživatel s rolí správce** na **Povolit**. 
5. Vyberte **Vytvořit**.
6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu. 

## <a name="create-a-function-project"></a>Vytvoření projektu funkce
Následujícími kroky vytvoříte pomocí editoru Visual Studio Code a rozšíření Azure IoT Edge funkci IoT Edge.

1. Otevřete Visual Studio Code.
2. Výběrem **View** (Zobrazit) > **Integrated Terminal** (Integrovaný terminál) otevřete integrovaný terminál VS Code. 
2. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code.
3. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se). Podle pokynů se přihlaste ke svému účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.
3. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   1. Vyberte složku, ve které chcete vytvořit řešení. 
   2. Zadejte název pro vaše řešení nebo přijměte výchozí název **EdgeSolution**.
   3. Jako šablonu modulu zvolte **Azure Functions – C#**. 
   4. Zadejte název modulu **CSharpFunction**. 
   5. Jako úložiště imagí pro první modul určete registr kontejneru Azure, který jste vytvořili v předchozí části. Nahraďte **localhost:5000** hodnotou pro přihlašovací server, kterou jste zkopírovali. Konečný řetězec vypadá takto: \<název_registru\>.azurecr.io/csharpfunction.

4. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge: složka \.vscode, složka s moduly a soubor šablony manifestu nasazení. a soubor \.env. V průzkumníku VS Code otevřete **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**.

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
                log.Info("Received and transferred a message with temperature above the threshold");
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
   ```

6. Uložte soubor.

## <a name="build-your-iot-edge-solution"></a>Sestavení řešení IoT Edge

V předchozí části jste vytvořili řešení IoT Edge a do modulu **CSharpFunction** jste přidali kód k odfiltrování zpráv, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Zadáním následujícího příkazu v integrovaném terminálu editoru Visual Studio Code se přihlaste k Dockeru. Potom můžete odeslat image modulu do služby Azure Container Registry: 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    Použijte uživatelské jméno a přihlašovací server, které jste předtím zkopírovali ze služby Azure Container Registry. Zobrazí se výzva k zadání hesla. Vložte na příkazový řádek své heslo a stiskněte **Enter**.

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. V průzkumníku VS Code otevřete soubor deployment.template.json v pracovním prostoru řešení IoT Edge. Tento soubor informuje modul runtime IoT Edge, které moduly se mají nasadit do zařízení. Další informace o manifestech nasazení najdete ve [vysvětlení, jak lze moduly IoT Edge používat, konfigurovat a opětovně používat](module-composition.md).

3. V manifestu nasazení vyhledejte část **registryCredentials**. Aktualizujte hodnoty **username** (Uživatelské jméno), **password** (Heslo) a **address** (Adresa) s použitím přihlašovacích údajů z vašeho registru kontejneru. Tato část uděluje modulu runtime IoT Edge na vašem zařízení oprávnění k vyžádání imagí kontejnerů uložených ve vašem privátním registru. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje.

5. Soubor uložte.

6. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build IoT Edge solution** (Vytvořit řešení IoT Edge). 

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

6. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for IoT Edge device** (Vytvořit nasazení pro zařízení IoT Edge). 

7. Přejděte do složky řešení, která obsahuje modul **CSharpFunction**. Otevřete složku config, vyberte soubor deployment.json a pak zvolte **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge).

8. Aktualizujte sekci **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). Měl by se zobrazit spuštěný nový modul **CSharpFunction** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. 

   ![Zobrazení nasazených modulů ve VS Code](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Všechny zprávy přicházející do vašeho IoT Hubu můžete zobrazit tak, že na paletě příkazů spustíte příkaz **Azure IoT Hub: Start Monitoring D2C Message** (Azure IoT Hub: Začít monitorovat zprávy D2C).

Pomocí filtru také můžete zobrazit všechny zprávy přicházející do vašeho IoT Hubu z konkrétního zařízení. Klikněte pravým tlačítkem na **Azure IoT Hub Devices** (Zařízení Azure IoT Hub) a vyberte **Start Monitoring D2C Messages** (Začít monitorovat zprávy D2C).

Pokud chcete monitorování zpráv zastavit, spusťte na paletě příkazů příkaz **Azure IoT Hub: Stop monitoring D2C message** (Azure IoT Hub: Zastavit monitorování zpráv D2C). 


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

V závislosti na platformě vašeho zařízení IoT (Linux nebo Windows) odeberte modul runtime služby IoT Edge.

#### <a name="windows"></a>Windows

Odeberte modul runtime IoT Edge.

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

Odstraňte kontejnery, které se vytvořily ve vašem zařízení. 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

Odeberte modul runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Odstraňte kontejnery, které se vytvořily ve vašem zařízení. 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

Odeberte kontejnerový modul runtime.

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul funkce Azure Functions s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete chtít vytvářet vlastní moduly, můžete si přečíst další informace o [vývoji funkcí Azure Functions pomocí Azure IoT Edge pro Visual Studio Code](how-to-develop-csharp-function.md). 

Pokračujte dalšími kurzy, ve kterých se seznámíte s jinými způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
