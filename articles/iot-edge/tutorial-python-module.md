---
title: Modul Azure IoT Edge Python | Microsoft Docs
description: Vytvořit modul IoT Edge s kódem jazyka Python a nasadíte ho do hraniční zařízení
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Vývoj a nasazení modul Python IoT Edge na simulovaného zařízení – náhled

Moduly IoT Edge můžete nasadit kód, který implementuje obchodní logiku přímo do zařízení IoT okraj. Tento kurz vás provede vytváření a nasazování modul IoT okraj, který filtruje data snímačů. Budete používat simulované zařízení IoT okraj, který jste vytvořili v nasazení Azure IoT Edge v simulovaném zařízení v [Windows] [ lnk-tutorial1-win] nebo [Linux] [ lnk-tutorial1-lin]kurzy. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Visual Studio Code použít k vytvoření modul IoT Edge Python
> * Pomocí Visual Studio Code a Docker vytvoření bitové kopie docker a publikujete ho v registru 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT okraj, který vytvoříte v tomto kurzu filtruje data teploty generována zařízení. Pouze odešle zprávy proti proudu pokud teplota překročí zadanou prahovou hodnotu. Tento typ analýzy na hranici je užitečné při snižování množství dat oznamovat a uložit v cloudu. 

> [!IMPORTANT]
> Aktuálně modul Python lze spustit pouze v kontejnerech Linux amd64; nelze ho spustit v systému Windows kontejnery nebo založené na ARM kontejnerů. 

## <a name="prerequisites"></a>Požadavky

* Azure IoT hraniční zařízení, který jste vytvořili v prvním kurzu nebo rychlý start.
* Primární připojovací řetězec klíče pro zařízení IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Rozšíření Python pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) na stejném počítači, který má Visual Studio Code. Edice Community (CE) je dostačující pro účely tohoto kurzu. 
* [Python](https://www.python.org/downloads/).
* [PIP](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Python (obvykle součástí instalace Python).

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následující kroky ukazují, jak vytvořit modul IoT Edge Python pomocí kódu v jazyce Visual Studio a rozšíření Azure IoT okraj.
1. V sadě Visual Studio Code vyberte **zobrazení** > **integrované terminálu** otevřete integrované terminálu VS Code.
2. Integrované terminálu, zadejte následující příkaz pro instalaci (nebo aktualizujte) **cookiecutter** (doporučujeme, že to buď do virtuálního prostředí, nebo jako uživatel instalaci ukazuje následující obrázek):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Vytvořte projekt pro nový modul. Následující příkaz vytvoří složce projektu **FilterModule**, s kontejner úložiště. Parametr `image_repository` by měl být ve tvaru `<your container registry name>.azurecr.io/filtermodule` Pokud používáte Azure kontejneru registru. V aktuální pracovní složce zadejte následující příkaz:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Vyberte **soubor** > **otevřete složku**.
5. Vyhledejte **FilterModule** složky a klikněte na tlačítko **vyberte složku** otevřete projekt v produktu VS Code.
6. V Průzkumníku VS Code, klikněte na tlačítko **main.py** ho otevřete.
7. V horní části **FilterModule** obor názvů, import `json` knihovny:

    ```python
    import json
    ```

8. Přidat `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS`, a `TWIN_CALLBACKS` v části globální čítače. Prahová hodnota teploty nastaví hodnotu, která nesmí být větší než teplota měřená v pořadí pro data k odeslání do služby IoT Hub.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Aktualizovat funkce `receive_message_callback` s níže obsah.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. Přidat novou funkci `device_twin_callback`. Tuto funkci bude volat, když jsou aktualizovány požadované vlastnosti.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. Ve třídě `HubManager`, přidejte nový řádek, kterým `__init__` metoda pro inicializaci `device_twin_callback` funkce, které jste právě přidali.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Uložte tento soubor.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření image Dockeru a její publikování do registru

1. Přihlaste se k Dockeru zadáním následujícího příkazu v integrovaném terminálu VS Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Použijte uživatelské jméno, heslo a přihlášení serveru, který jste zkopírovali z registru kontejner Azure, když jste ji vytvořili.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **module.json** a klikněte na **Sestavit a odeslat image Dockeru s modulem IoT Edge**. Do pole automaticky otevírané okno rozevírací seznam v horní části okna VS Code vyberte platformu kontejneru, například **amd64** pro Linux kontejneru. VS Code containerize `main.py` a požadované závislosti, pak vložit do kontejneru registru, který jste zadali. Může trvat několik minut, než poprvé vytvořit bitovou kopii.

3. Úplnou adresu image kontejneru můžete získat pomocí značky v integrovaném terminálu VS Code. Další informace o definici sestavení a odeslání najdete v souboru `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Přidejte pověření registru do hraniční runtime
Přidejte přihlašovací údaje k vašemu registru do modulu runtime Edge na počítači, na kterém spouštíte službu Edge. Tyto přihlašovací údaje poskytnout přístup runtime vyžádání kontejneru. 

- V případě Windows spusťte následující příkaz:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- V případě Linuxu spusťte následující příkaz:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Spuštění řešení

1. Na webu [Azure Portal](https://portal.azure.com) přejděte do svého centra IoT.
2. Přejděte na **IoT Edge (preview)** a vyberte zařízení IoT Edge.
3. Vyberte **Nastavit moduly**. 
2. Zkontrolujte, zda **tempSensor** modul se automaticky vyplní. Pokud není, přidejte ho pomocí následující kroky:
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Další nastavení ponechte beze změny a klikněte na **Uložit**.
9. Přidat **filterModule** modul, který jste vytvořili v předchozí části. 
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `filterModule`.
    3. Do pole **Identifikátor URI image** zadejte adresu své image, například `<your container registry address>/filtermodule:0.0.1-amd64`. Úplnou adresu image najdete v předchozí části.
    4. Zkontrolujte **povolit** pole tak, aby bylo možné upravit dvojici modulu. 
    5. Nahraďte kód JSON do textového pole pro dvojici modul s následujícím kódu JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klikněte na **Uložit**.
10. Klikněte na **Další**.
11. V kroku **Určení tras** zkopírujte do textového pole následující JSON. Moduly publikovat všechny zprávy do hraniční runtime. Deklarativní pravidel v modulu runtime definovat, kde toku zpráv. V tomto kurzu musíte dvě trasy. První trasa je určena k přenosu zpráv z teploty senzoru modulu filtru prostřednictvím koncového bodu "input1", což je koncový bod, který jste nakonfigurovali s **FilterMessages** obslužné rutiny. Druhá trasa přenáší zprávy z modulu filtru do služby IoT Hub. V této trase je `upstream` speciální cíl, který centru Edge říká, že má odesílat zprávy do služby IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. Klikněte na **Další**.
5. V kroku **Kontrola šablony** klikněte na **Odeslat**. 
6. Vraťte se na stránku podrobností o zařízení IoT Edge a klikněte na **Aktualizovat**. Byste měli vidět, že se vám nové **filtermodule** společně s **tempSensor** modulu a **IoT Edge runtime**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Monitorování zpráv typu zařízení-cloud odesílaných ze zařízení IoT Edge do centra IoT:
1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro vaše centrum IoT: 
    1. Otevřete Průzkumníka VS Code výběrem **zobrazení** > **Explorer**. 
    3. V Průzkumníku, klikněte na tlačítko **zařízení IOT HUB** a pak klikněte na **...** . Klikněte na tlačítko **nastavit připojovací řetězec centra IoT** a zadejte připojovací řetězec pro službu IoT hub, která zařízení IoT Edge připojuje v místním okně. 

        Chcete-li najít připojovací řetězec, klikněte na dlaždici služby IoT hub v portálu Azure a pak klikněte na **zásady sdíleného přístupu**. V **zásady sdíleného přístupu**, klikněte **iothubowner** zásad a zkopírujte IoT Hub připojovací řetězec v **iothubowner** okno.   

1. Ke sledování dat odesílaných do služby IoT hub, vyberte **zobrazení** > **příkaz palety** a vyhledejte **IoT: spuštění monitorování D2C zpráva** příkazu v nabídce. 
2. Chcete-li zastavit monitorování dat, použijte **IoT: zastavení monitorování D2C zpráva** příkazu nabídky. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili hraniční IoT modul, který obsahuje kód pro filtrování nezpracovaná data generována zařízení IoT okraj. Můžete pokračovat na některý z následujících kurzech informace o další způsoby, které mohou pomoci Azure IoT Edge, že zapněte data do podnikových statistik na hranici.

> [!div class="nextstepaction"]
> [Nasazení funkce Azure jako modul](tutorial-deploy-function.md)
> [nasazení Azure Stream Analytics jako modul](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
