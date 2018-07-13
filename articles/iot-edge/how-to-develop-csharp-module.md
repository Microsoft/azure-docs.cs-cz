---
title: Ladění jazyka C# moduly pro Azure IoT Edge | Dokumentace Microsoftu
description: Použití nástroje Visual Studio Code pro vývoj, sestavení a ladění modulu C# pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 93f5e4447f43cd8cda346743d813236bcc4ac947
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006324"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Použití Visual Studio Code pro vývoj a ladění modulů jazyka C# pro Azure IoT Edge

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio Code (VS Code) jako hlavní nástroje k vývoji a ladění modulů jazyka C#.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač s Windows nebo Linuxem jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení. Nebo můžete simulovat zařízení IoT Edge na vývojovém počítači.

> [!NOTE]
> Tento článek ladění ukazuje, jak se připojit proces v kontejneru modulu a ladit pomocí VS Code. Můžete ladit pouze funkcí jazyka C# v Linuxu amd64 kontejnery. Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging). 

Vzhledem k tomu tento článek používá Visual Studio Code jako hlavní vývojový nástroj, nainstalujte VS Code. Pak přidejte potřebnými rozšířeními:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Jak vytvořit modul, třeba .NET k vytváření složce projektu Dockeru k vytvoření bitové kopie modulu a container registry k uložení bitové kopie modulu:
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru. 

K otestování modulu do zařízení, potřebujete aktivní služby IoT hub s alespoň jedno zařízení IoT Edge. Pokud chcete použít počítač jako zařízení IoT Edge, postupujte podle kroků v tomto rychlém startu pro [Windows](quickstart.md) nebo [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Podle následujících kroků vytvořte modul IoT Edge založené na rozhraní .NET Core 2.0 pomocí Visual Studio Code a rozšíření Azure IoT Edge. Nejprve vytvořte řešení a pak vytvořte první modulu v tomto řešení. Každé řešení může obsahovat více než jeden modul. 

1. Ve Visual Studio Code, vyberte **zobrazení** > **integrovaný terminál**.
3. Vyberte **zobrazení** > **příkaz palety**. 
4. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT Edge**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Přejděte do složky, ve kterém chcete vytvořit nové řešení. Zvolte **vyberte složku**. 
6. Zadejte název pro vaše řešení. 
7. Vyberte **modulu jazyka C#** jako šablona pro první modul v rámci řešení.
8. Zadejte název modulu. Zvolte název, který je jedinečný v rámci vašeho registru kontejneru. 
9. Zadejte název modulu úložiště imagí. VS Code autopopulates modulu název s **localhost:5000**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **\<název registru\>. azurecr.io**.

VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně.

   ![Zobrazit řešení IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Existují čtyři položky v rámci řešení: 
* A **.vscode** složka obsahuje konfiguraci ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. V tomto okamžiku máte jenom jeden. Ale můžete přidat více v paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**. 
* **.Env** soubor obsahuje seznam proměnných prostředí. Pokud váš registr Azure Container Registry se v něm budete mít Azure Container Registry uživatelské jméno a heslo. 
* A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modulu, která simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [zjistěte, jak můžete nasadit moduly a vytvářet manifesty nasazení](module-composition.md). 

## <a name="build-and-deploy-your-module-for-debugging"></a>Vytvoření a nasazení modulu pro ladění

Ve složce každého modulu existuje několik souborů Docker pro typy jiný kontejner. Použijte některý z těchto souborů, které končí příponou **.debug** vytvořit váš modul pro testování. C# moduly v současné době podporují ladění pouze v amd64 kontejnery Linuxu.

1. V nástroji VS Code, přejděte `deployment.template.json` souboru. Aktualizovat adresu URL vaší funkce image tak, že přidáte **.debug** na konec.

   ![Přidat *** .debug na název obrázku](./media/how-to-develop-csharp-module/image-debug.png)

2. V paletu příkazů VS Code, zadejte a spusťte příkaz **hrany: řešení IoT Edge sestavení**.
3. Vyberte `deployment.template.json` soubor pro vaše řešení z palety příkazů. 
4. V Azure IoT Hub Device Explorer klikněte pravým tlačítkem na ID zařízení IoT Edge Potom vyberte **vytvoření nasazení pro zařízení IoT Edge**. 
5. Otevřít vaše řešení **config** složky. Vyberte `deployment.json` souboru. Zvolte **vyberte Manifest nasazení Edge**. 

Zobrazí se vám nasazení s ID nasazení, v terminálu VS Code integrované se úspěšně vytvořil.

Kontrola stavu kontejneru v Průzkumníku VS Code Dockeru nebo spuštěním `docker ps` příkazu v terminálu.

## <a name="start-debugging-c-module-in-vs-code"></a>Spuštění ladění modulu C# ve VS Code
VS Code uchovává informace o konfiguraci v ladění `launch.json` soubor umístěný ve `.vscode` složky v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytvoření nového řešení IoT Edge. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. 

1. Přejděte do zobrazení ladění VS Code. Vyberte konfigurační soubor ladění pro modul. Název možnosti ladění by měl být podobný **ModuleName vzdálené ladění (.NET Core)** ![ladění vyberte konfiguraci](./media/how-to-develop-csharp-module/debug-config.png).

2. Přejděte na adresu `program.cs`. Přidejte zarážku v tomto souboru.

3. Vyberte **spustit ladění** nebo vyberte **F5**. Vyberte proces pro připojení.

4. V zobrazení pro ladění pro VS Code zobrazí se vám proměnné na levém panelu. 

> [!NOTE]
> Tento příklad ukazuje, jak ladit moduly .NET Core IoT Edge v kontejnerech. Je založen na ladicí verze `Dockerfile.debug`, což zahrnuje příkazového řádku debugger .NET Core VSDBG ve vaší imagi kontejneru při jeho vytváření. Po ladění modulů jazyka C#, doporučujeme vám přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="next-steps"></a>Další postup

Po začlenění modulu, zjistěte, jak [nasadit moduly Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

