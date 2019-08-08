---
title: 'Kurz: vývoj modulu Node. js pro Linux-Azure IoT Edge | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Node.js a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: f6304c554858a29e8c6c886bcdec3b51efd635b6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840091"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj a nasazení IoT Edge modul Node. js pro zařízení se systémem Linux

Pomocí Visual Studio Code můžete vyvinout kód Node. js a nasadit ho do zařízení se systémem Linux se spuštěným Azure IoT Edge. 

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech. V tomto kurzu se naučíte:    

> [!div class="checklist"]
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Node.js
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru. 
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat


Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vytvořit modul v **Node. js** pomocí **Visual Studio Code**a jak ho nasadit na **zařízení se systémem Linux**. IoT Edge nepodporuje moduly node. js pro zařízení s Windows.

Následující tabulka vám pomůže pochopit možnosti vývoje a nasazení modulů Node. js: 

| Node.js | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití VS Code pro moduly node. js v systému Linux AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití VS Code pro moduly node. js v systému Linux ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste si měli projít předchozí kurz pro nastavení vývojového prostředí pro vývoj kontejnerů pro Linux: [Vývoj IoT Edgech modulů pro zařízení se systémem Linux](tutorial-develop-for-linux.md). Po dokončení některého z těchto kurzů byste měli mít následující požadavky: 

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Zařízení se systémem Linux se spuštěným Azure IoT Edge](quickstart-linux.md)
* Registr kontejneru, například [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) nakonfigurovaných pomocí [nástrojů Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) nakonfigurovaný pro spouštění kontejnerů Linux.

Chcete-li vytvořit modul IoT Edge v Node. js, nainstalujte na svém vývojovém počítači následující další požadavky: 

* [Node.js a npm](https://nodejs.org) Balíček npm se distribuuje společně s Node.js, takže když si stáhnete Node.js, nainstaluje se vám npm na počítač automaticky.

## <a name="create-a-module-project"></a>Vytvořit projekt modulu
Následující kroky ukazují, jak vytvořit IoT Edge modul Node. js pomocí Visual Studio Code a nástrojů Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Pomocí **npm** vytvořte šablonu řešení Node.js, na jejímž základě budete moct vytvářet dál. 

1. V nástroji Visual Studio Code vyberte, že chcete **zobrazit** > **integrovaný terminál**, aby se otevřel integrovaný terminál VS Code.

2. V integrovaném terminálu zadejte následující příkaz, kterým nainstalujete **yeoman** a generátor pro modul Node.js Azure IoT Edge: 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Výběrem **View** (Zobrazit)  > **Command Palette** (Paleta příkazů) otevřete paletu příkazů VS Code. 

3. V paletě příkazů zadejte a spusťte příkaz **Azure: Přihlaste se** a postupujte podle pokynů a přihlaste se k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

4. V paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení**IoT Edge. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **modul Node.js**. |
   | Zadejte název modulu | Dejte modulu název **NodeModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Vaše image kontejneru se předem vyplní názvem, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Finální úložiště imagí vypadá jako \<název\>registru. azurecr.IO/nodemodule. |
 
   ![Zadání úložiště imagí Dockeru](./media/tutorial-node-module/repository.png)


### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge. 

1. V průzkumníku VS Code otevřete soubor **.env**. 
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure. 
3. Soubor uložte. 

### <a name="select-your-target-architecture"></a>Vyberte cílovou architekturu.

V současné době Visual Studio Code může vyvíjet moduly node. js pro zařízení se systémem Linux AMD64 a Linux ARM32v7. Musíte vybrat architekturu, kterou cílíte na každé řešení, protože kontejner je sestavený a pro každý typ architektury funguje jinak. Výchozí hodnota je Linux AMD64. 

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro řešení**Edge nebo na bočním panelu v dolní části okna vyberte ikonu zástupce. 

2. V paletě příkazů vyberte v seznamu možností cílovou architekturu. Pro tento kurz používáme virtuální počítač s Ubuntu jako zařízení IoT Edge, takže se zachová výchozí hodnota **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Každá šablona obsahuje vzorový kód, který obsahuje Simulovaná data senzorů z modulu **SimulatedTemperatureSensor** a směruje je IoT Hub. V této části přidáte kód, který připraví NodeModule k analýze zpráv před jejich odesláním. 

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

6. Uložte soubor App. js.

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

9. Uložte soubor Deployment. template. JSON.


## <a name="build-and-push-your-module"></a>Sestavení a vložení modulu

V předchozí části jste vytvořili řešení IoT Edge a Přidali jste kód do NodeModule, který odfiltruje zprávy, kde je hlášená teplota počítače v přípustných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Výběrem **View** (Zobrazit) > **Terminal** (Terminál) otevřete integrovaný terminál VS Code.

1. Přihlaste se k Docker zadáním následujícího příkazu v terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru ze služby Azure Container Registry. Tyto hodnoty můžete načíst z oddílu **přístupové klíče** v registru v Azure Portal.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení, které doporučuje použití `--password-stdin`nástroje. I když se tento osvědčený postup doporučuje u produkčních scénářů, je mimo rozsah tohoto kurzu. Další informace najdete v tématu [přihlašovací](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) Reference k Docker.

2. V průzkumníku VS Code klikněte pravým tlačítkem na soubor **deployment.template.json** a vyberte **Build and Push IoT Edge solution** (Vytvořit a odeslat řešení IoT Edge).

   Příkaz Build a push spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config** , která obsahuje úplný manifest nasazení, a vyplní informace v šabloně nasazení a dalších souborech řešení. Za druhé se spustí `docker build` sestavení image kontejneru na základě vhodné souboru Dockerfile pro vaši cílovou architekturu. Pak se spustí a `docker push` nahraje úložiště imagí do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasadit moduly do zařízení

K nasazení projektu modulu do zařízení IoT Edge použijte Průzkumníka Visual Studio Code a rozšíření Azure IoT Tools. Již máte připravený manifest nasazení pro váš scénář, soubor **Deployment. JSON** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že je zařízení IoT Edge spuštěné.

1. V Průzkumníkovi Visual Studio Code rozbalte část **zařízení Azure IoT Hub** a podívejte se na seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měl by se zobrazit nový **NodeModule** spuštěný spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V Průzkumníku Visual Studio Code klikněte pravým tlačítkem myši na název vašeho zařízení IoT Edge a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

2. Zobrazení zpráv přicházejících do IoT Hub. Doručení zpráv může chvíli trvat, protože IoT Edge zařízení musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu NodeModule, čekají, dokud teplota počítače nedosáhne 25 stupňů před odesláním zpráv. Přidá také výstrahu typu zpráva pro všechny zprávy, které dosáhnou prahové hodnoty teploty. 

## <a name="edit-the-module-twin"></a>Upravit nevlákenný modul

V manifestu nasazení jsme použili vláken Module NodeModule, které nastaví prahovou hodnotu teploty na 25 stupních. Chcete-li změnit funkčnost, aniž byste museli aktualizovat kód modulu, můžete použít vlákna modulu.

1. V Visual Studio Code rozbalte podrobnosti pod zařízením IoT Edge a podívejte se na běžící moduly. 

2. Klikněte pravým tlačítkem na **NodeModule** a vyberte **Upravit modul**s dvojitou čárkou. 

3. V požadovaných vlastnostech vyhledejte **TemperatureThreshold** . Změňte jeho hodnotu na novou teplotu 5 stupňů na 10 stupňů vyšší než při nejnovější hlášené teplotě. 

4. Uložte modul s dvojitým pracovním souborem.

5. Klikněte pravým tlačítkem na libovolné místo v podoknì s dvojitou úpravou modulu a vyberte **aktualizovat modul vlákna**. 

6. Umožňuje monitorovat příchozí zprávy ze zařízení do cloudu. Měli byste vidět, že se zprávy zastavily, dokud se nedosáhne nové prahové hodnoty teploty. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení. 

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete získat další informace o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů pomocí Visual Studio Code](how-to-vs-code-develop-module.md). V dalších kurzech můžete pokračovat a zjistit, jak vám Azure IoT Edge může pomáhat s nasazením cloudových služeb Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)[](tutorial-deploy-custom-vision.md) [Machine Learning](tutorial-deploy-machine-learning.md)Custom Vision Service
> 
> 
