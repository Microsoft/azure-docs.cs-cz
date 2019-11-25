---
title: Tutorial develop Node.js module for Linux - Azure IoT Edge | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Node.js a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 16212f7229dc84b9495976bd40b6d48b3f2686bd
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457647"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Tutorial: Develop and deploy a Node.js IoT Edge module for Linux devices

Use Visual Studio Code to develop Node.js code and deploy it to a Linux device running Azure IoT Edge. 

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Node.js
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru. 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Solution scope

This tutorial demonstrates how to develop a module in **Node.js** using **Visual Studio Code**, and how to deploy it to a **Linux device**. IoT Edge does not support Node.js modules for Windows devices.

Use the following table to understand your options for developing and deploying Node.js modules: 

| Node.js | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use VS Code for Node.js modules on Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use VS Code for Node.js modules on Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Předpoklady

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing either of those tutorials, you should have the following prerequisites in place: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in Node.js, install the following additional prerequisites on your development machine: 

* [Node.js a npm](https://nodejs.org) Balíček npm se distribuuje společně s Node.js, takže když si stáhnete Node.js, nainstaluje se vám npm na počítač automaticky.

## <a name="create-a-module-project"></a>Create a module project
The following steps show you how to create an IoT Edge Node.js module using Visual Studio Code and the Azure IoT Tools.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Pomocí **npm** vytvořte šablonu řešení Node.js, na jejímž základě budete moct vytvářet dál. 

1. V nástroji Visual Studio Code vyberte, že chcete **zobrazit** > **integrovaný terminál**, aby se otevřel integrovaný terminál VS Code.

2. V integrovaném terminálu zadejte následující příkaz, kterým nainstalujete **yeoman** a generátor pro modul Node.js Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

3. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

4. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Vyberte šablonu modulu | Choose **Node.js Module**. |
   | Zadejte název modulu | Dejte modulu název **NodeModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Your container image is prepopulated from the name you provided in the last step. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>The final image repository looks like \<registry name\>.azurecr.io/nodemodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-node-module/repository.png)


### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor **.env**. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop Node.js modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Each template comes with sample code included, which takes simulated sensor data from the **SimulatedTemperatureSensor** module and routes it to IoT Hub. V této části přidáte kód, který připraví NodeModule k analýze zpráv před jejich odesláním. 

1. V průzkumníku VS Code otevřete **modules** > **NodeModule** > **app.js**.

2. Pod požadované moduly uzlu přidejte proměnnou prahové teploty. Prahová teplota definuje hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Celou funkci `PipeMessage` nahraďte funkcí `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Ve funkci `client.on()` nahraďte funkci s názvem `pipeMessage` funkcí s názvem `filterMessage`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Zkopírujte následující fragment kódu do zpětného volání funkce `client.open()`, za funkci `client.on()` v příkazu `else`. Tato funkce se bude volat při aktualizaci požadovaných vlastností.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Save the app.js file.

7. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

8. Přidejte do manifestu nasazení dvojče modulu NodeModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Add module twin to deployment template](./media/tutorial-node-module/module-twin.png)

9. Save the deployment.template.json file.


## <a name="build-and-push-your-module"></a>Build and push your module

In the previous section, you created an IoT Edge solution and added code to the NodeModule that will filter out messages where the reported machine temperature is within the acceptable limits. Teď je potřeba sestavit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code.

1. Sign in to Docker by entering the following command in the terminal. Sign in with the username, password, and login server from your Azure container registry. You can retrieve these values from the **Access keys** section of your registry in the Azure portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   You may receive a security warning recommending the use of `--password-stdin`. While that best practice is recommended for production scenarios, it's outside the scope of this tutorial. For more information, see the [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) reference.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

   The build and push command starts three operations. First, it creates a new folder in the solution called **config** that holds the full deployment manifest, built out of information in the deployment template and other solution files. Second, it runs `docker build` to build the container image based on the appropriate dockerfile for your target architecture. Then, it runs `docker push` to push the image repository to your container registry.

## <a name="deploy-modules-to-device"></a>Deploy modules to device

Use the Visual Studio Code explorer and the Azure IoT Tools extension to deploy the module project to your IoT Edge device. You already have a deployment manifest prepared for your scenario, the **deployment.json** file in the config folder. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Make sure that your IoT Edge device is up and running.

1. In the Visual Studio Code explorer, expand the **Azure IoT Hub Devices** section to see your list of IoT devices.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. You should see the new **NodeModule** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. In the Visual Studio Code explorer, right-click the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**.

2. View the messages arriving at your IoT Hub. It may take a while for the messages to arrive, because the IoT Edge device has to receive its new deployment and start all the modules. Then, the changes we made to the NodeModule code wait until the machine temperature reaches 25 degrees before sending messages. It also adds the message type **Alert** to any messages that reach that temperature threshold. 

## <a name="edit-the-module-twin"></a>Edit the module twin

We used the NodeModule module twin in the deployment manifest to set the temperature threshold at 25 degrees. You can use the module twin to change the functionality without having to update the module code.

1. In Visual Studio Code, expand the details under your IoT Edge device to see the running modules. 

2. Right-click **NodeModule** and select **Edit module twin**. 

3. Find **TemperatureThreshold** in the desired properties. Change its value to a new temperature 5 degrees to 10 degrees higher than the latest reported temperature. 

4. Save the module twin file.

5. Right-click anywhere in the module twin editing pane and select **Update module twin**. 

6. Monitor the incoming device-to-cloud messages. You should see the messages stop until the new temperature threshold is reached. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. When you're ready to build your own modules, you can learn more about [developing your own IoT Edge modules](module-development.md) or how to [develop modules with Visual Studio Code](how-to-vs-code-develop-module.md). You can continue on to the next tutorials to learn how Azure IoT Edge can help you deploy Azure cloud services to process and analyze data at the edge.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
