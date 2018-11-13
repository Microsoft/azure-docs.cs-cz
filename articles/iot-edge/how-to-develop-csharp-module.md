---
title: Ladění jazyka C# moduly pro Azure IoT Edge | Dokumentace Microsoftu
description: Použití nástroje Visual Studio Code pro vývoj, sestavení a ladění modulu C# pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: abfd65920348bd51a9923d0a7c74f0f980a01540
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567821"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Použití Visual Studio Code pro vývoj a ladění modulů jazyka C# pro Azure IoT Edge

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio Code (VS Code) jako hlavní nástroje k vývoji a ladění modulů jazyka C#.

## <a name="prerequisites"></a>Požadavky

Můžete použít počítač nebo virtuální počítač s Windows, macOS nebo Linux jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení.

Existují dva způsoby, jak ladění modulu C# ve VS Code. Jedním ze způsobů je připojit proces v kontejneru modulu, jinou možností je spustit kód modulu v režimu ladění. Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging).

Nejprve nainstalujte Visual Studio Code a potom přidejte následující nezbytná rozšíření:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Jak vytvořit modul, třeba .NET k vytváření složce projektu Dockeru k vytvoření bitové kopie modulu a container registry k uložení bitové kopie modulu:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru. 

Nastavit místní vývojové prostředí, chcete-li ladit, spouštět a testovat řešení IoT Edge, budete potřebovat [nástroj pro vývojáře služby Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Nainstalujte [(2.7/3.6) Python a Pip](https://www.python.org/). Nainstalujte **iotedgehubdev** spuštěním následující příkaz, v terminálu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

K otestování modulu do zařízení, potřebujete aktivní služby IoT hub s alespoň jeden ID zařízení IoT Edge vytvořili. Pokud spustíte proces démon IoT Edge na vývojovém počítači, můžete potřebovat zastavit EdgeHub a EdgeAgent teprve potom přejděte k dalšímu kroku. 

## <a name="create-a-new-solution-with-c-module"></a>Vytvoření nového řešení pomocí modulu jazyka C#

Podle následujících kroků vytvořte modul IoT Edge založené na rozhraní .NET Core 2.1 pomocí Visual Studio Code a rozšíření Azure IoT Edge. Nejprve vytvořte řešení a pak vytvořte první modulu v tomto řešení. Každé řešení může obsahovat více než jeden modul. 

1. Ve Visual Studio Code, vyberte **zobrazení** > **integrovaný terminál**.
2. Vyberte **zobrazení** > **příkaz palety**. 
3. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT Edge**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Přejděte do složky, ve kterém chcete vytvořit nové řešení. Zvolte **vyberte složku**. 
5. Zadejte název pro vaše řešení. 
6. Vyberte **modulu jazyka C#** jako šablona pro první modul v rámci řešení.
7. Zadejte název modulu. Zvolte název, který je jedinečný v rámci vašeho registru kontejneru. 
8. Zadejte název modulu úložiště imagí. VS Code autopopulates modulu název s **localhost:5000**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **\<název registru\>. azurecr.io**. V řetězci nahraďte pouze část localhost, název vašeho modulu neodstraňujte.

   ![Zadání úložiště imagí Dockeru](./media/how-to-develop-csharp-module/repository.png)

VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně.

   ![Zobrazit řešení IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Existují čtyři položky v rámci řešení: 

* A **.vscode** složka obsahuje konfiguraci ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. V tomto okamžiku máte jenom jeden. Ale můžete přidat více v paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**. 
* **.Env** soubor obsahuje seznam proměnných prostředí. Pokud váš registr Azure Container Registry se v něm budete mít Azure Container Registry uživatelské jméno a heslo. 

   >[!NOTE]
   >Pokud zadáte úložišti imagí pro modul je jenom vytvořen soubor prostředí. Pokud jste přijali výchozí nastavení localhost testovat a ladit v místním prostředí, pak není nutné deklarovat proměnné prostředí. 

* A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modulu, která simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [zjistěte, jak můžete nasadit moduly a vytvářet manifesty nasazení](module-composition.md). 

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí hodnota C# kód modulu, který je součástí řešení se nachází na **moduly** > ** [název vašeho modulu] ** > **Program.cs**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub. 

Jakmile budete připraveni k přizpůsobení šablony jazyka C# s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost. 

Podpora jazyka C# ve VS Code je optimalizovaná pro vývoj pro různé platformy .NET Core. Další informace o [jak pracovat s C# ve VS Code](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Spuštění a ladění kódu modulu bez kontejneru

Modul IoT Edge C#.Net Core je aplikace. A závisí na C# zařízení sady SDK Azure IoT. Vzhledem k tomu, že modul IoT C# vyžaduje nastavení prostředí spuštění a spuštění, v modulu kódu výchozí inicializaci **ModuleClient** nastavení prostředí a zadejte název. Také musíte odeslat nebo směrování zpráv do kanálů vstupu. Vaše výchozí modulu jazyka C# obsahuje pouze jednu vstupní kanál a název je **vstup1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Nastavení simulátoru IoT Edge pro jeden modul aplikace

1. Instalační program a spusťte simulátor, paletu příkazů VS Code, zadejte a vyberte **Azure IoT Edge: Start IoT Edge Hub simulátor pro jeden modul**. Musíte také zadat vstupní názvy pro vaši aplikaci jeden modul, typ **vstup1** a stiskněte klávesu Enter. Tento příkaz spustí **iotedgehubdev** rozhraní příkazového řádku a spuštění simulátoru IoT Edge a kontejner pro modul testování nástroj. Uvidíte výstupy níže v integrovaném terminálu simulátoru se spustil v režimu jednoho modulu úspěšně. Můžete také zobrazit `curl` příkaz, který pomůže přes poslat zprávu. Budete jej později potřebovat.

   ![Nastavení simulátoru IoT Edge pro jeden modul aplikace](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Můžete přesunout do Průzkumníka Dockeru a zobrazit stav spuštění modulu.

   ![Stav modulu simulátor](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** základní místní simulátor IoT Edge je kontejner. Může spouštět na vývojovém počítači bez démon zabezpečení IoT Edge a zadejte nastavení prostředí pro aplikace nativní modul nebo modul kontejnery. **Vstupní** kontejneru vystavený restAPIs umožňující most zprávy pro cílení na modul vstupní kanál.

2. Paleta příkazů VS Code, zadejte a vyberte **Azure IoT Edge: nastavit přihlašovací údaje modulu nastavení pro uživatele** nastavení modulu prostředí do `azure-iot-edge.EdgeHubConnectionString` a `azure-iot-edge.EdgeModuleCACertificateFile` v nastavení uživatele. Můžete najít tato nastavení prostředí jsou odkazovány v **.vscode** > **launch.json** a [uživatelská nastavení nástroji VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>Modul aplikace vytvářet a ladit v režimu spuštění

1. V integrovaném terminálu, změňte adresář na **CSharpModule** složky, spusťte následující příkaz k sestavení.Net Core aplikace.

    ```cmd
    dotnet build
    ```

2. Přejděte na adresu `program.cs`. Přidejte zarážku v tomto souboru.

3. Přejděte do zobrazení ladění VS Code: Zobrazení > ladění. Vyberte konfiguraci ladění **ModuleName místní ladění (.NET Core)** z rozevíracího seznamu. 

  ![Přejít do režimu ladění ve VS Code](media/how-to-develop-csharp-module/debug-view.png)

4. Klikněte na tlačítko **spustit ladění** nebo stiskněte klávesu **F5**. Spustí relaci ladění.

   > [!NOTE]
   > Pokud vaše.Net Core `TargetFramework` není konzistentní s vaše cesta k programu v `launch.json`. Je potřeba ručně aktualizovat cestu program v `launch.json` dodržovat `TargetFramework` v souboru csproj. Tak že VS Code můžete úspěšně spustit tento program.

5. V integrovaném terminálu VS Code, spusťte následující příkaz k odeslání **Hello World** zpráva, která má modul. Toto je příkaz v předchozích krocích jsme si ukázali, jakmile se instalační program IoT Edge simulátor úspěšně.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Pokud používáte Windows, musíte zajistit, že prostředí integrovaný terminál VS Code je **Git Bash** nebo **WSL Bash**. Nelze spustit `curl` příkazu v Powershellu nebo příkazového řádku. 
   
   > [!TIP]
   > Můžete také použít [PostMan](https://www.getpostman.com/) nebo jiné nástroje rozhraní API pro odesílání zpráv prostřednictvím místo `curl`.

6. V zobrazení pro ladění pro VS Code zobrazí se vám proměnné na levém panelu. 

    ![Proměnné Watch](media/how-to-develop-csharp-module/single-module-variables.png)

7. Chcete-li ukončit relaci ladění, klikněte na tlačítko Zastavit nebo stisknutím klávesy **Shift + F5**. Paleta příkazů VS Code, zadejte a vyberte **Azure IoT Edge: zastavení IoT Edge simulátor** zastavit a vyčistit simulátoru.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Vytvořit kontejner modulu pro ladění a ladění v připojení režimu

Výchozí řešení obsahuje dva moduly, jeden je modul simulované teplotní snímač a druhý je kanálu modulu jazyka C#. Simulované teplotní snímač odesílá zprávy do kanálu modulu jazyka C# a potom zprávy jsou směrované do služby IoT Hub. Ve složce modulu, který jste vytvořili existuje několik souborů Docker pro typy jiný kontejner. Použijte některý z těchto souborů, které končí příponou **.debug** vytvořit váš modul pro testování. V současné době C# moduly podporu ladění pouze v amd64 kontejnery Linuxu v připojení režimu.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Nastavení simulátoru IoT Edge pro řešení IoT Edge

Ve vývojovém počítači můžete spustit simulátor IoT Edge místo instalace démon zabezpečení IoT Edge pro spuštění vašeho řešení IoT Edge. 

1. V Průzkumníku zařízení na levé straně, klikněte pravým tlačítkem na hraničních zařízeních IoT zařízení ID, vyberte **nastavení IoT Edge simulátor** spusťte simulátor připojovacím řetězcem zařízení.

2. Uvidíte, že simulátor IoT Edge se úspěšně instalace v integrovaném terminálu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Sestavit a spustit kontejner pro ladění a ladění v připojení režimu

1. V nástroji VS Code, přejděte `deployment.template.json` souboru. Aktualizace vašeho jazyka C# modulu adresa URL obrázku tak, že přidáte **.debug** na konec.

   ![Přidat *** .debug na název obrázku](./media/how-to-develop-csharp-module/image-debug.png)

2. Přejděte na adresu `program.cs`. Přidejte zarážku v tomto souboru.

3. V Průzkumníku souborů VS Code, vyberte `deployment.template.json` soubor pro vaše řešení v místní nabídce klikněte na tlačítko **sestavení a spuštění hraničních zařízeních IoT řešení v simulátoru**. Můžete sledovat všechny kontejneru modulu protokoly ve stejném okně. Můžete také přejít na Průzkumník Dockeru a sledujte stav kontejneru.

   ![Proměnné Watch](media/how-to-develop-csharp-module/view-log.png)

4. Přejděte do zobrazení ladění VS Code. Vyberte konfigurační soubor ladění pro modul. Název možnosti ladění by měl být podobný **ModuleName vzdálené ladění (.NET Core)**

   ![Vyberte konfiguraci](media/how-to-develop-csharp-module/debug-config.png)

5. Vyberte **spustit ladění** nebo vyberte **F5**. Vyberte proces pro připojení.

6. V zobrazení pro ladění pro VS Code zobrazí se vám proměnné na levém panelu.

7. Chcete-li ukončit relaci ladění, klikněte na tlačítko Zastavit nebo stisknutím klávesy **Shift + F5**. Paleta příkazů VS Code, zadejte a vyberte **Azure IoT Edge: zastavení IoT Edge simulátor**.

    > [!NOTE]
    > Tento příklad ukazuje, jak ladit moduly .NET Core IoT Edge v kontejnerech. Je založen na ladicí verze `Dockerfile.debug`, což zahrnuje Visual Studio .NET příkazového řádku ladicí program jádra (VSDBG) ve vaší imagi kontejneru při jeho vytváření. Po ladění modulů jazyka C#, doporučujeme vám přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro moduly IoT Edge připravené pro produkční prostředí.


## <a name="next-steps"></a>Další postup

Po začlenění modulu, zjistěte, jak [nasadit moduly Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
