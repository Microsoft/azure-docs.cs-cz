---
title: 'Kurz: Vytváření a nasazování vlastních modulů – Machine Learning na Azure IoT Edge'
description: Tento kurz ukazuje, jak vytvořit a nasadit moduly IoT Edge, které zpracovávají data ze zařízení leaf prostřednictvím modelu strojového učení a pak odesílají přehledy do služby IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3cba7781ac80ae567b2bfd54c4131429ed94b90f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75772359"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Kurz: Vytvoření a nasazení vlastních modulů IoT Edge

> [!NOTE]
> Tento článek je součástí řady pro kurz o používání Azure Machine Learning na IoT Edge. Pokud jste k tomuto článku dorazili přímo, doporučujeme vám začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) v sérii pro dosažení nejlepších výsledků.

V tomto článku vytvoříme tři moduly IoT Edge, které přijímají zprávy ze zařízení leaf, spouštějí data prostřednictvím modelu strojového učení a pak předávají přehledy do služby IoT Hub.

Rozbočovač IoT Edge usnadňuje komunikaci modulů s modulem. Pomocí centra IoT Edge jako zprostředkovatele zpráv udržuje moduly nezávislé na sobě navzájem. Moduly stačí zadat vstupy, na kterých přijímají zprávy a výstupy, na které jsou zápis zpráv.

Chceme, aby zařízení IoT Edge pro nás splnilo čtyři věci:

* Příjem dat ze zařízení list
* Předpovědět zbývající životnost (RUL) pro zařízení, které odeslalo data
* Odeslat zprávu pouze s rul pro zařízení do služby IoT Hub (tato funkce může být upravena tak, aby odesílat data pouze v případě, že RUL klesne pod určitou úroveň)
* Uložte data listového zařízení do místního souboru na zařízení IoT Edge. Tento datový soubor se pravidelně nahrává do služby IoT Hub prostřednictvím nahrávání souborů, aby se zpřesnily trénování modelu strojového učení. Použití nahrávání souborů namísto neustálého streamování zpráv je nákladově efektivnější.

K provedení těchto úkolů používáme tři vlastní moduly:

* **Klasifikátor RUL:** Modul turboFanRulClassifier, který jsme vytvořili v [aplikaci Train a nasadit model Azure Machine Learning,](tutorial-machine-learning-edge-04-train-model.md) je standardní modul strojového učení, který zveřejňuje vstup s názvem "amlInput" a výstup s názvem "amlOutput". "AmlInput" očekává, že jeho vstup bude vypadat přesně jako vstup, který jsme odeslali do webové služby založené na ACI. Podobně "amlOutput" vrátí stejná data jako webová služba.

* **Avro spisovatel:** Tento modul přijímá zprávy na vstupu "avroModuleInput" a zachová zprávu ve formátu Avro na disk pro pozdější odeslání do služby IoT Hub.

* **Modul směrovače:** Modul směrovače přijímá zprávy z podřízeného listu zařízení, pak formáty a odešle zprávy třídění. Modul pak obdrží zprávy od třídění a předá zprávu do modulu avro zapisovače. Nakonec modul odešle pouze rul předpověď do centra IoT Hub.

  * Vstupy:
    * **deviceInput**: přijímá zprávy z listových zařízení
    * **rulInput:** přijímá zprávy z "amlOutput"

  * Výstupy:
    * **klasifikovat:** odesílá zprávy "amlInput"
    * **writeAvro:** odesílá zprávy "avroModuleInput"
    * **toIotHub:** odesílá zprávy $upstream, který předá zprávy připojenému centru IoT Hub

Následující diagram znázorňuje moduly, vstupy, výstupy a trasy IoT Edge Hub pro úplné řešení:

![Diagram architektury tří modulů IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Kroky v tomto článku jsou obvykle prováděny vývojáře cloudu.

## <a name="create-a-new-iot-edge-solution"></a>Vytvoření nového řešení IoT Edge

Během provádění druhého z našich dvou poznámkových bloků Azure jsme vytvořili a publikovali image kontejneru obsahující náš model RUL. Azure Machine Learning, jako součást procesu vytváření image, zabalené, že model tak, aby image je nasaditelný jako modul Azure IoT Edge. V tomto kroku vytvoříme řešení Azure IoT Edge pomocí modulu "Azure Machine Learning" a najedeme modul na image, kterou jsme publikovali pomocí poznámkových bloků Azure.

1. Otevřete relaci vzdálené plochy do vývojového počítače.

2. Otevřít složku **C:\\zdroj\\IoTEdgeAndMlSample** v kódu Visual Studia.

3. Klikněte pravým tlačítkem myši na panel průzkumníka (v prázdném prostoru) a vyberte **nové řešení IoT Edge .**

    ![Vytvoření nového řešení IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Přijměte výchozí název řešení **EdgeSolution**.

5. Zvolte **Azure Machine Learning** jako šablonu modulu.

6. Název modulu **turbofanRulClassifier**.

7. Vyberte si pracovní prostor strojového učení.

8. Vyberte image, kterou jste vytvořili při spuštění poznámkového bloku Azure.

9. Podívejte se na řešení a všimněte si souborů, které byly vytvořeny:

   * **deployment.template.json:** Tento soubor obsahuje definici každého z modulů v řešení. V tomto souboru je třeba věnovat pozornost třem částem:

     * **Pověření registru:** Definuje sadu vlastních registrů kontejnerů, které používáte ve vašem řešení. Právě teď by měl obsahovat registr z pracovního prostoru strojového učení, což je místo, kde byla uložena vaše image Azure Machine Learning. Můžete mít libovolný počet registrů kontejnerů, ale pro jednoduchost použijeme tento jeden registr pro všechny moduly

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Moduly:** Tato část obsahuje sadu uživatelem definovaných modulů, které jsou součástí tohoto řešení. Všimněte si, že tato část v současné době obsahuje dva moduly: SimulatedTemperatureSensor a turbofanRulClassifier. SimulatedTemperatureSensor byl nainstalován šablonou kódu sady Visual Studio, ale pro toto řešení jej nepotřebujeme. Definici modulu SimulatedTemperatureSensor můžete odstranit z části moduly. Všimněte si, že turbodmychadloRulClassifier definice modulu odkazuje na image v registru kontejneru. Jak přidáme další moduly do řešení, zobrazí se v této části.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Trasy:** budeme pracovat s trasami docela dost v tomto tutoriálu. Trasy definují vzájemnou komunikaci modulů. Dvě trasy definované šablonou se neshodují s směrováním, které potřebujeme. První trasa odešle všechna data z libovolného výstupu třídění do centra IoT Hub ($upstream). Druhá trasa je pro SimulatedTemperatureSensor, který jsme právě odstranili. Odstraňte dvě výchozí trasy.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **deployment.debug.template.json:** tento soubor je ladicí verze deployment.template.json. Měli bychom zrcadlit všechny změny z deployment.template.json do tohoto souboru.

   * **.env:** tento soubor je místo, kde byste měli zadat uživatelské jméno a heslo pro přístup k registru.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Klikněte pravým tlačítkem myši na soubor deployment.template.json v průzkumníku kódu sady Visual Studio a vyberte **build IoT Edge Solution**.

11. Všimněte si, že tento příkaz vytvoří konfigurační složku se souborem deployment.amd64.json. Tento soubor je konkrétní šablona nasazení pro řešení.

## <a name="add-router-module"></a>Přidat modul směrovače

Dále přidáme modul Router do našeho řešení. Modul Router zvládá několik odpovědností za naše řešení:

* **Přijímat zprávy ze zařízení list:** jako zprávy dorazí do zařízení IoT Edge z podřízených zařízení, modul Router obdrží zprávu a začne orchestrating směrování zprávy.
* **Odeslat zprávy modulu třídič RUL:** když je přijata nová zpráva z navazujícího zařízení, modul Router transformuje zprávu do formátu, který očekává třídič RUL. Směrovač odešle zprávu rul třídění pro předpověď RUL. Jakmile klasifikátor provedl předpověď, odešle zprávu zpět do modulu Router.
* **Odeslat rul zprávy do služby IoT Hub:** když směrovač přijímá zprávy od třídění, transformuje zprávu obsahovat pouze základní informace, ID zařízení a RUL a odešle zkrácenou zprávu do centra IoT. Další upřesnění, které jsme zde neprovedli, by odesílalo zprávy do centra IoT Hub pouze v případě, že předpověď RUL klesne pod prahovou hodnotu (například když rul je menší než 100 cyklů). Filtrování tímto způsobem by snížilo objem zpráv a snížilo náklady na centrum IoT hub.
* **Odeslat zprávu modulu Avro Writer:** chcete-li zachovat všechna data odeslaná navazujícím zařízením, modul Router odešle celou zprávu přijatou z třídění do modulu Avro Writer, který bude přetrvávat a nahrávat data pomocí nahrávání souborů služby IoT Hub.

> [!NOTE]
> Popis odpovědnosti modulu může způsobit, že zpracování bude vypadat sekvenční, ale tok je založen na zprávě nebo události. To je důvod, proč potřebujeme modul orchestrace, jako je náš modul Router.

### <a name="create-module-and-copy-files"></a>Vytvoření modulu a kopírování souborů

1. Klikněte pravým tlačítkem myši na složku modulů v kódu sady Visual Studio a zvolte **Přidat modul IoT Edge Module**.

2. Zvolte **modul C#**.

3. Název modulu **turbofanRouter**.

4. Po zobrazení výzvy k zobrazení grafického úložiště Dockeru použijte registr z pracovního prostoru strojového učení (registr najdete v uzlu registryCredentials souboru *deployment.template.json).* Tato hodnota je plně kvalifikovaná adresa registru, ** \<stejně\>** jako váš registr .azurecr.io/turbofanrouter .

    > [!NOTE]
    > V tomto článku používáme Azure Container Registry vytvořené pracovní prostor Azure Machine Learning, který jsme použili k trénování a nasazování našeho třídění. To je čistě pro pohodlí. Mohli jsme vytvořit nový registr kontejnerů a zveřejnit tam naše moduly.

5. Otevřete nové okno terminálu v aplikaci Visual Studio Code **(View** > **Terminal)** a zkopírujte soubory z adresáře modulů.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Po zobrazení výzvy k `y` přepsání `Enter`program.cs stiskněte klávesu a stiskněte klávesu .

### <a name="build-router-module"></a>Sestavení modulu směrovače

1. V kódu sady Visual Studio vyberte možnost**Konfigurace výchozí úlohy sestavení** **terminálu** > .

2. Klikněte na **Vytvořit soubor tasks.json ze šablony**.

3. Klikněte na **.NET Core**.

4. Při otevření souboru Tasks.json nahraďte obsah:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Uložit a zavřít tasks.json.

6. Spuštění sestavení `Ctrl + Shift + B` s úlohou sestavení nebo **Spuštění terminálu** > **Run Build Task**.

### <a name="set-up-module-routes"></a>Nastavení tras modulů

Jak bylo uvedeno výše, modul runtime IoT Edge používá trasy nakonfigurované v souboru *deployment.template.json* ke správě komunikace mezi volně vázanými moduly. V této sekci vyvrtáme návod, jak nastavit trasy pro modul turbodmychadla. Nejprve pokryjeme vstupní trasy a pak se přesuneme na výstupy.

#### <a name="inputs"></a>Vstupy

1. V metodě Init() Program.cs registrujeme dvě zpětná volání pro modul:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. První zpětné volání naslouchá zprávy odeslané do **jímky deviceInput.** Z výše uvedeného diagramu vidíme, že chceme směrovat zprávy z libovolného zařízení list na tento vstup. V souboru *deployment.template.json* přidejte trasu, která informuje hraniční rozbočovač pro směrování všech zpráv přijatých zařízením IoT Edge, která nebyla odeslána modulem IoT Edge, do vstupu nazvaného "deviceInput" v modulu turbofanrouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Dále přidejte trasu pro zprávy z modulu rulClassifier do modulu turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Výstupy

Přidejte čtyři další trasy do parametru $edgeHub trasy pro zpracování výstupů z modulu Směrovač.

1. Program.cs definuje metodu SendMessageToClassifier(), která používá klienta modulu k odeslání zprávy klasifikátoru RUL pomocí trasy:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() používá klienta modulu k odeslání pouze rul dat pro zařízení do služby IoT Hub přes trasu:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() používá klienta modulu k odeslání zprávy s daty RUL přidanými do modulu avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() odesílá neúspěšné zprávy proti proudu služby IoT Hub, kde mohou být směrovány na později.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Se všemi trasami dohromady by měl váš uzel "$edgeHub" vypadat jako následující JSON:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> Přidáním modulu turbodmychadla `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`router vytvořil následující další trasu: . Odeberte tuto trasu a ponechte v souboru deployment.template.json pouze výše uvedené trasy.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Kopírování tras na deployment.debug.template.json

Jako poslední krok, aby naše soubory v synchronizaci, zrcadlit změny provedené na deployment.template.json v deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Přidat modul Avro Writer

Modul Avro Writer má v našem řešení dvě povinnosti, ukládat zprávy a nahrávat soubory.

* **Uložit zprávy**: když modul Avro Writer obdrží zprávu, zapíše zprávu do místního systému souborů ve formátu Avro. Používáme vazbu připojit, který připojí adresář (v tomto případě /data/avrofiles) do cesty v kontejneru modulu. Toto připojení umožňuje modulu zapisovat na místní cestu (/avrofiles) a mít tyto soubory přístupné přímo ze zařízení IoT Edge.

* **Nahrát soubory**: Modul Avro Writer používá funkci nahrávání souborů azure IoT Hub k nahrání souborů do účtu úložiště Azure. Po úspěšném nahrání souboru modul odstraní soubor z disku

### <a name="create-module-and-copy-files"></a>Vytvoření modulu a kopírování souborů

1. V paletě příkazů vyhledejte a pak vyberte **Python: Vyberte interpret .**

1. Zvolte interpret nalezený\\v Jazyce C: Python37.

1. Znovu otevřete paletu příkazů a vyhledejte a pak vyberte **Terminál: Vyberte výchozí prostředí**.

1. Po zobrazení výzvy zvolte **Příkazový řádek**.

1. Otevřete nový terminálový shell **Terminál** > **Nový terminál**.

1. Klikněte pravým tlačítkem myši na složku modulů v kódu sady Visual Studio a zvolte **Přidat modul IoT Edge Module**.

1. Zvolte **Modul Python**.

1. Pojmenujte modul "avroFileWriter".

1. Po zobrazení výzvy k zobrazení grafického úložiště Dockeru použijte stejný registr, který jste použili při přidávání modulu Směrovač.

1. Zkopírujte soubory z ukázkového modulu do řešení.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Pokud se zobrazí výzva k `y` přepsání main.py, zadejte a stiskněte klávesu `Enter`.

1. Všimněte si, že filemanager.py a schema.py byly přidány do řešení a main.py byla aktualizována.

> [!NOTE]
> Při otevření souboru Pythonu můžete být vyzváni k instalaci pylintu. K dokončení tohoto výukového programu není nutné instalovat linter.

### <a name="bind-mount-for-data-files"></a>Připojení vazby pro datové soubory

Jak je uvedeno v úvodu, modul zapisovače spoléhá na přítomnost vazby připojit k zápisu souborů Avro do souborového systému zařízení.

#### <a name="add-directory-to-device"></a>Přidání adresáře do zařízení

1. Připojte se k virtuálnímu počítači zařízení IoT Edge pomocí SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Vytvořte adresář, ve který budou uloženy uložené zprávy listového zařízení.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Aktualizujte oprávnění adresáře tak, aby bylo možné jej zapisovat kontejnerem.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Ověření adresáře má nyní oprávnění k zápisu (w) pro uživatele, skupinu a vlastníka.

   ```bash
   ls -la /data
   ```

   ![Oprávnění adresáře pro avrosoubory](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Přidání adresáře do modulu

Chcete-li přidat adresář do kontejneru modulu, upravíme Dockerfiles přidružené k modulu avroFileWriter. Existují tři Dockerfiles přidružené k modulu: Dockerfile.amd64, Dockerfile.amd64.debug a Dockerfile.arm32v7. Tyto soubory by měly být synchronizovány v případě, že chceme ladit nebo nasadit na zařízení arm32. Pro tento článek se zaměřte pouze na Dockerfile.amd64.

1. Ve vývojovém počítači otevřete soubor **Dockerfile.amd64.**

2. Upravte soubor tak, aby vypadal jako následující příklad:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` Příkazy `chown` a instruují proces sestavení Dockeru k vytvoření adresáře nejvyšší úrovně s názvem /avrofiles v bitové kopii a poté k tomu, aby moduluser byl vlastníkem tohoto adresáře. Je důležité, aby tyto příkazy jsou vloženy po uživateli `useradd` modulu je přidán do obrazu s příkazem a před přepnutím kontextu na moduluser (USER moduleuser).

3. Proveďte odpovídající změny Dockerfile.amd64.debug a Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizace konfigurace modulu

Posledním krokem vytvoření vazby je aktualizace souborů deployment.template.json (a deployment.debug.template.json) informacemi o vazbě.

1. Otevřete soubor deployment.template.json.

2. Upravte definici modulu pro avroFileWriter přidáním parametru, `Binds` který odkazuje na adresář kontejneru /avrofiles do místního adresáře na hraničním zařízení. Definice modulu by měla odpovídat tomuto příkladu:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Proveďte odpovídající změny deployment.debug.template.json.

### <a name="bind-mount-for-access-to-configyaml"></a>Vazba připojit pro přístup k config.yaml

Musíme přidat ještě jednu vazbu pro modul zapisovače. Tato vazba poskytuje modulu přístup ke čtení připojovacího řetězce ze souboru /etc/iotedge/config.yaml na zařízení IoT Edge. Potřebujeme připojovací řetězec k vytvoření IoTHubClient tak, abychom mohli volat upload\_blob\_async metodu pro nahrávání souborů do služby IoT hub. Kroky pro přidání této vazby jsou podobné těm v předchozí části.

#### <a name="update-directory-permission"></a>Aktualizovat oprávnění k adresáři

1. Připojte se k zařízení IoT Edge pomocí SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Přidejte oprávnění ke čtení do souboru config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Ověřte, zda jsou oprávnění nastavena správně.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Ensure that the permissions for config.yaml are **-r--r--r--**.

#### <a name="add-directory-to-module"></a>Přidání adresáře do modulu

1. Ve vývojovém počítači otevřete soubor **Dockerfile.amd64.**

2. Přidejte do `mkdir` souboru další sadu a `chown` příkazy, aby vypadaly takto:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Proveďte odpovídající změny Dockerfile.amd64.debug a Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizace konfigurace modulu

1. Otevřete soubor **deployment.template.json.**

2. Upravte definici modulu pro avroFileWriter `Binds` přidáním druhého řádku k parametru, který odkazuje adresář kontejneru (/app/iotconfig) do místního adresáře v zařízení (/etc/iotedge).

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Proveďte odpovídající změny deployment.debug.template.json.

## <a name="install-dependencies"></a>Instalace závislostí

Modul zapisovače přebírá závislost na dvou knihovnách Pythonu, fastavro a PyYAML. Musíme nainstalovat závislosti na našem vývojovém počítači a instruovat proces sestavení Dockeru k jejich instalaci do image našeho modulu.

### <a name="pyyaml"></a>PyYAML

1. Ve vývojovém počítači otevřete soubor **requirements.txt** a přidejte pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Otevřete soubor **Dockerfile.amd64** `pip install` a přidejte příkaz pro upgrade instalačních nástrojů.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Proveďte odpovídající změny Dockerfile.amd64.debug. <!--may not be necessary. Add 'if needed'?-->

4. Instalace pyyaml místně otevřením terminálu v kódu Sady Visual Studio a zadáním

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro (Fastavro)

1. V requirements.txt přidejte fastavro po pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Nainstalujte fastavro do vývojového počítače pomocí terminálu Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Změna konfigurace služby IoT Hub

Zavedením zařízení a modulů IoT Edge do systému jsme změnili naše očekávání ohledně toho, jaká data budou odeslána do centra a za jakým účelem. Musíme překonfigurovat směrování v rozbočovači, abychom se vypořádali s naší novou realitou.

> [!NOTE]
> Před nasazením modulů překonfigurujeme rozbočovač, protože některé nastavení rozbočovače, konkrétně nahrávání souborů, musí být správně nastaveno pro správný provoz modulu avroFileWriter.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Nastavení trasy pro zprávy RUL v centru IoT Hub

S routerem a třídění na místě, očekáváme, že přijímat pravidelné zprávy obsahující pouze ID zařízení a RUL předpověď pro zařízení. Chceme směrovat rul data do vlastního úložiště, kde můžeme sledovat stav zařízení, vytvářet zprávy a požární výstrahy podle potřeby. Zároveň chceme, aby všechna data zařízení, která jsou stále odesílána přímo listovým zařízením, které ještě nebylo připojeno k našemu zařízení IoT Edge, pokračovala v cestě do aktuálního umístění úložiště.

#### <a name="create-a-rul-message-route"></a>Vytvoření trasy zprávy RUL

1. Na webu Azure Portal přejděte do služby IoT Hub.

2. V levém navigačním panelu zvolte **Message routing**.

3. Vyberte **Přidat**.

4. Pojmenujte trasu **RulMessageRoute**.

5. Vyberte **Přidat** vedle voliče **koncového bodu** a zvolte **úložiště objektů Blob**.

6. Ve formuláři **Přidat koncový bod úložiště** pojmenujte koncový bod **ruldata**.

7. Vyberte **Vybrat kontejner**.

8. Zvolte účet úložiště použitý v tomto kurzu, který je pojmenován jako **jedinečná přípona\<\>iotedgeandml**.

9. Zvolte kontejner **ruldata** a klepněte na **vybrat**.

10. Kliknutím na **Vytvořit** vytvořte koncový bod úložiště.

11. Do **směrovacího dotazu**zadejte následující dotaz:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Rozbalte část **Test** a potom část **Text zprávy.** Nahraďte zprávu tímto příkladem našich očekávaných zpráv:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Vyberte **možnost Testovací trasa**. Pokud je test úspěšný, zobrazí se zpráva odpovídající dotazu.

14. Klikněte na **Uložit**.

#### <a name="update-turbofandevicetostorage-route"></a>Aktualizovat trasu turbodmychadlaDeviceToStorage

Nechceme směrovat nová data předpovědi do našeho starého umístění úložiště, proto trasu aktualizujte, abyste jí zabránili.

1. Na stránce směrování **zpráv služby** IoT Hub vyberte kartu **Trasy.**

2. Vyberte **turbofanDeviceDataToStorage**, nebo jakýkoli název, který jste dal na počáteční datové trasy zařízení.

3. Aktualizace směrovacího dotazu

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Rozbalte část **Test** a potom část **Text zprávy.** Nahraďte zprávu tímto příkladem našich očekávaných zpráv:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. Vyberte **možnost Testovací trasa**. Pokud je test úspěšný, zobrazí se zpráva odpovídající dotazu.

6. Vyberte **Uložit**.

### <a name="configure-file-upload"></a>Konfigurace odesílání souborů

Nakonfigurujte funkci nahrávání souborů služby IoT Hub tak, aby modul zapisovač souborů mohl nahrávat soubory do úložiště.

1. V levém navigátoru v centru IoT Hub zvolte **Nahrání souboru**.

2. Vyberte **kontejner úložiště Azure**.

3. V seznamu vyberte účet úložiště.

4. Vyberte kontejner **uploadturbofanfiles** a klepněte na **tlačítko Vybrat**.

5. Vyberte **Uložit**. Portál vás upozorní po dokončení uložení.

> [!Note]
> Nezapínáme oznámení o nahrávání pro tento kurz, ale v článku [Příjem oznámení o nahrání souboru](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) s informacemi o tom, jak zpracovat oznámení o nahrání souboru.

## <a name="build-publish-and-deploy-modules"></a>Vytváření, publikování a nasazování modulů

Teď, když jsme provedli změny konfigurace, jsme připraveni vytvořit image a publikovat je do našeho registru kontejnerů Azure. Proces sestavení používá soubor deployment.template.json k určení, které moduly je třeba sestavit. Nastavení pro každý modul, včetně verze, naleznete v souboru module.json ve složce modulu. Proces sestavení nejprve spustí sestavení Dockeru na Dockerfiles odpovídající aktuální konfiguraci nalezené v souboru module.json k vytvoření bitové kopie. Potom publikuje bitovou kopii do registru ze souboru module.json se značkou verze odpovídající značce v souboru module.json. Nakonec vytvoří manifest nasazení specifické pro konfiguraci (například deployment.amd64.json), který nasadíme do zařízení IoT Edge. Zařízení IoT Edge čte informace z manifestu nasazení a na základě pokynů stáhne moduly, nakonfiguruje trasy a nastaví požadované vlastnosti. Tato metoda nasazení má dva vedlejší účinky, které byste měli znát:

* **Zpoždění nasazení:** vzhledem k tomu, že modul runtime IoT Edge musí rozpoznat změnu požadovaných vlastností, než začne překonfigurovat, může trvat nějakou dobu po nasazení modulů, dokud je modul runtime nezvedne a nezačne aktualizovat zařízení IoT Edge.

* **Verze modulu záležitost:** Pokud publikujete novou verzi kontejneru modulu do registru kontejneru pomocí stejné verze značky jako předchozí modul, modul runtime nebude stahovat novou verzi modulu. Provádí porovnání značky verze místní bitové kopie a požadované bitové kopie z manifestu nasazení. Pokud se tyto verze shodují, runtime neprovede žádnou akci. Proto je důležité, aby zvýšení verze modulu pokaždé, když chcete nasadit nové změny. Zvýšení verze změnou vlastnosti **version** pod vlastností **tag** v souboru module.json pro modul, který měníte. Potom vytvořte a publikujte modul.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Vytváření a publikování

1. V kódu Visual Studio na vývojovém virtuálním počítači otevřete okno terminálu kódu visual studia a přihlaste se do registru kontejnerů.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. V kódu Visual Studia klikněte pravým tlačítkem myši na deployment.template.json a zvolte **Build and Push IoT Edge Solution**.

### <a name="view-modules-in-the-registry"></a>Zobrazení modulů v registru

Po úspěšném dokončení sestavení budeme moct použít portál Azure ke kontrole našich publikovaných modulů.

1. Na webu Azure Portal přejděte do pracovního prostoru Azure Machine Learning a klikněte na hypertextový odkaz pro **registr**.

    ![Přechod na registr z pracovního prostoru služby strojového učení](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. V navigátoru na straně registru vyberte **možnost Repozitáře**.

3. Všimněte si, že oba moduly, které jste vytvořili, **avrofilewriter** a **turbofanrouter**, se zobrazí jako repozitáře.

4. Vyberte **turbodmychadlo** a všimněte si, že jste publikovali jeden obrázek označený jako 0.0.1-amd64.

   ![Zobrazit první označenou verzi turbodmychadla](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Nasazení modulů do zařízení IoT Edge

Vytvořili jsme a nakonfigurovali moduly v našem řešení, nyní nasadíme moduly do zařízení IoT Edge.

1. V kódu sady Visual Studio klikněte pravým tlačítkem myši na soubor **deployment.amd64.json** ve složce config.

2. Zvolte **Vytvořit nasazení pro jedno zařízení**.

3. Vyberte si zařízení IoT Edge, **aaTurboFanEdgeDevice**.

4. Aktualizujte panel zařízení Azure IoT Hub v Průzkumníku kódu Visual Studia. Měli byste vidět, že tři nové moduly jsou nasazeny, ale ještě nejsou spuštěny.

5. Po několika minutách znovu aktualizujte a uvidíte spuštěné moduly.

   ![Zobrazení spuštěných modulů v kódu sady Visual Studio](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Může trvat několik minut, než se moduly spustí a usadit se do stabilního provozního stavu. Během této doby se může zobrazit moduly spustit a zastavit při pokusu o navázání připojení s modulem rozbočovače IoT Edge.

## <a name="diagnosing-failures"></a>Diagnostika selhání

V této části sdílíme několik technik pro pochopení toho, co se stalo s modulem nebo moduly. Často selhání může být nejprve spatřen ze stavu v kódu sady Visual Studio.

### <a name="identify-failed-modules"></a>Identifikace neúspěšných modulů

* **Kód visual studia:** Podívejte se na panel zařízení Azure IoT Hub. Pokud většina modulů jsou ve spuštěném stavu, ale jeden je zastaven, je třeba prozkoumat, že zastavený modul dále. Pokud jsou všechny moduly v zastaveném stavu po dlouhou dobu, může to také znamenat selhání.

* **Portál Azure:** Přechodem do centra IoT na portálu a pak najít stránku podrobnosti o zařízení (v části IoT Edge, přejít k podrobnostem do zařízení), můžete zjistit, že modul ohlásil chybu nebo nikdy nic nenahlásil centru IoT.

### <a name="diagnosing-from-the-device"></a>Diagnostika ze zařízení

Po přihlášení k zařízení IoT Edge můžete získat přístup k velké množství informací o stavu vašich modulů. Hlavní mechanismus, který používáme, jsou příkazy Dockeru, které nám umožňují prozkoumat kontejnery a obrázky na zařízení.

1. Seznam všech spuštěné kontejnery. Očekáváme, že kontejner pro každý modul s názvem, který odpovídá modulu. Tento příkaz také uvádí přesný obrázek kontejneru včetně verze, takže můžete odpovídat vašemu očekávání. Můžete také seznam obrázků nahrazením "image" pro "kontejner" v příkazu.

   ```bash
   sudo docker container ls
   ```

2. Získejte protokoly pro kontejner. Tento příkaz výstupy, co bylo zapsáno do StdErr a StdOut v kontejneru. Tento příkaz funguje pro kontejnery, které byly spuštěny a pak zemřel z nějakého důvodu. Je také užitečné pro pochopení toho, co se děje s kontejnery edgeAgent nebo edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Zkontrolujte kontejner. Tento příkaz poskytuje tuny informací o obrázku. Data lze filtrovat v závislosti na tom, co hledáte. Jako příklad, pokud chcete zjistit, zda jsou vazby na avroFileWriter správné, můžete použít příkaz:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Připojte se k spuštěnému kontejneru. Tento příkaz může být užitečný, pokud chcete zkontrolovat kontejner, když je spuštěn:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Další kroky

V tomto článku jsme vytvořili řešení IoT Edge v kódu Sady Visual Studio se třemi moduly, tříděním, směrovačem a zapisovatelem a nahrazovatelem souborů. Nastavili jsme trasy, které umožňují modulům vzájemnou komunikaci na hraničním zařízení, upravili konfiguraci hraničního zařízení a aktualizovali soubory Dockerfiles, aby instalovaly závislosti a přidávaly připojení vazby do kontejnerů modulů. Dále jsme aktualizovali konfiguraci služby IoT Hub tak, aby směrovala naše zprávy na základě typu a zpracovávala nahrávání souborů. Se vším, co jsme měli na svém místě, jsme nasadili moduly do zařízení IoT Edge a zajistili, že moduly běží správně.

Více informací naleznete na následujících stránkách:

* [Nasazování modulů a vytváření tras ve službě IoT Edge](module-composition.md)
* [Syntaxe dotazu směrování zpráv IoT Hubu](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Směrování zpráv služby IoT Hub: nyní s směrováním v textu zprávy](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Nahrávání souborů s využitím služby IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Nahrávání souborů ze zařízení do cloudu pomocí služby IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Pokračujte k dalšímu článku a začněte odesílat data a podívejte se na své řešení v akci.

> [!div class="nextstepaction"]
> [Odesílání dat přes transparentní bránu](tutorial-machine-learning-edge-07-send-data-to-hub.md)
