---
title: Ladění více modulů ve Visual Studio Code – Azure IoT Edge | Dokumentace Microsoftu
description: Ladění více modulů Azure IoT Edge pomocí Visual Studio Code
author: shizn
manager: philmea
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0c421eb1532536a3d11013073f0474f5ac37311b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100430"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Ladění více modulů Azure IoT Edge pomocí Visual Studio Code
Tento článek obsahuje podrobné pokyny k používání [kódu Visual Studio (VS)](https://code.visualstudio.com/) ladit více modulů na hraničních zařízeních IoT.

## <a name="prerequisites"></a>Požadavky
Dokončení tohoto kurzu [vývoj řešení IoT Edge s několika moduly v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md) a ujistěte se, že máte alespoň dva moduly běžícího ve vašem zařízení IoT Edge.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Více cílových a vzdálené ladění ve VS Code
Připojit proces modulu v kontejneru, můžete rozšíření VS Code a Azure IoT Edge, jestli je kontejner spuštěný na vývojovém počítači nebo ve vzdáleném fyzické zařízení IoT Edge. Ladění více modulů spouštěných v kontejnerech je ve skutečnosti připojení více než jeden proces v samostatných kontejnery. VS Code [více cíl ladění](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) se dá použít při ladění je moduly.

   > [!TIP]
   > Pokud váš kontejner modulu je spuštěn v vzdálené fyzické zařízení IoT Edge, možná budete muset nastavení [Docker Machine](https://docs.docker.com/machine/overview/) tak, aby modul Docker na svém vývojovém počítači můžete komunikovat na vzdáleného hostitele Docker.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Vytváření modulů IoT Edge pro účely ladění
1. Spustit ladění vícemodulová, budete muset použít **Dockerfile.amd64.debug** znovu sestavte Image dockeru a znovu nasaďte řešení Edge. V Průzkumníku VS Code, přejděte na `deployment.template.json` souboru. Aktualizace adres URL vaší image tak, že přidáte `.debug` nakonec. Budete potřebovat dvě bitové kopie modulu s `.debug` alespoň. Pokud pracujete v řešení v předchozím kurzu, měli byste C# funkce modulu a C# modulu. Aktualizace adres URL tyto dvě image tak, že přidáte `.debug` nakonec a uložte tento soubor. 
2. Znovu sestavte řešení. Paleta příkazů VS Code, zadejte a spusťte příkaz **Azure IoT Edge: řešení IoT Edge sestavení**.
3. V Průzkumníku zařízení centra IoT Azure, klikněte pravým tlačítkem na ID zařízení IoT Edge a pak vyberte **vytvoření nasazení pro hraniční zařízení**. Vyberte `deployment.json` soubor `config` složky. Pak uvidíte, že je úspěšně vytvořeno s nasazením, které ID v nástroji VS Code integrované terminálu.

Můžete zkontrolovat stav kontejneru v Průzkumníku VS Code Dockeru nebo spuštěním `docker ps` příkazu v terminálu.

### <a name="start-debugging-c-function-in-vs-code"></a>Spuštění ladění C# funkce v nástroji VS Code
1. VS Code uchovává informace o konfiguraci v ladění `launch.json` soubor umístěný ve `.vscode` složky v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytvoření nového řešení IoT Edge. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. Přejděte do zobrazení ladění a vyberte odpovídající konfigurační soubor ladění pro C# funkce modul vzdáleného ladění.
2. Přejděte na adresu `run.csx`. Přidejte zarážku ve funkci.
3. Klikněte na tlačítko **spustit ladění** tlačítko nebo stisknutím klávesy **F5**a vyberte proces pro připojení.
4. V zobrazení pro ladění pro VS Code zobrazí se proměnné v levém panelu. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Spuštění ladění C# modulu současně v nástroji VS Code
1. V nástroji VS Code paletu příkazů zadejte a spusťte příkaz "Pracovní prostor: Duplicitní pracovní prostor v novém okně". Nové okno VS Code začíná u stejného pracovního prostoru.
2. Přejděte do zobrazení ladění a vyberte odpovídající konfigurační soubor ladění pro C# modul vzdáleného ladění.
3. Přejděte na adresu `program.cs`. Přidejte zarážku v C# modulu.
4. Klikněte na tlačítko **spustit ladění** tlačítko nebo stisknutím klávesy **F5**a vyberte proces pro připojení.
5. V zobrazení pro ladění pro VS Code zobrazí se proměnné v levém panelu. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Zobrazit proměnné v několika ladění systému windows
1. Nyní máte alespoň dva ladicí relace používané dvě okna VS Code. Jeden z zarážka by měla přístupů.
2. Stisknutím klávesy `F10` nebo klikněte na tlačítko Krokovat s přeskočením v **ladění nástrojů**.
3. By měl být dosažení zarážky v jiném okně VS Code. 
4. Dál nad dva kroky, se zobrazí proměnné z více modulů ve více VS Code ladění systému windows.

> [!NOTE]
> Nad příklad ukazuje, jak až po ladění více modulů Azure IoT Edge. Je založen na ladicí verze `Dockerfile.amd64.debug`, což zahrnuje VSDBG (.NET Core příkazového řádku. ladicí program) ve vaší imagi kontejneru při jeho vytváření. Doporučujeme vám přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge funkce po dokončení ladění vašeho C# funkce.

## <a name="next-steps"></a>Další postup

Jakmile budete mít modul sestaven, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) 0
