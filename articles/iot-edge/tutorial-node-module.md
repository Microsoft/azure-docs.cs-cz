---
title: Kurz Node.js modulu vývoj pro Linux – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Node.js a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2725f0a34ace3a353df5f746df299aeaf0ea92b3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574255"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj a nasazení modulu Node.js IoT Edge pro zařízení s Linuxem

Vývoj kódu Node.js a nasadit ho k Linuxovému zařízení s Azure IoT Edge pomocí Visual Studio Code. 

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Node.js
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru. 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Řešení rozsahu

Tento kurz ukazuje postupy při vývoji modulu v **Node.js** pomocí **Visual Studio Code**a jak ji nasadit **Linuxovému zařízení**. IoT Edge pro zařízení s Windows nepodporuje moduly Node.js.

Popis možností pro vývoj a nasazení modulů Node.js pomocí následující tabulky: 

| Node.js | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití VS Code pro moduly Node.js v Linuxu AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití VS Code pro moduly Node.js v Linuxu ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, by měl prošli předchozího kurzu věnovaného nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Pomocí některé z těchto kurzů, byste měli mít splněné následující požadavky na místě: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* A [Linuxovému zařízení s Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, třeba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovanou [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurované ke spuštění kontejnerů Linuxu.

K vývoji modul IoT Edge v Node.js, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [Node.js a npm](https://nodejs.org) Balíček npm se distribuuje společně s Node.js, takže když si stáhnete Node.js, nainstaluje se vám npm na počítač automaticky.

## <a name="create-a-module-project"></a>Vytvoření modulu projektu
Následující kroky ukazují, jak vytvořit modul služby IoT Edge Node.js pomocí Visual Studio Code a nástroje Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Pomocí **npm** vytvořte šablonu řešení Node.js, na jejímž základě budete moct vytvářet dál. 

1. V nástroji Visual Studio Code vyberte, že chcete **zobrazit** > **integrovaný terminál**, aby se otevřel integrovaný terminál VS Code.

2. V integrovaném terminálu zadejte následující příkaz, kterým nainstalujete **yeoman** a generátor pro modul Node.js Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

3. Do palety příkazů zadejte a spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů k přihlášení účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

4. Do palety příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **modul Node.js**. |
   | Zadejte název modulu | Dejte modulu název **NodeModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Svou image kontejneru je předem z názvu, který jste zadali v předchozím kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Finální bitové kopie úložiště bude vypadat jako \<název registru\>.azurecr.io/nodemodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-node-module/repository.png)


### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor **.env**. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="select-your-target-architecture"></a>Vyberte Cílová architektura

Visual Studio Code v současné době můžete vyvíjet modulů Node.js pro Linux AMD64 a Linux ARM32v7 zařízení. Budete muset vybrat, jakou architekturu vývoji cílíte jednotlivých řešení, protože kontejneru je sestaven a jinak spusťte pro každý typ architektury. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavit výchozí Cílová platforma pro řešení**, nebo vyberte ikonu zástupce v bočním panelu v dolní části okna. 

2. Vyberte Cílová architektura v paletu příkazů v seznamu možností. Pro účely tohoto kurzu používáme virtuálního počítače s Ubuntu jako zařízení IoT Edge, budou mít výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Každá šablona zahrnuje ukázkový kód, který směruje simulovaná data snímačů z modulu **tempSensor** do služby IoT Hub. V této části přidáte kód, který připraví NodeModule k analýze zpráv před jejich odesláním. 

1. V průzkumníku VS Code otevřete **modules** > **NodeModule** > **app.js**.

2. Pod požadované moduly uzlu přidejte proměnnou prahové teploty. Prahová teplota definuje hodnotu, kterou musí naměřená teplota překročit, aby se data odeslala do IoT Hubu.

    ```javascript
    var temperatureThreshold = 25;
    ```

3. Celou funkci `PipeMessage` nahraďte funkcí `FilterMessage`.
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. Ve funkci `client.on()` nahraďte funkci s názvem `pipeMessage` funkcí s názvem `filterMessage`.

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. Zkopírujte následující fragment kódu do zpětného volání funkce `client.open()`, za funkci `client.on()` v příkazu `else`. Tato funkce se bude volat při aktualizaci požadovaných vlastností.

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. Uložení souboru app.js.

7. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

8. Přidejte do manifestu nasazení dvojče modulu NodeModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`: 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Dvojče zařízení přidat do šablony nasazení](./media/tutorial-node-module/module-twin.png)

9. Uložte soubor deployment.template.json.


## <a name="build-and-push-your-module"></a>Vytváření a nasdílení změn modulu

V předchozí části jste vytvořili hraničních zařízeních IoT řešení a přidat kód do NodeModule, který odfiltruje zprávy kde teploty ohlášené počítač je v přijatelných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

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

4. Klikněte na tlačítko pro obnovení. Měl by se zobrazit spuštěný nový modul **NodeModule** společně s modulem **TempSensor** a moduly **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte **spustit monitorování zpráv D2C**.

2. Zobrazení zpráv přicházejících u služby IoT Hub. Může trvat nějakou dobu zprávy dorazí, protože zařízení IoT Edge se zobrazí její nové nasazení a spuštění všech modulů. Pak změny, které jsme provedli NodeModule kód Počkejte, dokud počítač teploty dosáhne 25 stupňů před odesláním zprávy. Také přidá typ zprávy **výstrah** pro všechny zprávy, které dosažení této prahové hodnoty teploty. 


## <a name="edit-the-module-twin"></a>Upravit dvojče modulu

Dvojče modulu NodeModule jsme použili v manifestu nasazení pro nastavení prahové hodnoty teploty ve stupních 25. Chcete-li změnit funkci bez nutnosti aktualizovat kód modulu můžete použít dvojče modulu.

1. Ve Visual Studio Code rozbalte podrobnosti v části zařízení IoT Edge zobrazíte spuštěné moduly. 

2. Klikněte pravým tlačítkem na **NodeModule** a vyberte **upravit dvojče zařízení**. 

3. Najít **TemperatureThreshold** v požadované vlastnosti. Změňte její hodnotu na nové teploty 5 až 10 stupňů vyšší než nejnovější ohlášené teploty. 

4. Uložte soubor dvojčete modulu.

5. Klikněte pravým tlačítkem na libovolné místo ve dvojčeti modulu úpravy podokně a vyberte **aktualizace dvojčete modulu**. 

5. Monitorování příchozích zpráv typu zařízení cloud. Měli byste vidět zprávy zastavit, dokud nebude dosaženo novou teploty prahovou hodnotu. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Jakmile budete připraveni k sestavení vlastních modulů, další informace o [vyvíjet vlastní moduly IoT Edge](module-development.md) nebo jak [vývoj modulů s Visual Studio Code](how-to-vs-code-develop-module.md). Budete moct pokračovat do další kurzy se dozvíte, jak Azure IoT Edge můžete nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [službu Custom Vision Service](tutorial-deploy-custom-vision.md)
