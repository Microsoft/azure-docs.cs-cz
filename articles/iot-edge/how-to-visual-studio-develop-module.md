---
title: Vývoj a ladění modulů ve Visual Studiu – Azure IoT Edge
description: Pomocí Visual Studia s nástroji Azure IoT můžete vyvinout modul C nebo C# IoT Edge a přetlačit ho z ioT hubu na zařízení IoT, jak je nakonfiguroval manifest nasazení.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384853"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Použití Visual Studia 2019 k vývoji a ladění modulů pro Azure IoT Edge

Svou obchodní logiku můžete převést na moduly pro Azure IoT Edge. Tento článek ukazuje, jak používat Visual Studio 2019 jako hlavní nástroj pro vývoj a ladění modulů.

Nástroje Azure IoT Edge pro Visual Studio poskytují následující výhody:

- Vytvářejte, upravujte, vytvářejte, spouštějte a laděte řešení a moduly Azure IoT Edge v místním vývojovém počítači.
- Nasaďte si své řešení Azure IoT Edge do zařízení Azure IoT Edge prostřednictvím služby Azure IoT Hub.
- Kód ovací moduly Azure IoT v Jazyce C nebo C# a přitom mít všechny výhody vývoje Visual Studia.
- Spravujte zařízení a moduly Azure IoT Edge pomocí ui.

Tento článek ukazuje, jak používat nástroje Azure IoT Edge pro Visual Studio 2019 k vývoji modulů IoT Edge. Dozvíte se také, jak nasadit svůj projekt do zařízení Azure IoT Edge. V současné době Visual Studio 2019 poskytuje podporu pro moduly napsané v jazyce C a C#. Podporované architektury zařízení jsou Windows X64 a Linux X64 nebo ARM32. Další informace o podporovaných operačních systémech, jazycích a architekturách naleznete v [tématu Podpora jazyků a architektury](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows jako vývojový počítač. V počítačích se systémem Windows můžete vyvíjet moduly systému Windows nebo Linux. Chcete-li vyvíjet moduly systému Windows, použijte počítač se systémem Windows s verzí 1809/build 17763 nebo novější. Chcete-li vyvíjet moduly Linuxu, použijte počítač se systémem Windows, který splňuje [požadavky na dockerovou plochu](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Vzhledem k tomu, že tento článek používá Visual Studio 2019 jako hlavní vývojový nástroj, nainstalujte Visual Studio. Ujistěte se, že do instalace Visual Studia 2019 zahrnete **vývoj a** vývoj azure **s úlohami C++.** Můžete [upravit Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) přidat požadované úlohy.

Až bude Visual Studio 2019 připravené, budete potřebovat taky následující nástroje a součásti:

- Stáhněte a nainstalujte [nástroje Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) z webu Visual Studio marketplace a vytvořte projekt IoT Edge ve Visual Studiu 2019.

> [!TIP]
> Pokud používáte Visual Studio 2017, stáhněte a nainstalujte [nástroje Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro VS 2017 z webu Visual Studio.

- Stáhněte a nainstalujte [Docker Community Edition](https://docs.docker.com/install/) do vývojového počítače a vytvořte a spusťte ifotky modulů. Budete muset nastavit Docker CE pro spuštění v režimu kontejneru Linux nebo Windows kontejneru.

- Nastavte místní vývojové prostředí pro ladění, spuštění a testování řešení IoT Edge instalací [nástroje Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/). Nainstalujte [Python (2.7/3.6+) a Pip](https://www.python.org/) a pak nainstalujte balíček **iotedgehubdev** spuštěním následujícího příkazu ve vašem terminálu. Ujistěte se, že vaše verze nástroje Azure IoT EdgeHub Dev Tool je větší než 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Klonujte úložiště a nainstalujte správce knihovny Vcpkg a pak nainstalujte **balíček azure-iot-sdk-c** pro Windows.

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
  > Místní registr Dockeru můžete použít pro účely prototypu a testování namísto registru cloudu.

- K testování modulu na zařízení budete potřebovat aktivní centrum IoT hub s alespoň jedním zařízením IoT Edge. Chcete-li počítač používat jako zařízení IoT Edge, postupujte podle pokynů v rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud používáte daemon IoT Edge ve vývojovém počítači, možná budete muset zastavit EdgeHub a EdgeAgent před zahájením vývoje v sadě Visual Studio.

### <a name="check-your-tools-version"></a>Zkontrolujte verzi nástrojů

1. V nabídce **Rozšíření** vyberte **Spravovat rozšíření**. Rozbalte **Nainstalované nástroje >** a najdete **nástroje Azure IoT Edge pro Visual Studio** a Cloud Explorer pro Visual **Studio**.

1. Všimněte si nainstalované verze. Tuto verzi můžete porovnat s nejnovější verzí na webu Visual Studio Marketplace[(Průzkumník cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)

1. Pokud je vaše verze starší než dostupná na webu Visual Studio Marketplace, aktualizujte nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizace nástrojů

1. V okně **Spravovat rozšíření** **rozbalte aktualizace > webu Visual Studio Marketplace**, vyberte Nástroje Azure **IoT Edge** nebo **Průzkumníka Cloudpro Visual Studio** a vyberte **Aktualizovat**.

1. Po stažení aktualizace nástrojů zavřete Visual Studio a spusťte aktualizaci nástrojů pomocí instalačního programu VSIX.

1. V instalačním programu vyberte **tlačítko OK,** které chcete spustit, a **pak Upravit,** chcete-li aktualizovat nástroje.

1. Po dokončení aktualizace vyberte **Zavřít** a restartujte Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Vytvoření projektu Azure IoT Edge

Šablona projektu Azure IoT Edge ve Visual Studiu vytvoří projekt, který se dá nasadit do zařízení Azure IoT Edge v Azure IoT Hub. Nejprve vytvoříte řešení Azure IoT Edge a pak vygenerujete první modul v tomto řešení. Každé řešení IoT Edge může obsahovat více než jeden modul.

> [!TIP]
> Struktura projektu IoT Edge vytvořená visual studio není stejná jako v kódu sady Visual Studio.

1. V dialogovém okně nového projektu Visual Studia vyhledejte a vyberte projekt **Azure IoT Edge** a klikněte na **Další**. V okně konfigurace projektu zadejte název projektu a zadejte umístění a pak vyberte **Vytvořit**. Výchozí název projektu je **AzureIoTEdgeApp1**.

   ![Vytvoření nového projektu](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. V okně **Přidat aplikaci a modul Okraje IoT** vyberte buď **modul C#** nebo **modul C** a zadejte název modulu a úložiště bitové kopie modulu. Visual Studio automaticky naplní název modulu **localhost:5000/<název\>modulu**. Nahraďte jej vlastními informacemi o registru. Pokud používáte místní registr Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, použijte přihlašovací server z nastavení registru. Přihlašovací server vypadá jako ** _ \<název\>registru_.azurecr.io**. Nahraďte pouze část řetězce **localhost:5000** tak, aby konečný výsledek vypadal jako ** \< *název*\>registru .azurecr.io/_\<název\>modulu_**. Výchozí název modulu je **IotEdgeModule1**

   ![Přidat aplikaci a modul](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Vyberte **OK** a vytvořte řešení Azure IoT Edge s modulem, který používá C# nebo C.

Teď máte projekt **AzureIoTEdgeApp1.Linux.Amd64** nebo projekt **AzureIoTEdgeApp1.Windows.Amd64** a také projekt **IotEdgeModule1** ve vašem řešení. Každý projekt **AzureIoTEdgeApp1** má `deployment.template.json` soubor, který definuje moduly, které chcete vytvořit a nasadit pro vaše řešení IoT Edge a také definuje trasy mezi moduly. Výchozí řešení má modul **SimulatedTemperatureSensor** a modul **IotEdgeModule1.** Modul **SimulatedTemperatureSensor** generuje simulovaná data do modulu **IotEdgeModule1,** zatímco výchozí kód v modulu **IotEdgeModule1** přímo přepězuje přijaté zprávy do služby Azure IoT Hub.

Chcete-li zjistit, jak funguje simulovaný teplotní senzor, zobrazte [zdrojový kód SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

Projekt **IotEdgeModule1** je konzolová aplikace .NET Core 2.1, pokud se jedná o modul Jazyka C#. Obsahuje požadované soubory Dockeru, které potřebujete pro vaše zařízení IoT Edge běžící s kontejnerem Windows nebo linuxovým kontejnerem. Soubor `module.json` popisuje metadata modulu. Skutečný kód modulu, který přebírá Azure IoT Device SDK `Program.cs` jako `main.c` závislost, se nachází v souboru nebo.

## <a name="develop-your-module"></a>Vyvíjejte svůj modul

Výchozí kód modulu, který je dodáván s řešením, je umístěn na **adrese IotEdgeModule1** > **Program.cs** (pro C#) nebo **main.c** (C). Modul a `deployment.template.json` soubor jsou nastaveny tak, aby bylo možné vytvořit řešení, přesunout jej do registru kontejneru a nasadit jej do zařízení a spustit testování bez dotyku jakéhokoli kódu. Modul je vytvořen tak, aby převzít vstup ze zdroje (v tomto případě **modul SimulatedTemperatureSensor,** který simuluje data) a potrubí do služby Azure IoT Hub.

Až budete připraveni přizpůsobit šablonu modulu s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) k vytvoření modulů, které řeší klíčové potřeby řešení IoT, jako je zabezpečení, správa zařízení a spolehlivost.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializovat iotedgehubdev pomocí připojovacího řetězce zařízení IoT Edge

1. Zkopírujte připojovací řetězec libovolného zařízení IoT Edge z **primárního připojovacího řetězce** v Průzkumníkovi cloudu sady Visual Studio. Nekopírujte připojovací řetězec zařízení, které není edge, protože ikona zařízení IoT Edge se liší od ikony zařízení jiného než Edge.

   ![Kopírovat připojovací řetězec zařízení Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. V nabídce **Nástroje** vyberte Azure **IoT Edge Tools** > **Setup IoT Edge Simulator**, vložte připojovací řetězec a klepněte na tlačítko **OK**.

   ![Otevřít okno připojovacího řetězce sady okrajů](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Zadejte připojovací řetězec od prvního kroku a pak vyberte **OK**.

> [!NOTE]
> Tyto kroky je třeba provést pouze jednou ve vývojovém počítači, protože výsledky se automaticky použijí na všechna následná řešení Azure IoT Edge. Tento postup lze provést znovu, pokud potřebujete změnit na jiný připojovací řetězec.

## <a name="build-and-debug-single-module"></a>Sestavení a ladění jednoho modulu

Obvykle budete chtít otestovat a ladit každý modul před spuštěním v rámci celého řešení s více moduly.

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **iotEdgeModule1** a z kontextové nabídky vyberte **Nastavit jako projekt spuštění.**

   ![Nastavit počáteční projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Stisknutím **klávesy F5** nebo kliknutím na tlačítko níže spusťte modul; poprvé může&ndash;trvat 10 20 sekund.

   ![Spustit modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Měli byste vidět spuštění konzoly .NET Core, pokud byl modul úspěšně inicializován.

   ![Modul spuštěný](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Pokud se vyvíjí v C#, `PipeMessage()` nastavte zarážku ve funkci v **Program.cs**; Pokud používáte C, nastavte `InputQueue1Callback()` zarážku ve funkci v **main.c**. Poté ji můžete otestovat odesláním zprávy spuštěním následujícího příkazu v prostředí **Git Bash** nebo **WSL Bash.** `curl` (Příkaz nelze spustit z prostředí PowerShell nebo z příkazového řádku.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Ladění jednoho modulu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Zarážka by měla být spuštěna. Proměnné můžete sledovat v okně **Místní** nastavení sady Visual Studio.

   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje `curl`rozhraní API k odesílání zpráv namísto .

1. Stisknutím **kláves Ctrl + F5** nebo kliknutím na tlačítko stop můžete ladění zastavit.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Sestavení a ladění řešení IoT Edge s více moduly

Po dokončení vývoje jednoho modulu můžete spustit a ladit celé řešení s více moduly.

1. V **Průzkumníku řešení**přidejte do řešení druhý modul tak, že kliknete pravým tlačítkem myši na **AzureIoTEdgeApp1** a vyberete **přidat** > **nový modul IoT Edge Module**. Výchozí název druhého modulu je **IotEdgeModule2** a bude fungovat jako jiný modul kanálu.

1. Otevřete `deployment.template.json` soubor a uvidíte, že **iotEdgeModule2** byl přidán v sekci **moduly.** Nahraďte úsek **trasy** následujícím. Pokud jste přizpůsobili názvy modulů, ujistěte se, že aktualizujete tyto názvy tak, aby odpovídaly.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a z kontextové nabídky vyberte **Nastavit jako projekt spuštění.**

1. Vytvořte zarážky a stisknutím **klávesy F5** spusťte a laděte více modulů současně. Měli byste vidět více oken konzoly .NET Core, které každé okno představuje jiný modul.

   ![Ladění více modulů](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Stisknutím **kláves Ctrl + F5** nebo vyberte tlačítko stop, které zastaví ladění.

## <a name="build-and-push-images"></a>Vytváření a nabízení obrázků

1. Ujistěte se, že **AzureIoTEdgeApp1** je počáteční projekt. Vyberte buď **ladění** nebo **uvolnění** jako konfiguraci pro sestavení pro image modulu.

    > [!NOTE]
    > Při výběru **ladění**, `Dockerfile.(amd64|windows-amd64).debug` Visual Studio používá k vytváření iniciací Dockeru. To zahrnuje ladicí program příkazového řádku .NET Core VSDBG v image kontejneru při jeho vytváření. Pro moduly IoT Edge připravené na produkční prostředí doporučujeme `Dockerfile.(amd64|windows-amd64)` použít konfiguraci **release,** která používá bez VSDBG.

1. Pokud používáte soukromý registr, jako je Azure Container Registry (ACR), přihlaste se k němu pomocí následujícího příkazu Dockeru.  Uživatelské jméno a heslo můžete získat na stránce **přístupových klíčů** v registru na webu Azure Portal. Pokud používáte místní registr, můžete [spustit místní registr](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Pokud používáte soukromý registr, jako je Azure Container Registry, musíte přidat přihlašovací údaje registru `deployment.template.json`do nastavení běhu nalezeného v souboru . Nahraďte zástupné symboly skutečným uživatelským jménem správce ACR, heslem a názvem registru.

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

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na **AzureIoTEdgeApp1** a vyberte **build and Push IoT Edge modules,** abyste vytvořili a posunuli image Dockeru pro každý modul.

## <a name="deploy-the-solution"></a>Nasazení řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Moduly můžete také nasadit pomocí Průzkumníka cloudu pro Visual Studio. Již máte manifest nasazení připravený pro `deployment.json` váš scénář, soubor a vše, co musíte udělat, je vybrat zařízení pro příjem nasazení.

1. Sem **otevřete Průzkumníka cloudu** klepnutím na **zobrazit** > **Průzkumníka cloudu**. Ujistěte se, že jste přihlášeni do Visual Studia 2019.

1. V **Průzkumníkovi Cloudu**rozbalte předplatné a najděte azure iot hub a zařízení Azure IoT Edge, které chcete nasadit.

1. Klikněte pravým tlačítkem myši na zařízení IoT Edge a vytvořte pro něj nasazení. Přejděte na manifest nasazení nakonfigurovaný pro vaši platformu umístěnou ve složce **config** v řešení sady Visual Studio, například `deployment.arm32v7.json`.

1. Kliknutím na tlačítko aktualizovat zobrazíte nové moduly běžící spolu s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. Pokud chcete sledovat zprávu D2C pro konkrétní zařízení IoT-Edge, vyberte ji ve svém centru IoT v **Průzkumníkovi Clouda** a v okně **Akce** klikněte na **Spustit monitorování integrovaného koncového bodu událostí.**

1. Chcete-li zastavit sledování dat, vyberte v okně **Akce** možnost **Zastavit sledování vestavěného koncového bodu událostí.**

## <a name="next-steps"></a>Další kroky

Chcete-li vyvíjet vlastní moduly pro vaše zařízení IoT Edge, [pochopte a použijte sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
