---
title: Vývoj a ladění modulů pro Azure IoT Edge | Microsoft Docs
description: Použití Visual Studio Code k vývoji, sestavování a ladění modulu pro Azure IoT Edge pomocí jazyků C#, Python, Node.js, Java nebo C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: ebc12e6d64d015267497497bebc22c8586adf999
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043727"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Použití Visual Studio Code k vývoji a ladění modulů pro Azure IoT Edge

Obchodní logiku můžete převést do modulů pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio Code jako hlavní nástroj pro vývoj a ladění modulů.

Existují dva způsoby, jak ladit moduly napsané v jazyce C#, Node.js nebo Java v Visual Studio Code: můžete buď připojit proces v kontejneru modulu, nebo spustit kód modulu v režimu ladění. Chcete-li ladit moduly napsané v Pythonu nebo C, můžete se připojit pouze k procesu v kontejnerech Linux amd64.

Pokud nejste obeznámeni s funkcemi ladění Visual Studio Code, přečtěte si o [ladění](https://code.visualstudio.com/Docs/editor/debugging).

Tento článek poskytuje pokyny pro vývoj a ladění modulů v několika jazycích pro více architektur. V současné době Visual Studio Code poskytuje podporu pro moduly napsané v jazycích C#, C, Python, Node.js a Java. Podporované architektury zařízení jsou x64 a ARM32. Další informace o podporovaných operačních systémech, jazycích a architekturách najdete v tématu [Podpora jazyků a architektur](module-development.md#language-and-architecture-support).

>[!NOTE]
>Podpora pro vývoj a ladění pro zařízení se systémem Linux ARM64 je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace najdete v tématu [vývoj a ladění ARM64 IoT Edgech modulů v Visual Studio Code (Preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Požadované součásti

Jako vývojový počítač můžete použít počítač nebo virtuální počítač s Windows, macOS nebo Linux. V počítačích s Windows můžete vyvíjet moduly pro Windows nebo Linux. Pro vývoj modulů Windows použijte počítač s Windows, na kterém běží verze 1809/Build 17763 nebo novější. Pokud chcete vyvíjet moduly pro Linux, použijte počítač s Windows, který splňuje [požadavky pro Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Nejdřív nainstalujte [Visual Studio Code](https://code.visualstudio.com/) a pak přidejte následující rozšíření:

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Rozšíření Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Rozšíření pro Visual Studio specifická pro jazyk, v němž vyvíjíte:
  - C#, včetně Azure Functions: [rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [rozšíření Pythonu](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [balíček rozšíření Java pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [rozšíření c/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Pro vývoj vašeho modulu budete taky muset nainstalovat některé další nástroje pro konkrétní jazyk:

- C#, včetně Azure Functions: [.NET Core 2,1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) a [PIP](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Pythonu (obvykle zahrnutých v instalaci Pythonu).

- Node.js: [Node.js](https://nodejs.org). Budete také chtít nainstalovat [Yeoman](https://www.npmjs.com/package/yo) a [generátor modulu Node.js Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java se Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) a [Maven](https://maven.apache.org/). Budete muset [nastavit `JAVA_HOME` proměnnou prostředí](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby odkazovala na instalaci JDK.

K sestavení a nasazení image modulu potřebujete Docker pro sestavení image modulu a registru kontejneru pro uložení image modulu:

- [Docker Community Edition](https://docs.docker.com/install/) na vašem vývojovém počítači.

- Centrum [Azure Container Registry](../container-registry/index.yml) nebo [Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Místo registru cloudu můžete použít místní registr Docker pro účely prototypů a testování.

Pokud nevyvíjíte modul v jazyce C, budete také potřebovat nástroj pro vývoj [Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) v Pythonu, aby bylo možné nastavit místní vývojové prostředí pro ladění, spouštění a testování vašeho řešení IoT Edge. Pokud jste to ještě neudělali, nainstalujte [Python (2.7/3.6/3.7) a PIP](https://www.python.org/) a pak **iotedgehubdev** Nainstalujte spuštěním tohoto příkazu v terminálu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> V současné době iotedgehubdev používá knihovnu Docker-py, která není kompatibilní s Pythonem 3,8.
>
> Pokud máte několik Pythonů včetně předem nainstalovaného Pythonu 2,7 (například na Ubuntu nebo macOS), ujistěte se, že používáte správnou `pip` instalaci nebo pokud `pip3` chcete nainstalovat **iotedgehubdev** .

K otestování modulu v zařízení budete potřebovat aktivní službu IoT Hub s aspoň jedním IoT Edge zařízením. Pokud chcete počítač použít jako zařízení IoT Edge, postupujte podle kroků v rychlém startu pro [Linux](quickstart-linux.md) nebo [Windows](quickstart.md). Pokud ve vývojovém počítači používáte IoT Edge démon, možná budete muset před přechodem na další krok zastavit EdgeHub a EdgeAgent.

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Následující kroky ukazují, jak vytvořit modul IoT Edge v preferovaném vývojovém jazyce (včetně Azure Functions, napsaných v jazyce C#) pomocí Visual Studio Code a nástrojů Azure IoT. Začnete vytvořením řešení a potom vygenerujete první modul v tomto řešení. Každé řešení může obsahovat více modulů.

1. Vyberte **zobrazení**  >  **paleta příkazů**.

1. V paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové IoT Edge řešení**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Přejděte do složky, ve které chcete vytvořit nové řešení, a pak vyberte **Vybrat složku**.

1. Zadejte název svého řešení.

1. Vyberte šablonu modulu pro preferovaný vývojový jazyk, který bude prvním modulem v řešení.

1. Zadejte název svého modulu. Vyberte název, který je jedinečný v rámci vašeho registru kontejneru.

1. Zadejte název úložiště imagí modulu. Visual Studio Code automaticky vyplní název modulu názvem **localhost: 5000/<název \> vašeho modulu**. Nahraďte je vlastními informacemi v registru. Pokud pro testování používáte místní registr Docker, pak je **localhost** v pořádku. Pokud používáte Azure Container Registry, použijte přihlašovací server z nastavení registru. Přihlašovací server vypadá jako ** _\<registry name\>_ . azurecr.IO**. Nahradí jenom část **localhost: 5000** řetězce, aby konečný výsledek vypadal jako ** \<*registry name*\> _\<your module name\>_ . azurecr.IO/**.

   ![Zadání úložiště imagí Dockeru](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code převezme poskytnuté informace, vytvoří řešení IoT Edge a pak ho načte do nového okna.

V rámci řešení jsou čtyři položky:

- Složka **. VSCode** obsahuje konfigurace ladění.

- Složka **modulů** má podsložky pro každý modul.  V rámci složky pro každý modul je soubor **module.jsv**, který určuje, jak jsou moduly sestaveny a nasazeny.  Tento soubor je třeba upravit, aby se změnil registr kontejneru nasazení modulu z místního hostitele na vzdálený registr. V tuto chvíli máte jenom jeden modul.  Ale další informace můžete přidat do palety příkazů pomocí příkazu **Azure IoT Edge: přidat IoT Edge modul**.

- Soubor **. env** obsahuje seznam proměnných prostředí. Pokud je Azure Container Registry vašeho registru, budete mít v něm Azure Container Registry uživatelské jméno a heslo.

  > [!NOTE]
  > Soubor prostředí je vytvořen pouze v případě, že pro modul zadáte úložiště imagí. Pokud jste přijali, že se místní localhost bude standardně testovat a ladit, pak nemusíte deklarovat proměnné prostředí.

- **deployment.template.jsv** souboru obsahuje nový modul společně s ukázkovým modulem **SimulatedTemperatureSensor** , který simuluje data, která můžete použít pro testování. Další informace o tom, jak manifesty nasazení fungují, najdete v tématu [Naučte se používat manifesty nasazení k nasazení modulů a vytváření tras](module-composition.md).

Chcete-li zjistit, jak funguje simulovaný modul teploty, zobrazte [zdrojový kód SimulatedTemperatureSensor. csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Přidat další moduly

Chcete-li do řešení přidat další moduly, spusťte příkaz **Azure IoT Edge: přidat IoT Edge modul** z palety příkazů. Můžete také kliknout pravým tlačítkem na složku **moduly** nebo `deployment.template.json` soubor v zobrazení Průzkumník Visual Studio Code a pak vybrat **Přidat IoT Edge modul**.

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí kód modulu, který je součástí řešení, je umístěn v následujícím umístění:

- Funkce Azure Functions (C#): **moduly > * &lt; název &gt; modulu*  >  * &lt; váš název &gt; modulu*. cs**
- C#: **moduly > * &lt; název &gt; modulu* > program.cs**
- Python: **moduly > * &lt; název &gt; modulu* > Main.py**
- Node.js: **moduly > * &lt; název &gt; vašeho modulu* > app.js**
- Java: **moduly > * &lt; &gt; název modulu* > src > main > java > com > edgemodulemodules > App. Java**
- C: **moduly > * &lt; název &gt; modulu* > Main. c**

Modul a deployment.template.jsv souboru jsou nastavené tak, aby bylo možné sestavit řešení, nasdílet ho do registru kontejnerů a nasazovat ho do zařízení pro spuštění testování bez zásahu do kódu. Modul je sestaven tak, aby jednoduše převzal vstup ze zdroje (v tomto případě modul SimulatedTemperatureSensor, který simuluje data) a přesměruje jej na IoT Hub.

Až budete připraveni šablonu přizpůsobit pomocí vlastního kódu, pomocí [sad SDK pro Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) Sestavte moduly, které řeší klíčová úložiště pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Ladění modulu bez kontejneru (C#, Node.js, Java)

Pokud vyvíjíte v jazyce C#, Node.js nebo Java, modul vyžaduje použití objektu **ModuleClient** ve výchozím kódu modulu, aby mohl spustit, spustit a směrovat zprávy. Použijete také výchozí vstupní kanál **input1** k provedení akce, když modul obdrží zprávy.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro IoT Edge řešení

Ve vývojovém počítači můžete spustit simulátor IoT Edge a nemusíte instalovat démona zabezpečení IoT Edge, abyste mohli spustit řešení IoT Edge.

1. V Průzkumníku zařízení na levé straně klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **nastavení IoT Edge simulátoru** , abyste spustili simulátor s připojovacím řetězcem zařízení.
1. V případě, že je možné úspěšně nastavit simulátor IoT Edge, přečtěte si podrobné informace o průběhu v integrovaném terminálu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Nastavení simulátoru IoT Edge pro aplikaci s jedním modulem

Chcete-li nastavit a spustit simulátor, spusťte příkaz **Azure IoT Edge: spustit simulátor IoT Edge centra pro jeden modul** z palety příkazů Visual Studio Code. Po zobrazení výzvy použijte hodnotu **input1** z výchozího kódu modulu (nebo ekvivalentní hodnoty z vašeho kódu) jako vstupní název vaší aplikace. Příkaz aktivuje rozhraní příkazového řádku **iotedgehubdev** a potom spustí simulátor IoT Edge a kontejner modulu testovacího nástroje. V případě úspěšného spuštění simulátoru v režimu jediného modulu můžete zobrazit výstupy uvedené níže v integrovaném terminálu. Můžete se také podívat `curl` na příkaz, který vám umožní poslat zprávu prostřednictvím. Budete jej později potřebovat.

   ![Nastavení simulátoru IoT Edge pro aplikaci s jedním modulem](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Chcete-li zobrazit stav spuštění modulu, můžete použít zobrazení Docker Exploreru v Visual Studio Code.

   ![Stav modulu simulátoru](media/how-to-develop-csharp-module/simulator-status.png)

   Kontejner **edgeHubDev** je základem místního simulátoru IoT Edge. Může běžet na vašem vývojovém počítači bez IoT Edge démon zabezpečení a poskytuje nastavení prostředí pro kontejnery aplikace v nativním modulu nebo modulu. **Vstupní** kontejner zpřístupňuje rozhraní REST API, které umožňuje přemostění zpráv do cílového vstupního kanálu v modulu.

### <a name="debug-module-in-launch-mode"></a>Ladit modul v režimu spuštění

1. Připravte prostředí pro ladění podle požadavků vašeho vývojového jazyka, nastavte zarážku v modulu a vyberte konfiguraci ladění, která se má použít:
   - **C#**
     - V Visual Studio Code integrovaný terminál změňte adresář na složku *** &lt; název &gt; modulu*** a pak spusťte následující příkaz pro sestavení aplikace .NET Core.

       ```cmd
       dotnet build
       ```

     - Otevřete soubor `Program.cs` a přidejte zarážku.

     - Výběrem možnosti **zobrazit > ladit**přejděte do zobrazení ladění Visual Studio Code. V rozevíracím seznamu vyberte konfiguraci ladění ** * &lt; název &gt; vašeho modulu* místní ladění (.NET Core)** .

        > [!NOTE]
        > Pokud vaše .NET Core není `TargetFramework` konzistentní s cestou k programu v `launch.json` , budete muset ručně aktualizovat cestu programu v aplikaci tak, aby se `launch.json` shodovala se `TargetFramework` souborem. csproj, aby Visual Studio Code mohl tento program úspěšně spustit.

   - **Node.js**
     - V Visual Studio Code integrovaný terminál, změňte adresář na složku *** &lt; název &gt; modulu*** a pak spusťte následující příkaz pro instalaci balíčků Node

       ```cmd
       npm install
       ```

     - Otevřete soubor `app.js` a přidejte zarážku.

     - Výběrem možnosti **zobrazit > ladit**přejděte do zobrazení ladění Visual Studio Code. V rozevíracím seznamu vyberte konfiguraci ladění ** * &lt; &gt; název vašeho modulu* místní ladění (Node.js)** .
   - **Java**
     - Otevřete soubor `App.java` a přidejte zarážku.

     - Výběrem možnosti **zobrazit > ladit**přejděte do zobrazení ladění Visual Studio Code. V rozevíracím seznamu vyberte konfiguraci ladění ** * &lt; název &gt; modulu* místní ladění (Java)** .

1. Kliknutím na tlačítko **Spustit ladění** nebo stisknutím klávesy **F5** spusťte relaci ladění.

1. V Visual Studio Code Integrated Terminal spusťte následující příkaz, který odešle **Hello World** zprávu do vašeho modulu. Toto je příkaz, který se zobrazí v předchozích krocích při nastavení IoT Edge simulátoru.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Pokud používáte systém Windows, ujistěte se, že prostředí Visual Studio Code integrovaným terminálem je **Git bash** nebo **WSL bash**. Nemůžete spustit `curl` příkaz z PowerShellu nebo příkazového řádku.
   > [!TIP]
   > Můžete [také použít odesílací](https://www.getpostman.com/) nebo jiné nástroje API k posílání zpráv místo `curl` .

1. V zobrazení ladění Visual Studio Code uvidíte proměnné na levém panelu.

1. Chcete-li zastavit relaci ladění, vyberte tlačítko Zastavit nebo stiskněte klávesy **SHIFT + F5**a potom spusťte **Azure IoT Edge: zastavit IoT Edge simulátoru** v paletě příkazů pro zastavení simulátoru a vyčištění.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Ladění v režimu připojení s IoT Edge simulátorem (C#, Node.js, Java, Azure Functions)

Vaše výchozí řešení obsahuje dva moduly, jeden je simulovaný modul snímače teploty a druhý je modul kanálu. Senzor simulované teploty odesílá zprávy do modulu kanálu a zprávy jsou přesměrované do IoT Hub. Ve složce modulu, kterou jste vytvořili, je k dispozici několik souborů Docker pro různé typy kontejnerů. Použijte kterýkoli ze souborů, které končí příponou **. laděním** sestavíte modul pro testování.

V současné době je ladění v režimu připojení podporováno pouze následujícím způsobem:

- Moduly C#, včetně těch pro Azure Functions, podporují ladění v kontejnerech Linux amd64.
- Node.js moduly podporují ladění v kontejnerech Linux AMD64 a arm32v7 a v kontejnerech Windows amd64.
- Moduly Java podporují ladění v kontejnerech Linux AMD64 a arm32v7

> [!TIP]
> Kliknutím na položku na stavovém řádku Visual Studio Code můžete přepínat mezi možnostmi výchozí platformy pro vaše řešení IoT Edge.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro IoT Edge řešení

Ve vývojovém počítači můžete spustit simulátor IoT Edge a nemusíte instalovat démona zabezpečení IoT Edge, abyste mohli spustit řešení IoT Edge.

1. V Průzkumníku zařízení na levé straně klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **nastavení IoT Edge simulátoru** , abyste spustili simulátor s připojovacím řetězcem zařízení.

1. V případě, že je možné úspěšně nastavit simulátor IoT Edge, přečtěte si podrobné informace o průběhu v integrovaném terminálu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Sestavení a spuštění kontejneru pro ladění a ladění v režimu připojení

1. Otevřete soubor modulu ( `Program.cs` , `app.js` , `App.java` nebo `<your module name>.cs` ) a přidejte zarážku.

1. V zobrazení Průzkumníka Visual Studio Code klikněte pravým tlačítkem na `deployment.debug.template.json` soubor pro vaše řešení a pak vyberte **sestavování a spouštění IoT Edge řešení v simulátoru**. Všechny protokoly kontejneru modulů můžete sledovat ve stejném okně. Můžete také přejít do zobrazení Docker a sledovat stav kontejneru.

   ![Sledovat proměnné](media/how-to-vs-code-develop-module/view-log.png)

1. Přejděte do zobrazení ladění Visual Studio Code a vyberte konfigurační soubor ladění pro váš modul. Název možnosti ladění by měl být podobný ** * &lt; vašemu názvu &gt; modulu* vzdáleného ladění.**

1. Vyberte **Spustit ladění** nebo stiskněte klávesu **F5**. Vyberte proces, ke kterému se chcete připojit.

1. V zobrazení ladění Visual Studio Code uvidíte proměnné na levém panelu.

1. Chcete-li zastavit relaci ladění, vyberte nejprve tlačítko Zastavit nebo stiskněte klávesy **SHIFT + F5**a potom vyberte **Azure IoT Edge: zastavit IoT Edge simulátor** z palety příkazů.

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit IoT Edge moduly na kontejnerech. Do nastavení kontejneru vašeho modulu se přidaly vystavené porty `createOptions` . Po dokončení ladění modulů doporučujeme odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.
>
> U modulů napsaných v jazyce C#, včetně Azure Functions, je tento příklad založen na ladicí verzi nástroje `Dockerfile.amd64.debug` , která zahrnuje ladicí program příkazového řádku .NET Core (VSDBG) v imagi kontejneru při jeho vytváření. Po ladění modulů C# doporučujeme, abyste přímo používali souboru Dockerfile bez VSDBG pro IoT Edge moduly připravené pro produkční prostředí.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Ladění modulu pomocí modulu runtime IoT Edge

V každé složce modulu existuje několik souborů Docker pro různé typy kontejnerů. Použijte kterýkoli ze souborů, které končí příponou **. laděním** sestavíte modul pro testování.

Při ladění modulů pomocí této metody jsou moduly spuštěny na IoT Edge modulu runtime. IoT Edge zařízení a Visual Studio Code se můžou nacházet na stejném počítači, nebo je obvykle Visual Studio Code ve vývojovém počítači a modul runtime IoT Edge a moduly běží na jiném fyzickém počítači. Chcete-li ladit z Visual Studio Code, je nutné:

- Nastavte zařízení IoT Edge, sestavte moduly IoT Edge pomocí souboru Dockerfile **. Debug** a pak je nasaďte do IoT Edgeho zařízení.
- Vystavte IP adresu a port modulu, aby bylo možné připojit ladicí program.
- Aktualizujte `launch.json` , aby se Visual Studio Code mohl připojit k procesu v kontejneru na vzdáleném počítači. Tento soubor je umístěn ve `.vscode` složce v pracovním prostoru a aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Sestavte a nasaďte modul do zařízení IoT Edge.

1. V Visual Studio Code otevřete `deployment.debug.template.json` soubor, který obsahuje ladicí verzi imagí modulu se správnými `createOptions` hodnotami sady.

1. Pokud vyvíjíte modul v Pythonu, proveďte následující kroky, než budete pokračovat:
   - Otevřete soubor `main.py` a přidejte tento kód za oddíl import:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Do zpětného volání, které chcete ladit, přidejte následující jednořádkový kód:

      ```python
      ptvsd.break_into_debugger()
      ```

     Například pokud chcete `receive_message_listener` funkci ladit, vložte tento řádek kódu, jak je znázorněno níže:

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

1. Na paletě příkazu Visual Studio Code:
   1. Spusťte příkaz **Azure IoT Edge: Build a Push IoT Edge řešení**.

   1. Vyberte `deployment.debug.template.json` soubor pro vaše řešení.

1. V části **IoT Hub zařízení Azure** v zobrazení Průzkumník Visual Studio Code:
   1. Klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **vytvořit nasazení pro jedno zařízení**.

      > [!TIP]
      > Pokud chcete potvrdit, že zařízení, které jste zvolili, je IoT Edge zařízení, vyberte ho a rozbalte seznam modulů a ověřte přítomnost **$edgeHub** a **$edgeAgent**. Každé zařízení IoT Edge zahrnuje tyto dva moduly.

   1. Přejděte do **konfigurační** složky vašeho řešení, vyberte `deployment.debug.amd64.json` soubor a pak vyberte **možnost vybrat manifest nasazení Edge**.

V integrovaném terminálu se nasazování úspěšně vytvoří s ID nasazení.

Stav kontejneru můžete zjistit spuštěním `docker ps` příkazu v terminálu. Pokud Visual Studio Code a modul runtime IoT Edge běží na stejném počítači, můžete také zjistit stav v zobrazení Docker Visual Studio Code.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Vystavení IP adresy a portu modulu pro ladicí program

Tuto část můžete přeskočit, pokud jsou moduly spuštěny na stejném počítači jako Visual Studio Code, protože používáte localhost pro připojení ke kontejneru a již mají správné nastavení portu v souboru **. Debug** souboru Dockerfile, nastavení kontejneru modulu `createOptions` a `launch.json` souboru. Pokud jsou moduly a Visual Studio Code spuštěné v samostatných počítačích, postupujte podle kroků pro vývoj v jazyce.

- **C#, včetně Azure Functions**

  [Nakonfigurujte kanál SSH na vašem vývojovém počítači a IoT Edge zařízení](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) a pak upravte `launch.json` soubor tak, aby se připojil.

- **Node.js**

  - Zajistěte, aby byl modul na počítači, který se má ladit, spuštěný a připravený pro ladicí program, a že je tento port 9229 přístupný externě. To můžete ověřit otevřením `http://<target-machine-IP>:9229/json` v počítači ladicího programu. Tato adresa URL by měla zobrazovat informace o modulu Node.js, který se má ladit.
  
  - Na svém vývojovém počítači otevřete Visual Studio Code a pak ho upravte `launch.json` tak, aby byla hodnota adresy profilu **vzdáleného ladění (Node.js) * &lt; &gt; názvu modulu* ** (nebo ** * &lt; název &gt; modulu* vzdáleného ladění (Node.js v kontejneru Windows)** , pokud je modul spuštěný jako kontejner Windows) je IP adresa počítače, který se právě ladí.

- **Java**

  - Vytvořte tunel SSH na počítači, který chcete ladit spuštěním `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Na svém vývojovém počítači otevřete Visual Studio Code a upravte profil **Java ( * &lt; název &gt; modulu* Remote Debug)** v nástroji, `launch.json` abyste se mohli připojit k cílovému počítači. Další informace o úpravách `launch.json` a ladění Java pomocí Visual Studio Code najdete v části o [konfiguraci ladicího programu](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Ujistěte se, že port 5678 na počítači, který se má ladit, je otevřený a přístupný.

  - V kódu `ptvsd.enable_attach(('0.0.0.0', 5678))` , který jste dříve vložili do `main.py` , změňte **0.0.0.0** na IP adresu počítače, který se má ladit. Sestavte, dosaďte a znovu nasaďte IoT Edge modul.

  - Na svém vývojovém počítači otevřete Visual Studio Code a pak upravte `launch.json` , aby `host` hodnota profilu " ** * &lt; &gt; název modulu* vzdáleného ladění (Python)** " používala IP adresu cílového počítače místo `localhost` .

### <a name="debug-your-module"></a>Ladit modul

1. V zobrazení ladění Visual Studio Code vyberte konfigurační soubor ladění pro váš modul. Název možnosti ladění by měl být podobný ** * &lt; vašemu názvu &gt; modulu* vzdáleného ladění.**

1. Otevřete soubor modulu pro vývojový jazyk a přidejte zarážku:

   - **Funkce Azure (C#)**: přidejte zarážku do souboru `<your module name>.cs` .
   - **C#**: přidejte do souboru zarážku `Program.cs` .
   - **Node.js**: přidejte do souboru zarážku `app.js` .
   - **Java**: přidejte do souboru zarážku `App.java` .
   - **Python**: přidejte do souboru zarážku `main.py` v metodě zpětného volání, kam jste přidali `ptvsd.break_into_debugger()` řádek.
   - **C**: přidejte do souboru zarážku `main.c` .

1. Vyberte **Spustit ladění** nebo vyberte **F5**. Vyberte proces, ke kterému se chcete připojit.

1. V zobrazení ladění Visual Studio Code uvidíte proměnné na levém panelu.

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit IoT Edge moduly na kontejnerech. Do nastavení kontejneru vašeho modulu se přidaly vystavené porty `createOptions` . Po dokončení ladění modulů doporučujeme odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="build-and-debug-a-module-remotely"></a>Vzdálené sestavení a ladění modulu

Díky nedávno provedeným změnám v Docker i v modulech Moby pro podporu připojení SSH a nové nastavení v nástrojích Azure IoT, které umožňují vkládání nastavení prostředí do palety příkazů Visual Studio Code a Azure IoT Edge terminály, teď můžete vytvářet a ladit moduly na vzdálených zařízeních.

Další informace a podrobné pokyny najdete v této [položce blogu vývojář pro IoT](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) .

## <a name="next-steps"></a>Další kroky

Po vytvoření modulu se naučíte, jak [nasadit Azure IoT Edge moduly z Visual Studio Code](how-to-deploy-modules-vscode.md).

Pro vývoj modulů pro zařízení IoT Edge, [pochopení a používání sady Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).