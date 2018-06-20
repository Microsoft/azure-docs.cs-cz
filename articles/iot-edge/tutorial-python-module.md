---
title: Modul Azure IoT Edge Python | Microsoft Docs
description: Vytvoření modulu IoT Edge s kódem v jazyce Python a jeho nasazení na zařízení Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88d772306cb9e67216b380aa885284ebedc77b5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632104"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Vývoj modulu IoT Edge Python a jeho nasazení na simulované zařízení – Preview

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v tématu o nasazení Azure IoT Edge na simulované zařízení ve [Windows][lnk-tutorial1-win] nebo [Linuxu][lnk-tutorial1-lin]. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Python.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru. 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

> [!IMPORTANT]
> V současnosti můžete modul Python spouštět jenom v linuxových kontejnerech amd64. Nedá se spustit v kontejnerech Windows ani v kontejnerech založených na procesorech ARM. 

## <a name="prerequisites"></a>Požadavky

* Zařízení Azure IoT Edge, které jste vytvořili v rychlém startu nebo v prvním kurzu.
* Primární připojovací řetězec klíče pro zařízení IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Rozšíření Python pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* [Docker](https://docs.docker.com/engine/installation/) na stejném počítači, jako je Visual Studio Code. Pro tento kurz stačí edice Community Edition (CE). 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Python (většinou je součástí instalace Pythonu).

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů
V tomto kurzu pomocí rozšíření Azure IoT Edge pro VS Code sestavíte modul a ze souborů vytvoříte **image kontejneru**. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro účely tohoto kurzu můžete použít jakýkoli registr kompatibilní s Dockerem. V cloudu jsou k dispozici dvě oblíbené služby registrů Dockeru – [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Centrum Dockeru](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Azure Container Registry**.
2. Zadejte název registru, zvolte předplatné a skupinu prostředků a nastavte skladovou položku na **Basic**. 
3. Vyberte **Vytvořit**.
4. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 
5. Přepněte přepínač **Uživatel s rolí správce** na **Povolit**.
6. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následující kroky ukazují, jak vytvořit modul IoT Edge Python pomocí Visual Studio Code a rozšíření Azure IoT Edge.
1. V nástroji Visual Studio Code vyberte, že chcete **zobrazit** > **integrovaný terminál**, aby se otevřel integrovaný terminál VS Code.
2. V integrovaném terminálu zadejte následující příkaz, kterým nainstalujete (nebo aktualizujete) **cookiecutter** (doporučujeme to udělat ve virtuálním prostředí nebo v uživatelské instalaci – viz následující ukázka):

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. Vytvořte projekt pro nový modul. Následující příkaz vytvoří složku projektu **FilterModule** s úložištěm kontejnerů. Pokud používáte registr kontejnerů Azure, musí být parametr `image_repository` ve tvaru `<your container registry name>.azurecr.io/filtermodule`. V aktuální pracovní složce zadejte následující příkaz:

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. Vyberte **File** > **Open Folder** (Soubor > Otevřít složku).
5. Přejděte ke složce **FilterModule** a klikněte na **Select Folder** (Vybrat složku). Projekt se otevře v průzkumníku VS Code.
6. Když v průzkumníku VS Code kliknete na soubor **main.py**, otevře se.
7. Na začátek oboru názvů **FilterModule** naimportujte knihovnu `json`:

    ```python
    import json
    ```

8. Do globálních čítačů přidejte `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` a `TWIN_CALLBACKS`. Prahová teplota definuje hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. Následující obsah použijte k aktualizaci funkce `receive_message_callback`.

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

10. Přidejte novou funkci `device_twin_callback`. Tato funkce se bude volat při aktualizaci požadovaných vlastností.

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

11. Do třídy `HubManager` přidejte nový řádek do metody `__init__`, který inicializuje právě přidanou funkci `device_twin_callback`.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. Soubor uložte.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Vytvoření image Dockeru a její publikování do registru

1. Přihlaste se k Dockeru zadáním následujícího příkazu v integrovaném terminálu VS Code: 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   Použijte uživatelské jméno, heslo a přihlašovací server, který jste zkopírovali z registru kontejneru Azure při jeho vytvoření.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **module.json** a klikněte na **Sestavit a odeslat image Dockeru s modulem IoT Edge**. V místním rozevíracím seznamu v horní části okna editoru VS Code vyberte platformu kontejneru, třeba **amd64** pro linuxový kontejner. VS Code vytvoří kontejner `main.py` a potřebné závislosti a potom je doručí do zadaného registru kontejneru. Vytvoření první image může trvat několik minut.

3. Úplnou adresu image kontejneru můžete získat pomocí značky v integrovaném terminálu VS Code. Další informace o definici sestavení a odeslání najdete v souboru `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Přidání přihlašovacích údajů registru do modulu runtime Edge
Přidejte přihlašovací údaje k vašemu registru do modulu runtime Edge na počítači, na kterém spouštíte službu Edge. S těmito přihlašovacími údaji může modul runtime načíst kontejner. 

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
2. Zkontrolujte, že je automaticky vyplněný modul **tempSensor**. Pokud tomu tak není, použijte k jeho přidání následující postup:
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `tempSensor`.
    3. Do pole **Identifikátor URI image** zadejte `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Další nastavení ponechte beze změny a klikněte na **Uložit**.
9. Přidejte modul **filterModule**, který jste vytvořili v předchozích částech. 
    1. Vyberte **Přidat modul IoT Edge**.
    2. Do pole **Název** zadejte `filterModule`.
    3. Do pole **Identifikátor URI image** zadejte adresu své image, například `<your container registry address>/filtermodule:0.0.1-amd64`. Úplnou adresu image najdete v předchozí části.
    4. Zaškrtněte políčko **Povolit**, abyste mohli upravit dvojče modulu. 
    5. Nahraďte kód JSON v textovém poli dvojčete modulu následujícím kódem JSON: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Klikněte na **Uložit**.
10. Klikněte na **Další**.
11. V kroku **Určení tras** zkopírujte do textového pole následující JSON. Moduly publikují všechny zprávy v modulu runtime Edge. Tok zpráv definují deklarační pravidla modulu runtime. V tomto kurzu potřebujete dvě trasy: První přenáší zprávy ze senzoru teploty do modulu filtru. Používá k tomu koncový bod „input1“, který jste nakonfigurovali v obslužné rutině **FilterMessages**. Druhá trasa přenáší zprávy z modulu filtru do služby IoT Hub. V této trase je `upstream` speciální cíl, který centru Edge říká, že má odesílat zprávy do služby IoT Hub. 

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
6. Vraťte se na stránku podrobností o zařízení IoT Edge a klikněte na **Aktualizovat**. Měl by se zobrazit spuštěný nový modul **filtermodule** společně s modulem **tempSensor** a **modulem runtime IoT Edge**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Monitorování zpráv typu zařízení-cloud odesílaných ze zařízení IoT Edge do centra IoT:
1. Nakonfigurujte rozšíření Azure IoT Toolkit s použitím připojovacího řetězce pro vaše centrum IoT: 
    1. Otevřete průzkumník VS Code tím, že vyberete **Zobrazení** > **Průzkumník**. 
    3. V průzkumníku klikněte na **IOT HUB DEVICES** (ZAŘÍZENÍ IOT HUB) a pak klikněte na **...**. Klikněte na **Set IoT Hub Connection String** (Nastavit připojovací řetězec pro IoT Hub) a v místním okně zadejte připojovací řetězec IoT Hubu, se kterým se připojí zařízení IoT Edge. 

        Pokud chcete najít připojovací řetězec, klikněte na dlaždici IoT Hubu na webu Azure Portal a pak klikněte na **Zásady sdíleného přístupu**. V části **Zásady sdíleného přístupu** klikněte na zásadu **iothubowner** a potom si z okna **iothubowner** zkopírujte připojovací řetězec IoT Hubu.   

1. Pokud chcete monitorovat data přicházející do IoT Hubu, vyberte, že chcete **zobrazit** > **paletu příkazů** a vyhledejte v nabídce příkaz **IoT: Start monitoring D2C message** (IoT: Spustit monitorování zpráv typu zařízení-cloud). 
2. Pokud chcete monitorování dat zastavit, vyberte z nabídky příkaz **IoT: Stop monitoring D2C message** (IoT: Zastavit monitorování zpráv typu zařízení-cloud). 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Pokračujte některým z následujících kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení funkce Azure Functions jako modulu](tutorial-deploy-function.md)
> [Nasazení služby Azure Stream Analytics jako modulu](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
