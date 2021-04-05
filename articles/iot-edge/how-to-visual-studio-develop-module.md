---
title: Vývoj a ladění modulů v aplikaci Visual Studio – Azure IoT Edge
description: Pomocí sady Visual Studio s nástroji Azure IoT můžete vyvinout modul IoT Edge jazyka C nebo C# a vložit ho z IoT Hub do zařízení IoT, jak je nakonfigurované v manifestu nasazení.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 6e5b5c021eb6a83de9ecfb31757855065b70c290
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103196941"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Použití sady Visual Studio 2019 k vývoji a ladění modulů pro Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Obchodní logiku můžete převést do modulů pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio 2019 jako hlavní nástroj pro vývoj a ladění modulů.

Nástroje pro Azure IoT Edge pro Visual Studio přináší následující výhody:

- Vytváření, úpravy, sestavování, spouštění a ladění Azure IoT Edgech řešení a modulů na místním vývojovém počítači.
- Nasaďte řešení Azure IoT Edge do Azure IoT Edge zařízení prostřednictvím Azure IoT Hub.
- Nahlaste své moduly Azure IoT v jazyce C nebo C# a využijte výhod vývoje sady Visual Studio.
- Spravujte Azure IoT Edge zařízení a moduly s uživatelským rozhraním.

V tomto článku se dozvíte, jak pomocí nástrojů pro Azure IoT Edge pro Visual Studio 2019 vyvíjet moduly IoT Edge. Naučíte se také, jak nasadit projekt do zařízení Azure IoT Edge. V současné době poskytuje Visual Studio 2019 podporu pro moduly napsané v jazyce C a C#. Podporované architektury zařízení jsou Windows x64 a Linux x64 nebo ARM32. Další informace o podporovaných operačních systémech, jazycích a architekturách najdete v tématu [Podpora jazyků a architektur](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že používáte počítač nebo virtuální počítač s Windows jako vývojový počítač. V počítačích s Windows můžete vyvíjet moduly pro Windows nebo Linux. Pro vývoj modulů Windows použijte počítač s Windows, na kterém běží verze 1809/Build 17763 nebo novější. Pokud chcete vyvíjet moduly pro Linux, použijte počítač s Windows, který splňuje [požadavky pro Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Vzhledem k tomu, že tento článek používá Visual Studio 2019 jako hlavní vývojový nástroj, nainstalujte Visual Studio. V instalaci sady Visual Studio 2019 se ujistěte, že jste zahrnuli vývoj pro vývoj a **desktopové** prostředí **Azure** s úlohami C++. Můžete [Upravit Visual Studio 2019](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) a přidat požadované úlohy.

Po přípravě sady Visual Studio 2019 budete potřebovat také následující nástroje a komponenty:

- Stáhněte si a nainstalujte [Azure IoT Edge nástrojů](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) z webu Visual Studio Marketplace a vytvořte projekt IoT Edge v aplikaci visual Studio 2019.

> [!TIP]
> Pokud používáte Visual Studio 2017, Stáhněte si a nainstalujte [Azure IoT Edge nástroje](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pro vs 2017 z webu Visual Studio Marketplace.

- Stáhněte a nainstalujte si [Docker Community Edition](https://docs.docker.com/install/) do vývojového počítače, abyste mohli sestavovat a spouštět image modulu. Je potřeba nastavit Docker CE tak, aby běžel v režimu kontejnerů Linux nebo v režimu kontejnerů systému Windows.

- Nastavte své místní vývojové prostředí pro ladění, spouštění a testování řešení IoT Edge instalací [vývojářského nástroje Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Nainstalujte [Python (2.7/3.6 +) a PIP](https://www.python.org/) a pak nainstalujte balíček **iotedgehubdev** spuštěním následujícího příkazu v terminálu. Ujistěte se, že verze vývojářského nástroje Azure IoT EdgeHub je větší než 0.3.0.

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

- [Azure Container Registry](../container-registry/index.yml) nebo [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Místo registru cloudu můžete použít místní registr Docker pro účely prototypů a testování.

- K otestování modulu v zařízení budete potřebovat aktivní službu IoT Hub s aspoň jedním IoT Edge zařízením. Pokud chcete počítač použít jako zařízení IoT Edge, postupujte podle kroků v rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud používáte IoT Edge démona ve vývojovém počítači, možná budete muset před zahájením vývoje v aplikaci Visual Studio zastavit EdgeHub a EdgeAgent.

### <a name="check-your-tools-version"></a>Podívejte se na verzi nástrojů

1. V nabídce **rozšíření** vyberte **Spravovat rozšíření**. Rozbalte položku **nainstalované nástroje >** a můžete najít **Azure IoT Edge Tools for Visual Studio** a **Průzkumníka cloudu pro Visual Studio**.

1. Poznamenejte si nainstalovanou verzi. Tuto verzi můžete porovnat s nejnovější verzí v Visual Studio Marketplace ([Průzkumník cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Pokud je vaše verze starší, než je k dispozici na Visual Studio Marketplace, aktualizujte nástroje v aplikaci Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizace nástrojů

1. V okně **Spravovat rozšíření** rozbalte položku **aktualizace > Visual Studio Marketplace**, vyberte **Azure IoT Edge nástroje** nebo **Průzkumníka cloudu pro Visual Studio** a vyberte **aktualizovat**.

1. Po stažení aktualizace nástrojů zavřete Visual Studio, aby se aktivovala aktualizace nástrojů pomocí instalačního programu VSIX.

1. V instalačním programu vyberte **OK** , aby se spustila a pak se **změnila** aktualizace nástrojů.

1. Po dokončení aktualizace vyberte **Zavřít** a restartovat Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Vytvoření projektu Azure IoT Edge

Šablona projektu Azure IoT Edge v aplikaci Visual Studio vytvoří projekt, který lze nasadit do Azure IoT Edge zařízení v Azure IoT Hub. Nejprve vytvoříte řešení Azure IoT Edge a potom vygenerujete první modul v tomto řešení. Každé řešení IoT Edge může obsahovat více než jeden modul.

> [!TIP]
> Struktura IoT Edge projektu vytvořená v aplikaci Visual Studio není stejná jako v Visual Studio Code.

1. V dialogovém okně Nový projekt aplikace Visual Studio vyhledejte a vyberte **Azure IoT Edge** projekt a klikněte na **Další**. V okně konfigurace projektu zadejte název projektu a zadejte umístění a pak vyberte **vytvořit**. Výchozí název projektu je **AzureIoTEdgeApp1**.

   ![Vytvoření nového projektu](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. V okně **přidat IoT Edge aplikaci a modul** vyberte buď **modul C#** , nebo **modul C** a pak zadejte název modulu a úložiště imagí modulu. Visual Studio automaticky vyplní název modulu pomocí **localhost: 5000/<název \> vašeho modulu**. Nahraďte je vlastními informacemi v registru. Pokud pro testování používáte místní registr Docker, pak je **localhost** v pořádku. Pokud používáte Azure Container Registry, použijte přihlašovací server z nastavení registru. Přihlašovací server vypadá jako **_\<registry name\>_ . azurecr.IO**. Nahradí jenom část **localhost: 5000** řetězce, aby konečný výsledek vypadal jako **\<*registry name*\> _\<your module name\>_ . azurecr.IO/**. Výchozí název modulu je **IotEdgeModule1**

   ![Přidat aplikaci a modul](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Vyberte **OK** a vytvořte tak řešení Azure IoT Edge s modulem, který používá C# nebo C.

Teď máte projekt **AzureIoTEdgeApp1. Linux. amd64** nebo projekt **AzureIoTEdgeApp1. Windows. amd64** a také projekt **IotEdgeModule1** ve vašem řešení. Každý projekt **AzureIoTEdgeApp1** má `deployment.template.json` soubor, který definuje moduly, které chcete sestavit a nasadit pro vaše řešení IoT Edge, a také definuje trasy mezi moduly. Výchozí řešení má modul **SimulatedTemperatureSensor** a modul **IotEdgeModule1** . Modul **SimulatedTemperatureSensor** generuje Simulovaná data do modulu **IotEdgeModule1** , zatímco výchozí kód v modulu **IotEdgeModule1** přímo předává přijaté zprávy do služby Azure IoT Hub.

Chcete-li zjistit, jak senzor simulované teploty funguje, zobrazte [zdrojový kód SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

Projekt **IotEdgeModule1** je Konzolová aplikace .net Core 2,1, pokud se jedná o modul C#. Obsahuje požadované soubory Docker, které potřebujete pro zařízení IoT Edge se systémem pomocí kontejneru Windows nebo kontejneru Linux. `module.json`Soubor popisuje metadata modulu. Skutečný kód modulu, který používá sadu SDK pro zařízení Azure IoT jako závislost, se nachází v `Program.cs` souboru nebo `main.c` .

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí kód modulu, který je součástí řešení, je umístěn v **IotEdgeModule1**  >  **program. cs** (pro jazyk C#) nebo **Main. c** (c). Modul a `deployment.template.json` soubor jsou nastavené tak, aby bylo možné sestavit řešení, nasdílet ho do registru kontejnerů a nasazovat ho do zařízení, aby se spouštělo testování bez zásahu jakéhokoli kódu. Modul je sestavený tak, aby převzal vstup ze zdroje (v tomto případě modul **SimulatedTemperatureSensor** , který simuluje data) a přesměruje ho do Azure IoT Hub.

Až budete připraveni přizpůsobit šablonu modulu vlastním kódem, použijte sady [SDK pro Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) k sestavování modulů, které řeší klíčová úložiště pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializovat iotedgehubdev s připojovacím řetězcem zařízení IoT Edge

1. Zkopírujte připojovací řetězec libovolného IoT Edge zařízení z **primárního připojovacího řetězce** v Průzkumníku cloudu sady Visual Studio. Ujistěte se, že nekopírujete připojovací řetězec nehraničního zařízení, protože ikona zařízení IoT Edge se liší od ikony zařízení, které není hraniční.

   ![Kopírovat připojovací řetězec hraničního zařízení](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. V nabídce **nástroje** vyberte **Azure IoT Edge nástroje**  >  **nastavení nástrojů IoT Edge simulátor**, vložte připojovací řetězec a klikněte na **OK**.

   ![Otevřít okno nastavení připojovacího řetězce Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Zadejte připojovací řetězec z prvního kroku a pak vyberte **OK**.

> [!NOTE]
> Tento postup je třeba provést pouze jednou ve vývojovém počítači, protože výsledky jsou automaticky aplikovány na všechna následující Azure IoT Edge řešení. Tento postup můžete znovu provést, pokud potřebujete změnit jiný připojovací řetězec.

## <a name="build-and-debug-single-module"></a>Sestavovat a ladit jeden modul

Obvykle budete chtít otestovat a ladit každý modul před jeho spuštěním v rámci celého řešení s více moduly.

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na **IotEdgeModule1** a v místní nabídce vyberte **nastavit jako spouštěný projekt** .

   ![Nastavit spouštěcí projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Stisknutím klávesy **F5** nebo kliknutím na tlačítko níže spusťte modul. Když to uděláte poprvé, může to trvat 10 &ndash; minut.

   ![Spustit modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Pokud byl modul úspěšně inicializován, měla by se zobrazit konzola aplikace .NET Core.

   ![Spuštěný modul](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Při vývoji v jazyce C# nastavte zarážku ve `PipeMessage()` funkci v **programu program. cs**; při použití jazyka C nastavte zarážku ve `InputQueue1Callback()` funkci v **Main. C**. Pak ji můžete otestovat odesláním zprávy spuštěním následujícího příkazu v prostředí **Git bash** nebo **WSL bash** Shell. (Nemůžete spustit `curl` příkaz z PowerShellu nebo příkazového řádku.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Ladit jeden modul](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Měla by se aktivovat zarážka. Proměnné můžete sledovat v okně **místní** hodnoty sady Visual Studio.

   > [!TIP]
   > K posílání zpráv místo toho můžete použít taky [post](https://www.getpostman.com/) nebo jiné nástroje API `curl` .

1. Stisknutím **kombinace kláves CTRL + F5** nebo kliknutím na tlačítko Zastavit Zastavte ladění.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Sestavení a ladění IoT Edge řešení s několika moduly

Až dokončíte vývoj jednoho modulu, můžete chtít spustit a ladit celé řešení s několika moduly.

1. V **Průzkumník řešení** přidejte do řešení druhý modul tak, že kliknete pravým tlačítkem na **AzureIoTEdgeApp1** a vyberete **Přidat**  >  **Nový IoT Edge modul**. Výchozí název druhého modulu je **IotEdgeModule2** a bude fungovat jako jiný modul kanálu.

1. Otevřete tento soubor `deployment.template.json` a uvidíte, že se v části **moduly** přidala **IotEdgeModule2** . V části **trasy** nahraďte následujícím. Pokud jste přizpůsobili názvy modulů, ujistěte se, že aktualizujete tyto názvy tak, aby odpovídaly.

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

## <a name="build-and-push-images"></a>Sestavování a vkládání imagí

1. Ujistěte se, že **AzureIoTEdgeApp1** je spouštěcí projekt. Vyberte buď **ladění** , nebo **vydání** jako konfiguraci pro sestavení imagí modulu.

    > [!NOTE]
    > Při výběru možnosti **ladit** aplikace Visual Studio používá `Dockerfile.(amd64|windows-amd64).debug` k sestavení imagí Docker. To zahrnuje ladicí program příkazového řádku .NET Core VSDBG do image kontejneru při jeho vytváření. Pro moduly IoT Edge připravené pro produkční prostředí doporučujeme použít konfiguraci **vydané verze** , která se používá `Dockerfile.(amd64|windows-amd64)` bez VSDBG.

1. Pokud používáte privátní registr, jako je Azure Container Registry (ACR), přihlaste se k němu pomocí následujícího příkazu Docker.  Uživatelské jméno a heslo můžete získat ze stránky **přístupové klávesy** v registru v Azure Portal. Pokud používáte místní registr, můžete [Spustit místní registr](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Pokud používáte privátní registr, jako je Azure Container Registry, je nutné přidat přihlašovací informace registru do nastavení modulu runtime zjištěného v souboru `deployment.template.json` . Zástupné symboly nahraďte skutečným uživatelským jménem správce ACR, heslem a názvem registru.

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

1. V **Průzkumník řešení** klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a vyberte **sestavování a nabízených IoT Edge modulů** pro sestavení a vložení image Docker pro každý modul.

## <a name="deploy-the-solution"></a>Nasazení řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Moduly můžete nasadit také pomocí Průzkumníka cloudu pro Visual Studio. Už máte připravený manifest nasazení pro váš scénář, `deployment.json` soubor a vše, co je potřeba udělat, je vybrat zařízení pro příjem nasazení.

1. Kliknutím na **Zobrazit** Průzkumníka cloudu otevřete **Průzkumníka cloudu**  >  . Ujistěte se, že jste přihlášeni k aplikaci Visual Studio 2019.

1. V **Průzkumníku cloudu** rozbalte své předplatné, najděte IoT Hub Azure a Azure IoT Edge zařízení, které chcete nasadit.

1. Klikněte pravým tlačítkem na zařízení IoT Edge a vytvořte pro něj nasazení. Přejděte k manifestu nasazení nakonfigurovanému pro vaši platformu ve složce **config** v řešení aplikace Visual Studio, jako je například `deployment.arm32v7.json` .

1. Kliknutím na tlačítko Aktualizovat zobrazíte nové moduly běžící společně s modulem **SimulatedTemperatureSensor** a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazit vygenerovaná data

1. Pokud chcete monitorovat zprávu D2C pro konkrétní IoT-Edge zařízení, vyberte ji v centru IoT v **Průzkumníku cloudu** a potom v okně **Akce** klikněte na **Spustit sledování vestavěného koncového bodu události** .

1. Chcete-li zastavit monitorování dat, vyberte možnost **Zastavit sledování vestavěného koncového bodu události** v okně **Akce** .

## <a name="next-steps"></a>Další kroky

Pro vývoj vlastních modulů pro IoT Edge zařízení, [pochopení a používání sady Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).