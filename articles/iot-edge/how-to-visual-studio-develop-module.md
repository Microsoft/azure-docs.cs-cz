---
title: Vývoj a ladění modulů v aplikaci Visual Studio – Azure IoT Edge | Microsoft Docs
description: Použití sady Visual Studio 2019 k vývoji a ladění modulů pro Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ed668f744716b062fd70d2f63d89152f6fc8a902
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999126"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Použití sady Visual Studio 2019 k vývoji a ladění modulů pro Azure IoT Edge

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio 2019 jako hlavní nástroj pro vývoj a ladění modulů.

Nástroje Azure IoT Edge pro Visual Studio nabízí následující výhody:

- Vytváření, úpravy, sestavování, spouštění a ladění Azure IoT Edgech řešení a modulů na místním vývojovém počítači.
- Nasazení řešení Azure IoT Edge pro zařízení Azure IoT Edge prostřednictvím služby Azure IoT Hub.
- Nahlaste své moduly Azure IoT v C# C nebo se všemi výhodami vývoje v rámci sady Visual Studio.
- Správa zařízení Azure IoT Edge a moduly pomocí uživatelského rozhraní.

V tomto článku se dozvíte, jak pomocí nástrojů pro Azure IoT Edge pro Visual Studio 2019 vyvíjet moduly IoT Edge. Také se dozvíte, jak nasadit svůj projekt na vaše zařízení Azure IoT Edge. V současné době poskytuje Visual Studio 2019 podporu pro moduly napsané v C#C a. Podporované architektury zařízení jsou Windows x64 a Linux x64 nebo ARM32. Další informace o podporovaných operačních systémech, jazycích a architekturách najdete v tématu [Podpora jazyků a architektur](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows jako vývojovém počítači. V počítačích s Windows můžete vyvíjet moduly pro Windows nebo Linux. Pro vývoj modulů Windows použijte počítač s Windows, na kterém běží verze 1809/Build 17763 nebo novější. Pokud chcete vyvíjet moduly pro Linux, použijte počítač s Windows, který splňuje [požadavky pro Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Vzhledem k tomu, že tento článek používá Visual Studio 2019 jako hlavní vývojový nástroj, nainstalujte Visual Studio. Při instalaci sady Visual Studio 2019 se ujistěte, že jste zahrnuli vývoj pro **vývoj** a **desktopy Azure s C++**  úlohami. Můžete [Upravit Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) a přidat požadované úlohy.

Po přípravě sady Visual Studio 2019 budete potřebovat také následující nástroje a komponenty:

- Stáhněte si a nainstalujte [Azure IoT Edge nástrojů](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) z webu Visual Studio Marketplace a vytvořte projekt IoT Edge v aplikaci visual Studio 2019.

> [!TIP]
> Pokud používáte Visual Studio 2017, Stáhněte si a nainstalujte [Azure IoT Edge nástroje](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro vs 2017 z webu Visual Studio Marketplace.

- Stáhněte a nainstalujte si [Docker Community Edition](https://docs.docker.com/install/) do vývojového počítače, abyste mohli sestavovat a spouštět image modulu. Je potřeba nastavit Docker CE tak, aby běžel v režimu kontejnerů Linux nebo v režimu kontejnerů systému Windows.

- Nastavte své místní vývojové prostředí pro ladění, spouštění a testování řešení IoT Edge instalací [vývojářského nástroje Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Nainstalujte [Python (2.7/3.6 +) a PIP](https://www.python.org/) a pak nainstalujte balíček **iotedgehubdev** spuštěním následujícího příkazu v terminálu. Ujistěte se, že váš nástroj pro vývojáře služby Azure IoT EdgeHub verze je větší než 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Naklonujte úložiště a nainstalujte správce knihovny Vcpkg a pak nainstalujte **balíček Azure-IoT-SDK-c** pro Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru.

- K otestování modulu v zařízení budete potřebovat aktivní službu IoT Hub s aspoň jedním IoT Edge zařízením. Pokud chcete počítač použít jako zařízení IoT Edge, postupujte podle kroků v rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud používáte IoT Edge démona ve vývojovém počítači, možná budete muset před zahájením vývoje v aplikaci Visual Studio zastavit EdgeHub a EdgeAgent.

### <a name="check-your-tools-version"></a>Zkontrolujte verzi nástroje

1. V nabídce **nástroje** vyberte **rozšíření a aktualizace**. Rozbalte položku **nainstalované nástroje >** a můžete najít **Azure IoT Edge nástroje** a **Průzkumníka cloudu pro Visual Studio**.

1. Poznamenejte si verzi nainstalovaného. Můžete porovnat tato verze na nejnovější verzi na Visual Studio Marketplace ([Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Pokud je vaše verze starší, než je k dispozici na Visual Studio Marketplace, aktualizujte nástroje v aplikaci Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizovat vaše nástroje

1. V dialogovém okně **rozšíření a aktualizace** rozbalte položku **aktualizace > Visual Studio Marketplace**, vyberte **Azure IoT Edge nástroje** nebo **Průzkumníka cloudu pro Visual Studio** a vyberte **aktualizovat**.

1. Po stažení aktualizace nástroje zavřete sadu Visual Studio na trigger nástroje aktualizace pomocí instalátor VSIX.

1. V instalačním programu vyberte **OK** , aby se spustila a pak se změnila aktualizace nástrojů.

1. Po dokončení aktualizace vyberte **Zavřít** a restartovat Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Vytvoření projektu Azure IoT Edge

Šablona projektu Azure IoT Edge v sadě Visual Studio vytvoří projekt, který je možné nasadit do zařízení Azure IoT Edge ve službě Azure IoT Hub. Nejprve vytvoříte řešení Azure IoT Edge a potom vygenerujete první modul v tomto řešení. Každé řešení IoT Edge může obsahovat více než jeden modul.

> [!TIP]
> Struktura IoT Edge projektu vytvořená v aplikaci Visual Studio není stejná jako v Visual Studio Code.

1. V dialogovém okně Nový projekt aplikace Visual Studio vyhledejte a vyberte **Azure IoT Edge** projekt a klikněte na **Další**. V okně konfigurace projektu zadejte název projektu a zadejte umístění a pak vyberte **vytvořit**. Výchozí název projektu je **AzureIoTEdgeApp1**.

   ![Vytvořit nový projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. V okně **Přidat IoT Edge aplikaci a modulu** vyberte  **C# modul nebo** **modul C** a pak zadejte název modulu a úložiště imagí modulu. Visual Studio automaticky vyplní název modulu pomocí **localhost: 5000/< název\>vašeho modulu**. Nahraďte ho vlastním registru informace. Pokud pro testování používáte místní registr Docker, pak je **localhost** v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá jako   **_\<název\>registru_. azurecr.IO**. Jenom nahraďte část **localhost: 5000** řetězce tak, aby konečný výsledek vypadal jako  **\< *název*\>registru. azurecr.IO/ _\<svůj název\>modulu_** . Výchozí název modulu je **IotEdgeModule1**

   ![Přidat aplikaci a modul](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Vyberte **OK** a vytvořte tak řešení Azure IoT Edge s modulem, který používá C# buď nebo C.

Teď máte projekt **AzureIoTEdgeApp1. Linux. amd64** nebo projekt **AzureIoTEdgeApp1. Windows. amd64** a také projekt **IotEdgeModule1** ve vašem řešení. Každý projekt **AzureIoTEdgeApp1** má `deployment.template.json` soubor, který definuje moduly, které chcete sestavit a nasadit pro vaše řešení IoT Edge, a také definuje trasy mezi moduly. Výchozí řešení má modul **SimulatedTemperatureSensor** a modul **IotEdgeModule1** . Modul **SimulatedTemperatureSensor** generuje Simulovaná data do modulu **IotEdgeModule1** , zatímco výchozí kód v modulu **IotEdgeModule1** přímo předává přijaté zprávy do služby Azure IoT Hub.

Projekt **IotEdgeModule1** je Konzolová aplikace .net Core 2,1, pokud se jedná o C# modul. Obsahuje požadované soubory Docker, které potřebujete pro zařízení IoT Edge se systémem pomocí kontejneru Windows nebo kontejneru Linux. `module.json` Soubor popisuje metadata modulu. Skutečný kód modulu, který používá sadu SDK pro zařízení Azure IoT jako závislost, se nachází v `Program.cs` souboru nebo. `main.c`

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí kód modulu, který je součástí řešení, je umístěn na adrese **IotEdgeModule1** > **program.cs** ( C#pro) nebo **Main. c** (c). Modul a `deployment.template.json` soubor jsou nastavené tak, aby bylo možné sestavit řešení, nasdílet ho do registru kontejnerů a nasazovat ho do zařízení, aby se spouštělo testování bez zásahu jakéhokoli kódu. Modul je sestavený tak, aby převzal vstup ze zdroje (v tomto případě modul **SimulatedTemperatureSensor** , který simuluje data) a přesměruje ho do Azure IoT Hub.

Až budete připraveni přizpůsobit šablonu modulu vlastním kódem, použijte sady [SDK pro Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) k sestavování modulů, které řeší klíčová úložiště pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializovat iotedgehubdev s připojovacím řetězcem zařízení IoT Edge

1. Zkopírujte připojovací řetězec libovolného IoT Edge zařízení z **primárního připojovacího řetězce** v Průzkumníku cloudu sady Visual Studio. Ujistěte se, že nekopírujete připojovací řetězec nehraničního zařízení, protože ikona zařízení IoT Edge se liší od ikony zařízení, které není hraniční.

   ![Zkopírujte připojovací řetězec zařízení Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Přejděte na **nástroje nástroje** > **Azure IoT Edge nástroje** > **Nastavení IoT Edge simulátor**, vložte připojovací řetězec a klikněte na **OK**.

   ![Okno sady Edge připojovací řetězec](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Zadejte připojovací řetězec z prvního kroku a pak vyberte **OK**.

> [!NOTE]
> Tento postup je třeba provést pouze jednou ve vývojovém počítači, protože výsledky jsou automaticky aplikovány na všechna následující Azure IoT Edge řešení. Tento postup můžete znovu provést, pokud potřebujete změnit jiný připojovací řetězec.

## <a name="build-and-debug-single-module"></a>Sestavovat a ladit jeden modul

Obvykle budete chtít otestovat a ladit každý modul před jeho spuštěním v rámci celého řešení s více moduly.

1. Klikněte pravým tlačítkem na **IotEdgeModule1** a v místní nabídce vyberte **nastavit jako spouštěný projekt** .

   ![Nastavit spouštěcí projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Stisknutím klávesy **F5** nebo kliknutím na tlačítko níže spusťte modul. Když to uděláte poprvé&ndash;, může to trvat 10 minut.

   ![Spusťte modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Pokud byl modul úspěšně inicializován, měla by se zobrazit konzola aplikace .NET Core.

   ![Spuštěný modul](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Při vývoji v C#systému nastavte zarážku ve `PipeMessage()` funkci v **program.cs**; Pokud používáte jazyk C `InputQueue1Callback()` , nastavte zarážku ve funkci v **Main. C**. Pak ji můžete otestovat odesláním zprávy spuštěním následujícího příkazu v prostředí **Git bash** nebo **WSL bash** Shell. (Nemůžete spustit `curl` příkaz z PowerShellu nebo příkazového řádku.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Ladění jednoho modulu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Zarážka by měla aktivovat. Proměnné můžete sledovat v okně **místní** hodnoty sady Visual Studio.

   > [!TIP]
   > K posílání zpráv [](https://www.getpostman.com/) místo toho `curl`můžete použít taky post nebo jiné nástroje API.

1. Stisknutím klávesy **Ctrl + F5** nebo klikněte na tlačítko stop, Zastavit ladění.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Sestavování a ladění řešení IoT Edge s několika cloudy

Až dokončíte vývoj jednoho modulu, můžete chtít spustit a ladit celé řešení s několika moduly.

1. Přidejte do řešení druhý modul tak, že kliknete pravým tlačítkem na **AzureIoTEdgeApp1** a vyberete **Přidat** > **Nový IoT Edge modul**. Výchozí název druhého modulu je **IotEdgeModule2** a bude fungovat jako jiný modul kanálu.

1. Otevřete tento soubor `deployment.template.json` a uvidíte, že se v části **moduly** přidala **IotEdgeModule2** . Nahradit **trasy** oddíl následujícím kódem. Pokud jste přizpůsobili názvy modulů, ujistěte se, že aktualizujete tyto názvy tak, aby odpovídaly.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a v místní nabídce vyberte **nastavit jako spouštěný projekt** .

1. Vytvořte zarážky a potom stiskněte klávesu **F5** ke spuštění a ladění více modulů současně. Mělo by se zobrazit několik oken konzolových aplikací .NET Core, která jednotlivá okna představují jiný modul.

   ![Ladění více modulů](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Stisknutím **kombinace kláves CTRL + F5** nebo kliknutím na tlačítko Zastavit Zastavte ladění.

## <a name="build-and-push-images"></a>Vytváření a nasdílení změn imagí

1. Ujistěte se, že **AzureIoTEdgeApp1** je spouštěcí projekt. Vyberte buď **ladění** , nebo **vydání** jako konfiguraci pro sestavení imagí modulu.

    > [!NOTE]
    > Při výběru možnosti **ladit**aplikace Visual Studio `Dockerfile.(amd64|windows-amd64).debug` používá k sestavení imagí Docker. To zahrnuje příkazového řádku debugger .NET Core VSDBG ve vaší imagi kontejneru při jeho vytváření. Pro moduly IoT Edge připravené pro produkční prostředí doporučujeme použít konfiguraci **vydané verze** , která se používá `Dockerfile.(amd64|windows-amd64)` bez VSDBG.

1. Pokud používáte privátní registr, jako je Azure Container Registry, přihlaste se k němu pomocí následujícího příkazu Docker. Pokud používáte místní registr, můžete [Spustit místní registr](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Pokud používáte privátní registr, jako je Azure Container Registry, je nutné přidat přihlašovací informace registru do nastavení modulu runtime zjištěného v souboru `deployment.template.json`. Zástupné symboly nahraďte skutečným uživatelským jménem správce ACR, heslem a názvem registru.

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

1. Klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a vyberte **řešení sestavení a nabízených oznámení** pro sestavení a vložení image Docker pro každý modul.

   ![Vytváření a nasdílení změn Imagí](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Nasazení řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Můžete také nasadit moduly pomocí Průzkumníka cloudu sady Visual Studio. Už máte připravený manifest nasazení pro váš scénář, `deployment.json` soubor a vše, co je potřeba udělat, je vybrat zařízení pro příjem nasazení.

1. Otevřít **Průzkumníka cloudu** kliknutím **zobrazení** > **Průzkumníka cloudu**. Ujistěte se, že jste přihlášeni k aplikaci Visual Studio 2019.

1. V **Průzkumníka cloudu**, rozšířit vaše předplatné, vyhledání služby Azure IoT Hub a zařízení Azure IoT Edge, které chcete nasadit.

1. Po kliknutí pravým tlačítkem myši na IoT Edge zařízení vytvořte nasazení pro něj, je nutné vybrat soubor manifestu nasazení v části `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Nesmí vyberete `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Kliknutím na tlačítko Aktualizovat zobrazíte nové moduly běžící společně s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. Chcete-li monitorovat zprávu D2C pro konkrétní zařízení, vyberte zařízení v seznamu a potom v okně **Akce** klikněte na možnost **Spustit sledování vestavěného koncového bodu události** .

1. Chcete-li zastavit sledování dat, vyberte zařízení v seznamu a potom v okně **Akce** vyberte možnost **Zastavit sledování vestavěného koncového bodu události** .

## <a name="next-steps"></a>Další postup

Pro vývoj vlastních modulů pro IoT Edge zařízení, [pochopení a používání sady Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
