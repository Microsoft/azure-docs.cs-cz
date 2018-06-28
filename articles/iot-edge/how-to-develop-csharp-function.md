---
title: Ladění moduly funkce pro hraniční Azure IoT | Microsoft Docs
description: Použití Visual Studio Code k ladění jazyka C# Azure Functions s hranou Azure IoT
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052823"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Visual Studio Code využívat k vývoji a ladění Azure Functions pro Azure IoT Edge

Tento článek obsahuje podrobné pokyny pro používání [Visual Studio (VS) kód](https://code.visualstudio.com/) k ladění funkcí Azure IoT hranu.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows nebo Linux jako vývojovém počítači. Zařízení IoT Edge může být jiné fyzické zařízení nebo zařízení IoT Edge můžete simulovat na vývojovém počítači.

> [!NOTE]
> Ladění kurz popisuje, jak připojit proces v kontejneru modulu a ladění kódem VS. Můžete ladit pouze moduly jazyka C# v kontejnerech linux amd64. Pokud nejste obeznámeni s možností ladění kódu v jazyce Visual Studio, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging). 

Tento článek používá jako hlavní vývojářského nástroje Visual Studio Code. Instalaci VS Code a pak přidejte potřebnými rozšířeními: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Chcete-li vytvořit modul, je třeba .NET k vytvoření složky, do projektu, k vytvoření bitové kopie modulu a registru kontejner pro uložení image modulu Docker:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) na vývojovém počítači. 
* [Kontejner Azure registru](https://docs.microsoft.com/azure/container-registry/) nebo [úložiště Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Místní Docker registru můžete použít pro prototypu a pro účely testování, místo registru cloudu. 

K testování modul na zařízení, musíte active IoT hub s alespoň jedno zařízení IoT okraj. Pokud chcete použít jako IoT hraničním zařízením, můžete tak učinit pomocí následujícího postupu v kurzech pro [Windows](quickstart.md) nebo [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Vytvořte novou šablonu řešení

Následující kroky ukazují, jak vytvářet řešení IoT Edge, která obsahuje modul funkce jeden C#. Každé řešení může obsahovat více modulů.

1. V sadě Visual Studio Code vyberte **zobrazení** > **integrované Terminálové**.
3. Vyberte **zobrazení** > **příkaz palety**.
4. Příkaz palety, zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT**. 

   ![Spustit nové řešení IoT](./media/how-to-develop-csharp-module/new-solution.png)

5. Přejděte do složky, kde chcete vytvořit nové řešení a klikněte na tlačítko **vyberte složku**. 
6. Zadejte název pro vaše řešení. 
7. Zvolte **Azure Functions - C#** jako šablonu pro první modul v řešení.
8. Zadejte název modulu. Zvolte název, který je jedinečná v rámci vašeho kontejneru registru. 
9. Zadejte úložiště bitové kopie pro modul. Název VS Code autopopulates modul, musíte se nahradit **localhost:5000** s vlastní informace registru. Pokud používáte místní Docker registru pro testování, je dobře localhost. Pokud používáte Azure kontejneru registru, použijte server přihlášení z nastavení v registru. Přihlášení na server vypadá jako  **\<název registru\>. azurecr.io**.

VS Code trvá informace zadat, vytvoří IoT řešení s projektem funkce a pak ho načte v novém okně.

V rámci řešení máte tři položky: 

* A **.vscode** složku, která obsahuje konfigurace ladění.
* A **moduly** složku, která obsahuje podsložky pro každý modul. Aktuálně máte pouze jednu, ale můžete přidat více prostřednictvím příkazu palety pomocí příkazu **Azure IoT Edge: Přidání okraj modulu IoT**.
* A **.env** soubor obsahuje seznam proměnných prostředí. Pokud jste ACR jako registru, správné Teď máte ACR uživatelské jméno a heslo v ní. 
* A **deployment.template.json** soubor obsahuje seznam nový modul společně s ukázku **tempSensor** modul, který simuluje data, která můžete použít pro testování. Další informace o tom, jak manifesty nasazení pracovní najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Sestavení modulu IoT Edge funkce pro účely ladění
1. Spustit ladění, budete muset použít **Dockerfile.amd64.debug** znovu sestavte docker image a znovu nasaďte řešení okraj. V Průzkumníku VS Code, přejděte na `deployment.template.json` souboru. Aktualizovat vaše adresa URL obrázku funkce přidáním `.debug` v části end.

    ![Sestavení bitové kopie pro ladění](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Znovu sestavte řešení. V příkazu palety VS Code, zadejte a spusťte příkaz **Azure IoT Edge: sestavení IoT řešení**.
3. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro hraniční zařízení**. Vyberte `deployment.json` souboru pod `config` složky. Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

Můžete zkontrolovat stav vaší kontejneru v Průzkumníku VS Code Docker nebo spuštěním `docker images` v terminálu.

## <a name="start-debugging-c-function-in-vs-code"></a>Spuštění ladění funkce jazyka C# v produktu VS Code
1. VS Code udržuje ladění informace o konfiguraci v `launch.json` soubor umístěný ve `.vscode` složku v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytváření nové řešení IoT okraj. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. Přejděte do zobrazení ladění a vyberte odpovídající soubor konfigurace ladění. Název možnosti ladění by měla být jako **ModuleName vzdáleného ladění (.NET Core)** ![konfiguraci vyberte ladění](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Přejděte na adresu `run.csx`. Přidáte zarážky ve funkci.
3. Klikněte **spustit ladění** tlačítko nebo klikněte na tlačítko **F5**a vyberte možnost připojit k procesu.
4. V zobrazení ladění kódu VS uvidíte proměnné v levém panelu. 


> [!NOTE]
> Výše uvedený příklad ukazuje postup ladění rozhraní .net Core IoT Edge funkce kontejnerům. Je založen na verzi ladění `Dockerfile.amd64.debug`, což zahrnuje VSDBG (.NET Core příkazového řádku ladicího programu) v bitové kopii kontejneru při jeho vytváření. Doporučujeme, abyste přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge funkci po dokončení ladění funkce jazyka C#.

## <a name="next-steps"></a>Další postup

Jakmile máte modul vytvořené, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md)
