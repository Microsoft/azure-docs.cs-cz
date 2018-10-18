---
title: Vývoj a ladění modulů jazyka C# pro Azure IoT Edge v sadě Visual Studio 2017 | Dokumentace Microsoftu
description: Pomocí sady Visual Studio 2017 na vývoj a ladění modulu C# pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 768ff899ca2c71cb32fe29bdd5d58654d8f7d431
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394744"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Pomocí sady Visual Studio 2017 na vývoj a ladění modulů jazyka C# pro Azure IoT Edge (Preview)

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. Tento článek ukazuje, jak pomocí sady Visual Studio 2017 jako hlavní nástroje k vývoji a ladění modulů jazyka C#.

Nástroje Azure IoT Edge pro Visual Studio nabízí následující výhody:

- Vytvářet, upravovat, vytvářet, spouštět a ladit řešení Azure IoT Edge a moduly na místním počítači pro vývoj.
- Nasazení řešení Azure IoT Edge pro zařízení Azure IoT Edge prostřednictvím služby Azure IoT Hub.
- Přitom má všechny výhody vývoj sady Visual Studio Code modulů Azure IoT v jazyce C#.
- Správa zařízení Azure IoT Edge a moduly pomocí uživatelského rozhraní. 

V tomto článku se dozvíte, jak pomocí nástroje Azure IoT Edge pro Visual Studio 2017 na vývoj modulů IoT Edge v jazyce C#. Také se dozvíte, jak nasadit svůj projekt na vaše zařízení Azure IoT Edge.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení.

Vzhledem k tomu tento článek používá jako hlavní vývojový nástroj Visual Studio 2017, nainstalujte Visual Studio. A nezapomeňte uvést **funkcí vývoj pro Azure** v instalaci sady Visual Studio 2017. Je možné [upravit Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) a přidejte úlohu vývoj pro Azure.

Poté, co Visual Studio 2017 je připraven, budete také muset:

- Stáhněte a nainstalujte [rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools) z webu marketplace Visual Studio k vytvoření IoT Edge projektu v sadě Visual Studio 2017.
- [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači sestavení a spuštění vaší bitové kopie modulu. Je nutné správně nastavit Docker CE spuštěna v režimu kontejneru Linuxu nebo Windows kontejneru.
- Pokud chcete nastavit místní vývojové prostředí pro ladit, spouštět a testovat řešení IoT Edge, musíte [nástroj pro vývojáře služby Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Nainstalujte [(2.7/3.6) Python a Pip](https://www.python.org/). Nainstalujte **iotedgehubdev** spuštěním následující příkaz, v terminálu. Ujistěte se, že váš nástroj pro vývojáře služby Azure IoT EdgeHub verze je větší než 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru. 

- K otestování modul, potřebujete aktivní služby IoT hub s vytvořit alespoň jednu ID zařízení IoT Edge. Pokud používáte démon zabezpečení IoT Edge na vývojovém počítači, můžete potřebovat zastavit EdgeHub a EdgeAgent před zahájením vývoje v sadě Visual Studio. 

### <a name="check-your-tools-version"></a>Zkontrolujte verzi nástroje

1. Z **nástroje** nabídce zvolte **rozšíření a aktualizace**. Rozbalte **nainstalováno > Nástroje** zjistíte **Azure IoT Edge** a **Průzkumníka cloudu**.

2. Poznamenejte si verzi nainstalovaného. Můžete porovnat tato verze na nejnovější verzi na Visual Studio Marketplace ([Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools))

3. Pokud je vaše verze starší, aktualizujte své nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizovat vaše nástroje

1. V **rozšíření a aktualizace** dialogového okna, rozbalte **aktualizace > Visual Studio Marketplace**, zvolte **Azure IoT Edge** nebo **Průzkumníka cloudu**a vyberte **aktualizace**.

2. Po stažení aktualizace nástroje zavřete sadu Visual Studio na trigger nástroje aktualizace pomocí instalátor VSIX.

3. V instalačním programu, zvolte **OK** start a následně upravit aktualizace nástrojů.

4. Po dokončení aktualizace zvolte zavřete a restartujte aplikaci Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Vytvoření projektu Azure IoT Edge

Šablona projektu Azure IoT Edge v sadě Visual Studio vytvoří projekt, který je možné nasadit do zařízení Azure IoT Edge ve službě Azure IoT Hub. Nejprve vytvořit řešení Azure IoT Edge, a pak vygenerovat první modulu jazyka C# v tomto řešení. Každé řešení IoT Edge může obsahovat více než jeden modul. 

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost **Nový** > **Projekt**.

2. V **nový projekt** dialogového okna, vyberte **nainstalováno**, rozbalte **Visual C# > Cloud**vyberte **Azure IoT Edge**, zadejte  **Název** pro váš projekt a zadejte umístění a klikněte na tlačítko **OK**. Výchozí název projektu je **AzureIoTEdgeApp1**. 

   ![Nový projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. V **konfigurace modulu IoT Edge** okně **modulu jazyka C#** zadejte a zadejte název modulu a modulu úložiště imagí.  Pojmenujte VS autopopulates modulu s **localhost:5000**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak místního hostitele je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **<registry name>. azurecr.io**. V řetězci nahraďte pouze část localhost, název vašeho modulu neodstraňujte. Výchozí název modulu je **IoTEdgeModule1**

4. Klikněte na tlačítko **OK** vytvoření projektu Azure IoT Edge se modulu jazyka C#.

Nyní máte **AzureIoTEdgeApp1** projektu a **IoTEdgeModule1** projekt v našich řešení. **AzureIoTEdgeApp1** projekt má **deployment.template.json** souboru. Tento soubor definuje moduly, které chcete sestavit a nasadit řešení IoT Edge a definuje trasy mezi moduly. Má řešení výchozí **tempSensor** modulu a **IoTEdgeModule1** modulu. **TempSensor** modul generuje Simulovaná data do **IoTEdgeModule1** modulu, zatímco výchozí kód v **IoTEdgeModule1** modul je modul kanálu zpráv, které přímo kanálu přijaté zprávy do služby IoT Hub.

**IoTEdgeModule1** je projekt.Net Core 2.1 konzolové aplikace. Obsahuje požadovaný **soubory Dockerfile** budete potřebovat pro zařízení IoT Edge s kontejneru Windows nebo Linuxem kontejneru. **Module.json** soubor popisuje metadata modulu. **Program.cs** je kód skutečné modul, který využívá sadu SDK Azure IoT zařízení jako závislost.

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí modul kód jazyka C#, která se dodává s řešením je umístěn v **IoTEdgeModule1** > **Program.cs**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub. 

Jakmile budete připraveni k přizpůsobení šablony jazyka C# s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost. 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>Inicializovat **iotegehubdev** připojovacím řetězcem zařízení IoT Edge

1. Je potřeba získat připojovací řetězec z libovolného zařízení IoT Edge, můžete kopírovat z Průzkumníku cloudu v sadě Visual Studio 2017 jako postupujte hodnotu "Primární připojovací řetězec". Nekopírovat prosím připojovací řetězec zařízení bez okrajů, ikonu zařízení IoT Edge se liší od jiných hraničních zařízení.

   ![Zkopírujte připojovací řetězec zařízení Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. Budete muset klikněte pravým tlačítkem na **AzureIoTEdgeApp1** projektu otevřete místní nabídku a pak klikněte na **nastavit připojovací řetězec zařízení Edge**, zobrazí se okno nastavení Azure IoT Edge.

   ![Okno sady Edge připojovací řetězec](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. V okně Nastavení prosím zadejte připojovací řetězec, který jste získali v prvním kroku a klikněte na tlačítko **OK** tlačítko.

>[!NOTE]
>Toto je jednorázová práce, stačí potřebovat provedením tohoto kroku jednou na jednom počítači, všechny následné Azure IoT Edge řešení se zobrazí, když se zdarma. Samozřejmě můžete znovu spustit tento krok Pokud připojovací řetězec je neplatný nebo je potřeba změnit na další připojovací řetězec.

## <a name="build-and-debug-single-c-module"></a>Vytvářejte a laďte jednoho modulu jazyka C#

Obvykle chcete testovací/debug každého modulu, před provedením jeho spuštění v rámci celého řešení s několika cloudy.

1. Vyberte **IoTEdgeModule1** jako spouštěcí projekt v místní nabídce.

   ![Nastavit spouštěcí projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Stisknutím klávesy **F5** nebo klikněte na tlačítko níže spusťte modul, může trvat 10 přibližně 20 sekund pro první čas.

   ![Spusťte modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. Měli byste vidět.Net Core pracovat konzolovou aplikaci, i když v modulu byl úspěšně inicializován.

   ![Spuštěný modul](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Nyní můžete nastavit zarážku **PipeMessage** v **Program.cs**, pak spuštěním následujícího příkazu v odesílání zprávy vaše **Git Bash** nebo **WSL Bash**  (nespouštějte ji do příkazového řádku nebo Powershellu) (Tento příkaz můžete najít také v okně výstupu VS):

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Ladění jednoho modulu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Zarážka by měla aktivovat. Podívejte se na proměnné v okně místních hodnot Visual Studio.

   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje rozhraní API pro odesílání zpráv prostřednictvím místo `curl`.

5. Stisknutím klávesy **Ctrl + F5** nebo klikněte na tlačítko stop, Zastavit ladění.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Sestavování a ladění řešení IoT Edge s několika cloudy

Jakmile dokončíme vývoj jeden modul, v dalším kroku chceme spustit a ladit celé řešení s několika cloudy.

1. Přidejte druhý modul jazyka C# do řešení. Klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a vyberte **přidat** -> **nový modul IoT Edge**. Výchozí název druhý modul je **IoTEdgeModule2** a je stále modulu kanálu.

2. Přejděte do **deployment.template.json**. Zobrazí se **IoTEdgeModule2** se přidala v **moduly** oddílu. Nahradit **trasy** oddíl následujícím kódem. Pokud jste upravili do názvu modulu, ujistěte se, že aktualizujete název následující po nahrazení.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Nastavte **AzureIoTEdgeApp1** projektu jako spouštěcí projekt v místní nabídce.

4. Nastavit zarážky a stiskněte klávesu F5, pak může spouštět a ladit více modulů současně. Měli byste vidět více.Net Core konzoly aplikace windows, každé okno označuje modulu jazyka C#. 

   ![Ladění více modulů](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Stisknutím klávesy **Ctrl + F5** nebo klikněte na tlačítko stop, Zastavit ladění.

## <a name="build-and-push-images"></a>Vytváření a nasdílení změn imagí

1. Ujistěte se, že **AzureIoTEdgeApp1** je počáteční projekt. Vyberte **ladění** nebo **vydání** konfigurace vaší bitové kopie modulu na sestavení.

    > [!NOTE]
    > Při výběru **ladění**, VS použije `Dockerfile.debug` k sestavení Image Dockeru. To zahrnuje příkazového řádku debugger .NET Core VSDBG ve vaší imagi kontejneru při jeho vytváření. Doporučujeme, abyste použili **vydání** konfigurace, který používá `Dockerfile` bez VSDBG pro moduly IoT Edge připravené pro produkční prostředí.

2. Pokud používáte privátního registru jako je Azure Container Registry, spusťte v terminálu Docker Přihlaste se pomocí následujícího příkazu. Pokud používáte místní registru, můžete si [spuštění místního registru](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Pokud používáte privátního registru jako je Azure Container Registry, musíte do registru uživatelské jméno a heslo v `deployment.template.json` v části Nastavení běhového prostředí s následujícím obsahem. Nezapomeňte nahradit zástupný text pomocí skutečné správce uživatelského jména a hesla.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

4. Klikněte pravým tlačítkem myši **AzureIoTEdgeApp1** a zvolte položku kontextové nabídky **sestavení a hraniční řešení, Push**, bude sestavení a nahrání image dockeru pro každý modul.

   ![Vytváření a nasdílení změn Imagí](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Nasazení řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Můžete také nasadit moduly pomocí Průzkumníka cloudu sady Visual Studio. Už máte manifest nasazení připravili pro váš scénář `deployment.json` souboru. Teď stačí jen vybrat zařízení, na které se nasazení provede.

1. Otevřít **Průzkumníka cloudu** kliknutím **zobrazení** > **Průzkumníka cloudu**. Ujistěte se, že jste se přihlásili v sadě Visual Studio 2017 s vaším účtem.

2. V **Průzkumníka cloudu**, rozšířit vaše předplatné, vyhledání služby Azure IoT Hub a zařízení Azure IoT Edge, které chcete nasadit.

3. Klikněte pravým tlačítkem na zařízení IoT Edge k vytvoření, nasazení, musíte zvolit soubor manifestu nasazení v rámci `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

>>[!NOTE]
>>Nesmí vyberete `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

4. Klikněte na tlačítko pro obnovení. Měli byste vidět nové moduly, spuštěná společně s **TempSensor** modulu a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. K monitorování D2C zprávy pro konkrétní zařízení, klikněte na zařízení zobrazí v seznamu a klikněte na tlačítko **spustit monitorování zpráv D2C** v **akce** okna.

2. Monitorování dat zastavit, klikněte na zařízení zobrazí v seznamu a vyberte **zastavit monitorování zpráv D2C** v **akce** okna.

## <a name="next-steps"></a>Další postup

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
