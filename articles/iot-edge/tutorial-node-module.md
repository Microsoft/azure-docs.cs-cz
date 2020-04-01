---
title: Výuka vývoje modulu Node.js pro Linux – Azure IoT Edge | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak vytvořit modul IoT Edge s kódem v jazyce Node.js a jak ho nasadit na hraniční zařízení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 71b22bf9bf040abcdf513a4f8baa916930c8972e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772223"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>Kurz: Vývoj a nasazení modulu Node.js IoT Edge pro linuxová zařízení

Pomocí kódu Visual Studia můžete vyvinout kód Node.js a nasadit ho do linuxového zařízení se systémem Azure IoT Edge.

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás povede při vytvoření a nasazení modulu IoT Edge, který filtruje data ze senzoru. Budete používat simulované zařízení IoT Edge, které jste vytvořili v rychlých startech. V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Používat Visual Studio Code k vytvoření modulu IoT Edge Node.js
> * Používat Visual Studio Code a Docker k vytvoření image Dockeru a jejímu publikování v registru.
> * Nasazení modulu do zařízení IoT Edge
> * Zobrazení vygenerovaných dat

Modul IoT Edge, který v tomto kurzu vytvoříte, filtruje teplotní údaje generované zařízením. Zprávy posílá dále, jen když teplota překročí zadanou prahovou hodnotu. Tento typ analýzy v zařízení Edge je užitečný kvůli zmenšení objemu dat přenášených a ukládaných do cloudu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Obor řešení

Tento kurz ukazuje, jak vyvíjet modul v **Node.js** pomocí **visual studio kód**a jak jej nasadit do zařízení S **IP**. IoT Edge nepodporuje moduly Node.js pro zařízení se systémem Windows.

V následující tabulce můžete porozumět možnostem vývoje a nasazování modulů Node.js:

| Node.js | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Použití kódu VS pro moduly Node.js v Linuxu AMD64](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Použití kódu VS pro moduly Node.js v Linuxu ARM32](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu byste měli projít předchozí kurz nastavení vývojového prostředí pro vývoj kontejnerů Linux: [Vývoj modulů IoT Edge pro zařízení s Linuxem](tutorial-develop-for-linux.md). Dokončením některého z těchto výukových programů byste měli mít následující předpoklady:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* [Linuxové zařízení se systémem Azure IoT Edge](quickstart-linux.md)
* Registr kontejnerů, jako je [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Kód](https://code.visualstudio.com/) nakonfigurovaný pomocí [nástrojů Azure IoT .](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* [Docker CE](https://docs.docker.com/install/) nakonfigurován pro spouštění kontejnerů Linuxu.

Chcete-li vyvinout modul IoT Edge v souboru Node.js, nainstalujte do vývojového počítače následující další předpoklady:

* [Node.js a npm](https://nodejs.org) Balíček npm se distribuuje společně s Node.js, takže když si stáhnete Node.js, nainstaluje se vám npm na počítač automaticky.

## <a name="create-a-module-project"></a>Vytvoření projektu modulu

Následující kroky ukazují, jak vytvořit modul IoT Edge Node.js pomocí kódu Visual Studia a nástrojů Azure IoT.

### <a name="create-a-new-project"></a>Vytvoření nového projektu

Pomocí **npm** vytvořte šablonu řešení Node.js, na jejímž základě budete moct vytvářet dál.

1. V kódu sady Visual Studio vyberte **možnost Zobrazit** > **integrovaný terminál** a otevřete integrovaný terminál VS Code.

2. V integrovaném terminálu zadejte následující příkaz, kterým nainstalujete **yeoman** a generátor pro modul Node.js Azure IoT Edge:

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. Vyberte **Zobrazit** > **paletu příkazů,** chcete-li otevřít paletu příkazů VS Code.

4. Na paletě příkazů zadejte a spusťte příkaz **Azure: Sign in** (Azure: Přihlásit se) a postupujte podle pokynů pro přihlášení k účtu Azure. Pokud jste už přihlášení, můžete tento krok přeskočit.

5. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge). Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení nebo přijměte výchozí **edgesolution**. |
   | Vyberte šablonu modulu | Zvolte **Modul Node.js**. |
   | Zadejte název modulu | Dejte modulu název **NodeModule**. |
   | Zadejte pro modul úložiště imagí Dockeru | Úložiště imagí zahrnuje název registru kontejneru a název image kontejneru. Image kontejneru je předem vyplněna z názvu, který jste zadali v posledním kroku. Nahraďte **localhost:5000** hodnotou přihlašovacího serveru z vašeho registru kontejneru Azure. Přihlašovací server můžete získat na stránce Přehled vašeho registru kontejneru na webu Azure Portal. <br><br>Konečné úložiště bitových \<obrazů\>vypadá jako název registru .azurecr.io/nodemodule. |

   ![Zadání úložiště imagí Dockeru](./media/tutorial-node-module/repository.png)

### <a name="add-your-registry-credentials"></a>Přidání přihlašovacích údajů registru

V souboru prostředí jsou uložené přihlašovací údaje pro kontejner úložiště, které soubor sdílí s modulem runtime IoT Edge. Modul runtime tyto přihlašovací údaje potřebuje k přetažení vašich privátních imagí do zařízení IoT Edge.

1. V průzkumníku kódu VS otevřete soubor **ENV.**
2. Aktualizujte pole hodnotami **uživatelské jméno** a **heslo**, které jste zkopírovali z registru kontejneru Azure.
3. Soubor uložte.

### <a name="select-your-target-architecture"></a>Vyberte si cílovou architekturu

V současné době visual studio kód můžete vyvíjet Node.js moduly pro Linux AMD64 a Linux ARM32v7 zařízení. Musíte vybrat architekturu, na kterou cílíte s každým řešením, protože kontejner je sestaven a spuštěn jinak pro každý typ architektury. Výchozí hodnota je Linux AMD64.

1. Otevřete paletu příkazů a vyhledejte **Azure IoT Edge: Nastavte výchozí cílovou platformu pro edge řešení**nebo vyberte ikonu zástupce v bočním panelu v dolní části okna.

2. V paletě příkazů vyberte cílovou architekturu ze seznamu možností. Pro účely tohoto kurzu používáme jako zařízení IoT Edge virtuální stroj Ubuntu, takže zachováme výchozí **amd64**.

### <a name="update-the-module-with-custom-code"></a>Aktualizace modulu pomocí vlastního kódu

Každá šablona je dodávána s ukázkovým kódem, který přebírá simulovaná data senzorů z modulu **SimulatedTemperatureSensor** a směruje je do ioT hubu. V této části přidáte kód, který připraví NodeModule k analýze zpráv před jejich odesláním.

1. V průzkumníku kódu VS otevřete **moduly** > **NodeModule** > **app.js**.

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

6. Uložte soubor app.js.

7. V průzkumníku VS Code otevřete soubor **deployment.template.json** v pracovním prostoru řešení IoT Edge.

8. Přidejte do manifestu nasazení dvojče modulu NodeModule. Vložte následující obsah JSON do dolní části oddílu `moduleContent`, za dvojče modulu `$edgeHub`:

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Přidání dvojčete modulu do šablony nasazení](./media/tutorial-node-module/module-twin.png)

9. Uložte soubor deployment.template.json.

## <a name="build-and-push-your-module"></a>Sestavte a tlačte svůj modul

V předchozí části jste vytvořili řešení IoT Edge a přidali kód do NodeModule, který odfiltruje zprávy, kde je hlášená teplota počítače v přijatelných mezích. Teď je potřeba vytvořit toto řešení jako image kontejneru a odeslat ho do registru kontejneru.

1. Otevřete integrovaný terminál VS Code výběrem **možnosti Zobrazit** > **terminál**.

1. Přihlaste se k Dockeru zadáním následujícího příkazu do terminálu. Přihlaste se pomocí uživatelského jména, hesla a přihlašovacího serveru z registru kontejnerů Azure. Tyto hodnoty můžete načíst z části **Přístupové klíče** v registru na webu Azure Portal.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Může se zobrazit upozornění zabezpečení doporučující použití programu `--password-stdin`. Zatímco tento osvědčený postup se doporučuje pro produkční scénáře, je mimo rozsah tohoto kurzu. Další informace naleznete v odkazu na [přihlášení dockeru.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. V průzkumníku kódu VS klikněte pravým tlačítkem myši na soubor **deployment.template.json** a vyberte **možnost Sestavit a push řešení IoT Edge**.

   Příkaz sestavení a nabízení spustí tři operace. Nejprve vytvoří novou složku v řešení s názvem **config,** která obsahuje úplný manifest nasazení, sestavený z informací v šabloně nasazení a dalších souborech řešení. Za druhé `docker build` spustí k vytvoření image kontejneru na základě příslušné dockerfile pro cílovou architekturu. Potom spustí `docker push` k nabízení úložiště bitové kopie do registru kontejneru.

## <a name="deploy-modules-to-device"></a>Nasazení modulů do zařízení

Pomocí průzkumníka kódu Visual Studio a rozšíření Nástroje Azure IoT k nasazení projektu modulu do zařízení IoT Edge. Již máte manifest nasazení připravený pro váš scénář, soubor **deployment.json** ve složce config. Teď stačí jen vybrat zařízení, na které se nasazení provede.

Ujistěte se, že vaše zařízení IoT Edge je v provozu.

1. V průzkumníku kódu Visual Studia rozbalte část **Zařízení služby Azure IoT Hub,** abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem na název vašeho zařízení IoT Edge a pak vyberte **Create Deployment for Single Device** (Vytvořit nasazení pro jedno zařízení).

3. Vyberte ve složce **config** soubor **deployment.json** a klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). Nepoužívejte soubor deployment.template.json.

4. Klikněte na tlačítko pro obnovení. Měli byste vidět nový **NodeModule** běží spolu s **modulu SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Po použití manifestu nasazení pro zařízení IoT Edge začne modul runtime IoT Edge na zařízení shromažďovat informace o novém nasazení a jednat podle nich. Všechny moduly spuštěné na zařízení, které nejsou zahrnuté do manifestu nasazení, se zastaví. Všechny moduly, které na zařízení chybí, se spustí.

Stav zařízení IoT Edge můžete zobrazit v části **Zařízení Azure IoT Hub** v průzkumníku Visual Studio Code. Rozbalením podrobností o zařízení zobrazíte seznam nasazených a spuštěných modulů.

1. V průzkumníku kódu Visual Studia klikněte pravým tlačítkem myši na název zařízení IoT Edge a vyberte **spustit sledování integrovaného koncového bodu událostí**.

2. Zobrazení zpráv přicházejících do vašeho IoT Hubu. Může chvíli trvat, než zprávy dorazí. Zařízení IoT Edge musí přijmout nové nasazení a spustit všechny moduly. Změny, které jsme provedli v kódu NodeModule, pak před odesláním zpráv počkejte, dokud teplota počítače nedosáhne 25 stupňů. Přidá také typ zprávy **Výstraha** všechny zprávy, které dosáhnou této prahové hodnoty teploty.

## <a name="edit-the-module-twin"></a>Úprava dvojčete modulu

Použili jsme dvojče modulu NodeModule v manifestu nasazení k nastavení teplotního prahu na 25 stupňů. Dvojče modulu můžete použít ke změně funkce bez nutnosti aktualizace kódu modulu.

1. V kódu Visual Studio rozbalte podrobnosti v zařízení IoT Edge a podívejte se na spuštěné moduly.

2. Klepněte pravým tlačítkem myši na příkaz **NodeModule** a vyberte příkaz **Upravit dvojče modulu**.

3. Najít **TemperatureThreshold** v požadovaných vlastnostech. Změňte jeho hodnotu na novou teplotu o 5 stupňů až 10 stupňů vyšší než poslední hlášená teplota.

4. Uložte soubor dvojčete modulu.

5. Klepněte pravým tlačítkem myši na libovolné místo v podokně úprav dvojčete modulu a vyberte **možnost Aktualizovat dvojče modulu**.

6. Sledujte příchozí zprávy mezi zařízeními a cloudy. Měli byste vidět zprávy zastavit, dokud není dosaženo nového prahu teploty.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili modul IoT Edge obsahující kód pro filtrování nezpracovaných dat generovaných zařízením IoT Edge. Až budete připraveni vytvořit vlastní moduly, můžete se dozvědět více o [vývoji vlastních modulů IoT Edge](module-development.md) nebo o [vývoji modulů s visual studio code](how-to-vs-code-develop-module.md). Příklady modulů IoT Edge, včetně modulu simulované teploty, viz [ukázky modulu IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules).

Můžete pokračovat na další kurzy se dozvíte, jak Azure IoT Edge vám může pomoci nasadit cloudové služby Azure pro zpracování a analýzu dat na hraničních zařízeních.

> [!div class="nextstepaction"]
> [Funkce](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Vlastní Vision Service](tutorial-deploy-custom-vision.md)
