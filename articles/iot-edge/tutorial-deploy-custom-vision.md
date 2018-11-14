---
title: Nasazení služby Custom Vision do zařízení Azure IoT Edge | Microsoft Docs
description: Zjistěte, jak zajistit spouštění modelu počítačového zpracování obrazu jako kontejneru s využitím služeb Custom Vision a IoT Edge.
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: a3bd7cf5e7c29ee44d696c0a27566b1a73436794
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50749748"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>Kurz: Provádění klasifikace obrázků na hraničních zařízeních s využitím služby Custom Vision

Azure IoT Edge může zvýšit efektivitu vašeho řešení IoT tím, že přesune úlohy z cloudu na hraniční zařízení. Tato funkce je vhodná pro služby, které zpracovávají velké množství dat, jako jsou modely počítačového zpracování obrazu. [Služba Custom Vision](../cognitive-services/custom-vision-service/home.md) umožňuje vytvářet vlastní klasifikátory obrázků a nasazovat je do zařízení jako kontejnery. Kombinace těchto dvou služeb umožňuje získávat informace z obrázků a video streamů bez nutnosti nejprve přenášet všechna tato data do jiného umístění. Služba Custom Vision poskytuje klasifikátor, který generuje přehledy porovnáváním obrázků s natrénovaným modelem. 

Služba Custom Vision na zařízení IoT Edge by například mohla určovat, jestli je na dálnici větší nebo menší provoz než normálně nebo jestli jsou v garáži v některé řadě volná parkovací místa. Tyto přehledy je možné sdílet s jinou službou, která na ně může reagovat. 


V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Vytvoření klasifikátoru obrázků s využitím služby Custom Vision
> * Vytvoření modulu IoT Edge, který dotazuje webový server služby Custom Vision na vašem zařízení
> * Odeslání výsledků klasifikátoru obrázků do služby IoT Hub

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v [rychlém startu pro Linux](quickstart-linux.md).
* Modul služby Custom Vision je v současné době dostupný pouze jako kontejner Linuxu pro architektury x64. 

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure. 
* Registr kontejneru. V tomto kurzu se používá služba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/). 
    * Znalost přihlašovacích údajů [účtu správce](../container-registry/container-registry-authentication.md#admin-account) registru kontejneru.

Prostředky pro vývoj:

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [Visual Studio Code](https://code.visualstudio.com/)
* Rozšíření [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code
* Rozšíření [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code
* [Docker CE](https://docs.docker.com/install/).

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
   | Název | Zadejte název projektu, například **EdgeTreeClassifier**. |
   | Popis | Volitelný popis projektu. |
   | Skupina prostředků | Přijměte výchozí hodnotu **Omezená zkušební verze**. |
   | Typy projektů | **Klasifikace** |
   | Typy klasifikace | **Více tříd (jedna značka na obrázek)** | 
   | Domény | **Obecné (kompaktní)** |

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

5. Vyberte **Nahrát soubory (10)**. 

   ![Nahrání souborů se značkou hemlock](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. Po úspěšném nahrání obrázků vyberte **Hotovo**.

7. Znovu vyberte **Přidat obrázky**.

8. Přejděte do druhé složky s obrázky **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**. Vyberte všech 10 obrázků ve složce a pak vyberte **Otevřít**. 

9. Přidejte k této skupině obrázků značku **japanese cherry** (Sakura) a stiskněte **Enter**, aby se značka použila. 

10. Vyberte **Nahrát soubory (10)**. Po úspěšném nahrání obrázků vyberte **Hotovo**. 

11. Jakmile budou obě sady obrázků označené a nahrané, vyberte **Trénovat** a natrénujte klasifikátor. 

### <a name="export-your-classifier"></a>Export klasifikátoru

1. Po natrénování klasifikátoru na stránce Výkon klasifikátoru vyberte **Exportovat**. 

   ![Export klasifikátoru obrázků](./media/tutorial-deploy-custom-vision/export.png)

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

1. Ve Visual Studio Code vyberte **Zobrazit** > **Terminál**, aby se otevřel integrovaný terminál VS Code.

2. V integrovaném terminálu zadejte následující příkaz k instalaci (nebo aktualizaci) balíčku **cookiecutter**, který použijete k vytvoření šablony modulu IoT Edge Python ve VS Code:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Ujistěte se, že se adresář, do kterého se balíček cookiecutter nainstaluje, nachází v cestě `Path` vašeho prostředí, aby bylo možné ho vyvolat z příkazového řádku.

3. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

4. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Na paletě příkazů zadejte následující informace k vytvoření řešení: 

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení, například **CustomVisionSolution**, nebo přijměte výchozí hodnotu. |
   | Vyberte šablonu modulu | Zvolte **Modul Python**. |
   | Zadejte název modulu | Pojmenujte modul **classifier**.<br><br>Je důležité, aby tento název modulu obsahoval pouze malá písmena. IoT Edge při odkazování na moduly rozlišuje malá a velká písmena a toto řešení využívá knihovnu, která všechny požadavky formátuje tak, aby obsahovaly pouze malá písmena. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. Konečný řetězec vypadá takto: \<název_registru\>.azurecr.io/classifier. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-deploy-custom-vision/repository.png)

V okně Visual Studio Code se načte pracovní prostor řešení IoT Edge.

### <a name="add-your-image-classifier"></a>Přidání klasifikátoru obrázků

Šablona modulu Python ve Visual Studio Code obsahuje vzorový kód, jehož spuštěním můžete otestovat IoT Edge. V tomto scénáři tento kód nevyužijete. Místo toho pomocí postupu v této části nahraďte vzorový kód kontejnerem klasifikátoru obrázků, který jste exportovali dříve. 

1. V Průzkumníku souborů přejděte k balíčku služby Custom Vision, který jste stáhli a extrahovali. Zkopírujte veškerý obsah extrahovaného balíčku. Mělo by se jednat o dvě složky (**app** a **azureml**) a dva soubory (**Dockerfile** a **README**). 

2. V Průzkumníku řešení přejděte do adresáře, který jste ve Visual Studio Code nastavili pro vytvoření řešení IoT Edge. 

3. Otevřete složku modulu classifier. Pokud jste v předchozí části použili navrhované názvy, vypadá struktura složek takto: **CustomVisionSolution / modules / classifier**. 

4. Vložte soubory do složky **classifier**. 

5. Vraťte se do okna Visual Studio Code. Ve vašem pracovním prostoru řešení by se teď ve složce modulu měly zobrazit soubory klasifikátoru obrázků. 

   ![Pracovní prostor řešení se soubory klasifikátoru obrázků](./media/tutorial-deploy-custom-vision/workspace.png)

6. Otevřete soubor **module.json** ve složce classifier. 

7. Aktualizujte parametr **platforms** tak, aby odkazoval na nový soubor Dockerfile, který jste přidali, a odeberte možnosti architektury ARM32 a AMD64.debug, které modul služby Custom Vision nepodporuje. 

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
   | Zadejte pro modul úložiště imagí Dockeru | Nahraďte **localhost:5000** hodnotou přihlašovacího serveru pro váš registr kontejneru Azure. Konečný řetězec vypadá takto: **\<název_registru\>.azurecr.io/cameracapture**. |

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

    import iothub_client
    # pylint: disable=E0611
    from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError
    # pylint: disable=E0401

    # messageTimeout - the maximum time in milliseconds until a message times out.
    # The timeout period starts at IoTHubModuleClient.send_event_async.
    MESSAGE_TIMEOUT = 10000

    # Choose HTTP, AMQP or MQTT as transport protocol.  
    PROTOCOL = IoTHubTransportProvider.MQTT

    # global counters
    SEND_CALLBACKS = 0

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = IoTHubMessage(bytearray(strMessage, 'utf8'))
        hubManager.send_event_to_output("output1", message, 0)

    # Callback received when the message that we send to IoT Hub is processed.
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        SEND_CALLBACKS += 1
        print ( "Confirmation received for message with result = %s" % result )
        print ( "   Total calls confirmed: %d \n" % SEND_CALLBACKS )

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

    class HubManager(object):
        def __init__(self, protocol, message_timeout):
            self.client_protocol = protocol
            self.client = IoTHubModuleClient()
            self.client.create_from_environment(protocol)
            # set the time until a message times out
            self.client.set_option("messageTimeout", message_timeout)
            
        # Sends a message to an output queue, to be routed by IoT Edge hub. 
        def send_event_to_output(self, outputQueueName, event, send_context):
            self.client.send_event_async(
                outputQueueName, event, send_confirmation_callback, send_context)

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global hubManager 
                hubManager = HubManager(PROTOCOL, MESSAGE_TIMEOUT)
            except IoTHubError as iothub_error:
                print ( "Unexpected error %s from IoTHub" % iothub_error )
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

3. Ve Visual Studio Code otevřete soubor **Dockerfile.amd64** s modulem cameraCapture. (ARM32 se v současné době v modulu služby Custom Vision nepodporuje). 

4. Za řádek, který určuje pracovní adresář (`WORKDIR /app`) přidejte následující řádek kódu: 

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

5. Uložte soubor Dockerfile. 

### <a name="prepare-a-deployment-manifest"></a>Příprava manifestu nasazení

Zatím jste v tomto kurzu natrénovali model služby Custom Vision pro klasifikaci obrázků stromů a tento model jste zabalili do modulu IoT Edge. Potom jste vytvořili druhý modul, který může odesílat dotazy na server klasifikace obrázků a hlásit jeho výsledky zpět do služby IoT Hub. Teď jste připraveni vytvořit manifest nasazení, který informuje zařízení IoT Edge, jak tyto dva moduly společně spustit a provozovat. 

Rozšíření IoT Edge pro Visual Studio Code poskytuje v každém řešení IoT Edge šablonu, která vám pomůže vytvořit manifest nasazení. 

1. Otevřete soubor **deployment.template.json** ve složce řešení. 

2. Vyhledejte část **modules**, která by měla obsahovat tři moduly: dva moduly, které jste vytvořili (classifier a cameraCapture), a třetí modul tempSensor, který je součástí výchozího nastavení. 

3. Odstraňte modul **tempSensor** i se všemi parametry. Tento modul je zahrnutý za účelem poskytování ukázkových dat pro testovací scénáře, ale v tomto nasazení ho nepotřebujeme. 

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

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V požadavcích pro tento kurz byl uvedený registr kontejneru, který je potřeba k ukládání imagí kontejneru pro moduly, které jste vytvořili. Přihlašovací údaje pro přístup k registru je potřeba zadat na dvou místech: ve Visual Studio Code, abyste mohli sestavit a odeslat image do registru, a v manifestu nasazení, aby zařízení IoT Edge mohlo tyto image stáhnout a nasadit. 

Pokud používáte službu Azure Container Registry, ujistěte se, že znáte uživatelské jméno, přihlašovací server a heslo [účtu správce](../container-registry/container-registry-authentication.md#admin-account). 

1. Ve Visual Studio Code otevřete integrovaný terminál tím, že vyberete **Zobrazit** > **Terminál**. 

2. V integrovaném terminálu zadejte následující příkaz: 

    ```csh/sh
    docker login -u <registry username> <registry login server>
    ```

3. Po zobrazení výzvy zadejte heslo registru a stiskněte **Enter**.

4. Otevřete soubor **.env** ve složce projektu. V tomto souboru, který Git ignoruje, jsou uložené přihlašovací údaje vašeho registru, abyste je nemuseli pevně kódovat do souboru šablony nasazení. 

5. Zadejte uživatelské jméno a heslo pro váš registr kontejneru (hodnoty zadejte bez uvozovek). 

6. Uložte soubor **.env**.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Vytvoření a nasazení řešení IoT Edge

Když máte vytvořené oba moduly a nakonfigurovanou šablonu manifestu nasazení, jste připraveni sestavit image kontejneru a odeslat je do registru kontejneru. 

Jakmile budou image ve vašem registru, můžete řešení nasadit do zařízení IoT Edge. Moduly na zařízení můžete nastavit prostřednictvím služby IoT Hub, ale přistupovat ke službě IoT Hub a zařízením můžete také přes Visual Studio Code. V této části nastavíte přístup ke službě IoT Hub a pak pomocí VS Code nasadíte své řešení do zařízení IoT Edge.

Nejprve sestavte řešení a odešlete ho do registru kontejneru. 

1. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Vytvořit a odeslat řešení IoT Edge**. Průběh této operace můžete sledovat v integrovaném terminálu VS Code. 
2. Všimněte si, že se do vašeho řešení přidala nová složka **config**. Rozbalte tuto složku a otevřete soubor **deployment.json**, který obsahuje.
3. Zkontrolujte informace v souboru deployment.json. Soubor deployment.json se vytvoří (nebo aktualizuje) automaticky na základě souboru šablony nasazení, který jste nakonfigurovali, a informací z řešení, včetně souboru .env a souborů module.json. 

Dále nastavte přístup ke službě IoT Hub z Visual Studio Code. 

1. Na paletě příkazů VS Code vyberte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).
2. Podle pokynů se přihlaste ke svému účtu Azure. 
3. Na paletě příkazů vyberte své předplatné Azure a pak vaši službu IoT Hub. 

Nakonec vyberte zařízení a nasaďte řešení.

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

Ve Visual Studio Code klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a vyberte **Začít monitorovat zprávy D2C**. 

Výsledky z modulu služby Custom Vision, které se odesílají jako zprávy z modulu cameraCapture, zahrnují pravděpodobnost, s jakou se jedná o obrázek jedlovce nebo sakury. Vzhledem k tomu, že se jedná o obrázek jedlovce, měla by se zobrazit pravděpodobnost 1.0. 


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste natrénovali model služby Custom Vision a nasadili jste ho jako modul do zařízení IoT Edge. Potom jste vytvořili modul, který může odesílat dotazy do služby klasifikace obrázků a hlásit její výsledky zpět do služby IoT Hub. 

Pokud chcete vyzkoušet podrobnější verzi tohoto scénáře s živým kanálem z kamery, podívejte si projekt [Custom Vision a Azure IoT Edge na Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) na GitHubu. 

Pokračujte dalšími kurzy, ve kterých se seznámíte s dalšími způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace na hraničním zařízení.

> [!div class="nextstepaction"]
> [Zjištění průměrů s využitím plovoucího okna v Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
