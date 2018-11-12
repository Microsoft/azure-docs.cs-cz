---
title: Ladění modulů Node.js pro Azure IoT Edge | Dokumentace Microsoftu
description: Použití Visual Studio Code pro vývoj a ladění modulů Node.js pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b05492941defc6ac3aa252d6bb29043d55e6b66c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261770"
---
# <a name="use-visual-studio-code-to-develop-and-debug-nodejs-modules-for-azure-iot-edge"></a>Použití Visual Studio Code pro vývoj a ladění modulů Node.js pro Azure IoT Edge

Odešlete vaši obchodní logiku pro provoz na hraničních zařízeních to zapnutím moduly pro Azure IoT Edge. Tento článek obsahuje podrobné pokyny k používání Visual Studio Code (VS Code) jako hlavní vývojový nástroj pro vývoj modulů Node.js.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows, macOS nebo Linux jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení.

> [!NOTE]
> Tento článek ladění ukazuje dva typické způsoby, jak ladit modul Node.js v nástroji VS Code. Jedním ze způsobů je připojit proces v kontejneru modulu, ale druhý se nachází na lanuch modulu kódu v režimu ladění. Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging).

Jelikož tento článek používá Visual Studio Code jako hlavní vývojový nástroj, nainstalujte VS Code a poté přidejte potřebnými rozšířeními:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Jak vytvořit modul, je třeba Node.js, který obsahuje sestavení složce projektu Dockeru k vytvoření bitové kopie modulu a container registry k uložení image modul npm:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Místního registru Docker můžete použít k prototypu a testování, namísto registru cloudu. 

Nastavit místní vývojové prostředí, chcete-li ladit, spouštět a testovat řešení IoT Edge, budete potřebovat [nástroj pro vývojáře služby Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Nainstalujte [(2.7/3.6) Python a Pip](https://www.python.org/). PIP je součástí instalační program Pythonu. Nainstalujte **iotedgehubdev** spuštěním následující příkaz, v terminálu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

K otestování modulu do zařízení, potřebujete aktivní služby IoT hub s alespoň jeden ID zařízení IoT Edge vytvořili. Pokud spustíte proces démon IoT Edge na vývojovém počítači, můžete potřebovat zastavit EdgeHub a EdgeAgent teprve potom přejděte k dalšímu kroku. 

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Následující kroky ukazují, jak vytvořit modul IoT Edge založené na Node.js pomocí Visual Studio Code a rozšíření Azure IoT Edge. Začnete vytvořením řešení a pak v něm generování modulu první. Každé řešení může obsahovat více modulů. 

1. Ve Visual Studio Code, vyberte **zobrazení** > **integrovaný terminál**.
2. V integrovaném terminálu zadejte následující příkaz k instalaci (nebo aktualizaci) nejnovější verzi šablony modulu Azure IoT Edge pro Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```

3. V aplikaci Visual Studio Code vyberte **Zobrazení** > **Paleta příkazů**. 
4. Do palety příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT Edge**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Přejděte do složky, ve kterém chcete vytvořit nové řešení a klikněte na tlačítko **vyberte složku**. 
6. Zadejte název pro vaše řešení. 
7. Zvolte **modul Node.js** jako šablona pro první modul v rámci řešení.
8. Zadejte název modulu. Zvolte název, který je jedinečný v rámci vašeho registru kontejneru. 
9. Zadejte úložiště imagí pro modul. VS Code autopopulates modulu pojmenovat, abyste měli pouze nahradit **localhost:5000** nahraďte svými vlastními informacemi registru. Pokud používáte místní registru Dockeru pro testování, pak místního hostitele je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **\<název registru\>. azurecr.io**. V řetězci nahraďte pouze část localhost, název vašeho modulu neodstraňujte.

   ![Zadání úložiště imagí Dockeru](./media/how-to-develop-node-module/repository.png)

VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a pak ho načte v novém okně.

V řešení máte tři položky: 
* A **.vscode** složka obsahuje konfiguraci ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. V tuto chvíli máte pouze jednu, ale můžete přidat více v paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**. 
* A **.env** soubor obsahuje seznam proměnných prostředí. Pokud váš registr Azure Container Registry se v něm budete mít Azure Container Registry uživatelské jméno a heslo.

   >[!NOTE]
   >Pokud zadáte úložišti imagí pro modul je jenom vytvořen soubor prostředí. Pokud jste přijali výchozí nastavení localhost testovat a ladit v místním prostředí, pak není nutné deklarovat proměnné prostředí. 

* A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modul, který simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí kód Node.js, která se dodává s řešením je umístěn v **moduly** > [název vašeho modulu] > **app.js**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub. 

Jakmile budete připraveni k přizpůsobení šablony Node.js s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost. 

Visual Studio Code podporuje pro Node.js. Další informace o [jak pracovat s Node.js v nástroji VS Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Spuštění a ladění kódu modulu bez kontejneru

Modul IoT Edge Node.js závisí na sadu SDK pro zařízení Azure IoT Node.js. V modulu kódu výchozí inicializaci **ModuleClient** nastavení prostředí a zadejte název, což znamená, že modul IoT Edge Node.js vyžaduje nastavení prostředí a spustíte a je také potřeba odeslat nebo směrování zpráv do vstupního kanálů. Výchozí modul Node.js obsahuje pouze jednu vstupní kanál a název je **vstup1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Nastavení simulátoru IoT Edge pro jeden modul aplikace

1. Instalační program a spusťte simulátor, paletu příkazů VS Code, zadejte a vyberte **Azure IoT Edge: Start IoT Edge Hub simulátor pro jeden modul**. Musíte také zadat vstupní názvy pro vaši aplikaci jeden modul, typ **vstup1** a stiskněte klávesu Enter. Tento příkaz spustí **iotedgehubdev** rozhraní příkazového řádku a spuštění simulátoru IoT Edge a kontejner pro modul testování nástroj. Uvidíte výstupy níže v integrovaném terminálu simulátoru se spustil v režimu jednoho modulu úspěšně. Můžete také zobrazit `curl` příkaz, který pomůže přes poslat zprávu. Budete jej později potřebovat.

   ![Nastavení simulátoru IoT Edge pro jeden modul aplikace](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Můžete přesunout do Průzkumníka Dockeru a zobrazit stav spuštění modulu.

   ![Stav modulu simulátor](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** základní místní simulátor IoT Edge je kontejner. Může spouštět na vývojovém počítači bez démon zabezpečení IoT Edge a zadejte nastavení prostředí pro aplikace nativní modul nebo modul kontejnery. **Vstupní** kontejneru vystavený restAPIs umožňující most zprávy pro cílení na modul vstupní kanál.

2. Paleta příkazů VS Code, zadejte a vyberte **Azure IoT Edge: nastavit přihlašovací údaje modulu nastavení pro uživatele** nastavení modulu prostředí do `azure-iot-edge.EdgeHubConnectionString` a `azure-iot-edge.EdgeModuleCACertificateFile` v nastavení uživatele. Můžete najít tato nastavení prostředí jsou odkazovány v **.vscode** > **launch.json** a [uživatelská nastavení nástroji VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-nodejs-module-in-launch-mode"></a>Ladění modulu Node.js v režimu spuštění

1. V integrovaném terminálu, změňte adresář na **NodeModule** složky, spusťte následující příkaz k instalaci balíčků uzlu

   ```cmd
   npm install
   ```

2. Přejděte na adresu `app.js`. Přidejte zarážku v tomto souboru.

3. Přejděte do zobrazení ladění VS Code. Vyberte konfiguraci ladění **ModuleName místní ladění (Node.js)**. 

4. Klikněte na tlačítko **spustit ladění** nebo stiskněte klávesu **F5**. Spustí relaci ladění.

5. V integrovaném terminálu VS Code, spusťte následující příkaz k odeslání **Hello World** zpráva, která má modul. Toto je příkaz v předchozích krocích jsme si ukázali, jakmile se instalační program IoT Edge simulátor úspěšně. Můžete potřebovat k vytvoření nebo přechod na jiný integrovaný terminál, pokud aktuální blokovaný.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Pokud používáte Windows, musíte zajistit, že prostředí integrovaný terminál VS Code je **Git Bash** nebo **WSL Bash**. Nelze spustit `curl` příkazu v Powershellu nebo příkazového řádku. 
   
   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje rozhraní API pro odesílání zpráv prostřednictvím místo `curl`.

6. V zobrazení pro ladění pro VS Code zobrazí se vám proměnné na levém panelu. 

7. Chcete-li ukončit relaci ladění, klikněte na tlačítko Zastavit nebo stisknutím klávesy **Shift + F5**. Paleta příkazů VS Code, zadejte a vyberte **Azure IoT Edge: zastavení IoT Edge simulátor** zastavit a vyčistit simulátoru.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Vytvořit kontejner modulu pro ladění a ladění v připojení režimu

Výchozí řešení obsahuje dva moduly, jeden je modul simulované teplotní snímač a druhý je modul Node.js kanálu. Simulované teplotní snímač odesílá zprávy do kanálu modulu Node.js, a pak zprávy jsou směrované do služby IoT Hub. Ve složce modulu, který jste vytvořili existuje několik souborů Docker pro typy jiný kontejner. Použijte některý z těchto souborů, které končí příponou **.debug** vytvořit váš modul pro testování. V současné době modulů Node.js podporují jenom v kontejnery linux amd64, windows amd64 a linuxem arm32v7 ladění.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro řešení IoT Edge

Ve vývojovém počítači můžete spustit simulátor IoT Edge místo instalace démon zabezpečení IoT Edge pro spuštění vašeho řešení IoT Edge. 

1. V Průzkumníku zařízení na levé straně, klikněte pravým tlačítkem na hraničních zařízeních IoT zařízení ID, vyberte **nastavení IoT Edge simulátor** spusťte simulátor připojovacím řetězcem zařízení.

2. Uvidíte, že simulátor IoT Edge se úspěšně instalace v integrovaném terminálu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Sestavit a spustit kontejner pro ladění a ladění v připojení režimu

1. V nástroji VS Code, přejděte `deployment.template.json` souboru. Aktualizovat adresu URL bitové kopie modulu přidáním **.debug** na konec.

2. Nahraďte CreateOptions field modul Node.js v **deployment.template.json** níže obsah a uložte tento soubor: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

3. Přejděte do zobrazení ladění VS Code. Vyberte konfigurační soubor ladění pro modul. Název možnosti ladění by měl být podobný **ModuleName vzdálené ladění (Node.js)** nebo **ModuleName vzdálené ladění (Node.js v kontejneru Windows)**, které závisí na typu vašeho kontejneru na vývojovém počítači.

4. Vyberte **spustit ladění** nebo vyberte **F5**. Vyberte proces pro připojení.

5. V zobrazení pro ladění pro VS Code zobrazí se vám proměnné na levém panelu.

6. Chcete-li ukončit relaci ladění, klikněte na tlačítko Zastavit nebo stisknutím klávesy **Shift + F5**. Paleta příkazů VS Code, zadejte a vyberte **Azure IoT Edge: zastavení IoT Edge simulátor**.

> [!NOTE]
> Předchozí příklad ukazuje, jak ladit moduly Node.js IoT Edge v kontejnerech. Přidá vystavené porty ve vaší CreateOptions field kontejner modulu. Po dokončení ladění modulů Node.js, doporučujeme že odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="next-steps"></a>Další postup

Jakmile budete mít modul sestaven, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md)

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
