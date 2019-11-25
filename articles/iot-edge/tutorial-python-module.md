---
title: Create custom Python module - Azure IoT Edge | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Python a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4db5089d940abb2d15fa7ca79bacce425ee30123
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457615"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Tutorial: Develop and deploy a Python IoT Edge module for Linux devices

Use Visual Studio Code to develop C code and deploy it to a Linux device running Azure IoT Edge. 

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. This tutorial walks you through creating and deploying an IoT Edge module that filters sensor data on the IoT Edge device that you set up in the quickstart. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Python.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> The following guide is for the V1 Python SDK, which has since been deprecated. We are currently working to make this guide V2 compatible. Please watch this space for updates.

## <a name="solution-scope"></a>Solution scope

This tutorial demonstrates how to develop a module in **Python** using **Visual Studio Code**, and how to deploy it to a **Linux device**. IoT Edge does not support Python modules for Windows devices. 

Use the following table to understand your options for developing and deploying Python modules to Linux: 

| Python | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use VS Code for Python modules on Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use VS Code for Python modules on Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Předpoklady

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing either of those tutorials, you should have the following prerequisites in place: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in Python, install the following additional prerequisites on your development machine: 

* [Rozšíření Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Python (většinou je součástí instalace Pythonu).

>[!Note]
>Ujistěte se, že se vaše složka `bin` nachází v cestě pro vaši platformu. Obvykle je to `~/.local/` pro UNIX a macOS nebo `%APPDATA%\Python` ve Windows.

## <a name="create-a-module-project"></a>Create a module project
The following steps create an IoT Edge Python module by using Visual Studio Code and the Azure IoT Tools.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Use the VS Code to create a Python solution template that you can build on top of. 

1. Ve Visual Studio Code vyberte **Zobrazit** > **Terminál**, aby se otevřel integrovaný terminál VS Code.

1. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

1. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

1. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Follow the prompts and provide the following information to create your solution:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **Modul Python**. |
   | Zadejte název modulu | Pojmenujte modul **PythonModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Your container image is prepopulated from the name you provided in the last step. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>The final image repository looks like \<registry name\>.azurecr.io/pythonmodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor **.env**. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Uložte soubor .env. 

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop C modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Each template includes sample code, which takes simulated sensor data from the **SimulatedTemperatureSensor** module and routes it to the IoT hub. V této části přidáte kód, který rozbalí **PythonModule** k analýze zpráv před jejich odesláním. 

1. V průzkumníku VS Code otevřete **modules** > **PythonModule** > **main.py**.

2. V horní části souboru **main.py** importujte knihovnu **json**:

    ```python
    import json
    ```

3. Do globálních čítačů přidejte proměnné **TEMPERATURE_THRESHOLD** a **TWIN_CALLBACKS**. Prahová teplota definuje hodnotu, kterou musí naměřená teplota počítače překročit, aby se data odeslala do IoT Hubu.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. Replace the **input1_listener** function with the following code:

    ```python
        # Define behavior for receiving an input message on input1
        # Because this is a filter module, we forward this message to the "output1" queue.
        async def input1_listener(module_client):
            global RECEIVED_MESSAGES
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    input_message = await module_client.receive_message_on_input("input1")  # blocking call
                    message = input_message.data
                    size = len(message)
                    message_text = message.decode('utf-8')
                    print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
                    custom_properties = input_message.custom_properties
                    print ( "    Properties: %s" % custom_properties )
                    RECEIVED_MESSAGES += 1
                    print ( "    Total messages received: %d" % RECEIVED_MESSAGES )
                    data = json.loads(message_text)
                    if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                        custom_properties["MessageType"] = "Alert"
                        print ( "Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
                        await module_client.send_message_to_output(input_message, "output1")
                except Exception as ex:
                    print ( "Unexpected error in input1_listener: %s" % ex )
        
        # twin_patch_listener is invoked when the module twin's desired properties are updated.
        async def twin_patch_listener(module_client):
            global TWIN_CALLBACKS
            global TEMPERATURE_THRESHOLD
            while True:
                try:
                    data = await module_client.receive_twin_desired_properties_patch()  # blocking call
                    print( "The data in the desired properties patch was: %s" % data)
                    if "TemperatureThreshold" in data:
                        TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
                    TWIN_CALLBACKS += 1
                    print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
                except Exception as ex:
                    print ( "Unexpected error in twin_patch_listener: %s" % ex )
    ```

5. Update the **listeners** to also listen twin updates.

    ```python
        # Schedule task for C2D Listener
        listeners = asyncio.gather(input1_listener(module_client), twin_patch_listener(module_client))

        print ( "The sample is now waiting for messages. ")
    ```

6. Save the main.py file.

7. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. 

9. Přidejte do manifestu nasazení dvojče modulu **PythonModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Add module twin to deployment template](./media/tutorial-python-module/module-twin.png)

10. Save the deployment.template.json file.

## <a name="build-and-push-your-module"></a>Build and push your module

In the previous section, you created an IoT Edge solution and added code to the PythonModule that will filter out messages where the reported machine temperature is within the acceptable limits. Teď je potřeba sestavit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

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

4. Klikněte na tlačítko pro obnovení. You should see the new **PythonModule** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. In the Visual Studio Code explorer, right-click the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**.

2. View the messages arriving at your IoT Hub. It may take a while for the messages to arrive, because the IoT Edge device has to receive its new deployment and start all the modules. Then, the changes we made to the PythonModule code wait until the machine temperature reaches 25 degrees before sending messages. It also adds the message type **Alert** to any messages that reach that temperature threshold. 

## <a name="edit-the-module-twin"></a>Edit the module twin

We used the PythonModule module twin in the deployment manifest to set the temperature threshold at 25 degrees. You can use the module twin to change the functionality without having to update the module code.

1. In Visual Studio Code, expand the details under your IoT Edge device to see the running modules. 

2. Right-click **PythonModule** and select **Edit module twin**. 

3. Find **TemperatureThreshold** in the desired properties. Change its value to a new temperature 5 degrees to 10 degrees higher than the latest reported temperature. 

4. Save the module twin file.

5. Right-click anywhere in the module twin editing pane and select **Update module twin**. 

6. Monitor the incoming device-to-cloud messages. You should see the messages stop until the new temperature threshold is reached. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. When you're ready to build your own modules, you can learn more about [developing your own IoT Edge modules](module-development.md) or how to [develop modules with Visual Studio Code](how-to-vs-code-develop-module.md). You can continue on to the next tutorials to learn how Azure IoT Edge can help you deploy Azure cloud services to process and analyze data at the edge.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)
