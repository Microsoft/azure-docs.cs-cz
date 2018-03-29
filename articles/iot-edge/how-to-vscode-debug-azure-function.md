---
title: Funkce Azure s Azure IoT hranou ladění pomocí Visual Studio Code | Microsoft Docs
description: Ladění jazyka C# funkce Azure s Azure IoT Edge v produktu VS Code
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8da16ffe72ad265f0201c2fe7e00e585dfa255e8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Pomocí Visual Studio Code funkce Azure s Azure IoT hranou ladění

Tento článek obsahuje podrobné pokyny pro používání [Visual Studio Code](https://code.visualstudio.com/) jako hlavní vývojový nástroj k ladění funkcí Azure IoT hranu.

## <a name="prerequisites"></a>Požadavky
Tento kurz předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows nebo Linux jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení nebo zařízení IoT Edge můžete simulovat na vývojovém počítači.

Ujistěte se, že máte dokončené následující kurzy, než začnete v tomto návodu.
- [Vývoj IoT řešení s více modulů v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

Po dokončení předchozí kurz, byste měli mít připravený, následující položky
- Místní registru Docker na vývojovém počítači spuštěna. Je navržený pro použití místního registru Docker pro prototypu a účely testování. Můžete aktualizovat v registru kontejneru `module.json` souboru ve složce každý modul.
- Okraj IoT řešení projektu pracovní prostor služby s podsložku modulu funkce Azure v ní.
- `run.csx` Soubor s kód funkce.
- Modulu runtime Edge na vývojovém počítači spuštěna.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Sestavení modulu IoT Edge funkce pro účely ladění
1. Spustit ladění, budete muset použít **Dockerfile.amd64.debug** znovu sestavte docker image a znovu nasaďte řešení okraj. V Průzkumníku VS Code, přejděte na `deployment.template.json` souboru. Aktualizovat vaše adresa URL obrázku funkce přidáním `.debug` v části end.

    ![Sestavení bitové kopie pro ladění](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Znovu sestavte řešení. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: sestavení IoT řešení**.

3. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro hraniční zařízení**. Vyberte `deployment.json` pod `config` složky. Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

> [!NOTE]
> Můžete zkontrolovat stav vaší kontejneru v Průzkumníku VS Code Docker nebo spustit `docker images` v terminálu.

## <a name="start-debugging-c-function-in-vs-code"></a>Spuštění ladění funkce jazyka C# v produktu VS Code
1. VS Code udržuje ladění informace o konfiguraci v `launch.json` soubor umístěný ve `.vscode` složku v pracovním prostoru. To `launch.json` soubor byl vytvořen při vytváření nové řešení IoT okraj. A, bude aktualizována pokaždé, když přidáte nový modul, který podporují ladění. Přejděte do zobrazení ladění a vyberte odpovídající soubor konfigurace ladění.
    ![Konfiguraci vyberte ladění](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Přejděte na adresu `run.csx`. Přidáte zarážky ve funkci.

3. Klikněte na tlačítko Spustit ladění nebo klikněte na tlačítko **F5**a vyberte možnost připojit k procesu.

4. V zobrazení ladění kódu VS uvidíte proměnné v levém panelu. 


> [!NOTE]
> Výše příklad ukazuje, jak ladění rozhraní .net Core IoT Edge funkce kontejnerům. Je založen na verzi ladění `Dockerfile.amd64.debug`, což zahrnuje VSDBG (.NET Core příkazového řádku ladicího programu) v bitové kopii kontejneru při jeho vytváření. Doporučujeme, abyste přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge funkci po dokončení ladění funkce jazyka C#.

## <a name="next-steps"></a>Další postup

V tomto kurzu vytvořit funkci Azure a nasadit IoT okraj pro účely ladění a spuštění ladění v produktu VS Code. Můžete pokračovat na některý z následujících kurzech Další informace o další scénáře při vývoji Azure IoT Edge v produktu VS Code. 

> [!div class="nextstepaction"]
> [Vývoj IoT řešení s více modulů v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md)

