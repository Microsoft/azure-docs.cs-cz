---
title: Ladění více modulů pro Azure IoT Edge v produktu VS Code | Microsoft Docs
description: Použití Visual Studio Code k ladění více modulů s hranou Azure IoT
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049587"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Použití Visual Studio Code k ladění více modulů s hranou Azure IoT
Tento článek obsahuje podrobné pokyny pro používání [Visual Studio (VS) kód](https://code.visualstudio.com/) k ladění více modulů na IoT okraj.

## <a name="prerequisites"></a>Požadavky
Dokončení tohoto kurzu [vyvíjet IoT řešení s více modulů v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md) a zajistěte, aby byla minimálně dvě modulů spuštěných ve vašem zařízení IoT okraj.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Více cíle a vzdálené ladění v produktu VS Code
S příponou VS Code a hraniční Azure IoT můžete připojit proces modulu v kontejneru, zda kontejner běží na vývojovém počítači nebo ve vzdáleném fyzického zařízení IoT okraj. Ladění více aplikačními modulů spuštěných v kontejnerech je ve skutečnosti připojení více než jeden proces v samostatných kontejnerech. VS Code [více cíl ladění](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) lze použít při ladění více aplikačními moduly.

   > [!TIP]
   > Pokud vaše kontejner modulu běží ve vzdálené fyzické zařízení IoT Edge, možná budete muset instalační program [počítač Docker](https://docs.docker.com/machine/overview/) tak, aby vzdálené hostitelů Docker může komunikovat modulu Docker na vývojovém počítači.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Sestavení vaší hraniční IoT moduly pro účely ladění
1. Spustit ladění – více modulu, budete muset použít **Dockerfile.amd64.debug** znovu sestavte imagí dockeru a znovu nasaďte řešení okraj. V Průzkumníku VS Code, přejděte na `deployment.template.json` souboru. Aktualizovat přidáním adresy URL vaší bitové kopie `.debug` v části end. Budete potřebovat dvě bitové kopie modulu s `.debug` alespoň. Pokud pracujete v řešení z předchozí kurz, byste měli mít funkce modulu jazyka C# a modul C#. Aktualizovat přidáním tyto adresy URL dvě bitové kopie `.debug` v end a uložte tento soubor. 
2. Znovu sestavte řešení. V příkazu palety VS Code, zadejte a spusťte příkaz **Azure IoT Edge: sestavení IoT řešení**.
3. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro hraniční zařízení**. Vyberte `deployment.json` souboru pod `config` složky. Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

Můžete zkontrolovat stav vaší kontejneru v Průzkumníku VS Code Docker nebo spuštěním `docker ps` v terminálu.

### <a name="start-debugging-c-function-in-vs-code"></a>Spuštění ladění funkce jazyka C# v produktu VS Code
1. VS Code udržuje ladění informace o konfiguraci v `launch.json` soubor umístěný ve `.vscode` složku v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytváření nové řešení IoT okraj. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. Přejděte do zobrazení ladění a vyberte odpovídající soubor konfiguraci ladění jazyka C# funkce modulu vzdáleného ladění.
2. Přejděte na adresu `run.csx`. Přidáte zarážky ve funkci.
3. Klikněte **spustit ladění** tlačítko nebo klikněte na tlačítko **F5**a vyberte možnost připojit k procesu.
4. V zobrazení ladění kódu VS uvidíte proměnné v levém panelu. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Spuštění ladění jazyka C# modulu ve stejnou dobu v produktu VS Code
1. V příkazu palety VS Code zadejte a spusťte příkaz "Pracovního prostoru: Duplicitní prostoru v novém okně". Nové okno VS Code začíná ve stejném pracovním prostoru.
2. Přejděte do zobrazení ladění a vyberte odpovídající soubor konfiguraci ladění jazyka C# modulu vzdáleného ladění.
3. Přejděte na adresu `program.cs`. V modulu jazyka C# přidejte zarážku.
4. Klikněte **spustit ladění** tlačítko nebo klikněte na tlačítko **F5**a vyberte možnost připojit k procesu.
5. V zobrazení ladění kódu VS uvidíte proměnné v levém panelu. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Najdete v části proměnných ve více ladění windows
1. Nyní máte alespoň dvě ladicí relace spuštěné v dvě VS kódu – okno. Jeden z zarážce by měl přístupů.
2. Stiskněte klávesu `F10` nebo klikněte na tlačítko Krokovat s přeskočením **ladění nástrojů**.
3. Zarážka v jiném okně VS Code by měl přístupů. 
4. Pokračujte výše dva kroky, uvidíte proměnné z více modulů v několika VS Code ladění windows.

> [!NOTE]
> Výše příklad ukazuje, jak k ladění více modulů s Azure IoT hranou. Je založen na verzi ladění `Dockerfile.amd64.debug`, což zahrnuje VSDBG (.NET Core příkazového řádku ladicího programu) v bitové kopii kontejneru při jeho vytváření. Doporučujeme, abyste přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge funkci po dokončení ladění funkce jazyka C#.

## <a name="next-steps"></a>Další postup

Jakmile máte modul vytvořené, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) 0
