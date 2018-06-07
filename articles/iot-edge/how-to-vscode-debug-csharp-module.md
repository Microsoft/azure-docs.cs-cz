---
title: Ladění jazyka C# moduly s hranou Azure IoT | Microsoft Docs
description: Pro ladění modulu jazyka C# s Azure IoT hrany v Visual Studio Code použijte Visual Studio Code.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5d521a4e5b22222f0b0a5505f14684598d7d5b0a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631390"
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Použití Visual Studio Code k ladění modulu jazyka C# s hranou Azure IoT
Tento článek obsahuje podrobné pokyny pro používání [Visual Studio (VS) kód](https://code.visualstudio.com/) jako hlavní vývojový nástroj k ladění moduly Azure IoT okraj.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows nebo Linux jako vývojovém počítači. Může být zařízení IoT Edge jiného fyzického zařízení, nebo můžete simulovat zařízení IoT Edge na vývojovém počítači.

> [!NOTE]
> Můžete ladit pouze modulu jazyka C# v kontejnerech linux amd64.

Než následující pokyny v tomto článku, proveďte kroky v [vyvíjet IoT řešení s více modulů v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Potom byste měli mít připravené následující položky:
- Místní registru Docker na vývojovém počítači spuštěna. Je navržený pro použití místního registru Docker pro prototypu a účely testování. Můžete aktualizovat v registru kontejneru `module.json` souboru ve složce každý modul.
- Okraj IoT řešení projektu pracovní prostor služby s podsložku modulu jazyka C# v ní.
- `Program.cs` Soubor s nejnovější modul kódu.
- Modulu runtime Edge na vývojovém počítači spuštěna.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Sestavení modulu IoT Edge C# pro ladění
1. Spustit ladění, budete muset použít **Dockerfile.amd64.debug** znovu sestavte docker image a znovu nasaďte řešení okraj. V Průzkumníku VS Code, přejděte na `deployment.template.json` souboru. Aktualizovat vaše adresa URL obrázku funkce přidáním `.debug` v části end.

2. Znovu sestavte řešení. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: sestavení IoT řešení**.

3. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro hraniční zařízení**. Vyberte `deployment.json` v soubor `config` složky. Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

Můžete zkontrolovat stav vaší kontejneru v Průzkumníku VS Code Docker nebo spustit `docker images` v terminálu.

## <a name="start-debugging-c-module-in-vs-code"></a>Spuštění ladění modulu jazyka C# v produktu VS Code
1. VS Code udržuje ladění informace o konfiguraci v `launch.json` soubor umístěný ve `.vscode` složku v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytváření nové řešení IoT okraj. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. Přejděte do zobrazení ladění a vyberte odpovídající soubor konfigurace ladění.
    ![Konfiguraci vyberte ladění](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Přejděte na adresu `program.cs`. Přidáte zarážky v tomto souboru.

3. Klikněte **spustit ladění** tlačítko nebo klikněte na tlačítko **F5**a vyberte možnost připojit k procesu.

4. V zobrazení ladění kódu VS uvidíte proměnné v levém panelu. 

> [!NOTE]
> Předchozí příklad ukazuje, jak k ladění modulů .NET Core IoT Edge na kontejnery. Je založen na verzi ladění `Dockerfile.debug`, což zahrnuje VSDBG (.NET Core příkazového řádku ladicího programu) v bitové kopii kontejneru při jeho vytváření. Po dokončení ladění moduly jazyka C#, doporučujeme přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge moduly.

## <a name="next-steps"></a>Další postup

[Pomocí Visual Studio Code funkce Azure s Azure IoT hranou ladění](how-to-vscode-debug-azure-function.md)

