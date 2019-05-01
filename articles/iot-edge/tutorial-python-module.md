---
title: Vytvořit vlastní modul Python - Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Python a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.reviewer: kgremban
ms.author: xshi
ms.date: 03/24/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 82da44409c4500ff097805efec33cec8cf6bbedd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64575647"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj a nasazení modulu Python IoT Edge pro zařízení s Linuxem

Vývoj kódu jazyka C a nasadit ho k Linuxovému zařízení s Azure IoT Edge pomocí Visual Studio Code. 

Moduly Azure IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provedete vytvořením a nasazením modul IoT Edge, který filtruje data senzorů na zařízení IoT Edge, které jste vytvořili v tomto rychlém startu. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Python.
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasadit modul do zařízení IoT Edge.
> * Zobrazit vygenerovaná data.


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Řešení rozsahu

Tento kurz ukazuje postupy při vývoji modulu v **Python** pomocí **Visual Studio Code**a jak ji nasadit **Linuxovému zařízení**. IoT Edge pro zařízení s Windows nepodporuje moduly Pythonu. 

V následující tabulce použijte k pochopení možností pro vývoj a nasazení moduly Pythonu pro Linux: 

| Python | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití VS Code pro moduly Pythonu na systému Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití VS Code pro moduly Pythonu ve službě Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, by měl prošli předchozího kurzu věnovaného nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Pomocí některé z těchto kurzů, byste měli mít splněné následující požadavky na místě: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linuxovému zařízení s Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovanou [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurované ke spuštění kontejnerů Linuxu.

K vývoji modul IoT Edge v jazyce Python, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [Rozšíření Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Python (většinou je součástí instalace Pythonu).

>[!Note]
>Ujistěte se, že se vaše složka `bin` nachází v cestě pro vaši platformu. Obvykle je to `~/.local/` pro UNIX a macOS nebo `%APPDATA%\Python` ve Windows.

## <a name="create-a-module-project"></a>Vytvoření modulu projektu
Následující kroky vytvoření modul IoT Edge Python pomocí Visual Studio Code a nástroje Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Pomocí balíčku Python **cookiecutter** vytvořte šablonu řešení Python, na jejímž základě můžete tvořit. 

1. Ve Visual Studio Code vyberte **Zobrazit** > **Terminál**, aby se otevřel integrovaný terminál VS Code.

2. V terminálu zadejte následující příkaz k instalaci (nebo aktualizaci) **cookiecutter**, který použijete k vytvoření šablony řešení IoT Edge:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >Zkontrolujte adresář nainstalovanou cookiecutter aby bylo možné vyvolat z příkazového řádku je v CESTĚ k vašemu prostředí. Adresář je součástí výstupu instalační skript, například `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`.
   >
   >Restartujte Visual Studio Code, aby přebíral změny do cesty. 

3. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

4. V paletu příkazů zadejte a spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů k přihlášení účtu Azure. Pokud už přihlášení jste, můžete tento krok přeskočit.

5. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Postupujte podle zobrazených výzev a zadejte následující informace potřebné k vytvoření řešení:

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **Modul Python**. |
   | Zadejte název modulu | Pojmenujte modul **PythonModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z názvu, který jste zadali v předchozím kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/pythonmodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-python-module/repository.png)


### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor **.env**. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Uložte soubor .env. 

### <a name="select-your-target-architecture"></a>Vyberte Cílová architektura

Visual Studio Code v současné době můžete vyvíjet C moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Budete muset vybrat, jakou architekturu vývoji cílíte jednotlivých řešení, protože kontejneru je sestaven a jinak spusťte pro každý typ architektury. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavit výchozí Cílová platforma pro řešení**, nebo vyberte ikonu zástupce v bočním panelu v dolní části okna. 

2. Vyberte Cílová architektura v paletu příkazů v seznamu možností. Pro účely tohoto kurzu používáme virtuálního počítače s Ubuntu jako zařízení IoT Edge, budou mít výchozí **amd64**. 

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

8. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge. 

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

## <a name="build-and-push-your-module"></a>Vytváření a nasdílení změn modulu

V předchozí části jste vytvořili hraničních zařízeních IoT řešení a přidat kód do PythonModule, který odfiltruje zprávy kde teploty ohlášené počítač je v přijatelných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code.

1. Přihlaste se k Dockeru zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure container registry. Takové hodnoty můžete načíst **přístupové klíče** části vašeho registru na webu Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití `--password-stdin`. Tento osvědčený postup doporučuje pro produkční scénáře, je mimo rámec tohoto návodu. Další informace najdete v tématu [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odkaz.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

   Sestavení a odeslání příkaz spustí tři operace. Nejprve vytvoří novou složku řešení, nazývá **config** , který obsahuje soubory manifestu, sestavení si informace při nasazení šablony a dalších řešení úplné nasazení. Za druhé, poběží `docker build` k sestavení image kontejneru, který je založen na příslušný soubor dockerfile pro cílové architektury. Pak spustí `docker push` tak, aby nabízel úložiště imagí do vašeho registru kontejneru.


## <a name="deploy-modules-to-device"></a>Do zařízení nasadit moduly

Pomocí Průzkumníka Visual Studio Code a rozšíření IoT nástroje Azure k nasazení projektu modulu do zařízení IoT Edge. Už máte manifest nasazení připravili pro váš scénář **deployment.json** souboru ve složce Konfigurace. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že zařízení IoT Edge je zprovozněný.

1. V Průzkumníku Visual Studio Code, rozbalte **zařízení Azure IoT Hub** část a zobrazit seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **PythonModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte **spustit monitorování zpráv D2C**.

2. Zobrazení zpráv přicházejících u služby IoT Hub. Může trvat nějakou dobu zprávy dorazí, protože zařízení IoT Edge se zobrazí její nové nasazení a spuštění všech modulů. Pak změny, které jsme provedli PythonModule kód Počkejte, dokud počítač teploty dosáhne 25 stupňů před odesláním zprávy. Také přidá typ zprávy **výstrah** pro všechny zprávy, které dosažení této prahové hodnoty teploty. 

## <a name="edit-the-module-twin"></a>Upravit dvojče modulu

Dvojče modulu PythonModule jsme použili v manifestu nasazení pro nastavení prahové hodnoty teploty ve stupních 25. Chcete-li změnit funkci bez nutnosti aktualizovat kód modulu můžete použít dvojče modulu.

1. Ve Visual Studio Code rozbalte podrobnosti v části zařízení IoT Edge zobrazíte spuštěné moduly. 

2. Klikněte pravým tlačítkem na **PythonModule** a vyberte **upravit dvojče zařízení**. 

3. Najít **TemperatureThreshold** v požadované vlastnosti. Změňte její hodnotu na nové teploty 5 až 10 stupňů vyšší než nejnovější ohlášené teploty. 

4. Uložte soubor dvojčete modulu.

5. Klikněte pravým tlačítkem na libovolné místo ve dvojčeti modulu úpravy podokně a vyberte **aktualizace dvojčete modulu**. 

5. Monitorování příchozích zpráv typu zařízení cloud. Měli byste vidět zprávy zastavit, dokud nebude dosaženo novou teploty prahovou hodnotu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

V opačném případě můžete odstranit místní konfigurací a použité v tomto článku se vyhnout poplatkům za prostředky Azure. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Jakmile budete připraveni k sestavení vlastních modulů, další informace o [vyvíjet vlastní moduly IoT Edge](module-development.md) nebo jak [vývoj modulů s Visual Studio Code](how-to-vs-code-develop-module.md). Budete moct pokračovat do další kurzy se dozvíte, jak Azure IoT Edge můžete nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [službu Custom Vision Service](tutorial-deploy-custom-vision.md)