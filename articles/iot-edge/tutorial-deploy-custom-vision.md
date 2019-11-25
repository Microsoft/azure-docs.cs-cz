---
title: Tutorial deploy Custom Vision classifier to a device - Azure IoT Edge | Microsoft Docs
description: In this tutorial, learn how to make a computer vision model run as a container using Custom Vision and IoT Edge.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3418c57493e19580f0d3dbd9ea979b0322d930b8
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457286"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Kurz: Provádění klasifikace obrázků na hraničních zařízeních s využitím služby Custom Vision

Azure IoT Edge může zvýšit efektivitu vašeho řešení IoT tím, že přesune úlohy z cloudu na hraniční zařízení. Tato funkce je vhodná pro služby, které zpracovávají velké množství dat, jako jsou modely počítačového zpracování obrazu. [Služba Custom Vision](../cognitive-services/custom-vision-service/home.md) umožňuje vytvářet vlastní klasifikátory obrázků a nasazovat je do zařízení jako kontejnery. Kombinace těchto dvou služeb umožňuje získávat informace z obrázků a video streamů bez nutnosti nejprve přenášet všechna tato data do jiného umístění. Služba Custom Vision poskytuje klasifikátor, který generuje přehledy porovnáváním obrázků s natrénovaným modelem.

Služba Custom Vision na zařízení IoT Edge by například mohla určovat, jestli je na dálnici větší nebo menší provoz než normálně nebo jestli jsou v garáži v některé řadě volná parkovací místa. Tyto přehledy je možné sdílet s jinou službou, která na ně může reagovat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření klasifikátoru obrázků s využitím služby Custom Vision
> * Vytvoření modulu IoT Edge, který dotazuje webový server služby Custom Vision na vašem zařízení
> * Odeslání výsledků klasifikátoru obrázků do služby IoT Hub

<center>

![Diagram - Tutorial architecture, stage and deploy classifier](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

>[!TIP]
>This tutorial is a simplified version of the [Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) sample project. This tutorial was designed to run on a cloud VM and uses static images to train and test the image classifier, which is useful for someone just starting to evaluate Custom Vision on IoT Edge. The sample project uses physical hardware and sets up a live camera feed to train and test the image classifier, which is useful for someone who wants to try a more detailed, real-life scenario.

Before beginning this tutorial, you should have gone through the previous tutorial to set up your environment for Linux container development: [Develop IoT Edge modules for Linux devices](tutorial-develop-for-linux.md). By completing that tutorial, you should have the following prerequisites in place: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linux device running Azure IoT Edge](quickstart-linux.md)
* A container registry, like [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configured with the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configured to run Linux containers.

To develop an IoT Edge module with the Custom Vision service, install the following additional prerequisites on your development machine: 

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Python extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 

## <a name="build-an-image-classifier-with-custom-vision"></a>Vytvoření klasifikátoru obrázků s využitím služby Custom Vision

Pokud chcete vytvořit klasifikátor obrázků, je potřeba vytvořit projekt služby Custom Vision a poskytnout trénovací obrázky. Další informace o krocích, které provedete v této části, najdete v tématu [Postup vytvoření klasifikátoru s využitím služby Custom Vision](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md).

Jakmile bude klasifikátor obrázků vytvořený a natrénovaný, můžete ho exportovat jako kontejner Dockeru a nasadit do zařízení IoT Edge. 

### <a name="create-a-new-project"></a>Vytvoření nového projektu

1. Ve webovém prohlížeči přejděte na [webovou stránku služby Custom Vision](https://customvision.ai/).

2. Vyberte **Přihlásit se** a přihlaste se pomocí stejného účtu, který používáte pro přístup k prostředkům Azure. 

3. Vyberte **Nový projekt**.

4. Vytvořte projekt s použitím následujících hodnot:

   | Pole | Hodnota |
   | ----- | ----- |
   | Name (Název) | Zadejte název projektu, například **EdgeTreeClassifier**. |
   | Popis | Volitelný popis projektu. |
   | Prostředek | Select one of your Azure resource groups that includes a Custom Vision Service resource or **create new** if you haven't yet added one. |
   | Typy projektů | **Klasifikace** |
   | Typy klasifikace | **Více tříd (jedna značka na obrázek)** |
   | Domény | **Obecné (kompaktní)** |
   | Export Capabilities | **Basic platforms (Tensorflow, CoreML, ONNX, ...)** |

5. Vyberte **Vytvořit projekt**.

### <a name="upload-images-and-train-your-classifier"></a>Nahrání obrázků a trénování klasifikátoru

Při vytváření klasifikátoru obrázků je potřeba sada trénovacích obrázků a také testovací obrázky. 

1. Na svůj místní vývojový počítač si naklonujte nebo stáhněte ukázkové obrázky z úložiště [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows). 

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. Vraťte se ke svému projektu služby Custom Vision a vyberte **Přidat obrázky**. 

3. Přejděte do místního úložiště Git, které jste naklonovali, a pak do první složky s obrázky **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**. Vyberte všech 10 obrázků ve složce a pak vyberte **Otevřít**. 

4. Přidejte k této skupině obrázků značku **hemlock** (Jedlovec) a stiskněte **Enter**, aby se značka použila. 

5. Vyberte **Nahrát soubory (10)** . 

   ![Upload hemlock tagged files to Custom Vision](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Po úspěšném nahrání obrázků vyberte **Hotovo**.

7. Znovu vyberte **Přidat obrázky**.

8. Přejděte do druhé složky s obrázky **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**. Vyberte všech 10 obrázků ve složce a pak vyberte **Otevřít**. 

9. Přidejte k této skupině obrázků značku **japanese cherry** (Sakura) a stiskněte **Enter**, aby se značka použila. 

10. Vyberte **Nahrát soubory (10)** . Po úspěšném nahrání obrázků vyberte **Hotovo**. 

11. Jakmile budou obě sady obrázků označené a nahrané, vyberte **Trénovat** a natrénujte klasifikátor. 

### <a name="export-your-classifier"></a>Export klasifikátoru

1. Po natrénování klasifikátoru na stránce Výkon klasifikátoru vyberte **Exportovat**. 

   ![Export your trained image classifier](./media/tutorial-deploy-custom-vision/export.png)

2. Jako platformu vyberte **DockerFile**. 

3. Jako verzi vyberte **Linux**.  

4. Vyberte **Exportovat**. 

   ![Export jako soubor DockerFile s kontejnery Linuxu](./media/tutorial-deploy-custom-vision/export-2.png)

5. Po dokončení exportu vyberte **Stáhnout** a uložte balíček .zip místně do svého počítače. Extrahujte z balíčku všechny soubory. Tyto soubory použijete k vytvoření modulu IoT Edge, který bude obsahovat server klasifikace obrázků. 

Až se k tomuto bodu dostanete, budete mít vytvořený a natrénovaný projekt služby Custom Vision. Exportované soubory použijete v další části, ale s webovou stránkou služby Custom Vision jste už skončili. 

## <a name="create-an-iot-edge-solution"></a>Vytvoření zařízení IoT Edge

Teď máte soubory pro kontejnerovou verzi klasifikátoru obrázků na svém místním vývojovém počítači. V této části nakonfigurujete kontejner klasifikátoru obrázků tak, aby se spouštěl jako modul IoT Edge. Také vytvoříte druhý modul, který se nasadí společně s klasifikátorem obrázků. Druhý modul odesílá požadavky do klasifikátoru a výsledky odesílá jako zprávy do služby IoT Hub. 

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Řešení představuje logický způsob vývoje a uspořádání více modulů pro jedno nasazení IoT Edge. Řešení obsahuje kód jednoho nebo několika modulů a manifest nasazení, který určuje, jak se mají nakonfigurovat na zařízení IoT Edge. 

1. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

1. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení, například **CustomVisionSolution**, nebo přijměte výchozí hodnotu. |
   | Vyberte šablonu modulu | Zvolte **Modul Python**. |
   | Zadejte název modulu | Pojmenujte modul **classifier**.<br><br>Je důležité, aby tento název modulu obsahoval pouze malá písmena. IoT Edge při odkazování na moduly rozlišuje malá a velká písmena a toto řešení využívá knihovnu, která všechny požadavky formátuje tak, aby obsahovaly pouze malá písmena. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal.<br><br>The final string looks like **\<registry name\>.azurecr.io/classifier**. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-custom-vision/repository.png)

V okně Visual Studio Code se načte pracovní prostor řešení IoT Edge.

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro registr kontejneru, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku VS Code otevřete soubor .env.
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Select your target architecture

Currently, Visual Studio Code can develop modules for Linux AMD64 and Linux ARM32v7 devices. You need to select which architecture you're targeting with each solution, because the container is built and run differently for each architecture type. The default is Linux AMD64, which is what we'll use for this tutorial. 

1. Open the command palette and search for **Azure IoT Edge: Set Default Target Platform for Edge Solution**, or select the shortcut icon in the side bar at the bottom of the window. 

2. In the command palette, select the target architecture from the list of options. For this tutorial, we're using an Ubuntu virtual machine as the IoT Edge device, so will keep the default **amd64**. 

### <a name="add-your-image-classifier"></a>Přidání klasifikátoru obrázků

Šablona modulu Python ve Visual Studio Code obsahuje vzorový kód, jehož spuštěním můžete otestovat IoT Edge. V tomto scénáři tento kód nevyužijete. Místo toho pomocí postupu v této části nahraďte vzorový kód kontejnerem klasifikátoru obrázků, který jste exportovali dříve. 

1. V Průzkumníku souborů přejděte k balíčku služby Custom Vision, který jste stáhli a extrahovali. Zkopírujte veškerý obsah extrahovaného balíčku. Mělo by se jednat o dvě složky (**app** a **azureml**) a dva soubory (**Dockerfile** a **README**). 

2. V Průzkumníku řešení přejděte do adresáře, který jste ve Visual Studio Code nastavili pro vytvoření řešení IoT Edge. 

3. Otevřete složku modulu classifier. Pokud jste v předchozí části použili navrhované názvy, vypadá struktura složek takto: **CustomVisionSolution / modules / classifier**. 

4. Vložte soubory do složky **classifier**. 

5. Vraťte se do okna Visual Studio Code. Ve vašem pracovním prostoru řešení by se teď ve složce modulu měly zobrazit soubory klasifikátoru obrázků. 

   ![Pracovní prostor řešení se soubory klasifikátoru obrázků](./media/tutorial-deploy-custom-vision/workspace.png)

6. Otevřete soubor **module.json** ve složce classifier. 

7. Update the **platforms** parameter to point to the new Dockerfile that you added, and remove all the options besides AMD64, which is the only architecture we're using for this tutorial. 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. Uložte provedené změny. 

### <a name="create-a-simulated-camera-module"></a>Vytvoření modulu simulované kamery

Ve skutečném nasazení služby Custom Vision byste měli kameru poskytující živé obrázky nebo streamy videa. V tomto scénáři budete simulovat kameru tím, že vytvoříte modul, který do klasifikátoru obrázků odešle testovací obrázek. 

#### <a name="add-and-configure-a-new-module"></a>Přidání a konfigurace nového modulu

V této části do stejného řešení CustomVisionSolution přidáte nový modul a zadáte kód pro vytvoření simulované kamery. 

1. Ve stejném okně Visual Studio Code pomocí palety příkazů spusťte **Azure IoT Edge:Přidání modulu IoT Edge**. Na paletě příkazů zadejte následující informace o novém modulu: 

   | Výzva | Hodnota | 
   | ------ | ----- |
   | Vyberte soubor šablony nasazení | Vyberte soubor deployment.template.json ve složce CustomVisionSolution. |
   | Vyberte šablonu modulu | Vyberte **Modul Python**. |
   | Zadejte název modulu | Pojmenujte modul **cameraCapture**. |
   | Zadejte pro modul úložiště imagí Dockeru | Nahraďte **localhost:5000** hodnotou přihlašovacího serveru pro váš registr kontejneru Azure.<br><br>Konečný řetězec vypadá takto: **\<název_registru\>.azurecr.io/cameracapture**. |

   V okně VS Code se nový modul načte do pracovního prostoru řešení a aktualizuje se soubor deployment.template.json. Teď by se měly zobrazit dvě složky modulu: classifier a cameraCapture. 

2. Otevřete soubor **main.py** ve složce **modules** / **cameraCapture**. 

3. Celý soubor nahraďte následujícím kódem. Tento vzorový kód odesílá požadavky POST do služby pro zpracování obrázků spuštěné v modulu classifier. Pro tento kontejner modulu zadáme ukázkový obrázek, který se použije v požadavcích. Kód pak zabalí odpověď do zprávy pro službu IoT Hub a odešle ji do výstupní fronty.  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("Response from classification service: (" + str(response.status_code))

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. Uložte soubor **main.py**. 

5. Otevřete soubor **requrements.txt**. 

6. Přidejte nový řádek pro knihovnu, která se zahrne do kontejneru.

   ```Text
   requests
   ```

7. Uložte soubor **requirements.txt**.


#### <a name="add-a-test-image-to-the-container"></a>Přidání testovacího obrázku do kontejneru

Místo toho, abychom k poskytování kanálu obrázků pro tento scénář použili skutečnou kameru, použijeme jeden testovací obrázek. Testovací obrázek je součástí úložiště GitHub s trénovacími obrázky, které jste stáhli dříve v tomto kurzu. 

1. Přejděte k testovacímu obrázku, který se nachází v umístění **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test**. 

2. Zkopírujte soubor **test_image.jpg**. 

3. Přejděte do adresáře řešení IoT Edge a vložte testovací obrázek do složky **modules** / **cameraCapture**. Obrázek musí být ve stejné složce jako soubor main.py, který jste upravovali v předchozí části. 

3. Ve Visual Studio Code otevřete soubor **Dockerfile.amd64** s modulem cameraCapture. 

4. Za řádek, který určuje pracovní adresář (`WORKDIR /app`) přidejte následující řádek kódu: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Uložte soubor Dockerfile. 

### <a name="prepare-a-deployment-manifest"></a>Příprava manifestu nasazení

Zatím jste v tomto kurzu natrénovali model služby Custom Vision pro klasifikaci obrázků stromů a tento model jste zabalili do modulu IoT Edge. Potom jste vytvořili druhý modul, který může odesílat dotazy na server klasifikace obrázků a hlásit jeho výsledky zpět do služby IoT Hub. Teď jste připraveni vytvořit manifest nasazení, který informuje zařízení IoT Edge, jak tyto dva moduly společně spustit a provozovat. 

Rozšíření IoT Edge pro Visual Studio Code poskytuje v každém řešení IoT Edge šablonu, která vám pomůže vytvořit manifest nasazení. 

1. Otevřete soubor **deployment.template.json** ve složce řešení. 

2. Find the **modules** section, which should contain three modules: the two that you created, classifier and cameraCapture, and a third that's included by default, SimulatedTemperatureSensor. 

3. Delete the **SimulatedTemperatureSensor** module with all of its parameters. Tento modul je zahrnutý za účelem poskytování ukázkových dat pro testovací scénáře, ale v tomto nasazení ho nepotřebujeme. 

4. Pokud jste modul klasifikace obrázků pojmenovali jinak než **classifier**, zkontrolujte teď název a ujistěte se, že obsahuje pouze malá písmena. Modul cameraCapture k volání modulu classifier používá knihovnu requests, která všechny požadavky formátuje tak, aby obsahovaly pouze malá písmena, a IoT Edge rozlišuje malá a velká písmena. 

5. Aktualizujte parametr **createOptions** pro modul cameraCapture s použitím následujícího kódu JSON. S použitím těchto informací se v kontejneru modulu vytvoří proměnné prostředí, které se načítají v procesu main.py. Díky zahrnutí těchto informací do manifestu nasazení může změnit obrázek nebo koncový bod, aniž byste museli znovu sestavovat image modulu. 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    Pokud jste modul služby Custom Vision pojmenovali jinak než *classifier*, aktualizujte odpovídajícím způsobem hodnotu koncového bodu pro zpracování obrázků. 

5. V dolní části souboru aktualizujte parametr **routes** pro modul $edgeHub. Výsledky předpovědí z modulu cameraCapture chcete směrovat do služby IoT Hub. 

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    Pokud jste druhý modul pojmenovali jinak než *cameraCapture*, aktualizujte odpovídajícím způsobem hodnotu trasy. 

7. Uložte soubor **deployment.template.json**.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Vytvoření a nasazení řešení IoT Edge

Když máte vytvořené oba moduly a nakonfigurovanou šablonu manifestu nasazení, jste připraveni sestavit image kontejneru a odeslat je do registru kontejneru. 

Jakmile budou image ve vašem registru, můžete řešení nasadit do zařízení IoT Edge. Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge.

Nejprve sestavte řešení a odešlete ho do registru kontejneru. 

1. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Vytvořit a odeslat řešení IoT Edge**. Průběh této operace můžete sledovat v integrovaném terminálu VS Code. 
2. Notice that a new folder was added to your solution, **config**. Expand this folder and open the **deployment.json** file inside.
3. Zkontrolujte informace v souboru deployment.json. Soubor deployment.json se vytvoří (nebo aktualizuje) automaticky na základě souboru šablony nasazení, který jste nakonfigurovali, a informací z řešení, včetně souboru .env a souborů module.json. 

Next, select your device and deploy your solution.

1. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 
2. Klikněte pravým tlačítkem na zařízení, na které chcete cílit nasazení, a vyberte **Create deployment for single device** (Vytvořit nasazení pro jedno zařízení). 
3. V Průzkumníku souborů přejděte do složky **config** uvnitř vašeho řešení a zvolte soubor **deployment.json**. Klikněte na **Select Edge deployment manifest** (Vybrat manifest nasazení Edge). 

Pokud nasazení proběhne úspěšně, ve výstupu VS Code se zobrazí potvrzovací zpráva. V průzkumníku VS Code rozbalte podrobnosti o zařízení IoT Edge, které jste použili pro toto nasazení. Pokud se moduly hned nezobrazí, najeďte kurzorem na nadpis **Zařízení Azure IoT Hub**, aby se aktivovalo tlačítko pro aktualizaci. Spuštění modulů a jejich ohlášení zpět do služby IoT Hub může chvíli trvat. 

Můžete také zkontrolovat, jestli jsou na vašem zařízení zprovozněné všechny moduly. Spuštěním následujícího příkazu na vašem zařízení IoT Edge zobrazte stav modulů. Spuštění modulů může chvíli trvat.

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>Zobrazení výsledků klasifikace

Výsledky modulů můžete zobrazit dvěma způsoby – buď na samotném zařízení během generování a odesílání zpráv, nebo ve Visual Studio Code při příjmu zpráv ve službě IoT Hub. 

Na svém zařízení si prohlédněte protokoly modulu cameraCapture, kde uvidíte odesílané zprávy a potvrzení jejich příjmu službou IoT Hub. 

   ```bash
   iotedge logs cameraCapture
   ```

From Visual Studio Code, right-click on the name of your IoT Edge device and select **Start Monitoring Built-in Event Endpoint**. 

Výsledky z modulu služby Custom Vision, které se odesílají jako zprávy z modulu cameraCapture, zahrnují pravděpodobnost, s jakou se jedná o obrázek jedlovce nebo sakury. Vzhledem k tomu, že se jedná o obrázek jedlovce, měla by se zobrazit pravděpodobnost 1.0. 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Otherwise, you can delete the local configurations and the Azure resources that you used in this article to avoid charges. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste natrénovali model služby Custom Vision a nasadili jste ho jako modul do zařízení IoT Edge. Potom jste vytvořili modul, který může odesílat dotazy do služby klasifikace obrázků a hlásit její výsledky zpět do služby IoT Hub. 

Pokud chcete vyzkoušet podrobnější verzi tohoto scénáře s živým kanálem z kamery, podívejte si projekt [Custom Vision a Azure IoT Edge na Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) na GitHubu. 

Pokračujte dalšími kurzy, ve kterých se seznámíte s dalšími způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Uložení dat na hraničních zařízeních s využitím databází SQL Serveru](tutorial-store-data-sql-server.md)
