---
title: Vývoj a ladění modulů pro Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použití kódu Visual Studio k vývoji, sestavení a ladění modulu pro Azure IoT Edge pomocí C#, Pythonu, Node.js, Javy nebo C
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944298"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Použití kódu Visual Studia k vývoji a ladění modulů pro Azure IoT Edge

Svou obchodní logiku můžete převést na moduly pro Azure IoT Edge. Tento článek ukazuje, jak používat Visual Studio kód jako hlavní nástroj pro vývoj a ladění modulů.

Existují dva způsoby ladění modulů napsaných v jazyce C#, Node.js nebo Java v kódu sady Visual Studio: Můžete buď připojit proces v kontejneru modulu, nebo spustit kód modulu v režimu ladění. Chcete-li ladit moduly napsané v Pythonu nebo C, můžete připojit pouze k procesu v kontejnerech Linux amd64.

Pokud nejste obeznámeni s možnostmi ladění kódu sady Visual Studio, přečtěte si o [ladění](https://code.visualstudio.com/Docs/editor/debugging).

Tento článek obsahuje pokyny pro vývoj a ladění modulů ve více jazycích pro více architektur. V současné době Visual Studio Code poskytuje podporu pro moduly napsané v Jazyce C#, C, Pythonu, Node.js a Javě. Podporované architektury zařízení jsou X64 a ARM32. Další informace o podporovaných operačních systémech, jazycích a architekturách naleznete v [tématu Podpora jazyků a architektury](module-development.md#language-and-architecture-support).

>[!NOTE]
>Vývoj a ladění podpory pro linuxová zařízení ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace naleznete v [tématu Vývoj a ladění modulů ARM64 IoT Edge v kódu Visual Studio (preview).](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)

## <a name="prerequisites"></a>Požadavky

Jako vývojový počítač můžete použít počítač nebo virtuální počítač se systémem Windows, macOS nebo Linuxem. V počítačích se systémem Windows můžete vyvíjet moduly Windows nebo Linux. Chcete-li vyvíjet moduly systému Windows, použijte počítač se systémem Windows s verzí 1809/build 17763 nebo novější. Chcete-li vyvíjet moduly Linuxu, použijte počítač se systémem Windows, který splňuje [požadavky na dockerovou plochu](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Nejprve nainstalujte [kód Sady Visual Studio](https://code.visualstudio.com/) a přidejte následující rozšíření:

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Rozšíření Dockeru](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Rozšíření visual studia specifická pro jazyk, který vyvíjíte v:
  - C#, včetně Azure Functions: [Rozšíření Jazyka C#.](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack pro Visual Studio kód](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [Rozšíření C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Budete také muset nainstalovat některé další, jazykově specifické nástroje pro vývoj modulu:

- C#, včetně funkcí Azure: [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) a [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Pythonu (obvykle součástí instalace Pythonu).

- Node.js: [Node.js](https://nodejs.org). Budete také chtít nainstalovat [Yeoman](https://www.npmjs.com/package/yo) a [Azure IoT Edge Node.js Generátor modulů](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Vývoj Kit 10](https://aka.ms/azure-jdks) a [Maven](https://maven.apache.org/). Budete muset [nastavit proměnnou `JAVA_HOME` prostředí](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby ukazovala na instalaci Sady JDK.

Chcete-li vytvořit a nasadit bitovou kopii modulu, potřebujete Docker k vytvoření bitové kopie modulu a registru kontejneru pro uložení bitové kopie modulu:

- [Docker Community Edition](https://docs.docker.com/install/) na vašem vývojovém stroji.

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Místní registr Dockeru můžete použít pro účely prototypu a testování namísto registru cloudu.

Pokud nevyvíjíte modul v jazyce C, potřebujete také [nástroj Azure IoT EdgeHub Dev založený](https://pypi.org/project/iotedgehubdev/) na Pythonu, abyste mohli nastavit místní vývojové prostředí pro ladění, spuštění a testování vašeho řešení IoT Edge. Pokud jste tak ještě neučinili, nainstalujte [Python (2.7/3.6/3.7) a Pip](https://www.python.org/) a pak nainstalujte **iotedgehubdev** spuštěním tohoto příkazu ve vašem terminálu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> V současné době iotedgehubdev používá docker-py knihovnu, která není kompatibilní s Python 3.8.
>
> Pokud máte více Python, včetně předinstalovaný python 2.7 (například na Ubuntu nebo macOS), ujistěte se, že používáte správné `pip` nebo `pip3` nainstalovat **iotedgehubdev**

K testování modulu na zařízení budete potřebovat aktivní centrum IoT hub s alespoň jedním zařízením IoT Edge. Chcete-li počítač používat jako zařízení IoT Edge, postupujte podle pokynů v rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud používáte daemon IoT Edge ve vývojovém počítači, možná budete muset zastavit EdgeHub a EdgeAgent, než přejdete k dalšímu kroku.

## <a name="create-a-new-solution-template"></a>Vytvoření nové šablony řešení

Následující kroky ukazují, jak vytvořit modul IoT Edge ve vašem preferovaném vývojovém jazyce (včetně funkcí Azure, napsaných v Jazyce C#) pomocí kódu Visual Studia a nástrojů Azure IoT. Můžete začít vytvořením řešení a potom generování první modul v tomto řešení. Každé řešení může obsahovat více modulů.

1. Vyberte **Zobrazit** > **paletu příkazů**.

1. V paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge Solution**.

   ![Spuštění nového řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Přejděte do složky, do které chcete vytvořit nové řešení, a pak vyberte **Vybrat složku**.

1. Zadejte název řešení.

1. Vyberte šablonu modulu pro preferovaný vývojový jazyk jako první modul v řešení.

1. Zadejte název modulu. V registru kontejnerů zvolte jedinečný název.

1. Zadejte název úložiště obrázků modulu. Visual Studio Code automaticky zaplní název modulu **s localhost:5000/<název\>modulu**. Nahraďte jej vlastními informacemi o registru. Pokud používáte místní registr Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, použijte přihlašovací server z nastavení registru. Přihlašovací server vypadá jako ** _ \<název\>registru_.azurecr.io**. Nahraďte pouze část řetězce **localhost:5000** tak, aby konečný výsledek vypadal jako ** \< *název*\>registru .azurecr.io/_\<název\>modulu_**.

   ![Zadání úložiště imagí Dockeru](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code převezme informace, které jste zadali, vytvoří řešení IoT Edge a pak je načte do nového okna.

V rámci řešení jsou čtyři položky:

- Složka **.vscode** obsahuje konfigurace ladění.

- Složka **modulů** obsahuje podsložky pro každý modul.  Ve složce pro každý modul je soubor **module.json**, který řídí, jak jsou moduly sestaveny a nasazeny.  Tento soubor by musel být upraven, aby se změnil registr kontejneru nasazení modulu z localhost na vzdálený registr. V tomto okamžiku máte pouze jeden modul.  Ale můžete přidat další v paletě příkazů s příkazem **Azure IoT Edge: Přidat IoT Edge Module**.

- Soubor **ENV** obsahuje seznam proměnných prostředí. Pokud je váš registr kontejnerů Azure, budete mít uživatelské jméno a heslo registru kontejnerů Azure.

  > [!NOTE]
  > Soubor prostředí je vytvořen pouze v případě, že poskytujete úložiště obrázků pro modul. Pokud jste přijali localhost výchozí testování a ladění místně, pak není nutné deklarovat proměnné prostředí.

- Soubor **deployment.template.json** uvádí nový modul spolu s ukázkovým modulem **SimulatedTemperatureSensor,** který simuluje data, která můžete použít pro testování. Další informace o tom, jak fungují manifesty nasazení, najdete v [tématu Naučte se používat manifesty nasazení k nasazení modulů a navázání tras](module-composition.md).

Chcete-li zjistit, jak modul simulované teploty funguje, zobrazte [zdrojový kód SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Přidání dalších modulů

Chcete-li do vašeho řešení přidat další moduly, spusťte příkaz **Azure IoT Edge: Add IoT Edge Module** z palety příkazů. Můžete také klepnout pravým tlačítkem `deployment.template.json` myši na složku **modulů** nebo soubor v zobrazení Průzkumníka kódů sady Visual Studio a potom vybrat přidat modul **IoT Edge Module**.

## <a name="develop-your-module"></a>Vyvíjejte svůj modul

Výchozí kód modulu, který je dodáván s řešením, je umístěn v následujícím umístění:

- Funkce Azure (C#): **moduly > * &lt;&gt;název* > *&lt;&gt;* modulu .cs**
- C#: **moduly > * &lt;&gt; název modulu* > Program.cs**
- Python: **moduly > * &lt;&gt; název vašeho modulu* > main.py**
- Node.js: **moduly > * &lt;&gt; název modulu* > app.js**
- Java: **moduly > * &lt;&gt; název modulu* > src > hlavní > java > com > edgemodulemodules > App.java**
- C: **moduly > * &lt;&gt; název modulu* > main.c**

Modul a soubor deployment.template.json jsou nastaveny tak, abyste mohli vytvořit řešení, zasunout do registru kontejneru a nasadit jej do zařízení a začít testovat bez dotyku jakéhokoli kódu. Modul je vytvořen tak, aby jednoduše převzít vstup ze zdroje (v tomto případě modul SimulatedTemperatureSensor, který simuluje data) a potrubí do IoT Hub.

Až budete připraveni přizpůsobit šablonu pomocí vlastního kódu, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) k vytvoření modulů, které řeší klíčové potřeby řešení IoT, jako je zabezpečení, správa zařízení a spolehlivost.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Ladění modulu bez kontejneru (C#, Node.js, Java)

Pokud vyvíjíte v jazyce C#, Node.js nebo Java, váš modul vyžaduje použití objektu **ModuleClient** ve výchozím kódu modulu, aby bylo možné spouštět, spouštět a směrovat zprávy. Budete také používat výchozí vstupní kanál **input1** provést akci, když modul přijímá zprávy.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro řešení IoT Edge

Ve vývojovém počítači můžete místo instalace demonu zabezpečení IoT Edge spustit simulátor IoT Edge, abyste mohli spustit řešení IoT Edge.

1. V průzkumníku zařízení na levé straně klikněte pravým tlačítkem myši na ID zařízení IoT Edge a pak vyberte **instalační nástroj IoT Edge Simulator** a spusťte simulátor pomocí připojovacího řetězce zařízení.
1. Můžete vidět IoT Edge Simulator byl úspěšně nastaven čtením podrobnosti průběhu v integrovaném terminálu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Nastavení simulátoru IoT Edge pro aplikaci s jedním modulem

Chcete-li simulátor nastavit a spustit, spusťte příkaz **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** z palety příkazů kódu visual studia. Po zobrazení výzvy použijte jako vstupní název aplikace **input input1** z výchozího kódu modulu (nebo ekvivalentní hodnotu z kódu). Příkaz spustí rozhraní příkazu **CLI iotedgehubdev** a pak spustí simulátor IoT Edge a kontejner testovacího modulu nástroje. Výstupy níže můžete vidět v integrovaném terminálu, pokud byl simulátor úspěšně spuštěn v režimu jednoho modulu. Můžete také zobrazit `curl` příkaz, který vám pomůže odeslat zprávu. Budete jej později potřebovat.

   ![Nastavení simulátoru IoT Edge pro aplikaci s jedním modulem](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Pomocí zobrazení Průzkumníka Dockeru v kódu Visual Studia můžete zobrazit stav spuštěné modulu.

   ![Stav modulu simulátoru](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** kontejner je jádrem místní simulátor IoT Edge. Může běžet na vývojovém počítači bez daemonu zabezpečení IoT Edge a poskytuje nastavení prostředí pro vaše nativní modulové aplikace nebo kontejnery modulů. **Vstupní** kontejner zpřístupňuje rozhraní REST API, která pomáhají přemostit zprávy do cílového vstupního kanálu ve vašem modulu.

### <a name="debug-module-in-launch-mode"></a>Ladicí modul v režimu spuštění

1. Připravte prostředí pro ladění podle požadavků vývojového jazyka, nastavte zarážku v modulu a vyberte konfiguraci ladění, kterou chcete použít:
   - **C #**
     - V terminálu integrovaného kódu sady Visual *** &lt;&gt; *** Studio změňte adresář na složku názvů modulů a spusťte následující příkaz pro sestavení aplikace .NET Core.

       ```cmd
       dotnet build
       ```

     - Otevřete `Program.cs` soubor a přidejte zarážku.

     - Přejděte do zobrazení ladění kódu sady Visual Studio výběrem **možnosti Ladění > zobrazení**. V rozevíracím souboru vyberte konfiguraci ** * &lt;ladění, název&gt; modulu* Místní ladění (.NET Core).**

        > [!NOTE]
        > Pokud vaše .NET `TargetFramework` Core není konzistentní `launch.json`s vaší cestou programu v aplikaci `launch.json` , `TargetFramework` budete muset ručně aktualizovat cestu programu v tak, aby odpovídala v souboru .csproj tak, aby Visual Studio Code můžete úspěšně spustit tento program.

   - **Node.js**
     - V terminálu integrovaného kódu sady Visual *** &lt;&gt; *** Studio změňte adresář na složku názvů modulů a spusťte následující příkaz pro instalaci balíčků uzlů

       ```cmd
       npm install
       ```

     - Otevřete `app.js` soubor a přidejte zarážku.

     - Přejděte do zobrazení ladění kódu sady Visual Studio výběrem **možnosti Ladění > zobrazení**. V rozevíracím souboru vyberte konfiguraci ** * &lt;ladění, název&gt; modulu* Místní ladění (Node.js).**
   - **Java**
     - Otevřete `App.java` soubor a přidejte zarážku.

     - Přejděte do zobrazení ladění kódu sady Visual Studio výběrem **možnosti Ladění > zobrazení**. V rozevíracím souboru vyberte konfiguraci ** * &lt;ladění, název&gt; modulu* Místní ladění (Java).**

1. Kliknutím na **Tlačítko Spustit ladění** nebo stisknutím **klávesy F5** spusťte relaci ladění.

1. V terminálu integrovaného kódu sady Visual Studio spusťte následující příkaz a odešlete zprávu **Hello World** do modulu. Toto je příkaz zobrazený v předchozích krocích při nastavení simulátoru IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Pokud používáte Windows, ujistěte se, že prostředí integrovaného terminálu Visual Studio Code je **Git Bash** nebo **WSL Bash**. `curl` Příkaz nelze spustit z prostředí PowerShell nebo příkazového řádku.
   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje rozhraní `curl`API pro odesílání zpráv prostřednictvím namísto .

1. V zobrazení ladění kódu sady Visual Studio uvidíte proměnné v levém panelu.

1. Chcete-li zastavit relaci ladění, vyberte tlačítko Stop nebo stiskněte **Shift + F5**a spusťte **Azure IoT Edge: Stop IoT Edge Simulator** v paletě příkazů zastavit simulátor a vyčistit.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Ladění v režimu připojení pomocí Simulátoru IoT Edge (C#, Node.js, Java, Azure Functions)

Vaše výchozí řešení obsahuje dva moduly, jeden je simulovaný modul teplotního senzoru a druhý je modul potrubí. Simulovaný teplotní senzor odesílá zprávy do modulu potrubí a pak jsou zprávy převedeny do centra IoT Hub. Ve složce modulu, kterou jste vytvořili, existuje několik souborů Dockeru pro různé typy kontejnerů. Použijte některý ze souborů, které končí s příponou **.debug** k vytvoření modulu pro testování.

V současné době je ladění v režimu připojení podporováno pouze následujícím způsobem:

- Moduly C#, včetně modulů pro funkce Azure, podporují ladění v kontejnerech Linuxu amd64
- Moduly Node.js podporují ladění v kontejnerech Linux amd64 a arm32v7 a kontejnerech Windows amd64
- Java moduly podporují ladění v kontejnerech Linux amd64 a arm32v7

> [!TIP]
> Kliknutím na položku ve stavovém řádku kódu visual studia můžete přepnout mezi možnostmi výchozí platformy pro vaše řešení IoT Edge.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro řešení IoT Edge

Ve vývojovém počítači můžete místo instalace demonu zabezpečení IoT Edge spustit simulátor IoT Edge, abyste mohli spustit řešení IoT Edge.

1. V průzkumníku zařízení na levé straně klikněte pravým tlačítkem myši na ID zařízení IoT Edge a pak vyberte **instalační nástroj IoT Edge Simulator** a spusťte simulátor pomocí připojovacího řetězce zařízení.

1. Můžete vidět IoT Edge Simulator byl úspěšně nastaven čtením podrobnosti průběhu v integrovaném terminálu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Sestavení a spuštění kontejneru pro ladění a ladění v režimu připojení

1. Otevřete soubor`Program.cs`modulu `App.java`( `<your module name>.cs`, `app.js`, , nebo ) a přidejte zarážku.

1. V zobrazení Průzkumníkkódu sady Visual Studio `deployment.debug.template.json` klepněte pravým tlačítkem myši na soubor pro vaše řešení a pak **vyberte build and run IoT Edge řešení v simulátoru**. Můžete sledovat všechny protokoly kontejneru modulu ve stejném okně. Můžete také přejít do zobrazení Dockeru a sledovat stav kontejneru.

   ![Sledovat proměnné](media/how-to-vs-code-develop-module/view-log.png)

1. Přejděte do zobrazení ladění kódu sady Visual Studio a vyberte konfigurační soubor ladění pro váš modul. Název možnosti ladění by měl být podobný ** * &lt;názvu&gt; modulu* Vzdálené ladění**

1. Vyberte **Možnost Spustit ladění** nebo stiskněte **klávesu F5**. Vyberte proces, ke kterému chcete být připojeni.

1. V zobrazení ladění kódu sady Visual Studio uvidíte proměnné v levém panelu.

1. Chcete-li relaci ladění zastavit, nejprve vyberte tlačítko Stop nebo stiskněte **Shift + F5**a pak z palety příkazů vyberte **Azure IoT Edge: Stop IoT Edge Simulator.**

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit moduly IoT Edge v kontejnerech. Do nastavení kontejneru modulu `createOptions` přidala vystavené porty. Po dokončení ladění modulů doporučujeme odebrat tyto exponované porty pro moduly IoT Edge připravené pro produkční prostředí.
>
> Pro moduly napsané v C#, včetně Funkce Azure, tento `Dockerfile.amd64.debug`příklad je založen na ladicí verzi , která zahrnuje ladicí program příkazového řádku .NET Core (VSDBG) v image kontejneru při jeho vytváření. Po ladění modulů Jazyka C# doporučujeme přímo použít Dockerfile bez VSDBG pro produkční připravené moduly IoT Edge.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Ladění modulu pomocí modulu runtime IoT Edge

V každé složce modulu existuje několik souborů Dockeru pro různé typy kontejnerů. Použijte některý ze souborů, které končí s příponou **.debug** k vytvoření modulu pro testování.

Při ladění modulů pomocí této metody jsou vaše moduly spuštěny v horní části modulu runtime IoT Edge. Zařízení IoT Edge a kód Sady Visual Studio mohou být ve stejném počítači nebo obvykle kód Visual Studio je ve vývojovém počítači a moduly IoT Edge běží na jiném fyzickém počítači. Chcete-li ladit z kódu sady Visual Studio, musíte:

- Nastavte zařízení IoT Edge, sestavte moduly IoT Edge pomocí **souboru .debug** Dockerfile a pak se nasaďte do zařízení IoT Edge.
- Vystavit IP a port modulu tak, aby ladicí program lze připojit.
- Aktualizujte `launch.json` tak, aby visual studio kód můžete připojit k procesu v kontejneru na vzdáleném počítači. Tento soubor je `.vscode` umístěn ve složce v pracovním prostoru a aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Sestavení a nasazení modulu do zařízení IoT Edge

1. V kódu sady Visual `deployment.debug.template.json` Studio otevřete soubor, který obsahuje ladicí verzi bitových kopií modulu se správnými `createOptions` nastavenými hodnotami.

1. Pokud vyvíjíte modul v Pythonu, postupujte takto, než budete pokračovat:
   - Otevřete `main.py` soubor a přidejte tento kód za oddíl importu:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Do zpětného volání, které chcete ladit, přidejte následující řádek kódu:

      ```python
      ptvsd.break_into_debugger()
      ```

     Chcete-li například ladit `receive_message_listener` funkci, vložíte tento řádek kódu, jak je znázorněno níže:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. V paletě příkazů Kódu sady Visual Studio:
   1. Spusťte příkaz **Azure IoT Edge: Build and Push IoT Edge solution**.

   1. Vyberte `deployment.debug.template.json` soubor pro vaše řešení.

1. V části **Zařízení služby Azure IoT Hub** v zobrazení Průzkumníka kódů Visual Studia:
   1. Klikněte pravým tlačítkem myši na ID zařízení IoT Edge a pak vyberte **Vytvořit nasazení pro jedno zařízení**.

      > [!TIP]
      > Chcete-li ověřit, zda je vybrané zařízení zařízení IoT Edge, vyberte ho, chcete-li rozbalit seznam modulů a ověřit přítomnost **$edgeHub** a **$edgeAgent**. Každé zařízení IoT Edge obsahuje tyto dva moduly.

   1. Přejděte do **konfigurační** složky `deployment.debug.amd64.json` řešení, vyberte soubor a pak vyberte **Vybrat manifest nasazení okraje**.

Uvidíte nasazení úspěšně vytvořené s ID nasazení v integrovaném terminálu.

Stav kontejneru můžete zkontrolovat `docker ps` spuštěním příkazu v terminálu. Pokud váš Visual Studio kód a IoT Edge runtime běží na stejném počítači, můžete také zkontrolovat stav v zobrazení Visual Studio Code Docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Vystavit IP a port modulu pro ladicí program

Tuto část můžete přeskočit, pokud jsou moduly spuštěny na stejném počítači jako Visual Studio Code, protože používáte localhost připojit ke kontejneru `createOptions` a již `launch.json` mají správné nastavení portu v **.debug** Dockerfile, nastavení kontejneru modulu a souboru. Pokud jsou vaše moduly a kód sady Visual Studio spuštěny na samostatných počítačích, postupujte podle pokynů pro vývojový jazyk.

- **C#, včetně funkcí Azure**

  [Nakonfigurujte kanál SSH ve vývojovém počítači a zařízení IoT Edge](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) a pak upravte `launch.json` soubor, který chcete připojit.

- **Node.js**

  - Ujistěte se, že modul v počítači, který má být laděn, je spuštěn a připraven k připojení ladicích programů a že port 9229 je přístupný externě. Můžete to ověřit `http://<target-machine-IP>:9229/json` otevřením v ladicím automatu. Tato adresa URL by měla zobrazovat informace o modulu Node.js, který má být laděn.
  
  - Ve vývojovém počítači otevřete kód `launch.json` sady Visual Studio a upravte tak, aby hodnota adresy ** * &lt;profilu&gt; * vzdáleného ladění (Node.js) (nebo** ** * &lt;&gt; název modulu* Vzdálené ladění (Node.js v kontejneru systému Windows)** byla IP adresa laděného počítače.

- **Java**

  - Vytvořte tunel SSH do počítače, který `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`má být laděn spuštěním .
  
  - Ve vývojovém počítači otevřete visual studio kód a ** * &lt;upravte profil název&gt; modulu* Vzdálené ladění (Java)** tak, `launch.json` aby se můžete připojit k cílovému počítači. Další informace o `launch.json` úpravách a ladění jazyka Java pomocí kódu sady Visual Studio najdete v části [o konfiguraci ladicího programu](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Ujistěte se, že port 5678 na počítači, který má být odladěn, je otevřený a přístupný.

  - V kódu, `ptvsd.enable_attach(('0.0.0.0', 5678))` do kterého `main.py`jste dříve vložili , změňte **0.0.0.0** na IP adresu počítače, který má být laděn. Znovu sestavujte, tlačte a nasaďte modul IoT Edge.

  - Ve vývojovém počítači otevřete kód `launch.json` sady `host` Visual Studio a upravte tak, aby hodnota ** * &lt;profilu&gt; * vzdáleného ladění (Python)** pomocí hodnoty vašeho kódu vzdáleného ladění (Python) místo . `localhost`

### <a name="debug-your-module"></a>Ladění modulu

1. V zobrazení ladění kódu sady Visual Studio vyberte konfigurační soubor ladění pro váš modul. Název možnosti ladění by měl být podobný ** * &lt;názvu&gt; modulu* Vzdálené ladění**

1. Otevřete soubor modulu pro vývojový jazyk a přidejte zarážku:

   - **Azure Function (C#):** Přidejte zarážku do souboru `<your module name>.cs`.
   - **C#**: Přidejte zarážku do souboru `Program.cs`.
   - **Node.js**: Přidejte zarážku do souboru `app.js`.
   - **Java**: Přidejte zarážku do souboru `App.java`.
   - **Python**: Přidejte zarážku do souboru `main.py`v `ptvsd.break_into_debugger()` metodě zpětného volání, kde jste přidali řádek.
   - **C**: Přidejte zarážku do souboru `main.c`.

1. Vyberte **Spustit ladění** nebo vyberte **Možnost F5**. Vyberte proces, ke kterému chcete být připojeni.

1. V zobrazení ladění kódu sady Visual Studio uvidíte proměnné v levém panelu.

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit moduly IoT Edge v kontejnerech. Do nastavení kontejneru modulu `createOptions` přidala vystavené porty. Po dokončení ladění modulů doporučujeme odebrat tyto exponované porty pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="build-and-debug-a-module-remotely"></a>Vzdálené sestavení a ladění modulu

S nedávnými změnami v modulech Docker i Moby pro podporu připojení SSH a novým nastavením v nástrojích Azure IoT, které umožňuje vkládání nastavení prostředí do palety příkazů kódu Visual Studia a terminálů Azure IoT Edge, můžete teď vytvářet a ladit moduly na vzdálených zařízeních.

Další informace a podrobné pokyny najdete v této [položce blogu pro vývojáře IoT.](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)

## <a name="next-steps"></a>Další kroky

Po nastavení modulu se dozvíte, jak [nasadit moduly Azure IoT Edge z visual studia Code](how-to-deploy-modules-vscode.md).

Chcete-li vyvíjet moduly pro vaše zařízení IoT Edge, [pochopte a použijte sady Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
