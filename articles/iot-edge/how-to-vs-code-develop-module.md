---
title: Vývoj a ladění modulů Azure IoT Edge | Dokumentace Microsoftu
description: Použití Visual Studio Code k vývoji, sestavení a ladění modulu pro pomocí Azure IoT Edge C#, Python, Node.js, Java nebo C
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb3d19d35a15d5476594948b035a39ae703f1c3a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551299"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Použití Visual Studio Code pro vývoj a ladění modulů Azure IoT Edge

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio Code jako hlavní nástroje k vývoji a ladění modulů.

## <a name="prerequisites"></a>Požadavky

Můžete použít počítač nebo virtuální počítač s Windows, macOS nebo Linux jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení.

Pro moduly napsanými v C#, Node.js nebo Java, existují dva způsoby ladění modulu ve Visual Studio Code: Můžete připojit proces v kontejneru modulu nebo spuštění modulu kódu v režimu ladění. Pro moduly napsanými v Pythonu nebo C, mohou se proto ladit jenom k procesu v kontejnery Linuxu amd64 připojením.

> [!TIP]
> Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging).

Nainstalujte [Visual Studio Code](https://code.visualstudio.com/) první a pak přidejte následující rozšíření:

- [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio rozšíření specifické pro jazyk, kterou vyvíjíte v:
  - C#, včetně Azure Functions: [Rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Balíčkem rozšíření Java pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [Rozšíření jazyka C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Také budete muset nainstalovat nějaké další specifické pro jazyk nástrojů, aby vývoj vašeho modulu:

- C#, včetně Azure Functions: [sady SDK .NET Core 2.1](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) a [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Pythonu (obvykle je součástí instalace Pythonu). Po instalaci Pip nainstalovat **Cookiecutter** balíček pomocí následujícího příkazu:

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

- Node.js: [Node.js](https://nodejs.org). Budete také chtít nainstalovat [Yeoman](https://www.npmjs.com/package/yo) a [Azure IoT Edge Node.js modulu generátor](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) a [Maven](https://maven.apache.org/). Budete muset [nastavit `JAVA_HOME` proměnnou prostředí](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na instalaci sady JDK.

Pokud chcete sestavit a nasadit svou image modulu, musíte Dockeru k vytvoření bitové kopie modulu a container registry k uložení bitové kopie modulu:

- [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru.

Pokud vyvíjíte modulu v jazyce C, budete také potřebovat založené na Pythonu [nástroj pro vývojáře služby Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) Pokud chcete nastavit svoje místní vývojové prostředí, chcete-li ladit, spouštět a testovat řešení IoT Edge. Pokud jste tak již neučinili, nainstalujte [(2.7/3.6) Python a Pip](https://www.python.org/) a pak nainstalujte **iotedgehubdev** spuštěním následujícího příkazu v terminálu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Pokud chcete otestovat modulu do zařízení, budete potřebovat aktivním centrem IoT s alespoň jedno zařízení IoT Edge. Pokud chcete použít počítač jako zařízení IoT Edge, postupujte podle kroků v tomto rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud spustíte proces démon IoT Edge na vývojovém počítači, můžete potřebovat zastavit EdgeHub a EdgeAgent teprve potom přejděte k dalšímu kroku.

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Následující kroky ukazují, jak vytvořit modul IoT Edge ve vašem jazyce oblíbeným vývojovým (včetně Azure Functions, v C#) pomocí Visual Studio Code a rozšíření Azure IoT Edge. Začnete vytvořením řešení a pak v něm generování modulu první. Každé řešení může obsahovat více modulů.

1. Vyberte **zobrazení** > **příkaz palety**.

1. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Přejděte do složky, ve kterém chcete vytvořit nové řešení a pak vyberte **vyberte složku**.

1. Zadejte název pro vaše řešení.

1. Vyberte šablonu modul pro jazyk oblíbeným vývojovým bude první modulu v řešení.

1. Zadejte název modulu. Zvolte název, který je jedinečný v rámci vašeho registru kontejneru.

1. Zadejte název modulu úložiště imagí. Visual Studio Code autopopulates modulu název s **localhost:5000 / < název vašeho modulu\>**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá jako ***\<název registru\>*. azurecr.io**. Nahradit jenom **localhost:5000** část řetězce tak, aby konečný výsledek bude vypadat jako **\<* název registru*\>.azurecr.io/* \<název modulu\>***.

   ![Zadání úložiště imagí Dockeru](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně.

Existují čtyři položky v rámci řešení:

- A **.vscode** složka obsahuje konfiguraci ladění.

- A **moduly** složka obsahuje podsložky pro každý modul. V tomto okamžiku máte jenom jeden. Ale můžete přidat více v paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**.

- **.Env** soubor obsahuje seznam proměnných prostředí. Pokud váš registr Azure Container Registry se v něm budete mít Azure Container Registry uživatelské jméno a heslo.

  > [!NOTE]
  > Pokud zadáte úložišti imagí pro modul je jenom vytvořen soubor prostředí. Pokud jste přijali výchozí nastavení localhost testovat a ladit v místním prostředí, pak není nutné deklarovat proměnné prostředí.

- A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modulu, která simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [zjistěte, jak můžete nasadit moduly a vytvářet manifesty nasazení](module-composition.md).

## <a name="add-additional-modules"></a>Přidání dalších modulů

Pokud chcete přidat další moduly do vašeho řešení, spusťte příkaz **Azure IoT Edge: Přidat modul IoT Edge** z palety příkazů. Můžete také kliknout pravým tlačítkem **moduly** složky nebo `deployment.template.json` v zobrazení Průzkumník kódu Visual Studio a pak vyberte možnost **přidat modul IoT Edge**.

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí modul kód, který je součástí řešení se nachází v následujícím umístění:

- Funkce Azure Functions (C#): **moduly >  *&lt;název modulu&gt;* > *&lt;název modulu&gt;*.cs**
- C#: **moduly > *&lt;název modulu&gt;* > Program.cs**
- Python: **moduly > *&lt;název modulu&gt;* > main.py**
- Node.js: **moduly > *&lt;název modulu&gt;* > app.js**
- Java: **moduly > *&lt;název modulu&gt;* > src > hlavní > java > com > edgemodulemodules > App.java**
- C: **moduly > *&lt;název modulu&gt;* > main.c**

V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub.

Jakmile budete připraveni k přizpůsobení šablony s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Ladění modulu bez kontejneru (C#, Node.js, Java)

Pokud vyvíjíte v C#, Node.js nebo Java, modul vyžaduje použití **ModuleClient** objekt v kódu modulu výchozí tak, aby ho můžete spustit, spusťte a směrování zpráv. Můžete také využít výchozí vstupní kanál **vstup1** provádět akci, pokud modul přijímá zprávy.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro řešení IoT Edge

Ve vývojovém počítači můžete spustit simulátor IoT Edge místo instalace démona zabezpečení IoT Edge, aby mohla spustit vaše řešení IoT Edge.

1. V Průzkumníku zařízení na levé straně, klikněte pravým tlačítkem na vaše ID zařízení IoT Edge a pak vyberte **nastavení IoT Edge simulátor** spusťte simulátor připojovacím řetězcem zařízení.
1. Uvidíte, že simulátor IoT Edge se úspěšně nastavila najdete podrobnosti o pokroku v integrovaném terminálu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Nastavení simulátoru IoT Edge pro jeden modul aplikace

Pokud chcete nastavit a spusťte simulátor, spusťte příkaz **Azure IoT Edge: Spusťte simulátor centra IoT Edge pro jeden modul** z palety příkazů Visual Studio Code. Po zobrazení výzvy použijte hodnotu **vstup1** z výchozího modulu kódu (nebo ekvivalentní hodnotu v kódu) jako název vstupu pro vaši aplikaci. Příkaz aktivační události **iotedgehubdev** rozhraní příkazového řádku a potom spustí simulátor IoT Edge a kontejner pro modul testování nástroj. Uvidíte výstupy níže v integrovaném terminálu simulátoru se spustil v režimu jednoho modulu úspěšně. Můžete také zobrazit `curl` příkaz, který pomůže přes poslat zprávu. Budete jej později potřebovat.

   ![Nastavení simulátoru IoT Edge pro jeden modul aplikace](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Zobrazení Průzkumníka Docker můžete použít v aplikaci Visual Studio Code zobrazíte stav spuštění modulu.

   ![Stav modulu simulátor](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** základní místní simulátor IoT Edge je kontejner. Můžete spustit na vývojovém počítači bez démona zabezpečení IoT Edge a poskytuje nastavení prostředí pro aplikace nativní modul nebo modul kontejnery. **Vstupní** kontejner zpřístupňuje rozhraní REST API můžete líp most zprávy do cílové vstupní kanál na modul.

### <a name="debug-module-in-launch-mode"></a>Ladění modulu v režimu spuštění

1. Příprava prostředí pro ladění podle požadavků vývojovém jazyce, nastavte zarážku v modulu a vyberte konfiguraci ladění používat:
   - **C#**
     - V integrovaném terminálu Visual Studio Code, změňte adresář na ***&lt;název modulu&gt;*** složku a potom spusťte následující příkaz k sestavení.Net Core aplikace.

       ```cmd
       dotnet build
       ```

     - Otevřete soubor `program.cs` a přidejte zarážku.

     - Přejděte do zobrazení ladění Visual Studio Code tak, že vyberete **zobrazení > ladění**. Vyberte konfiguraci ladění  ***&lt;název modulu&gt;* místní ladění (.NET Core)** z rozevíracího seznamu.

        > [!NOTE]
        > Pokud vaše.Net Core `TargetFramework` není konzistentní s vaše cesta k programu v `launch.json`, budete muset ručně aktualizovat cestu program v `launch.json` tak, aby odpovídaly `TargetFramework` v souboru .csproj tak, že kód Visual Studio můžete úspěšně spustit to program.

   - **Node.js**
     - V integrovaném terminálu Visual Studio Code, změňte adresář na ***&lt;název modulu&gt;*** složku a potom spusťte následující příkaz k instalaci balíčků uzlu

       ```cmd
       npm install
       ```

     - Otevřete soubor `app.js` a přidejte zarážku.

     - Přejděte do zobrazení ladění Visual Studio Code tak, že vyberete **zobrazení > ladění**. Vyberte konfiguraci ladění  ***&lt;název modulu&gt;* místní ladění (Node.js)** z rozevíracího seznamu.
   - **Java**
     - Otevřete soubor `App.java` a přidejte zarážku.

     - Přejděte do zobrazení ladění Visual Studio Code tak, že vyberete **zobrazení > ladění**. Vyberte konfiguraci ladění  ***&lt;název modulu&gt;* místní ladění (Java)** z rozevíracího seznamu.

1. Klikněte na tlačítko **spustit ladění** nebo stiskněte klávesu **F5** ke spuštění relace ladění.

1. V integrovaném terminálu Visual Studio Code, spusťte následující příkaz k odeslání **Hello World** zpráva, která má modul. Toto je příkaz je znázorněno v předchozích krocích při nastavování simulátor IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Pokud používáte Windows, musíte zajistit, že prostředí integrovaný terminál aplikace Visual Studio Code je **Git Bash** nebo **WSL Bash**. Nelze spustit `curl` příkaz z Powershellu nebo příkazového řádku.
   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje rozhraní API pro odesílání zpráv prostřednictvím místo `curl`.

1. V zobrazení ladění Visual Studio Code zobrazí se vám proměnné na levém panelu.

1. Pokud chcete zastavit ladicí relaci, vyberte tlačítko Zastavit nebo stisknutím klávesy **Shift + F5**a pak spusťte **Azure IoT Edge: Zastavit IoT Edge simulátor** v paletu příkazů simulátoru zastavit a vyčistit.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Ladění v režimu připojení se simulátorem IoT Edge (C#, Node.js, Java, Azure Functions)

Výchozí řešení obsahuje dva moduly, jeden je modul simulované teplotní snímač a druhý je v modulu kanálu. Simulované teplotní snímač odesílá zprávy do modulu kanálu a pak zprávy jsou směrované do služby IoT Hub. Ve složce modulu, který jste vytvořili existuje několik souborů Docker pro typy jiný kontejner. Použijte některý ze souborů, které končí příponou **.debug** vytvořit váš modul pro testování.

V současné době připojit ladění v režimu je podporováno pouze následujícím způsobem:

- C#moduly, včetně těch, které pro službu Azure Functions, podporu ladění v amd64 kontejnery Linuxu
- Moduly Node.js podpory ladění Linux amd64 a kontejnery arm32v7 a amd64 kontejnery Windows
- Java moduly podpory ladění v amd64 a arm32v7 kontejnery Linuxu

> [!TIP]
> Kliknutím na položku ve stavovém řádku Visual Studio Code můžete přepínat možnosti pro výchozí platformu pro vaše řešení IoT Edge.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro řešení IoT Edge

Ve vývojovém počítači můžete spustit simulátor IoT Edge místo instalace démona zabezpečení IoT Edge, aby mohla spustit vaše řešení IoT Edge.

1. V Průzkumníku zařízení na levé straně, klikněte pravým tlačítkem na vaše ID zařízení IoT Edge a pak vyberte **nastavení IoT Edge simulátor** spusťte simulátor připojovacím řetězcem zařízení.

1. Uvidíte, že simulátor IoT Edge se úspěšně nastavila najdete podrobnosti o pokroku v integrovaném terminálu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Sestavit a spustit kontejner pro ladění a ladění v připojení režimu

1. Otevřete soubor modulu (`program.cs`, `app.js`, `App.java`, nebo `<your module name>.cs`) a přidejte zarážku.

1. V okně Průzkumník kódu Visual Studio klikněte pravým tlačítkem myši `deployment.debug.template.json` pro vaše řešení a potom vyberte možnost **sestavení a spuštění hraničních zařízeních IoT řešení v simulátoru**. Můžete sledovat všechny kontejneru modulu protokoly ve stejném okně. Můžete také přejít do zobrazení Dockeru a sledujte stav kontejneru.

   ![Proměnné Watch](media/how-to-develop-csharp-module/view-log.png)

1. Přejděte do zobrazení ladění Visual Studio Code a vyberte konfigurační soubor ladění pro modul. Název možnosti ladění by měl být podobný  ***&lt;název modulu&gt;* vzdálené ladění**

1. Vyberte **spustit ladění** nebo stiskněte klávesu **F5**. Vyberte proces pro připojení.

1. V zobrazení pro ladění Visual Studio Code zobrazí se vám proměnné na levém panelu.

1. Zastavit relaci ladění, nejprve vyberte tlačítko Zastavit nebo stisknutím klávesy **Shift + F5**a pak vyberte **Azure IoT Edge: Zastavit IoT Edge simulátor** z palety příkazů.

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit moduly IoT Edge v kontejnerech. Přidá do vašeho modulu kontejneru vystavené porty `createOptions` nastavení. Po dokončení ladění modulů, doporučujeme že odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.
>
> Pro moduly napsanými v C#, včetně Azure Functions, tento příklad vychází ladicí verze `Dockerfile.amd64.debug`, což zahrnuje příkazového řádku debugger .NET Core (VSDBG) ve vaší imagi kontejneru při jeho vytváření. Po ladění vašeho C# moduly, doporučujeme vám, že používáte přímo soubor Dockerfile bez VSDBG pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="debug-a-module-with-iot-edge-runtime-python-c"></a>Ladění modulu runtime IoT Edge (Python, C)

Ve složce každého modulu existuje několik souborů Docker pro typy jiný kontejner. Použijte některý ze souborů, které končí příponou **.debug** vytvořit váš modul pro testování.

Ladění podpora modulů Python a C je v současné době k dispozici pouze v amd64 kontejnery Linuxu.

### <a name="build-and-deploy-your-module"></a>Vytvoření a nasazení modulu

1. V sadě Visual Studio Code otevřete `deployment.debug.template.json` soubor, který obsahuje ladicí verze vaší bitové kopie modulu správné `createOptions` hodnoty nastavené.

1. Pokud vytváříte modulu v Pythonu, následujícím postupem než budete pokračovat:
   - Otevřete soubor `main.py` a přidejte tento kód za část importování:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```
   - Přidejte následující jediný řádek kódu do zpětného volání, kterou chcete ladit:

      ```python
      ptvsd.break_into_debugger()
      ```

     Například, pokud chcete ladit `receive_message_callback` metody by vložení tohoto řádku kódu, jak je znázorněno níže:

      ```python
      def receive_message_callback(message, hubManager):
          ptvsd.break_into_debugger()
          global RECEIVE_CALLBACKS
          message_buffer = message.get_bytearray()
          size = len(message_buffer)
          print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode ('utf-8'), size) )
          map_properties = message.properties()
          key_value_pair = map_properties.get_internals()
          print ( "    Properties: %s" % key_value_pair )
          RECEIVE_CALLBACKS += 1
          print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
          hubManager.forward_event_to_output("output1", message, 0)
          return IoTHubMessageDispositionResult.ACCEPTED
      ```

1. V aplikaci Visual Studio Code paletu příkazů:
   1. Spusťte příkaz **Azure IoT Edge: Sestavení a Push hraničních zařízeních IoT řešení**.

   1. Vyberte `deployment.debug.template.json` soubor pro vaše řešení.

1. V **zařízení Azure IoT Hub** část zobrazení Průzkumníka Visual Studio Code:
   1. Klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **vytvoření nasazení pro jedno zařízení**.

   1. Přejděte do svého řešení **config** složky, vyberte `deployment.debug.amd64.json` souboru a pak vyberte **vyberte Manifest nasazení Edge**.

Zobrazí se vám nasazení s ID nasazení v integrovaném terminálu se úspěšně vytvořil.

Můžete zkontrolovat stav kontejneru v zobrazení Docker kódu Visual Studio nebo spuštěním `docker ps` příkazu v terminálu.

### <a name="debug-your-module"></a>Ladění modulu

Visual Studio Code uchovává informace o konfiguraci v ladění `launch.json` soubor umístěný ve `.vscode` složky v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytvoření nového řešení IoT Edge. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění.

1. V zobrazení ladění Visual Studio Code vyberte konfigurační soubor ladění pro modul. Název možnosti ladění by měl být podobný  ***&lt;název modulu&gt;* vzdálené ladění**

1. Otevřete soubor modulu pro vývojový jazyk a přidejte zarážku:
   - **Python**: Otevřít `main.py` a přidejte zarážku v metodě zpětného volání, které jste přidali `ptvsd.break_into_debugger()` řádku.
   - **C**: Otevřete soubor `main.c` a přidejte zarážku.

1. Vyberte **spustit ladění** nebo vyberte **F5**. Vyberte proces pro připojení.

1. V zobrazení ladění Visual Studio Code zobrazí se vám proměnné na levém panelu.

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit moduly IoT Edge v kontejnerech. Přidá do vašeho modulu kontejneru vystavené porty `createOptions` nastavení. Po dokončení ladění modulů, doporučujeme že odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="next-steps"></a>Další postup

Po začlenění modulu, zjistěte, jak [nasadit moduly Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
