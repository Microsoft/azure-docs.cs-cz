---
title: Vývoj a ladění C# moduly v sadě Visual Studio – Azure IoT Edge | Dokumentace Microsoftu
description: Pomocí sady Visual Studio 2017 na vývoj a ladění modulu C# pro Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a6c892a71c452a0c6c0dcd43509e345280a810e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054915"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Pomocí sady Visual Studio 2017 na vývoj a ladění modulů jazyka C# pro Azure IoT Edge (Preview)

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. Tento článek ukazuje, jak pomocí sady Visual Studio 2017 jako hlavní nástroje k vývoji a ladění modulů jazyka C#.

Nástroje Azure IoT Edge pro Visual Studio nabízí následující výhody:

- Vytvořit, upravit, sestavení, spuštění a ladění řešení Azure IoT Edge a moduly na vašem místním vývojovém počítači.
- Nasazení řešení Azure IoT Edge pro zařízení Azure IoT Edge prostřednictvím služby Azure IoT Hub.
- Přitom má všechny výhody vývoj sady Visual Studio Code modulů Azure IoT v jazyce C#.
- Správa zařízení Azure IoT Edge a moduly pomocí uživatelského rozhraní.

V tomto článku se dozvíte, jak pomocí nástroje Azure IoT Edge pro Visual Studio 2017 na vývoj modulů IoT Edge v jazyce C#. Také se dozvíte, jak nasadit svůj projekt na vaše zařízení Azure IoT Edge.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení.

Vzhledem k tomu tento článek používá jako hlavní vývojový nástroj Visual Studio 2017, nainstalujte Visual Studio. A nezapomeňte uvést **funkcí vývoj pro Azure** v instalaci sady Visual Studio 2017. Je možné [upravit Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) a přidejte úlohu vývoj pro Azure.

Poté, co Visual Studio 2017 je připraven, budete potřebovat následující komponenty a nástrojů:

- Stáhněte a nainstalujte [rozšíření Azure IoT Edge (Preview)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) z webu marketplace Visual Studio k vytvoření IoT Edge projektu v sadě Visual Studio 2017.

- Stáhněte a nainstalujte [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači sestavení a spuštění vaší bitové kopie modulu. Budete muset nastavit Docker CE pro spuštění v režimu kontejneru Linuxu nebo režimu kontejneru Windows.

- Nastavení svoje místní vývojové prostředí pro ladění, spuštění a testování vašeho řešení IoT Edge nainstalováním [nástroj pro vývojáře služby Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Nainstalujte [(2.7/3.6) Python a Pip](https://www.python.org/) a pak nainstalujte **iotedgehubdev** balíčku spuštěním následujícího příkazu v terminálu. Ujistěte se, že váš nástroj pro vývojáře služby Azure IoT EdgeHub verze je větší než 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru.

- Pokud chcete otestovat modulu do zařízení, budete potřebovat aktivním centrem IoT s alespoň jedno zařízení IoT Edge. Pokud chcete použít počítač jako zařízení IoT Edge, postupujte podle kroků v tomto rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud spustíte proces démon IoT Edge na vývojovém počítači, můžete potřebovat zastavit EdgeHub a EdgeAgent před zahájením vývoje v sadě Visual Studio.

### <a name="check-your-tools-version"></a>Zkontrolujte verzi nástroje

1. Z **nástroje** nabídce vyberte možnost **rozšíření a aktualizace**. Rozbalte **nainstalováno > Nástroje** zjistíte **nástroje Azure IoT Edge** a **Cloud Explorer for Visual Studio**.

1. Poznamenejte si verzi nainstalovaného. Můžete porovnat tato verze na nejnovější verzi na Visual Studio Marketplace ([Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge))

1. Pokud je vaše verze starší než co je k dispozici na webu Visual Studio Marketplace, aktualizujte své nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizovat vaše nástroje

1. V **rozšíření a aktualizace** dialogového okna, rozbalte **aktualizace > Visual Studio Marketplace**vyberte **nástroje Azure IoT Edge** nebo **Cloud Explorer pro Visual Studio** a vyberte **aktualizace**.

1. Po stažení aktualizace nástroje zavřete sadu Visual Studio na trigger nástroje aktualizace pomocí instalátor VSIX.

1. V instalačním programu, vyberte **OK** spuštění a pak **změnit** aktualizace nástrojů.

1. Po dokončení aktualizace, vyberte **Zavřít** a restartujte aplikaci Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Vytvoření projektu Azure IoT Edge

Šablona projektu Azure IoT Edge v sadě Visual Studio vytvoří projekt, který je možné nasadit do zařízení Azure IoT Edge ve službě Azure IoT Hub. Nejprve vytvořit řešení Azure IoT Edge, a pak vygenerovat první modulu jazyka C# v tomto řešení. Každé řešení IoT Edge může obsahovat více než jeden modul.

1. V sadě Visual Studio zvolte v nabídce **Soubor** možnost **Nový** > **Projekt**.

1. V **nový projekt** dialogového okna, vyberte **nainstalováno**, rozbalte **Visual C# > Cloud**vyberte **Azure IoT Edge**, zadejte název váš projekt a zadejte umístění a potom vyberte **OK**. Výchozí název projektu je **AzureIoTEdgeApp1**.

   ![Nový projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. V **konfigurace modulu IoT Edge** okně  **C# modulu** a zadejte název modulu a modulu úložiště imagí. Visual Studio autopopulates modulu název s **localhost:5000 / < název vašeho modulu\>**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá jako ***\<název registru\>*. azurecr.io**. Nahradit jenom **localhost:5000** část řetězce tak, aby konečný výsledek bude vypadat jako **\<* název registru*\>.azurecr.io/* \<název modulu\>***. Výchozí název modulu je **IoTEdgeModule1**

1. Vyberte **OK** k vytvoření projektu Azure IoT Edge se C# modulu.

Nyní máte **AzureIoTEdgeApp1** projektu a **IoTEdgeModule1** projekt ve vašem řešení. **AzureIoTEdgeApp1** projekt má **deployment.template.json** souboru. Tento soubor definuje moduly, které chcete sestavit a nasadit řešení IoT Edge a také definuje trasy mezi moduly. Má řešení výchozí **tempSensor** modulu a **IoTEdgeModule1** modulu. **TempSensor** modul generuje Simulovaná data do **IoTEdgeModule1** modulu, zatímco výchozí kód v **IoTEdgeModule1** modul přímo kanály přijetí zprávy do služby Azure IoT Hub.

**IoTEdgeModule1** projekt je konzolová aplikace .NET Core 2.1. Obsahuje požadované soubory Dockeru, které potřebujete pro spouštění kontejnerů Windows nebo linuxového kontejneru zařízení IoT Edge. **Module.json** soubor popisuje metadata modulu. **Program.cs** je kód skutečné modul, který využívá sadu SDK Azure IoT zařízení jako závislost.

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí modul kód jazyka C#, která se dodává s řešením je umístěn v **IoTEdgeModule1** > **Program.cs**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je vytvořená tak trvat, než vstupní zdroj (v tomto případě **tempSensor** modul, který simuluje data.) a zřetězit ho do služby Azure IoT Hub.

Jakmile budete připraveni k přizpůsobení šablony jazyka C# s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicializovat iotedgehubdev připojovacím řetězcem zařízení IoT Edge

1. Zkopírujte připojovací řetězec z libovolného zařízení IoT Edge z **primární připojovací řetězec** v Průzkumníku cloudu sady Visual Studio. Nezapomeňte zkopírujte připojovací řetězec zařízení bez okrajů, protože se liší od ikony zařízení bez okrajů na ikonu zařízení IoT Edge.

   ![Zkopírujte připojovací řetězec zařízení Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Klikněte pravým tlačítkem myši **AzureIoTEdgeApp1** projektu a pak klikněte na tlačítko **nastavit připojovací řetězec zařízení Edge** otevřete okno nastavení Azure IoT Edge.

   ![Okno sady Edge připojovací řetězec](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Zadejte připojovací řetězec z prvního kroku a pak vyberte **OK**.

> [!NOTE]
> Budete muset postupovat podle těchto kroků pouze jednou na vašem vývojovém počítači jako výsledky budou automaticky použita pro všechny další řešení Azure IoT Edge. Tento postup může znovu dodržet, pokud potřebujete změnit na jiné připojovací řetězec.

## <a name="build-and-debug-single-c-module"></a>Vytvářejte a laďte jednoho modulu jazyka C#

Obvykle budete chtít testování a ladění modulů před spuštěním v rámci celého řešení s několika cloudy.

1. Klikněte pravým tlačítkem na **IoTEdgeModule1** a vyberte **nastavit jako spouštěný projekt** v místní nabídce.

   ![Nastavit spouštěcí projekt](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Stisknutím klávesy **F5** nebo klikněte na tlačítko níže spusťte modul; může trvat 10&ndash;20 sekund poprvé, můžete tak učinit.

   ![Spusťte modul](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Měli byste vidět konzolovou aplikaci .NET Core, spustit, pokud modul byl úspěšně inicializován.

   ![Spuštěný modul](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Nastavte zarážku v `PipeMessage()` fungovat v **Program.cs** a pak ji otestovat spuštěním následujícího příkazu v odesílání zprávy **Git Bash** nebo **WSL Bash** prostředí . (Nelze spustit `curl` příkaz z Powershellu nebo příkazového řádku.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Ladění jednoho modulu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Zarážka by měla aktivovat. Proměnné v sadě Visual Studio můžete sledovat **lokální** okna.

   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje rozhraní API pro odesílání zpráv místo `curl`.

1. Stisknutím klávesy **Ctrl + F5** nebo klikněte na tlačítko stop, Zastavit ladění.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Sestavování a ladění řešení IoT Edge s několika cloudy

Až to budete mít vývoj jeden modul, můžete chtít spustit a ladit celého řešení s několika cloudy.

1. Přidejte druhý C# modulu do řešení kliknutím pravým tlačítkem myši **AzureIoTEdgeApp1** a vyberete **přidat** > **nový modul IoT Edge**. Výchozí název druhý modul je **IoTEdgeModule2** a bude fungovat jako jiný modul v kanálu.

1. Otevřete soubor `deployment.template.json` a zobrazí se vám **IoTEdgeModule2** se přidala v **moduly** oddílu. Nahradit **trasy** oddíl následujícím kódem. Pokud jste upravili do názvu modulu, nezapomeňte že aktualizovat tyto názvy tak, aby odpovídaly.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a vyberte **nastavit jako spouštěný projekt** v místní nabídce.

1. Vytvořte své zarážky a potom stiskněte klávesu **F5** ke spuštění a ladění více modulů současně. Měli byste vidět více .NET Core konzoly aplikace systému windows, každý okna představující jiný C# modulu.

   ![Ladění více modulů](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Stisknutím klávesy **Ctrl + F5** nebo klikněte na tlačítko stop, Zastavit ladění.

## <a name="build-and-push-images"></a>Vytváření a nasdílení změn imagí

1. Ujistěte se, že **AzureIoTEdgeApp1** je počáteční projekt. Vyberte buď **ladění** nebo **vydání** jako konfigurace sestavení pro vaší bitové kopie modulu.

    > [!NOTE]
    > Při výběru **ladění**, použije sada Visual Studio `Dockerfile.debug` k sestavení Image Dockeru. To zahrnuje příkazového řádku debugger .NET Core VSDBG ve vaší imagi kontejneru při jeho vytváření. Moduly IoT Edge připravené pro produkční prostředí, doporučujeme použít **vydání** konfigurace, která používá `Dockerfile` bez VSDBG.

1. Pokud používáte privátního registru jako je Azure Container Registry, použijte následující příkaz Docker pro přihlášení k němu. Pokud používáte místní registru, můžete si [spuštění místního registru](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Pokud používáte privátního registru jako je Azure Container Registry, musíte přidat přihlašovací údaje pro váš registr nacházejí v souboru nastavení běhového prostředí `deployment.template.json`. Nahraďte zástupné symboly vaší skutečné ACR uživatelské jméno, heslo a registru jméno správce.

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

1. Klikněte pravým tlačítkem na **AzureIoTEdgeApp1** a vyberte **sestavení a hraniční řešení, Push** sestavíte a odešlete image Dockeru pro každý modul.

   ![Vytváření a nasdílení změn Imagí](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Nasazení řešení

V článku Rychlý start, pomocí kterého jste nastavili své zařízení IoT Edge, jste nasadili modul pomocí webu Azure Portal. Můžete také nasadit moduly pomocí Průzkumníka cloudu sady Visual Studio. Už máte manifest nasazení připravili pro váš scénář `deployment.json` soubor a všechny je třeba provést je vybrat zařízení mohlo přijmout nasazení.

1. Otevřít **Průzkumníka cloudu** kliknutím **zobrazení** > **Průzkumníka cloudu**. Ujistěte se, že jste přihlášeni k sadě Visual Studio 2017.

1. V **Průzkumníka cloudu**, rozšířit vaše předplatné, vyhledání služby Azure IoT Hub a zařízení Azure IoT Edge, které chcete nasadit.

1. Klikněte pravým tlačítkem na zařízení IoT Edge k vytvoření, nasazení, je nutné vybrat soubor manifestu nasazení v rámci `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Nesmí vyberete `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Klikněte na tlačítko aktualizace zobrazíte nové moduly, spuštěná společně s **TempSensor** modulu a **$edgeAgent** a **$edgeHub**.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. K monitorování D2C zprávy pro konkrétní zařízení, v seznamu vyberte zařízení a pak klikněte na tlačítko **spustit monitorování zpráv D2C** v **akce** okna.

1. Monitorování dat zastavit, vyberte v seznamu zařízení a pak vyberte **zastavit monitorování zpráv D2C** v **akce** okna.

## <a name="next-steps"></a>Další postup

K vývoji vlastních modulů pro vaše zařízení IoT Edge [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
