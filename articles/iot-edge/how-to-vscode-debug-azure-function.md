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
ms.openlocfilehash: 8bd3513e932540f2dd710e0ac1536e757c00a3e1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Pomocí Visual Studio Code funkce Azure s Azure IoT hranou ladění

Tento článek obsahuje podrobné pokyny pro používání [Visual Studio Code](https://code.visualstudio.com/) jako hlavní vývojový nástroj k ladění funkcí Azure IoT hranu.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows nebo Linux jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení nebo zařízení IoT Edge můžete simulovat na vývojovém počítači.

> [!NOTE]
> Můžete ladit pouze funkce jazyka C# v kontejnerech linux amd64.

Než následující pokyny v tomto článku, proveďte kroky v [vyvíjet IoT řešení s více modulů v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Potom byste měli mít připravené následující položky:
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


[Použití Visual Studio Code k ladění modulu jazyka C# s hranou Azure IoT](how-to-vscode-debug-csharp-module.md)

