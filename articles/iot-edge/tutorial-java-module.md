---
title: Custom Java module tutorial - Azure IoT Edge | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Java a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8f5b34d3195ec6807f6eea6388e24d283f403c0c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457503"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Tutorial: Develop a Java IoT Edge module for Linux devices

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. You'll use the simulated IoT Edge device that you created in the Deploy Azure IoT Edge on a simulated device in [Linux](quickstart-linux.md) quickstart. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Použít Visual Studio Code k vytvoření modulu IoT Edge Java na základě balíčku šablon Azure IoT Edge maven a sady SDK pro zařízení Azure IoT Java
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Solution scope

This tutorial demonstrates how to develop a module in **Java** using **Visual Studio Code**, and how to deploy it to a **Linux device**. IoT Edge does not support Java modules for Windows devices. 

Use the following table to understand your options for developing and deploying Java modules: 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Use VS Code for Java modules on Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Use VS Code for Java modules on Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Předpoklady

Before beginning this tutorial, you should have gone through the previous tutorial to set up your development environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing either of those tutorials, you should have the following prerequisites in place: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module in Java, install the following additional prerequisites on your development machine: 

* [Balíček rozšíření Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) pro Visual Studio Code
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) s [nastavením proměnné prostředí `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na vaši instalaci JDK
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Create a module project
The following steps create an IoT Edge module project that's based on the Azure IoT Edge maven template package and Azure IoT Java device SDK. You create the project by using Visual Studio Code and the Azure IoT Tools.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Vytvořte šablonu řešení v jazyce Java, kterou můžete přizpůsobit pomocí vlastního kódu. 

1. V nástroji Visual Studio Code zvolte **View** (Zobrazit)  > **Command Palette** (Paleta příkazů). Otevře se paleta příkazů VS Code. 

2. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Enter a descriptive name for your solution or accept the default **EdgeSolution**. |
   | Vyberte šablonu modulu | Choose **Java Module**. |
   | Provide value for groupId | Enter a group ID value or accept the default **com.edgemodule**. |
   | Zadejte název modulu | Name your module **JavaModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Your container image is prepopulated from the name you provided in the last step. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>The final image repository looks like \<registry name\>.azurecr.io/javamodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-java-module/repository.png)
   
Pokud modul Java vytváříte poprvé, může stažení balíčků maven trvat několik minut. Pak se v okně nástroje VS Code načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně. The **modules** folder contains the Java code for your module as well as Docker files for building your module as a container image. V souboru **\.env** jsou uložené přihlašovací údaje k vašemu registru kontejneru. Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení. And **deployment.debug.template.json** file containers the debug version of modules. Složku **\.vscode** ani soubor **\.gitignore** v tomto kurzu upravovat nebudete. 

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env. 

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor .env. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop Java modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

1. V průzkumníku VS Code otevřete **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**.

2. Na začátek souboru přidejte následující kód k importování nových odkazovaných tříd.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Přidejte následující definici do třídy **App**. This variable sets a temperature threshold. The measured machine temperature won't be reported to IoT Hub until it goes over this value. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. Nahraďte metodu spuštění **MessageCallbackMqtt** následujícím kódem. Tato metoda se volá pokaždé, když modul dostane zprávu MQTT z IoT Edge Hubu. Odfiltruje zprávy, které hlásí nižší teploty, než je prahová hodnota nastavená ve dvojčeti modulu.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Přidejte následující dvě statické vnitřní třídy do třídy **App**. These classes update the tempThreshold variable when the module twin's desired property changes. Všechny moduly mají vlastní dvojče, abyste mohli kód, který je spuštěný v modulu, konfigurovat přímo z cloudu.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Přidejte následující řádky do metody **main** za **client.open()** k odběru aktualizací dvojčete modulu.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Save the App.java file.

8. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

9. Přidejte do manifestu nasazení dvojče modulu **JavaModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Add module twin to deployment template](./media/tutorial-java-module/module-twin.png)

10. Save the deployment.template.json file.

## <a name="build-and-push-your-module"></a>Build and push your module

In the previous section, you created an IoT Edge solution and added code to the **JavaModule** to filter out messages where the reported machine temperature is below the acceptable limit. Now, build the solution as a container image and push it to your container registry. 

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

4. Klikněte na tlačítko pro obnovení. You should see the new **JavaModule** running along with the **SimulatedTemperatureSensor** module and the **$edgeAgent** and **$edgeHub**.  

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. In the Visual Studio Code explorer, right-click the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**.

2. View the messages arriving at your IoT Hub. It may take a while for the messages to arrive, because the IoT Edge device has to receive its new deployment and start all the modules. Then, the changes we made to the JavaModule code wait until the machine temperature reaches 25 degrees before sending messages. It also adds the message type **Alert** to any messages that reach that temperature threshold. 

## <a name="edit-the-module-twin"></a>Edit the module twin

We used the JavaModule module twin in the deployment manifest to set the temperature threshold at 25 degrees. You can use the module twin to change the functionality without having to update the module code.

1. In Visual Studio Code, expand the details under your IoT Edge device to see the running modules. 

2. Right-click **JavaModule** and select **Edit module twin**. 

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
