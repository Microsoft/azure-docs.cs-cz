---
title: Vytvoření a nasazení vlastních modulů – Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: Vytvořit a nasadit moduly IoT Edge, které zpracovávají data ze zařízení typu list prostřednictvím modelu strojového učení a přehledy pak můžete odeslat do služby IoT Hub.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16c32fc14805ac8ae1412671b2bb400456b4ab7d
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603648"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Kurz: Vytvoření a nasazení vlastních modulů IoT Edge

> [!NOTE]
> Tento článek je součástí série kurz o používání Azure Machine learningu na hraničních zařízeních IoT. Pokud jste jste dostali přímo v tomto článku, doporučujeme vám začneme [nejprve článek](tutorial-machine-learning-edge-01-intro.md) v řadě nejlepších výsledků.

V tomto článku se nám vytvořit tři moduly IoT Edge, které příjem zpráv ze zařízení typu list, spusťte data prostřednictvím váš model strojového učení a potom vpřed insights do služby IoT Hub.

Centrum IoT Edge umožňuje komunikaci modulu do modulu. Pomocí IoT Edge hub jako zprostředkovatel zpráv uchovává moduly nezávisle na sobě navzájem. Moduly stačí zadat vstupy, na kterých přijetí zprávy a výstupy, ke kterým se zápis zpráv.

Chceme, aby zařízení IoT Edge pro nás provést čtyři kroky:

* Příjem dat ze zařízení typu list
* Pro zařízení, který data odeslal předpovídat zbývající doby životnosti
* Odeslala se zpráva s pouze zbývající doby životnosti zařízení do služby IoT Hub (Tato funkce může upravit tak, aby odesílala jen data, pokud zbývající doby životnosti klesne pod určitou úroveň)
* Data zařízení typu list uložte do místního souboru na zařízení IoT Edge. Tento datový soubor je pravidelně odesílat do služby IoT Hub přes nahrávání souborů pro upřesnění trénování modelu strojového učení. Pomocí možnosti nahrávání souborů místo streamování konstantní zprávy je nákladově efektivní.

K provedení následujících úkolů, můžeme použít tři vlastních modulů:

* **Zbývající doby životnosti třídění:** Modul turboFanRulClassifier jsme vytvořili v [trénování a nasadit model ve službě Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md) je modul learning standardní počítače, která poskytuje vstup s názvem "amlInput" a výstup nazvaný "amlOutput". "amlInput" očekává, že jeho vstupem vypadat přesně jako vstup, který jsme vám poslali na základě ACI webovou službu. Podobně "amlOutput" vrátí stejná data jako webovou službu.

* **Avro zapisovače:** Tento modul přijímá zprávy na vstupu "avroModuleInput" a opakuje zprávy ve formátu Avro na disk pro novější nahrávání do služby IoT Hub.

* **Směrovač modul:** Směrovač modul přijímá zprávy z podřízenými zařízeními typu list, potom formátuje a odesílá zprávy do třídění. V modulu pak přijme zprávy z třídění a předá zprávu do modulu zapisovače Avro. Nakonec modul odešle jenom predikcí zbývající doby životnosti do služby IoT Hub.

  * Vstupy:
    * **deviceInput**: přijímá zprávy ze zařízení typu list
    * **rulInput:** přijímá zprávy z "amlOutput"

  * Výstupy:
    * **klasifikace:** odešle zprávy do "amlInput"
    * **writeAvro:** odesílá zprávy "avroModuleInput"
    * **toIotHub:** odesílá zprávy do $upstream, který předává zprávy do připojeného centra IoT

Následující diagram znázorňuje moduly, vstupy, výstupy a Centrum IoT Edge trasy pro kompletní řešení:

![Diagram architektury moduly IoT Edge tři](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Kroky v tomto článku se obvykle provádí vývojář cloudu.

## <a name="create-a-new-iot-edge-solution"></a>Vytvoření nového řešení IoT Edge

Během provádění druhého naše dva poznámkové bloky Azure jsme vytvořeny a publikovány image kontejneru obsahující našeho modelu zbývající doby životnosti. Azure Machine Learning, jako součást procesu vytváření image vytvořené v části a vytvořit image nasadit jako modul služby Azure IoT Edge. V tomto kroku jsme se chystáte vytvořit vaše řešení Azure IoT Edge s využitím modulu "Azure Machine Learning" a image, kterou jsme zveřejnili pomocí poznámkových bloků Azure přejděte na příkaz modulu.

1. Otevře relaci vzdálené plochy do svého vývojového počítače.

2. Otevřít složku **C:\\zdroj\\IoTEdgeAndMlSample** ve Visual Studio Code.

3. Klikněte pravým tlačítkem myši klikněte na panelu Průzkumníku (v prázdné místo) a vyberte **nové řešení IoT Edge**.

    ![Vytvoření nového řešení IoT Edge](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. Přijměte výchozí název řešení **EdgeSolution**.

5. Zvolte **Azure Machine Learning** jako šablona modulu.

6. Název modulu **turbofanRulClassifier**.

7. Zvolte váš pracovní prostor machine learning.

8. Vyberte bitovou kopii, kterou jste vytvořili při spuštění poznámkového bloku Azure.

9. Podívejte se na řešení a Všimněte si, že soubory, které byly vytvořeny:

   * **deployment.template.json:** Tento soubor obsahuje definici jednotlivých modulů v řešení. Existují tři části, je potřeba věnovat pozornost v tomto souboru:

     * **Přihlašovací údaje registru:** Definuje sadu registrů kontejnerů vlastní, které používáte ve svém řešení. Pravé teď by měl obsahovat registru z pracovního prostoru machine learning, byla uložené image Azure Machine Learning. Můžete mít libovolný počet registrů kontejnerů, ale pro zjednodušení budeme používat tento jeden registr pro všechny moduly

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io”
         }
       }
       ```

     * **Moduly:** Tato část obsahuje sadu uživatelem definované moduly, které s tímto řešením. Všimnete si, že tato část aktuálně obsahuje dva moduly: tempSensor a turbofanRulClassifier. TempSensor byla nainstalována pomocí šablony Visual Studio Code, ale ten nepotřebujeme pro toto řešení. Můžete odstranit definici tempSensor modulu z části moduly. Všimněte si, že definice modulu turbofanRulClassifier odkazuje na image v registru kontejneru. Jak jsme do řešení přidat více modulů, zobrazí se v této části.

       ```json
       "modules": {
         "tempSensor": {
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

     * **Postupy:** budeme pracovat s trasami odlišují v tomto kurzu. Trasy definovat, jak moduly komunikovat mezi sebou. Dvě trasy definované v šabloně se neshodují s směrování, které potřebujeme. První trasa odešle všechna data z libovolné výstupu třídění do služby IoT Hub ($upstream). Druhá trasa se pro tempSensor, který jsme právě odstraněn. Odstraňte dvě výchozí trasy.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment.Debug.template.JSON:** tento soubor je ladicí verzi deployment.template.json. Všechny změny z deployment.template.json jsme měli zrcadlení do tohoto souboru.

   * **.env:** tento soubor je, kde by mělo nabízet uživatelské jméno a heslo pro přístup k vašemu registru.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Klikněte pravým tlačítkem na soubor deployment.template.json v Průzkumníku Visual Studio Code a vyberte **sestavit řešení IoT Edge**.

11. Všimněte si, že tento příkaz vytvoří složku konfiguračním souborem deployment.amd64.json. Tento soubor je šablona konkrétní nasazení pro řešení.

## <a name="add-router-module"></a>Přidat modul směrovače

V dalším kroku přidáme modul směrovače na naše řešení. Modul směrovače zpracovává několik odpovědnost za naše řešení:

* **Příjem zpráv ze zařízení typu list:** při doručování zpráv: do zařízení IoT Edge z podřízené zařízení, modul směrovač obdrží zprávu a začne Orchestrace směrování zprávy.
* **Odesílání zpráv do modulu zbývající doby životnosti třídění:** po přijetí nové zprávy z podřízené zařízení modulu směrovač transformuje zprávy do formátu, který očekává třídění hodnot RUL. Směrovač odesílá zprávy na zbývající doby životnosti třídění pro predikci zbývající doby životnosti. Po provedení predikcí třídění odešle zprávu zpět do modulu směrovače.
* **Odesílání zpráv zbývající doby životnosti do služby IoT Hub:** Pokud směrovači obdrží zprávy z třídění, transformuje zprávy tak, aby obsahovala pouze základní informace, ID zařízení a zbývající doby životnosti a odešle zkrácený zprávy do služby IoT hub. Další upřesnění, které jsme tady neučinili, bude posílat zprávy do služby IoT Hub, pouze v případě, že predikcí zbývající doby životnosti nedosahuje prahové hodnoty (například, když zbývající doby životnosti je méně než 100 cykly). Filtrování tímto způsobem byste snížit množství zpráv a snížit náklady na služby IoT hub.
* **Odeslat zprávu do modulu Avro zapisovače:** zachovat veškerá data odeslaná podřízené zařízení, modul směrovač odesílá celou zprávu přijatou ze třídění pro modul Avro zapisovače, který bude zachována a nahrajte data s využitím služby IoT Hub souboru nahrajte.

> [!NOTE]
> Popis odpovědnosti modulu může provádět zpracování, vypadá to, že sekvenční, ale tok je zpráva/založené na události. To je důvod, proč potřebujeme modul Orchestrace jako naše směrovače modulu.

### <a name="create-module-and-copy-files"></a>Vytvoření modulu a kopírovat soubory

1. Klikněte pravým tlačítkem na složku moduly ve Visual Studio Code a zvolte **přidat modul IoT Edge**.

2. Zvolte  **C# modulu**.

3. Název modulu **turbofanRouter**.

4. Po zobrazení výzvy pro vaše úložiště Imagí Dockeru, použijte registru z pracovního prostoru machine learning (registru najdete v uzlu registryCredentials vaše *deployment.template.json* souboru). Tato hodnota je plně kvalifikovanou adresu do registru, jako je třeba  **\<registru\>.azurecr.io/turbofanrouter**.

    > [!NOTE]
    > V tomto článku používáme Azure Container Registry vytvořené pracovní prostor služby Azure Machine Learning, který jsme použili trénovat a nasazovat naše třídění. To se čistě ke zvýšení pohodlí. Jsme mohli vytvořit nový registr kontejneru a naše moduly není publikována.

5. Otevřete nové okno terminálu ve Visual Studio Code (**zobrazení** > **terminálu**) a zkopírujte soubory v adresáři modulů.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Po zobrazení výzvy k přepsání souboru program.cs stiskněte `y` a pak klikněte na tlačítko `Enter`.

### <a name="build-router-module"></a>Vytvořit modul směrovače

1. Ve Visual Studio Code, vyberte **terminálu** > **nakonfigurovat výchozí Build Task**.

2. Klikněte na **vytvořit soubor tasks.json ze šablony**.

3. Klikněte na **.NET Core**.

4. Po otevření tasks.json nahraďte jeho obsah se:

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

5. Uložte a zavřete tasks.json.

6. Spuštění sestavení s `Ctrl + Shift + B` nebo **terminálu** > **spustit úlohu sestavení**.

### <a name="set-up-module-routes"></a>Nastavit modul trasy

Jak je uvedeno výše, používá modul runtime IoT Edge trasy nakonfigurované v *deployment.template.json* souboru pro správu komunikace mezi volně vázány moduly. V této části přejdeme jak nastavit pro modul turbofanRouter trasy. Budeme nejprve pokrývat vstupní trasy a poté přesuňte na výstupy.

#### <a name="inputs"></a>Vstupy

1. V metodě Init() souboru program.cs můžeme zaregistrovat dva zpětná volání pro modul:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. Čeká na zprávy odeslané do první zpětné volání **deviceInput** jímky. V diagramu výše vidíme, že má být pro směrování zpráv z libovolného zařízení typu list pro tento vstup. V *deployment.template.json* přidejte trasu, která edge říká, směrovat všechny zprávy zařízení IoT Edge, která nebyla odeslána modul IoT Edge do vstupu volá "deviceInput" turbofanRouter modulu:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Dále přidejte trasu pro zprávy z modulu rulClassifier do modulu turbofanRouter:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Výstupy

Přidáte čtyři další trasy k parametru $edgeHub trasy pro zpracování vytvořené jako výstupy z modulu směrovače.

1. Soubor program.cs definuje metodu SendMessageToClassifier(), který používá klienta modulu pro odeslání zprávy na zbývající doby životnosti třídění pomocí trasy:

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub() modulu klienta používá k odesílání jen zbývající doby životnosti data pro zařízení do služby IoT Hub přes trasy:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter() používá k odeslání zprávy s zbývající doby životnosti data přidaná do modulu avroFileWriter klienta modulu.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage() odesílá neúspěšné zprávy dále IoT Hub, kde je možné směrovat na později.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Všechny trasy dohromady vaší "$edgeHub" uzlu by měl vypadat podobně jako následující kód JSON:

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
> Přidání modulu turbofanRouter vytvoří následující další trasy: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Odebrat tuto trasu, byste museli opustit pouze trasy v souboru deployment.template.json uvedené výše.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Zkopírujte do deployment.debug.template.json trasy

V posledním kroku zrcadlení pro synchronizaci našich souborů, změny, které jste provedli deployment.template.json v deployment.debug.template.json.

## <a name="add-avro-writer-module"></a>Přidat modul zapisovače Avro

Modul Avro zapisovače obsahuje dvě odpovědnost našich řešení k ukládání zpráv a odesílání souborů.

* **Store zprávy**: když Avro zapisovací modul obdrží zprávu, zapisuje zprávu do místního systému souborů ve formátu Avro. Používáme připojení vazby, který připojí adresář (v tomto případu /data/avrofiles) do cesty v kontejneru modulu. Toto připojení umožní modulu pro zápis na místní cestu (/ avrofiles) a tyto soubory dostupné přímo ze zařízení IoT Edge.

* **Nahrání souborů**: Avro zapisovače modul používá funkce služby Azure IoT Hub pro odesílání souborů k nahrání souborů do účtu služby Azure storage. Po úspěšném odeslání souboru modulu odstraní soubor z disku

### <a name="create-module-and-copy-files"></a>Vytvoření modulu a kopírovat soubory

1. Paleta příkazů, vyhledejte a vyberte položku **Pythonu: Vyberte interpret**.

1. Vyberte interpret v C:\\Python37.

1. Znovu otevřete paletu příkazů a vyhledejte a vyberte **terminálu: Vyberte výchozí prostředí**.

1. Po zobrazení výzvy zvolte **příkazový řádek**.

1. Otevřete nový terminálu prostředí **terminálu** > **nové terminálu**.

1. Klikněte pravým tlačítkem na složku moduly ve Visual Studio Code a zvolte **přidat modul IoT Edge**.

1. Zvolte **Modul Python**.

1. Název modulu "avroFileWriter".

1. Po zobrazení výzvy pro vaše úložiště Imagí Dockeru, použijte stejný registru jako jste použili při přidávání modulu směrovače.

1. Kopírování souborů z vzorový modul do řešení.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Pokud se zobrazí výzva k přepsání main.py, zadejte `y` a pak klikněte na tlačítko `Enter`.

1. Všimněte si, že filemanager.py a schema.py byly přidány k řešení a main.py se aktualizovala.

> [!NOTE]
> Když otevřete soubor Pythonu může výzva k instalaci pylintu. Chcete-li nainstalovat linter k dokončení tohoto kurzu nepotřebujete.

### <a name="bind-mount-for-data-files"></a>Navázat připojení pro datové soubory

Jak je uvedeno v úvodní, modul zapisovače spoléhá na přítomnost vazby připojení k zápisu souborů Avro do systému souborů zařízení.

#### <a name="add-directory-to-device"></a>Přidat adresář do zařízení

1. Připojte se k zařízení IoT Edge virtuálních počítačů pomocí protokolu SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Vytvoření adresáře, který bude obsahovat uloženého listu zprávy typu zařízení.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Aktualizace oprávnění adresáře pro možnit kontejnerem.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Ověření adresáře teď má oprávnění zapisovat (w) pro uživatele, skupiny a vlastník.

   ```bash
   ls -la /data
   ```

   ![Oprávnění adresáře pro avrofiles](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Přidat adresář do modulu

Chcete-li přidat adresář do kontejneru modulu, upravíme soubory Dockerfile související s modulem avroFileWriter. Existují tři soubory Dockerfile přidružený modul: Dockerfile.amd64 Dockerfile.amd64.debug a Dockerfile.arm32v7. Tyto soubory by měly být udržovat synchronizované v případě, že bychom ale ladit nebo nasazovat do zařízení s arm32. Pro účely tohoto článku se zaměříte jenom na Dockerfile.amd64.

1. Na svém vývojovém počítači, otevřete **Dockerfile.amd64** souboru.

2. Upravte soubor tak, aby se vypadá jako v následujícím příkladu:

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

   `mkdir` a `chown` příkazy dáte pokyn, aby proces sestavení Dockeru k vytvoření adresář nejvyšší úrovně s názvem /avrofiles na obrázku a ujistěte se, moduleuser vlastník tohoto adresáře. Je důležité, že tyto příkazy jsou vloženy po přidání uživatele modulu do image s `useradd` příkazu a před přepnutí kontextu do moduleuser (moduleuser uživatele).

3. Proveďte odpovídající změny Dockerfile.amd64.debug a Dockerfile.arm32v7.

#### <a name="update-the-module-configuration"></a>Aktualizace konfigurace modulu

Posledním krokem vytvoření vazba je aktualizovat soubory deployment.template.json (a deployment.debug.template.json) s informacemi o vazbu.

1. Otevřete deployment.template.json.

2. Upravit definici modulu pro avroFileWriter přidáním `Binds` parametr, který odkazuje na místní adresáře na hraniční zařízení /avrofiles adresář kontejneru. V tomto příkladu by měl odpovídat vaší definice modulu:

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

3. Deployment.debug.template.json proveďte odpovídající změny.

### <a name="bind-mount-for-access-to-configyaml"></a>Vytvoření vazby připojení pro přístup k config.yaml

Potřebujeme přidat jeden další vazby pro modul zapisovače. Tato vazba dává přístup modul načíst připojovací řetězec ze souboru /etc/iotedge/config.yaml na zařízení IoT Edge. Potřebujeme připojovací řetězec k vytvoření pro IoTHubClient tak, aby říkáme nahrávání\_blob\_asynchronní metody k nahrání souborů do služby IoT hub. Postup pro přidání tato vazba je podobný v předchozí části.

#### <a name="update-directory-permission"></a>Aktualizace oprávnění adresáře

1. Připojte se k zařízení IoT Edge pomocí protokolu SSH.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Přidání oprávnění ke čtení souboru config.yaml.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. Ověření oprávnění jsou nastavena správně.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Ujistěte se, že jsou oprávnění pro config.yaml **- r – r - r –** .

#### <a name="add-directory-to-module"></a>Přidat adresář do modulu

1. Na svém vývojovém počítači, otevřete **Dockerfile.amd64** souboru.

2. Přidat další sadu `mkdir` a `chown` příkazy do souboru tak tedy vypadá jako:

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

1. Otevřít **deployment.template.json** souboru.

2. Upravit definici modulu pro avroFileWriter tak, že přidáte druhý řádek `Binds` parametr, který odkazuje na místní adresáře na zařízení (/ etc/iotedge) adresář kontejneru (/ aplikace/iotconfig).

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

3. Deployment.debug.template.json proveďte odpovídající změny.

## <a name="install-dependencies"></a>Instalace závislostí

Modul zapisovače je závislá na dvou knihovny jazyka Python, fastavro a PyYAML. Musíme nainstalovat závislosti na vývojovém počítači s naší a dáte pokyn, aby proces sestavení Dockeru k instalaci v imagi naše modulu.

### <a name="pyyaml"></a>PyYAML

1. Na svém vývojovém počítači, otevřete **souboru requirements.txt** a přidejte pyyaml.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Otevřít **Dockerfile.amd64** a přidejte `pip install` příkaz pro upgrade setuptools.

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

3. Dockerfile.amd64.debug proveďte odpovídající změny. <!--may not be necessary. Add 'if needed'?-->

4. Nainstalovat pyyaml místně v aplikaci Visual Studio Code otevřete terminál a zadáte

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. V souboru requirements.txt přidejte po pyyaml fastavro.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Nainstalujte fastavro do svého vývojového počítače pomocí nástroje Visual Studio Code terminálu.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>Reconfigure IoT Hub

Zavedením zařízení IoT Edge a moduly systému jsme změnili naše očekávání, o jaká data se budou odesílat do centra a pro jaké účely. Je potřeba znovu nakonfigurovat směrování v centru se naše realitou.

> [!NOTE]
> Můžeme změnit konfiguraci centra před nasazením modulů, protože některé z nastavení centra, konkrétně soubor nahrát, musí být správně nastavit pro modul avroFileWriter správně spustit

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>Nastavte trasy pro RUL zpráv ve službě IoT Hub

Směrovač a třídění na místě Očekáváme, že pravidelné přijímání obsahující ID zařízení a predikcí zbývající doby životnosti pro zařízení. Chcete směrovat data zbývající doby životnosti do své vlastní umístění úložiště, můžeme monitorovat stav zařízení, vytváření sestav a aktivují upozornění, podle potřeby. Ve stejnou dobu chceme, aby všechna data zařízení, která je stále odesíláno přímo listu zařízení, která ještě nebyla připojena k naší zařízení IoT Edge a pokračujte v směrovat do aktuálního umístění úložiště.

#### <a name="create-a-rul-message-route"></a>Vytvoření směrování zpráv zbývající doby životnosti

1. Na webu Azure Portal přejděte do služby IoT Hub.

2. V levém navigačním panelu zvolte **směrování zpráv**.

3. Vyberte **Přidat**.

4. Název trasy **RulMessageRoute**.

5. Vyberte **přidat** vedle **koncový bod** výběr a zvolte **úložiště objektů Blob**.

6. V **přidat koncový bod úložiště** formulářů, zadejte název koncového bodu **ruldata**.

7. Vyberte **vyberte kontejner**.

8. Zvolte účet úložiště používaný v celém tomto kurzu, který se nazývá jako **iotedgeandml\<jedinečnou příponu\>** .

9. Zvolte **ruldata** kontejneru a klikněte na **vyberte**.

10. Klikněte na tlačítko **vytvořit** vytvořte koncový bod úložiště.

11. Pro **směrování dotazů**, zadejte následující příkaz:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. Rozbalte **testovací** část a pak **tělo zprávy** oddílu. Nahraďte zprávy v tomto příkladu náš očekávané zprávy:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. Vyberte **trasy Test**. Je-li test úspěšný, zobrazí "zpráva odpovídá dotazu."

14. Klikněte na **Uložit**.

#### <a name="update-turbofandevicetostorage-route"></a>Aktualizace turbofanDeviceToStorage trasy

Nechceme nová data předpovědi směrovat naše původní umístění úložiště, takže aktualizace trasy, aby se zabránilo jejímu.

1. Ve službě IoT Hub **směrování zpráv** stránky, vyberte **trasy** kartu.

2. Vyberte **turbofanDeviceDataToStorage**, nebo cokoli, co název udělili počáteční zařízení data trasy.

3. Aktualizuje dotaz pro směrování

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. Rozbalte **testovací** část a pak **tělo zprávy** oddílu. Nahraďte zprávy v tomto příkladu náš očekávané zprávy:

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

5. Vyberte **trasy Test**. Je-li test úspěšný, zobrazí "zpráva odpovídá dotazu."

6. Vyberte **Uložit**.

### <a name="configure-file-upload"></a>Konfigurace odesílání souborů

Nakonfigurujte funkci odesílání souborů služby IoT Hub umožňuje modulu zapisovače soubor k nahrání souborů do úložiště.

1. Zvolte z levé tabulky ve službě IoT Hub, **nahrání souboru**.

2. Vyberte **kontejner úložiště Azure**.

3. Ze seznamu vyberte svůj účet úložiště.

4. Vyberte **uploadturbofanfiles** kontejneru a klikněte na **vyberte**.

5. Vyberte **Uložit**. Portál vás upozorní, při dokončení uložení.

> [!Note]
> Jsme nejsou zapnutí oznámení o nahrání pro účely tohoto kurzu, ale zobrazit [přijímat oznámení o nahrání souborů](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) pro podrobnosti o tom, jak zpracovat soubor odeslat oznámení.

## <a name="build-publish-and-deploy-modules"></a>Sestavení, publikování a nasazení modulů

Teď, když jsme provedli změny konfigurace, jsme připraveni k sestavení Image a publikovat je do našich Azure container registry. Proces sestavení použije soubor deployment.template.json k určení, které moduly musí být sestaveny. Nastavení pro každý modul, včetně verze, se nacházejí v souboru module.json ve složce modulu. Proces sestavení nejprve systémem sestavení Dockeru soubory Dockerfile odpovídající aktuální konfiguraci v souboru module.json pro vytvoření image. Pak publikuje image do registru ze souboru module.json značkou verze v souboru module.json odpovídající. Nakonec se vytvoří manifest určených pro konfigurace nasazení (například deployment.amd64.json), který nasadíme do zařízení IoT Edge. Zařízení IoT Edge čte informace z nasazení manifestu a založená na pokyny se stáhnout moduly, nakonfigurovat trasy a nastavte požadované vlastnosti. Tato metoda nasazení má dvě vedlejší účinky, které byste měli vědět:

* **Prodleva nasazení:** vzhledem k tomu, že modul runtime IoT Edge musí rozpoznat změnu jeho požadované vlastnosti než začne změna konfigurace, může trvat nějakou dobu poté, co nasadíte moduly, dokud se modul runtime přebere příspěvky a začne se aktualizovat na hraničních zařízeních IoT zařízení.

* **Věci verze modulu:** Pokud publikujete novou verzi modulu kontejneru do registru kontejnerů pomocí stejné verze značky jako předchozí modul, modul runtime nebude stahovat nové verze modulu. Provádí porovnání značka verze místní image a požadovanou image z manifestu nasazení. Pokud se shodují verzí modulu runtime neprovede žádnou akci. Proto je důležité se zvýší pokaždé, když chcete nasadit nové změny verze modulu. Zvýšit verzi tak, že změníte **verze** vlastnosti v části **značka** vlastnost v souboru module.json pro modul se mění. Potom sestavíte a publikovat v modulu.

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

### <a name="build-and-publish"></a>Vytvářejte a publikujte

1. Ve Visual Studio Code na vašem virtuálním počítači otevřete okno terminálu Visual Studio Code a přihlaste se do vašeho registru kontejneru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. V sadě Visual Studio Code klikněte pravým tlačítkem na deployment.template.json a zvolte **sestavení a Push řešení IoT Edge**.

### <a name="view-modules-in-the-registry"></a>Zobrazení modulů v registru

Po úspěšném dokončení sestavení budeme moct použít ke kontrole našich publikovaných moduly na webu Azure portal.

1. Na webu Azure Portal, přejděte do pracovního prostoru služby Azure Machine Learning a klikněte na hypertextový odkaz **registru**.

    ![Přejděte do registru z pracovního prostoru služby machine learning](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. V části Navigátor registru na straně vyberte **úložišť**.

3. Všimněte si, že oba moduly jste vytvořili, **avrofilewriter** a **turbofanrouter**, se zobrazí jako úložiště.

4. Vyberte **turbofanrouter** a Všimněte si, že jste publikovali označené jako 0.0.1-amd64 jednu image.

   ![Zobrazit první označené verzí turbofanrouter](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Moduly nasazení do zařízení IoT Edge

Budeme mít vytvořit a konfigurovat moduly našich řešení nyní nasadíme moduly na zařízení IoT Edge.

1. Ve Visual Studio Code, klikněte pravým tlačítkem na **deployment.amd64.json** souboru ve složce Konfigurace.

2. Zvolte **vytvoření nasazení pro jedno zařízení**.

3. Vyberte zařízení IoT Edge, **aaTurboFanEdgeDevice**.

4. Aktualizujte panel zařízení Azure IoT Hub v Průzkumníku Visual Studio Code. Měli byste vidět, že jsou tři nové moduly nasazené, ale ještě není spuštěná.

5. Aktualizovat znovu za pár minut a zobrazí se moduly spuštěné.

   ![Zobrazení modulů spuštěných ve Visual Studio Code](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Může trvat několik minut, než moduly, které chcete spustit a usadila se konstantní spuštěném stavu. Během této doby se může zobrazit moduly spouští a zastavují se pokusí navázat připojení s modulem IoT Edge hub.

## <a name="diagnosing-failures"></a>Diagnostika selhání

V této části sdílíme několik technik pro pochopení, co se pokazilo s modulu nebo modulech. Často můžete selhání nanese nejprve ze stavu ve Visual Studio Code.

### <a name="identify-failed-modules"></a>Identifikace modulů se nezdařilo

* **Visual Studio Code:** Podívejte se na panelu zařízení Azure IoT Hub. Pokud většina modulů ve spuštěném stavu, ale jeden zastavená, musíte prozkoumat další zastavené modul. Pokud všechny moduly jsou ve stavu Zastaveno pro dlouhou dobu, může to znamenat i selhání.

* **Azure portal:** Přejděte do služby IoT hub v portálu a vyhledáním stránku Podrobnosti o zařízení (v části IoT Edge, přejít k podrobnostem zařízení) možná zjistíte, že modul oznámil chybu nebo nikdy oznámila nic do služby IoT hub.

### <a name="diagnosing-from-the-device"></a>Diagnostika ze zařízení

Po přihlášení na zařízení IoT Edge, můžete získat přístup k mnoho informací o stavu modulů. Hlavní mechanismus, který používáme jsou příkazy Dockeru, které nám prozkoumat kontejnery a obrázků na zařízení.

1. Vypsat všechny kontejnery spuštěné. Očekáváme, že zobrazíte kontejner pro každý modul s názvem, který odpovídá modulu. Navíc tento příkaz vypíše přesné image kontejneru, včetně verze, takže můžete porovnat s vaší očekávání. Můžete také seznam imagí nahrazením "image", "kontejneru" v příkazu.

   ```bash
   sudo docker container ls
   ```

2. Získáte protokoly pro kontejner. Tento příkaz vypíše cokoli, co byl zapsán do StdErr a StdOut v kontejneru. Tento příkaz lze použít pro kontejnery, které mají spustit a potom z nějakého důvodu ukončené. Je také užitečné pro pochopení, co se děje s edgeAgent nebo edgeHub kontejnery.

   ```bash
   sudo docker container logs <container name>
   ```

3. Zkontrolujte, zda kontejner. Tento příkaz nabízí spoustu informací o imagi. Data lze filtrovat podle toho, co jste hledali. Jako příklad Pokud chcete zjistit, zda jsou správná vytvoří vazbu na avroFileWriter můžete použít příkaz:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Připojte ke spuštěnému kontejneru. Tento příkaz může být užitečné, pokud chcete zkontrolovat kontejner během jejího běhu:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Další postup

V tomto článku jsme vytvořili řešení IoT Edge ve Visual Studio Code pomocí tří modulů, třídění, směrovače a zapisovače souboru/odeslání. Jsme nastavit trasy a povolit moduly, které vzájemně komunikovat na hraniční zařízení se změnil konfiguraci hraniční zařízení a aktualizované soubory Dockerfile nainstalujte závislosti a přidejte vazby připojí do kontejnerů moduly. Aktualizovali jsme v dalším kroku konfigurace služby IoT Hub, můžete směrovat naše zprávy na základě typu a pro zpracování nahrávání souborů. Všechno v místě můžeme moduly nasazené do zařízení IoT Edge a zajistit, že byla správně spuštěna moduly.

Další informace najdete na následujících stránkách:

* [Zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md)
* [Syntaxi dotazů směrování zpráv služby IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [Směrování zpráv služby IoT Hub: nyní se směrováním zprávy](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [Nahrávání souborů s využitím služby IoT Hub](../iot-hub/iot-hub-devguide-file-upload.md)
* [Nahrání souborů ze zařízení do cloudu pomocí služby IoT Hub](../iot-hub/iot-hub-python-python-file-upload.md)

Pokračujte v dalším článku se začít odesílat data a zobrazit vaše řešení v akci.

> [!div class="nextstepaction"]
> [Posílat data přes transparentní brány](tutorial-machine-learning-edge-07-send-data-to-hub.md)
