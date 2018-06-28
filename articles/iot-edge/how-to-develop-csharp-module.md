---
title: Ladění jazyka C# moduly pro hraniční Azure IoT | Microsoft Docs
description: Pomocí kódu v jazyce Visual Studio k vývoji, vytvářet a ladit modul C# pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c237b1eb9874357afa922f809109cf8f2181561e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048182"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Visual Studio Code využívat k vývoji a ladění jazyka C# moduly pro Azure IoT Edge

Můžete odeslat obchodní logiky pracovat na hranici vypnutím do modulů pro Azure IoT okraj. Tento článek obsahuje podrobné pokyny pro použití kódu v jazyce Visual Studio (VS Code) jako hlavní vývojový nástroj k vývoji moduly jazyka C#.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows nebo Linux jako vývojovém počítači. Může být zařízení IoT Edge jiného fyzického zařízení, nebo můžete simulovat zařízení IoT Edge na vývojovém počítači.

> [!NOTE]
> Ladění kurz popisuje, jak připojit proces v kontejneru modulu a ladění kódem VS. Můžete ladit pouze funkce jazyka C# v kontejnerech linux amd64. Pokud nejste obeznámeni s možností ladění kódu v jazyce Visual Studio, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging). 

Vzhledem k tomu, že tento článek používá jako hlavní vývojářského nástroje Visual Studio Code, nainstalujte VS Code a poté přidejte potřebnými rozšířeními:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Chcete-li vytvořit modul, je třeba rozhraní .NET, který sestaví složce projektu Docker k vytvoření bitové kopie modulu a registru kontejner pro uložení image modul:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) na vývojovém počítači. 
* [Kontejner Azure registru](https://docs.microsoft.com/azure/container-registry/) nebo [úložiště Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Místní Docker registru můžete použít pro prototypu a pro účely testování, místo registru cloudu. 

K testování modul na zařízení, musíte active IoT hub s alespoň jedno zařízení IoT okraj. Pokud chcete použít jako IoT hraničním zařízením, můžete tak učinit pomocí následujícího postupu v kurzech pro [Windows](quickstart.md) nebo [Linux](quickstart-linux.md) 

## <a name="create-a-new-solution-template"></a>Vytvořte novou šablonu řešení

Následující kroky ukazují, jak vytvořit modul IoT Edge založené na rozhraní .NET Core 2.0 pomocí kódu v jazyce Visual Studio a rozšíření Azure IoT okraj. Můžete začít vytváření řešení a potom generování první modul v tomto řešení. Každé řešení může obsahovat více modulů. 

1. V sadě Visual Studio Code vyberte **zobrazení** > **integrované Terminálové**.
3. Vyberte **zobrazení** > **příkaz palety**. 
4. Příkaz palety, zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT**.

   ![Spustit nové řešení IoT](./media/how-to-develop-csharp-module/new-solution.png)

5. Přejděte do složky, kde chcete vytvořit nové řešení a klikněte na tlačítko **vyberte složku**. 
6. Zadejte název pro vaše řešení. 
7. Zvolte **C# modulu** jako šablonu pro první modul v řešení.
8. Zadejte název modulu. Zvolte název, který je jedinečná v rámci vašeho kontejneru registru. 
9. Zadejte úložiště bitové kopie pro modul. Název VS Code autopopulates modul, musíte se nahradit **localhost:5000** s vlastní informace registru. Pokud používáte místní Docker registru pro testování, je dobře localhost. Pokud používáte Azure kontejneru registru, použijte server přihlášení z nastavení v registru. Přihlášení na server vypadá jako  **\<název registru\>. azurecr.io**.

VS Code trvá informace zadat, vytvoří řešení IoT a pak ho načte v novém okně.

   ![Zobrazení řešení IoT](./media/how-to-develop-csharp-module/view-solution.png)

V rámci řešení máte tři položky: 
* A **.vscode** složka obsahuje konfigurace ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. Aktuálně máte pouze jednu, ale můžete přidat více v příkazu palety pomocí příkazu **Azure IoT Edge: Přidání okraj modulu IoT**. 
* A **.env** soubor obsahuje seznam proměnných prostředí. Pokud jste ACR jako registru, správné Teď máte ACR uživatelské jméno a heslo v ní. 
* A **deployment.template.json** soubor obsahuje seznam nový modul společně s ukázku **tempSensor** modul, který simuluje data, která můžete použít pro testování. Další informace o tom, jak manifesty nasazení pracovní najdete v tématu [pochopit, jak IoT Edge moduly můžete použít, nakonfigurovat a znovu použít](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Vytváření a nasazování modul pro ladění

V každé složky modul existuje více souborů Docker pro typy jiný kontejner. Můžete použít některý z těchto souborů, které končí příponou **.debug** k sestavení modul pro testování. V současné době C# moduly podporují jenom v kontejnerech linux amd64 ladění.

1. V produktu VS Code, přejděte na `deployment.template.json` souboru. Aktualizovat vaše adresa URL obrázku funkce přidáním **.debug** do konce.

   ![Přidání .debug do název bitové kopie](./media/how-to-develop-csharp-module/image-debug.png)

2. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: sestavení IoT řešení**.
3. Vyberte `deployment.template.json` soubor pro vaše řešení z palety příkaz. 
4. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro IoT hraniční zařízení**. 
5. Otevřete **konfigurace** složky vašeho řešení, vyberte `deployment.json` souboru. Klikněte na tlačítko **vyberte Edge – Manifest nasazení**. 

Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

Můžete zkontrolovat stav vaší kontejneru v Průzkumníku VS Code Docker nebo spustit `docker ps` v terminálu.

## <a name="start-debugging-c-module-in-vs-code"></a>Spuštění ladění modulu jazyka C# v produktu VS Code
VS Code udržuje ladění informace o konfiguraci v `launch.json` soubor umístěný ve `.vscode` složku v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytváření nové řešení IoT okraj. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. 

1. Přejděte do zobrazení ladění VS Code a vyberte soubor konfigurace ladění pro modul. Název možnosti ladění by měla být jako **ModuleName vzdáleného ladění (.NET Core)** ![konfiguraci vyberte ladění](./media/how-to-develop-csharp-module/debug-config.png)

2. Přejděte na adresu `program.cs`. Přidáte zarážky v tomto souboru.

3. Klikněte **spustit ladění** tlačítko nebo klikněte na tlačítko **F5**a vyberte možnost připojit k procesu.

4. V zobrazení ladění kódu VS uvidíte proměnné v levém panelu. 

> [!NOTE]
> Předchozí příklad ukazuje, jak k ladění modulů .NET Core IoT Edge na kontejnery. Je založen na verzi ladění `Dockerfile.debug`, což zahrnuje VSDBG (.NET Core příkazového řádku ladicího programu) v bitové kopii kontejneru při jeho vytváření. Po dokončení ladění moduly jazyka C#, doporučujeme přímo použít nebo si přizpůsobit `Dockerfile` bez VSDBG pro produkční prostředí IoT Edge moduly.

## <a name="next-steps"></a>Další postup

Jakmile máte modul vytvořené, zjistěte, jak [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md)

