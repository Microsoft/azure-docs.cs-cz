---
title: Ladění modulů Node.js pro Azure IoT Edge | Dokumentace Microsoftu
description: Použití Visual Studio Code pro vývoj a ladění modulů Node.js pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8032fd2a0150597c55178648511c80233e63a911
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054722"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Vývoj a ladění modulů Node.js s použitím Azure IoT Edge pro Visual Studio Code

Odešlete vaši obchodní logiku pro provoz na hraničních zařízeních to zapnutím moduly pro Azure IoT Edge. Tento článek obsahuje podrobné pokyny k používání Visual Studio Code (VS Code) jako hlavní vývojový nástroj pro vývoj modulů jazyka C#.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač s Windows nebo Linuxem jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení nebo zařízení IoT Edge můžete simulovat na vývojovém počítači.

> [!NOTE]
> V tomto kurzu ladění popisuje, jak připojit proces v kontejneru modulu a ladit pomocí VS Code. Můžete ladit modulů Node.js v linuxu amd64, windows a arm32 kontejnery. Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging). 

Jelikož tento článek používá Visual Studio Code jako hlavní vývojový nástroj, nainstalujte VS Code a poté přidejte potřebnými rozšířeními:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Jak vytvořit modul, je třeba Node.js, který obsahuje sestavení složce projektu Dockeru k vytvoření bitové kopie modulu a container registry k uložení image modul npm:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Místního registru Docker můžete použít k prototypu a testování, namísto registru cloudu. 

K otestování modulu do zařízení, potřebujete aktivní služby IoT hub s alespoň jedno zařízení IoT Edge. Pokud chcete použít počítač jako zařízení IoT Edge, lze provést podle pokynů v kurzech pro [Windows](quickstart.md) nebo [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Následující kroky ukazují, jak vytvořit modul IoT Edge založené na rozhraní .NET Core 2.0 pomocí Visual Studio Code a rozšíření Azure IoT Edge. Začnete vytvořením řešení a pak v něm generování modulu první. Každé řešení může obsahovat více modulů. 

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
9. Zadejte úložiště imagí pro modul. VS Code autopopulates modulu pojmenovat, abyste měli pouze nahradit **localhost:5000** nahraďte svými vlastními informacemi registru. Pokud používáte místní registru Dockeru pro testování, pak místního hostitele je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **\<název registru\>. azurecr.io**.

VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a pak ho načte v novém okně.

V řešení máte tři položky: 
* A **.vscode** složka obsahuje konfiguraci ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. V tuto chvíli máte pouze jednu, ale můžete přidat více v paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**. 
* A **.env** soubor obsahuje seznam proměnných prostředí. Pokud jste ACR jako registr, doprava, teď máte ACR uživatelské jméno a heslo v ní. 

   >[!NOTE]
   >Pokud zadáte úložišti imagí pro modul je jenom vytvořen soubor prostředí. Pokud jste přijali výchozí nastavení localhost testovat a ladit v místním prostředí, pak není nutné deklarovat proměnné prostředí. 

* A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modul, který simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).

## <a name="devlop-your-module"></a>Devlop modulu

Výchozí funkce Azure Functions se kód, který je součástí řešení nachází v **moduly** > **\<název modulu\>**   >   **app.js**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub. 

Jakmile budete připraveni k přizpůsobení šablony Node.js s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Vytvoření a nasazení modulu pro ladění

V každé složky modulu existuje několik souborů Docker pro jiný kontejner typy. Můžete použít některý z těchto souborů, které končí příponou **.debug** vytvořit váš modul pro testování. C# moduly v současné době pouze podpory ladění do kontejnerů linuxu amd64.

1. V nástroji VS Code, přejděte `deployment.template.json` souboru. Nahraďte CreateOptions field modul Node.js v **deployment.template.json** níže obsah a uložte tento soubor: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. Paleta příkazů VS Code, zadejte a spusťte příkaz **Azure IoT Edge: řešení IoT Edge sestavení**.
3. Vyberte `deployment.template.json` soubor pro vaše řešení z palety příkazů. 
4. V Průzkumníku zařízení centra IoT Azure, klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **vytvoření nasazení pro zařízení IoT Edge**. 
5. Otevřít **config** složky vašeho řešení, vyberte `deployment.json` souboru. Klikněte na **Select Edge Deployment Manifest** (Vybrat manifest nasazení Edge). 

Pak uvidíte, že je úspěšně vytvořeno s nasazením, které ID v nástroji VS Code integrované terminálu.

Můžete zkontrolovat stav kontejneru v Průzkumníku VS Code Dockeru nebo spuštěním `docker ps` příkazu v terminálu.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Spuštění ladění modulu Node.Js v nástroji VS Code

VS Code uchovává informace o konfiguraci v ladění `launch.json` soubor umístěný ve `.vscode` složky v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytvoření nového řešení IoT Edge. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. 

1. Přejděte do zobrazení ladění VS Code a vyberte konfigurační soubor ladění pro modul.

2. Přejděte na adresu `app.js`. Přidejte zarážku v tomto souboru.

3. Klikněte na tlačítko **spustit ladění** tlačítko nebo stisknutím klávesy **F5**a vyberte proces pro připojení.

4. V zobrazení pro ladění pro VS Code zobrazí se proměnné v levém panelu. 

Předchozí příklad ukazuje, jak ladit moduly Node.js IoT Edge v kontejnerech. Přidá vystavené porty ve vaší CreateOptions field kontejner modulu. Po dokončení ladění modulů Node.js, doporučujeme že odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="next-steps"></a>Další postup

Jakmile budete mít modul sestaven, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md)

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
