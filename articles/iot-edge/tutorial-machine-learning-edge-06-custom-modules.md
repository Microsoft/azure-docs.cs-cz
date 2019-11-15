---
title: 'Kurz: vytvoření a nasazení vlastních modulů – Machine Learning v Azure IoT Edge'
description: 'Kurz: vytváření a nasazování IoT Edge modulů, které zpracovávají data ze seznamu zařízení prostřednictvím modelu strojového učení, a pak tyto přehledy odesílají IoT Hub.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7bfe620510d5ff88a20c518be1f4dd1fb422daa2
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106560"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Kurz: vytvoření a nasazení vlastních modulů IoT Edge

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

V tomto článku vytvoříme tři IoT Edge moduly, které přijímají zprávy ze zařízení ze seznamu, spustíte data prostřednictvím modelu strojového učení a pak dodáte přehledy k IoT Hub.

Centrum IoT Edge usnadňuje komunikaci s modulem. Použití centra IoT Edge jako zprostředkovatele zpráv uchovává moduly nezávislé na sobě. Moduly stačí zadat vstupy, na kterých přijetí zprávy a výstupy, ke kterým se zápis zpráv.

Chceme, aby zařízení IoT Edge pro nás dosáhlo čtyř věcí:

* Příjem dat z listových zařízení
* Předpověď zbývající doby životnosti (RUL) pro zařízení, které odesílá data
* Pošle zprávu jenom RUL, aby se zařízení dalo IoT Hub (Tato funkce se dá upravit tak, aby se zasílaly jenom v případě, že RUL klesne pod určitou úroveň).
* Uložte data na listovém zařízení do místního souboru na zařízení IoT Edge. Tento datový soubor se pravidelně nahrává do IoT Hub prostřednictvím nahrávání souborů za účelem upřesnění školení modelu Machine Learning. Použití nahrávání souborů místo konstantního streamování zpráv je cenově výhodnější.

K provedení těchto úloh používáme tři vlastní moduly:

* **Třídění RUL:** Modul turboFanRulClassifier, který jsme vytvořili v [výuce a nasazujeme model Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) , je standardní modul machine learningu, který zpřístupňuje vstup s názvem "amlInput" a výstup s názvem "amlOutput". "AmlInput" očekává, že jeho vstup vypadá přesně stejně jako vstup, který jsme poslali do webové služby založené na ACI. Podobně "amlOutput" vrací stejná data jako webová služba.

* **Zapisovač Avro:** Tento modul přijímá zprávy ve vstupu "avroModuleInput" a uchovává zprávu ve formátu Avro na disk pro pozdější nahrání do IoT Hub.

* **Modul směrovače:** Modul směrovače přijímá zprávy z podřízených zařízení a následně je formátuje a odesílá do třídění. Modul pak přijme zprávy ze klasifikátoru a předá zprávu do modulu zapisovače Avro. Nakonec modul pošle pouze předpověď RUL do IoT Hub.

  * Vztahují
    * **deviceInput**: přijímá zprávy ze zařízení typu list
    * **rulInput:** přijímá zprávy z "amlOutput".

  * Činnosti
    * **klasifikovat:** odesílá zprávy do amlInput.
    * **writeAvro:** odesílá zprávy "avroModuleInput".
    * **toIotHub:** odesílá zprávy do $upstream, které předá zprávy připojeným IoT Hub

Následující diagram znázorňuje moduly, vstupy, výstupy a trasy centra IoT Edge pro úplné řešení:

![Diagram architektury IoT Edge tří modulů](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Kroky v tomto článku jsou obvykle prováděny vývojářem cloudu.

## <a name="create-a-new-iot-edge-solution"></a>Vytvoření nového řešení IoT Edge

Během provádění druhé z našich dvou Azure Notebooks jsme vytvořili a publikovali image kontejneru obsahující náš model RUL. Azure Machine Learning jako součást procesu vytváření bitové kopie zabalený model, aby bylo možné image nasadit jako modul Azure IoT Edge. V tomto kroku vytvoříme Azure IoT Edge řešení pomocí modulu "Azure Machine Learning" a nasměruje modul na image, kterou jsme publikovali pomocí Azure Notebooks.

1. Otevřete na svém vývojovém počítači relaci vzdálené plochy.

2. Otevřete složku **C:\\zdrojové\\IoTEdgeAndMlSample** v Visual Studio Code.

3. Klikněte pravým tlačítkem na panel Průzkumník (v prázdném prostoru) a vyberte **nový IoT Edge řešení**.

    ![Vytvořit nové řešení IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Přijměte výchozí název řešení **EdgeSolution**.

5. Jako šablonu modulu vyberte **Azure Machine Learning** .

6. Pojmenujte modul **turbofanRulClassifier**.

7. Vyberte pracovní prostor Machine Learning.

8. Vyberte bitovou kopii, kterou jste vytvořili při spuštění poznámkového bloku Azure.

9. Podívejte se na řešení a Všimněte si souborů, které byly vytvořeny:

   * **Deployment. template. JSON:** Tento soubor obsahuje definici každého z modulů v řešení. Existují tři části, které vám umožní věnovat pozornost tomuto souboru:

     * **Přihlašovací údaje registru:** Definuje sadu vlastních registrů kontejnerů, které používáte ve vašem řešení. V současné době by měl obsahovat registr z pracovního prostoru Machine Learning, ve kterém je uložená image Azure Machine Learning. Můžete mít libovolný počet registrů kontejnerů, ale pro zjednodušení budeme používat tento jeden registr pro všechny moduly.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Moduly:** Tato část obsahuje sadu uživatelsky definovaných modulů, které procházejí tímto řešením. Všimněte si, že v této části jsou aktuálně dva moduly: SimulatedTemperatureSensor a turbofanRulClassifier. SimulatedTemperatureSensor byl nainstalován šablonou Visual Studio Code, ale pro toto řešení ji nepotřebujeme. Definici modulu SimulatedTemperatureSensor můžete z části moduly odstranit. Všimněte si, že definice modulu turbofanRulClassifier odkazuje na Image ve vašem registru kontejneru. Když do řešení přidáte více modulů, zobrazí se v této části.

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

     * **Trasy:** v tomto kurzu budeme pracovat s trasami poměrně v tomto kurzu. Trasy definují, jak spolu vzájemně komunikují moduly. Tyto dvě trasy definované šablonou neodpovídají směrování, které potřebujeme. První trasa pošle všechna data z libovolného výstupu klasifikátoru do IoT Hub ($upstream). Druhá trasa je určena pro SimulatedTemperatureSensor, kterou jsme právě odstranili. Odstraňte dvě výchozí trasy.

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

   * **Deployment. Debug. template. JSON:** tento soubor je ladicí verze souboru Deployment. template. JSON. Do tohoto souboru byste měli zrcadlit všechny změny z souboru Deployment. template. JSON.

   * **. env:** v tomto souboru byste měli zadávat uživatelské jméno a heslo pro přístup k vašemu registru.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. V Visual Studio Code Exploreru klikněte pravým tlačítkem na soubor Deployment. template. JSON a vyberte **sestavit IoT Edge řešení**.

11. Všimněte si, že tento příkaz vytvoří konfigurační složku se souborem Deployment. amd64. JSON. Tento soubor je konkrétní šablonou nasazení pro řešení.

## <a name="add-router-module"></a>Přidat modul směrovače

V dalším kroku přidáme modul směrovače do našeho řešení. Modul směrovače zpracovává několik odpovědností pro naše řešení:

* **Přijímat zprávy ze zařízení na listech: když se zprávy dostanou** do IoT Edge zařízení ze zařízení pro příjem dat, modul router zprávu přijme a začne orchestrovat směrování zprávy.
* **Odeslat zprávy do modulu třídění RUL:** při přijetí nové zprávy ze zařízení pro příjem dat modul směrovače transformuje zprávu na formát, který klasifikátor RUL očekává. Směrovač pošle zprávu do klasifikátoru RUL pro předpověď RUL. Jakmile klasifikátor provede předpověď, pošle zprávu zpět do modulu směrovače.
* **Posílání zpráv RUL do IoT Hub:** když směrovač přijímá zprávy od klasifikátoru, transformuje zprávu tak, aby obsahovala jenom základní informace, ID zařízení a RUL, a pošle zkrácenou zprávu do služby IoT Hub. Další vylepšení, které jsme tady neudělali, by poslalo zprávy do IoT Hub jenom v případě, že předpověď RUL klesne pod prahovou hodnotu (například když je RUL menší než 100 cyklů). Filtrování tímto způsobem omezuje objem zpráv a snižuje náklady centra IoT.
* **Poslat zprávu do modulu zapisovače Avro:** Pokud chcete zachovat všechna data odesílaná zařízením pro příjem dat, modul router pošle celou zprávu přijatou z třídění do modulu zapisovače Avro, který zachová a nahraje data pomocí IoT Hubho nahrání souboru.

> [!NOTE]
> Popis odpovědností modulu může být zpracování sekvenční, ale tok je založen na zprávě nebo události. Důvodem je, že potřebujeme modul orchestrace, jako je náš modul směrovače.

### <a name="create-module-and-copy-files"></a>Vytvořit modul a kopírovat soubory

1. Klikněte pravým tlačítkem na složku moduly v Visual Studio Code a vyberte **přidat IoT Edge modul**.

2. Vyberte  **C# modul**.

3. Pojmenujte modul **turbofanRouter**.

4. Po zobrazení výzvy pro úložiště imagí Docker použijte registr z pracovního prostoru Machine Learning (registr najdete v uzlu registryCredentials souboru *Deployment. template. JSON* ). Tato hodnota je plně kvalifikovaná adresa registru, například **\<registru\>. azurecr.IO/turbofanrouter**.

    > [!NOTE]
    > V tomto článku používáme Azure Container Registry vytvořeného pracovním prostorem Azure Machine Learning, který jsme použili ke školení a nasazení našeho třídění. To je čistě pro pohodlí. Mohli jsme vytvořit nový registr kontejnerů a publikovat naše moduly tam.

5. Otevřete nové okno terminálu v Visual Studio Code (**zobrazit** > **terminálu**) a zkopírujte soubory z adresáře modulů.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Po zobrazení výzvy k přepsání program.cs stiskněte `y` a potom stiskněte `Enter`.

### <a name="build-router-module"></a>Modul směrovače sestavení

1. V Visual Studio Code vyberte **terminál** > **konfigurovat výchozí úlohu sestavení**.

2. Klikněte na **vytvořit Tasks. JSON soubor ze šablony**.

3. Klikněte na **.NET Core**.

4. Když Tasks. JSON otevře, nahradí obsah:

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

5. Uložte a zavřete Tasks. JSON.

6. Spusťte sestavování pomocí `Ctrl + Shift + B` nebo **terminálu** > **Spustit úlohu sestavení**.

### <a name="set-up-module-routes"></a>Nastavení směrování modulů

Jak je uvedeno výše, modul runtime IoT Edge používá trasy nakonfigurované v souboru *Deployment. template. JSON* ke správě komunikace mezi volně vázanými moduly. V této části se naučíme, jak nastavit trasy pro modul turbofanRouter. Nejprve pokryjeme vstupní trasy a potom se přesunou na výstupy.

#### <a name="inputs"></a>Vstupy

1. V metodě Init () pro Program.cs zaregistrujeme dvě zpětná volání pro modul:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. První zpětné volání naslouchá zprávám odeslaným do jímky **deviceInput** . Z diagramu výše uvidíme, že chceme směrovat zprávy z libovolného listového zařízení do tohoto vstupu. V souboru *Deployment. template. JSON* přidejte trasu, která dává hraničnímu centru pokyn ke směrování všech zpráv přijatých IoT Edge zařízením, které se neodeslaly modulem IoT Edge do vstupu s názvem "deviceInput" v modulu turbofanRouter:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Dále přidejte trasu pro zprávy z modulu rulClassifier do modulu turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Výstupy

Přidejte čtyři další trasy do parametru trasy $edgeHub, abyste mohli zpracovávat výstupy z modulu směrovače.

1. Program.cs definuje metodu SendMessageToClassifier (), která používá klienta modulu k odeslání zprávy do třídění RUL pomocí trasy:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub () používá klienta modulu k posílání jenom RUL dat zařízení do IoT Hub prostřednictvím trasy:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter () používá klienta modulu k odeslání zprávy s daty RUL přidanými do modulu avroFileWriter.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage () odesílá nezdařené zprávy, které IoT Hub, kde mohou být směrovány pro pozdější použití.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Všechny trasy společně s uzlem "$edgeHub" by měly vypadat jako následující JSON:

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
> Přidáním modulu turbofanRouter jste vytvořili následující další trasu: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Odeberte tuto trasu a nechte pouze trasy uvedené výše v souboru Deployment. template. JSON.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Kopírovat trasy do nasazení. Debug. template. JSON

Jako poslední krok, pokud chcete, aby se soubory udržovaly synchronizované, zrcadlí změny, které jste udělali v souboru Deployment. template. JSON v souboru Deployment. Debug. template. JSON.

## <a name="add-avro-writer-module"></a>Přidat modul Avro Writer

Modul pro zápis Avro má dvě zodpovědnosti v našem řešení, aby bylo možné ukládat zprávy a nahrávat soubory.

* **Zprávy ze Storu**: když modul Avro Writer obdrží zprávu, zapíše zprávu do místního systému souborů ve formátu Avro. Použijeme připojení BIND, které připojí adresář (v tomto případě/data/avrofiles) do cesty v kontejneru modulu. Toto připojení umožňuje modulu zapisovat do místní cesty (/avrofiles) a tyto soubory zpřístupnit přímo z IoT Edgeho zařízení.

* **Nahrání souborů**: modul zapisovače Avro využívá funkci nahrávání souborů Azure IoT Hub k nahrání souborů do účtu služby Azure Storage. Po úspěšném nahrání souboru modul odstraní soubor z disku.

### <a name="create-module-and-copy-files"></a>Vytvořit modul a kopírovat soubory

1. V paletě příkazů vyhledejte a vyberte **Python: vybrat Interpret**.

1. Vyberte překladač nalezený v C:\\Python37.

1. Znovu otevřete paletu příkazů a vyhledejte a vyberte **terminál: Vyberte výchozí prostředí**.

1. Po zobrazení výzvy vyberte **příkazový řádek**.

1. Otevřete nové Terminálové prostředí, **terminál** > **nového terminálu**.

1. Klikněte pravým tlačítkem na složku moduly v Visual Studio Code a vyberte **přidat IoT Edge modul**.

1. Zvolte **Modul Python**.

1. Pojmenujte modul "avroFileWriter".

1. Po zobrazení výzvy pro úložiště imagí Docker použijte stejný registr, jako jste použili při přidávání modulu směrovače.

1. Zkopírujte soubory z ukázkového modulu do řešení.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Pokud se zobrazí výzva k přepsání main.py, zadejte `y` a potom stiskněte `Enter`.

1. Všimněte si, že filemanager.py a schema.py byly přidány do řešení a aktualizace main.py byla aktualizována.

> [!NOTE]
> Když otevřete soubor Pythonu, může se zobrazit výzva k instalaci Pylint. K dokončení tohoto kurzu nemusíte instalovat linter.

### <a name="bind-mount-for-data-files"></a>Připojení vazby pro datové soubory

Jak je uvedeno v úvodu, modul zapisovače závisí na přítomnosti připojení BIND k zápisu souborů Avro do systému souborů zařízení.

#### <a name="add-directory-to-device"></a>Přidat adresář do zařízení

1. Připojte se k VIRTUÁLNÍmu počítači s IoT Edge zařízení pomocí SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Vytvořte adresář, který bude obsahovat uložené zprávy na listovém zařízení.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Aktualizujte oprávnění adresáře tak, aby kontejner mohl zapisovat.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Ověřte, jestli má adresář teď oprávnění zapisovat (w) pro uživatele, skupinu a vlastníka.

   ```bash
   ls -la /data
   ```

   ![Oprávnění adresáře pro avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Přidat adresář do modulu

Pro přidání adresáře do kontejneru modulu změníme fázemi přidružené k modulu avroFileWriter. K modulu jsou přidruženy tři fázemi: souboru Dockerfile. AMD64, souboru Dockerfile. amd64. Debug a souboru Dockerfile. arm32v7. Tyto soubory by měly být uchovávány v synchronizaci v případě, že chceme ladit nebo nasazovat na zařízení arm32. V tomto článku se zaměřte jenom na souboru Dockerfile. amd64.

1. Na svém vývojovém počítači otevřete soubor **souboru Dockerfile. amd64** .

2. Upravte soubor tak, aby vypadal jako v následujícím příkladu:

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

   Příkazy `mkdir` a `chown` instruují proces sestavení Docker, aby vytvořil adresář nejvyšší úrovně s názvem/avrofiles v imagi a pak moduleuser vlastníka tohoto adresáře. Je důležité, aby tyto příkazy byly vloženy po přidání uživatele modulu do obrázku pomocí příkazu `useradd` a před přepnutím kontextu do moduleuser (USER moduleuser).

3. Proveďte odpovídající změny v souboru Dockerfile. amd64. Debug a souboru Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizovat konfiguraci modulu

Posledním krokem při vytváření vazby je aktualizace souborů Deployment. template. JSON (a Deployment. Debug. template. JSON) s informacemi o vazbě.

1. Otevřete Deployment. template. JSON.

2. Upravte definici modulu pro avroFileWriter přidáním parametru `Binds`, který ukazuje adresář kontejneru/avrofiles na místní adresář na hraničním zařízení. Definice modulu by měla odpovídat následujícímu příkladu:

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

3. Proveďte příslušné změny v nasazení. Debug. template. JSON.

### <a name="bind-mount-for-access-to-configyaml"></a>Připojení ke svázání pro přístup ke config. yaml

Musíme pro modul zapisovače přidat ještě jednu další datovou vazby. Tato vazba poskytuje modulu přístup ke čtení připojovacího řetězce ze souboru/etc/iotedge/config.yaml na zařízení IoT Edge. K vytvoření IoTHubClient potřebujeme připojovací řetězec, abychom mohli volat metodu upload\_BLOB\_Async k nahrání souborů do služby IoT Hub. Postup pro přidání této vazby je podobný těm, které jsou uvedené v předchozí části.

#### <a name="update-directory-permission"></a>Aktualizovat oprávnění adresáře

1. Připojte se k zařízení IoT Edge pomocí SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Přidejte do souboru config. yaml oprávnění ke čtení.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Ověřte, že jsou oprávnění nastavena správně.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Ujistěte se, že oprávnění pro config. yaml jsou **-r--r--r--** .

#### <a name="add-directory-to-module"></a>Přidat adresář do modulu

1. Na svém vývojovém počítači otevřete soubor **souboru Dockerfile. amd64** .

2. Přidejte další sadu `mkdir` a `chown` příkazů do souboru tak, aby vypadala takto:

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

3. Proveďte odpovídající změny v souboru Dockerfile. amd64. Debug a souboru Dockerfile. arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizovat konfiguraci modulu

1. Otevřete soubor **Deployment. template. JSON** .

2. Upravte definici modulu pro avroFileWriter přidáním druhého řádku do parametru `Binds`, který odkazuje adresář kontejneru (/App/iotconfig) na místní adresář na zařízení (/etc/iotedge).

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

3. Proveďte příslušné změny v nasazení. Debug. template. JSON.

## <a name="install-dependencies"></a>Instalace závislostí

Modul zapisovače přebírá závislost na dvou knihovnách Pythonu, fastavro a PyYAML. Musíme nainstalovat závislosti na našem vývojovém počítači a dát pokyn procesu Docker, aby je nainstaloval do image našeho modulu.

### <a name="pyyaml"></a>PyYAML

1. Na svém vývojovém počítači otevřete soubor **. txt s požadavky** a přidejte pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Otevřete soubor **souboru Dockerfile. amd64** a přidejte příkaz `pip install` pro upgrade setuptools.

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

3. Proveďte odpovídající změny souboru Dockerfile. amd64. Debug. <!--may not be necessary. Add 'if needed'?-->

4. Místní instalace pyyaml otevřením terminálu v Visual Studio Code a zadáním

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. V požadavcích. txt přidejte fastavro po pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Nainstalujte fastavro do vývojového počítače pomocí terminálu Visual Studio Code.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Překonfigurujte IoT Hub

Představujeme IoT Edge zařízení a moduly do systému jsme změnili očekávané informace o tom, jaká data se budou odesílat do centra a za jaký účel. Abychom se mohli vypořádat s naší novou realitou, musíme směrování v centru překonfigurovat.

> [!NOTE]
> Před nasazením modulů jsme provedli překonfigurování rozbočovače, protože některá nastavení centra, konkrétně nahrávání souborů, musí být správně nastavená, aby se správně spouštěl modul avroFileWriter.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Nastavení trasy pro zprávy RUL v IoT Hub

Když je směrovač a klasifikátor zavedený, očekáváme, že budete dostávat pravidelné zprávy obsahující jenom ID zařízení a RUL předpověď pro dané zařízení. Chceme směrovat RUL data na vlastní umístění úložiště, kde můžeme monitorovat stav zařízení, sestavovat sestavy a aktivovat výstrahy podle potřeby. Zároveň chceme, aby se všechna data zařízení, která se pořád odesílají přímo na listovém zařízení, které ještě není připojené k našemu IoT Edge zařízení, pokračovala v směrování do aktuálního umístění úložiště.

#### <a name="create-a-rul-message-route"></a>Vytvoření trasy zpráv RUL

1. V Azure Portal přejděte na IoT Hub.

2. V levém navigačním panelu vyberte **směrování zpráv**.

3. Vyberte **Přidat**.

4. Pojmenujte **RulMessageRoute**trasy.

5. Vyberte **Přidat** vedle možnosti selektor **koncových bodů** a zvolte **úložiště objektů BLOB**.

6. Ve formuláři **přidat koncový bod úložiště zadejte** název koncového bodu **ruldata**.

7. Vyberte vybrat **kontejner**.

8. Vyberte účet úložiště, který se používá v celém rámci tohoto kurzu, který se jmenuje jako **iotedgeandml\<jedinečných\>přípon** .

9. Zvolte kontejner **ruldata** a klikněte na **Vybrat**.

10. Kliknutím na **vytvořit** vytvořte koncový bod úložiště.

11. Pro **dotaz směrování**zadejte následující dotaz:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Rozbalte část **test** a pak část **tělo zprávy** . Nahraďte zprávu tímto příkladem očekávaných zpráv:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Vyberte možnost **testovací trasa**. Pokud je test úspěšný, zobrazí se zpráva, že dotaz odpovídá dotazu.

14. Klikněte na možnost **Uložit**.

#### <a name="update-turbofandevicetostorage-route"></a>Aktualizovat turbofanDeviceToStorage trasu

Nechceme směrovat nová data předpovědi do našeho starého umístění úložiště, takže aktualizujte trasu tak, aby se zabránilo.

1. Na stránce **směrování zpráv** IoT Hub vyberte kartu **trasy** .

2. Vyberte **turbofanDeviceDataToStorage**nebo libovolný název, který jste přiřadili k počátečnímu směrování dat zařízení.

3. Aktualizovat dotaz směrování na

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Rozbalte část **test** a pak část **tělo zprávy** . Nahraďte zprávu tímto příkladem očekávaných zpráv:

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

5. Vyberte možnost **testovací trasa**. Pokud je test úspěšný, zobrazí se zpráva, že dotaz odpovídá dotazu.

6. Vyberte **Uložit**.

### <a name="configure-file-upload"></a>Konfigurace odesílání souborů

Nakonfigurujte funkci nahrávání souborů IoT Hub, aby modul zapisovače souborů mohl nahrávat soubory do úložiště.

1. Z levého navigátoru v IoT Hub vyberte **nahrávání souboru**.

2. Vyberte **Azure Storage kontejner**.

3. V seznamu vyberte svůj účet úložiště.

4. Vyberte kontejner **uploadturbofanfiles** a klikněte na **Vybrat**.

5. Vyberte **Uložit**. Portál vás upozorní, až se uložení dokončí.

> [!Note]
> Pro tento kurz nezapneme oznámení o nahrání, ale další informace o tom, jak zpracovávat oznámení o nahrávání souborů, najdete v tématu [přijetí oznámení o nahrání souboru](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) .

## <a name="build-publish-and-deploy-modules"></a>Vytváření, publikování a nasazování modulů

Teď, když jsme udělali změny konfigurace, jsme připraveni sestavit image a publikovat je do našeho služby Azure Container Registry. Proces sestavení používá soubor Deployment. template. JSON k určení, které moduly je třeba sestavit. Nastavení pro každý modul, včetně verze, se nachází v souboru Module. JSON ve složce modul. Proces sestavení nejprve spustí sestavení Docker na fázemi, které odpovídá aktuální konfiguraci nalezené v souboru Module. JSON pro vytvoření image. Pak publikuje image do registru ze souboru Module. JSON se značkou verze, která odpovídá značce v souboru Module. JSON. Nakonec vytvoří manifest nasazení specifický pro konfiguraci (například Deployment. amd64. JSON), který nasadíme do zařízení IoT Edge. Zařízení IoT Edge přečte informace z manifestu nasazení a na základě pokynů bude stahovat moduly, konfigurovat trasy a nastavit požadované vlastnosti. Tato metoda nasazení má dvě vedlejší účinky, o kterých byste měli vědět:

* **Prodleva nasazení:** vzhledem k tomu, že modul runtime IoT Edge musí rozpoznat změnu požadovaných vlastností před tím, než se začne znovu konfigurovat, může trvat i několik času po nasazení modulů, dokud je modul runtime nevybere a začne aktualizovat IoT Edge zařízení.

* **Verze modulu:** Pokud publikujete novou verzi kontejneru modulu do registru kontejneru pomocí stejných značek verze jako předchozí modul, modul runtime nebude stahovat novou verzi modulu. Jedná se o porovnání značky verze místní image a požadované image z manifestu nasazení. Pokud se tyto verze shodují, modul runtime neprovede žádnou akci. Proto je důležité zvýšit verzi modulu pokaždé, když chcete nasadit nové změny. Zvyšte verzi tak, že změníte vlastnost **verze** v souboru Module. JSON pro modul, který měníte, na vlastnost **značka** . Pak Sestavte a publikujte modul.

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

### <a name="build-and-publish"></a>Sestavování a publikování

1. V Visual Studio Code na vývojovém VIRTUÁLNÍm počítači otevřete Visual Studio Code okno terminálu a přihlaste se k registru kontejneru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. V Visual Studio Code klikněte pravým tlačítkem na Deployment. template. JSON a vyberte **sestavení a nabízené IoT Edge řešení**.

### <a name="view-modules-in-the-registry"></a>Zobrazit moduly v registru

Po úspěšném dokončení sestavení budeme moci použít Azure Portal ke kontrole publikovaných modulů.

1. V Azure Portal přejděte do pracovního prostoru Azure Machine Learning a klikněte na hypertextový odkaz pro **registr**.

    ![Přechod k registru z pracovního prostoru služby Machine Learning](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Z navigátoru na straně registru vyberte **úložiště**.

3. Všimněte si, že oba moduly, které jste vytvořili, **avrofilewriter** a **turbofanrouter**, se zobrazí jako úložiště.

4. Vyberte **turbofanrouter** a Všimněte si, že jste publikovali jeden obrázek označený jako 0.0.1-amd64.

   ![Zobrazit první označenou verzi turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Nasazení modulů pro IoT Edge zařízení

V našem řešení jsme sestavili a nakonfigurovali moduly, teď tyto moduly nasadíme do zařízení IoT Edge.

1. V Visual Studio Code klikněte pravým tlačítkem myši na soubor **Deployment. amd64. JSON** ve složce config.

2. Vyberte **vytvořit nasazení pro jedno zařízení**.

3. Vyberte zařízení IoT Edge **aaTurboFanEdgeDevice**.

4. Aktualizujte panel zařízení Azure IoT Hub v Průzkumníkovi Visual Studio Code. Měli byste vidět, že jsou nasazené tři nové moduly, ale ještě nejsou spuštěné.

5. Obnovte se znovu za několik minut a zobrazí se moduly, které jsou spuštěné.

   ![Zobrazit spuštěné moduly v Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Může trvat několik minut, než se moduly spustí a narovnávají do stabilního běžícího stavu. Během této doby se můžou zobrazit moduly, které se spouštějí a zastavují, protože se snaží navázat spojení s modulem IoT Edge hub.

## <a name="diagnosing-failures"></a>Diagnostikování selhání

V této části sdílíme několik techniků pro porozumění tomu, co se v modulu nebo modulech pokazilo. Selhání je často možné nejprve Spotted ze stavu v Visual Studio Code.

### <a name="identify-failed-modules"></a>Identifikujte moduly, které selhaly

* **Visual Studio Code:** Podívejte se na panel zařízení Azure IoT Hub. Pokud je většina modulů ve spuštěném stavu, ale jeden z nich je zastavený, musíte tento zastavený modul dále prozkoumat. Pokud jsou všechny moduly v zastaveném stavu po dlouhou dobu, může to znamenat také selhání.

* **Azure Portal:** Když přejdete na portál IoT Hub na portálu a pak vyhledáte stránku s podrobnostmi o zařízení (v části IoT Edge přejdete do svého zařízení), může se stát, že modul ohlásil chybu nebo nikdy nenahlásil cokoli službě IoT Hub.

### <a name="diagnosing-from-the-device"></a>Diagnostika ze zařízení

Když se přihlásíte do zařízení IoT Edge, získáte přístup k dobrým informacím o stavu modulů. Hlavním mechanismem, který používáme, jsou příkazy Docker, které nám umožňují prošetřit kontejnery a image na zařízení.

1. Vypíše všechny spuštěné kontejnery. Očekáváme, že se pro každý modul zobrazí kontejner s názvem, který odpovídá modulu. Tento příkaz také vypíše přesný obrázek kontejneru, včetně verze, abyste se mohli shodovat s očekáváním. Můžete také zobrazit seznam obrázků nahrazením "image" pro "Container" v příkazu.

   ```bash
   sudo docker container ls
   ```

2. Získání protokolů pro kontejner. Tento příkaz provede výstup všech zápisů do StdErr a StdOut v kontejneru. Tento příkaz funguje u kontejnerů, které byly spuštěny a poté z nějakého důvodu zemřely. Je také užitečné pochopit, co se děje s kontejnery edgeAgent nebo edgeHub.

   ```bash
   sudo docker container logs <container name>
   ```

3. Kontrola kontejneru. Tento příkaz poskytuje spoustu informací o imagi. Data je možné filtrovat podle toho, co hledáte. Například pokud chcete zjistit, zda jsou vazby na avroFileWriter správné, můžete použít příkaz:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Připojte se ke spuštěnému kontejneru. Tento příkaz může být užitečný, pokud chcete prošetřit kontejner, když je spuštěný:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Další kroky

V tomto článku jsme vytvořili řešení IoT Edge v Visual Studio Code se třemi moduly, klasifikátorem, směrovačem a zapisovačem nebo odesláním souborů. Nastavili jsme trasy, aby mohly moduly vzájemně komunikovat na hraničním zařízení, upravili konfiguraci hraničního zařízení a aktualizovali fázemi pro instalaci závislostí a přidali připojení vazby k kontejnerům modulů. Dále jsme aktualizovali konfiguraci IoT Hub pro směrování našich zpráv na základě typu a pro zpracování nahrávání souborů. Na všech místech jsme nasadili moduly na zařízení IoT Edge a zajistili, že moduly běžely správně.

Další informace najdete na následujících stránkách:

* [Nasazování modulů a vytváření tras ve službě IoT Edge](module-composition.md)
* [Syntaxe dotazu směrování zpráv IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Směrování zpráv IoT Hub: teď se směrováním na text zprávy](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Nahrávání souborů s využitím služby IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Nahrání souborů ze zařízení do cloudu pomocí IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Přejděte k dalšímu článku a začněte posílat data a podívejte se na vaše řešení v akci.

> [!div class="nextstepaction"]
> [Posílání dat přes transparentní bránu](tutorial-machine-learning-edge-07-send-data-to-hub.md)
