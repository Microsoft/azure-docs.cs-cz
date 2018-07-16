---
title: Ladění funkce Azure Functions moduly pro Azure IoT Edge | Dokumentace Microsoftu
description: Použití nástroje Visual Studio Code pro ladění jazyka C# Azure functions s Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3176a3a4acc6e9ca486d409d861f2ed0e63473ec
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056500"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Použití Visual Studio Code pro vývoj a ladění Azure functions pro Azure IoT Edge

V tomto článku se dozvíte, jak používat [Visual Studio Code (VS Code)](https://code.visualstudio.com/) ladit vaše Azure functions na hraničních zařízeních IoT Azure.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač s Windows nebo Linuxem jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení. Nebo můžete simulovat zařízení IoT Edge na vývojovém počítači.

> [!NOTE]
> Tento článek ladění ukazuje, jak se připojit proces v kontejneru modulu a ladit pomocí VS Code. Můžete ladit pouze moduly jazyka C# v Linuxu amd64 kontejnery. Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging). 

Tento článek používá jako hlavní vývojový nástroj Visual Studio Code. Instalace Visual Studio Code. Pak přidejte potřebnými rozšířeními: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Jak vytvořit modul, třeba .NET k vytváření složce projektu Dockeru k vytvoření bitové kopie modulu a container registry k uložení bitové kopie modulu:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download)
* [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru. 

K otestování modulu do zařízení, potřebujete aktivní služby IoT hub s alespoň jedno zařízení IoT Edge. Pokud chcete použít počítač jako zařízení IoT Edge, postupujte podle kroků v tomto rychlém startu pro [Windows](quickstart.md) nebo [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Proveďte tyto kroky k vytvoření řešení IoT Edge, který má funkce modulu jeden jazyka C#. Každé řešení může mít několik modulů.

1. Ve Visual Studio Code, vyberte **zobrazení** > **integrovaný terminál**.
3. Vyberte **zobrazení** > **příkaz palety**.
4. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT Edge**. 

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Přejděte do složky, ve kterém chcete vytvořit nové řešení. Zvolte **vyberte složku**. 
6. Zadejte název pro vaše řešení. 
7. Zvolte **Azure Functions – C#** jako šablona pro první modul v rámci řešení.
8. Zadejte název modulu. Zvolte název, který je jedinečný v rámci vašeho registru kontejneru. 
9. Zadejte úložiště imagí pro modul. VS Code autopopulates modulu název s **localhost:5000**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **\<název registru\>. azurecr.io**.

VS Code přebírá informace k dispozici, vytvoří se projekt Azure Functions hraničních zařízeních IoT řešení a pak načte v novém okně.

Existují čtyři položky v rámci řešení: 

* A **.vscode** složka obsahuje konfiguraci ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. V tomto okamžiku máte jenom jeden. Ale můžete přidat více prostřednictvím paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**.
* **.Env** soubor obsahuje seznam proměnných prostředí. Pokud váš registr Azure Container Registry se v něm budete mít Azure Container Registry uživatelské jméno a heslo. 

   >[!NOTE]
   >Pokud zadáte úložišti imagí pro modul je jenom vytvořen soubor prostředí. Pokud jste přijali výchozí nastavení localhost testovat a ladit v místním prostředí, pak není nutné deklarovat proměnné prostředí. 

* A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modulu, která simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [zjistěte, jak můžete nasadit moduly a vytvářet manifesty nasazení](module-composition.md).

## <a name="devlop-your-module"></a>Devlop modulu

Výchozí funkce Azure Functions se kód, který je součástí řešení nachází v **moduly** > **\<název modulu\>**   >   **EdgeHubTrigger-Csharp** > **run.csx**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub. 

Jakmile budete připraveni k přizpůsobení šablony funkce Azure Functions s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost. 

## <a name="build-your-module-for-debugging"></a>Vytvoření modulu pro ladění
1. Chcete-li spustit ladění, použijte **Dockerfile.amd64.debug** znovu sestavte image dockeru a znovu nasaďte řešení Edge. V Průzkumníku VS Code, přejděte `deployment.template.json` souboru. Aktualizovat adresu URL vaší funkce image tak, že přidáte `.debug` na konec.

    ![Sestavení image ladění](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Znovu sestavte řešení. V nástroji VS Code paletu příkazů, zadejte a spusťte příkaz **Azure IoT Edge: řešení IoT Edge sestavení**.
3. V Průzkumníku zařízení centra IoT Azure, klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **vytvoření nasazení pro hraniční zařízení**. Vyberte `deployment.json` soubor `config` složky. Zobrazí se vám nasazení s ID nasazení, v terminálu VS Code integrované se úspěšně vytvořil.

Kontrola stavu kontejneru v Průzkumníku VS Code Dockeru nebo spuštěním `docker images` příkazu v terminálu.

## <a name="start-debugging-c-functions-in-vs-code"></a>Spuštění ladění funkcí jazyka C# ve VS Code
1. VS Code uchovává informace o konfiguraci v ladění `launch.json` soubor umístěný ve `.vscode` složky v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytvoření nového řešení IoT Edge. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. Přejděte do zobrazení ladění. Vyberte odpovídající konfigurační soubor ladění. Název možnosti ladění by měl být podobný **ModuleName vzdálené ladění (.NET Core)**.

   ![Ladění vyberte konfiguraci](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Přejděte na adresu `run.csx`. Přidejte zarážku ve funkci.
3. Vyberte **spustit ladění** nebo vyberte **F5**. Vyberte proces pro připojení.
4. V zobrazení ladění VS Code zobrazí se vám proměnné na levém panelu. 


> [!NOTE]
> Tento příklad ukazuje, jak ladit.Net Core funkce IoT Edge v kontejnerech. Je založen na ladicí verze `Dockerfile.amd64.debug`, což zahrnuje příkazového řádku debugger .NET Core VSDBG ve vaší imagi kontejneru při jeho vytváření. Doporučujeme vám, že přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge funkce po ladění funkcí jazyka C#.

## <a name="next-steps"></a>Další postup

Po začlenění modulu, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
