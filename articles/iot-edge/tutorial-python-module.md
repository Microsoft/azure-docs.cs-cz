---
title: Kurz vytvoření vlastní modul Python - Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Python a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 89c19adc571d500fff54d493072bb9976ce51aa9
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052883"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>Kurz: Vývoj a nasazení modulu Python IoT Edge na simulovaném zařízení

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Python.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro [Linux](quickstart-linux.md).
* Moduly Python pro IoT Edge nepodporují zařízení s Windows.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure. 

Prostředky pro vývoj:

* [Visual Studio Code](https://code.visualstudio.com/). 
* [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) pro Visual Studio Code.
* [Rozšíření Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code. 
* [Docker CE](https://docs.docker.com/engine/installation/). 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Python (většinou je součástí instalace Pythonu).

>[!Note]
>Ujistěte se, že se vaše složka `bin` nachází v cestě pro vaši platformu. Obvykle je to `~/.local/` pro UNIX a macOS nebo `%APPDATA%\Python` ve Windows.

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

V tomto kurzu se pomocí nástroje Azure IoT pro Visual Studio Code sestavíte modul a vytvořte **image kontejneru** ze souborů. Tuto image pak nasdílíte do **registru**, ve kterém se ukládají a spravují vaše image. Nakonec nasadíte svou image z registru pro spuštění na zařízení IoT Edge.  

Pro uložení imagí kontejnerů, můžete použít jakýkoli registr kompatibilní s Dockerem. Jsou dvě oblíbené služby registrů Dockeru [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) a [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). V tomto kurzu se používá služba Azure Container Registry. 

Pokud ještě nemáte registr kontejnerů, postupujte podle těchto kroků a vytvořte nový v Azure:

1. Na webu [Azure Portal](https://portal.azure.com) vyberte **Vytvořit prostředek** > **Kontejnery** > **Container Registry**.

2. Zadejte následující hodnoty pro vytvoření registru kontejneru:

   | Pole | Hodnota | 
   | ----- | ----- |
   | Název registru | Zadejte jedinečný název. |
   | Předplatné | V rozevíracím seznamu vyberte předplatné. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Uživatel s rolí správce | Nastavte na **Povolit**. |
   | Skladová jednotka (SKU) | Vyberte **Basic**. | 

5. Vyberte **Vytvořit**.

6. Po vytvoření registru kontejneru do něj přejděte a vyberte **Přístupové klíče**. 

7. Zkopírujte hodnoty pro **Přihlašovací server**, **Uživatelské jméno** a **Heslo**. Tyto hodnoty použijete v pozdější části kurzu a zajistit tak přístup do registru kontejneru. 

## <a name="create-an-iot-edge-module-project"></a>Vytvoření projektu modulu IoT Edge
Následující kroky vytvoření modul IoT Edge Python pomocí Visual Studio Code a nástroje Azure IoT.

### <a name="create-a-new-solution"></a>Vytvoření nového řešení

Pomocí balíčku Python **cookiecutter** vytvořte šablonu řešení Python, na jejímž základě můžete tvořit. 

1. Ve Visual Studio Code vyberte **Zobrazit** > **Terminál**, aby se otevřel integrovaný terminál VS Code.

2. V integrovaném terminálu zadejte následující příkaz k instalaci (nebo aktualizaci) balíčku **cookiecutter**, který použijete k vytvoření šablony řešení IoT Edge v editoru VS Code:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Zkontrolujte adresář nainstalovanou cookiecutter aby bylo možné vyvolat z příkazového řádku je v CESTĚ k vašemu prostředí. Adresář je součástí výstupu instalační skript, například `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Restartujte Visual Studio Code, aby přebíral změny do cesty. 

3. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

4. V paletu příkazů zadejte a spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů k přihlášení účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

5. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **Modul Python**. |
   | Zadejte název modulu | Pojmenujte modul **PythonModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněná z předchozího kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. Konečný řetězec vypadá takto: \<název_registru\>.azurecr.io/pythonmodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-python-module/repository.png)

V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Pracovní prostor řešení obsahuje pět komponent nejvyšší úrovně. Složka **modules** obsahuje kód Pythonu pro váš modul a také soubory Dockerfile pro sestavení modulu jako image kontejneru. V souboru **\.env** jsou uložené přihlašovací údaje k vašemu registru kontejneru. Soubor **deployment.template.json** obsahuje informace, které modul runtime IoT Edge používá k nasazení modulů do zařízení. A **deployment.debug.template.json** souboru kontejnery ladicí verzi modulů. Složku **\.vscode** ani soubor **\.gitignore** v tomto kurzu upravovat nebudete.  

Pokud jste při vytváření řešení nezadali registr kontejneru, ale přijali jste výchozí hodnotu localhost:5000, nebudete mít soubor \.env. 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor .env. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Každá šablona zahrnuje ukázkový kód, který vezme simulovaná data snímačů z modulu **tempSensor** a směruje je do IoT Hubu. V této části přidáte kód, který rozbalí **PythonModule** k analýze zpráv před jejich odesláním. 

1. V průzkumníku VS Code otevřete **modules** > **PythonModule** > **main.py**.

2. V horní části souboru **main.py** importujte knihovnu **json**:

    ```python
    import json
    ```

3. Do globálních čítačů přidejte proměnné **TEMPERATURE_THRESHOLD** a **TWIN_CALLBACKS**. Prahová teplota definuje hodnotu, kterou musí naměřená teplota počítače překročit, aby se data odeslala do IoT Hubu.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. Funkci **receive_message_callback** nahraďte následujícím kódem:

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. Přidejte novou funkci nazvanou **module_twin_callback**. Tato funkce se bude volat při aktualizaci požadovaných vlastností.

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. Do třídy **HubManager** přidejte nový řádek do metody **__init__**, který inicializuje právě přidanou funkci **module_twin_callback**:

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. Uložte soubor main.py.

8. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. Tento soubor říká agentovi, IoT Edge které moduly chcete nasadit, v tomto případě **tempSensor** a **PythonModule**a informuje Centrum IoT Edge, jak můžete směrovat zprávy mezi nimi. Rozšíření Visual Studio Code automaticky naplní většinu informací, že v šablonu nasazení, ale ověřte, že je vše přesné pro vaše řešení: 

   1. Výchozí platformu IoT Edge je nastavena **amd64** ve vaší VS Code stavového řádku, což znamená, že vaše **PythonModule** je nastavena na Linuxu amd64 verzi image. Změnit výchozí platforma ve stavovém řádku z **amd64** k **arm32v7** nebo **windows amd64** Pokud tomu tak architektuře zařízení IoT Edge. 

      ![Aktualizace modulu image platformy](./media/tutorial-python-module/image-platform.png)

   2. Zkontrolujte, jestli má šablona správný název modulu, ne výchozí název **SampleModule**, který jste změnili při vytváření řešení IoT Edge.

   3. **RegistryCredentials** části ukládá přihlašovací údaje registru Dockeru, tak, aby se agent IoT Edge můžete vyžádat bitové kopie modulu. Skutečná uživatelská jména a hesla se ukládají do souboru .env, který git ignoruje. Pokud jste to ještě neudělali, přidejte do souboru .env svoje přihlašovací údaje.  

   4. Pokud chcete získat další informace o manifesty nasazení, přečtěte si téma [zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).

9. Přidejte do manifestu nasazení dvojče modulu **PythonModule**. Vložte následující obsah JSON do dolní části oddílu **moduleContent** za dvojče modulu **$edgeHub**: 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![Dvojče zařízení přidat do šablony nasazení](./media/tutorial-python-module/module-twin.png)

10. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-solution"></a>Vytváření a nasdílení změn vašeho řešení

V předchozí části jste vytvořili řešení IoT a do modulu **PythonModule** jste přidali kód, který odfiltruje zprávy, ve kterých je hlášená teplota počítače nižší než přípustná mezní hodnota. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru. 

1. Zadáním následujícího příkazu v integrovaném terminálu editoru Visual Studio Code se přihlaste k Dockeru. Potom můžete odeslat image modulu do služby Azure Container Registry: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Použijte uživatelské jméno, heslo a přihlašovací server zkopírované ze služby Azure Container Registry v první části. Tyto hodnoty můžete také načíst z oddílu **Přístupové klíče** v registru na webu Azure Portal.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge). 

Když editoru Visual Studio Code sdělíte, že má sestavit vaše řešení, nejdříve se načtou informace ze šablony nasazení a v nové složce s názvem **config** se vygeneruje soubor deployment.json. Pak se v integrovaném terminálu spustí dva příkazy: `docker build` a `docker push`. Tyto dva příkazy sestaví kód, provedou kontejnerizaci vašeho kódu Python a kód odešlou do registru kontejneru, který jste zadali při inicializaci řešení. 

Úplnou adresu image kontejneru se značkou můžete vidět v příkazu `docker build`, který spouští integrovaný terminál VS Code. Adresa image je sestavená z informací v souboru module.json a má formát \<úložiště\>:\<verze\>-\<platforma\>. V tomto kurzu by měla vypadat takto: název_registru.azurecr.io/pythonmodule:0.0.1-amd6.

## <a name="deploy-and-run-the-solution"></a>Nasazení a spuštění řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Můžete také nasadit moduly pomocí rozšíření Azure IoT Hub Toolkit (dříve rozšíření Azure IoT Toolkit) pro Visual Studio Code. Pro svůj scénář už máte připravený manifest nasazení – soubor **deployment.json**. Teď stačí jen vybrat zařízení, na které se nasazení provede.

1. V nástroji VS Code paletu příkazů, spusťte **Azure IoT Hub: Vyberte službu IoT Hub**. 

2. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. 

3. V průzkumníku VS Code rozbalte oddíl **Azure IoT Hub Devices** (Zařízení Azure IoT Hub). 

4. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení). 

   ![Vytvoření nasazení pro jedno zařízení](./media/tutorial-python-module/create-deployment.png)

5. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json. 

6. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **PythonModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí. 

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů. 

Na samotném zařízení IoT Edge můžete stav modulů nasazení zobrazit pomocí příkazu `iotedge list`. Měly by se zobrazit čtyři moduly: dva moduly runtime IoT Edge, tempSensor a vlastní modul, který jste vytvořili v tomto kurzu. Spuštění všech modulů může několik minut trvat, proto příkaz spusťte znovu, pokud se zpočátku všechny nezobrazí. 

Pokud chcete zobrazit zprávy, které jednotlivé moduly generují, použijte příkaz `iotedge logs <module name>`. 

Zprávy přicházející do centra IoT můžete zobrazit pomocí Visual Studio Code. 

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte tři tečky (**...**) a potom vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
2. Pokud chcete monitorovat zprávy D2C pro konkrétní zařízení, klikněte pravým tlačítkem na příslušné zařízení v seznamu a vyberte **Start Monitoring D2C Messages** (Zahájit monitorování zpráv D2C).
3. Pokud chcete monitorování dat zastavit, spusťte příkaz **Azure IoT Hub: Zastavit monitorování zpráv D2C** v paletu příkazů. 
4. Pokud chcete zobrazit nebo aktualizovat dvojče modulu, klikněte pravým tlačítkem na příslušný modul v seznamu a vyberte **Edit module twin** (Upravit dvojče modulu). Pokud chcete aktualizovat dvojče modulu, uložte soubor JSON dvojčete, klikněte pravým tlačítkem na oblast editoru a vyberte **Update Module Twin** (Aktualizovat dvojče modulu).
5. Pokud chcete zobrazit protokoly Dockeru, nainstalujte [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) pro VS Code. Spuštěné moduly můžete vyhledat místně v průzkumníku Dockeru. Kliknutím na **Show Logs** (Zobrazit protokoly) v místní nabídce je zobrazíte v integrovaném terminálu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

### <a name="delete-local-resources"></a>Odstranění místních prostředků

Pokud ze svého zařízení chcete odebrat modul runtime IoT Edge a související prostředky, použijte následující příkazy. 

Odeberte modul runtime IoT Edge.

   ```bash
   sudo apt-get remove --purge iotedge
   ```

Při odebrání modulu runtime IoT Edge se zastaví kontejnery, které vytvořil, ale na zařízení se zachovají. Zobrazte všechny kontejnery.

   ```bash
   sudo docker ps -a
   ```

Odstraňte kontejnery modulu runtime, které se vytvořily ve vašem zařízení.

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

S použitím názvů kontejnerů odstraňte všechny další kontejnery, které se zobrazily ve výstupu příkazu `docker ps`. 

Odeberte kontejnerový modul runtime.

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge s kódem pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Pokračujte dalšími kurzy, ve kterých se naučíte další způsoby, jak vám může Azure IoT Edge pomoct přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Nasazení služby Azure Functions jako modulů IoT Edge](tutorial-deploy-function.md)
> [Nasazení služby Azure Stream Analytics jako modulu IoT Edge](tutorial-deploy-stream-analytics.md)
