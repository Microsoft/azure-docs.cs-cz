---
title: Ladění moduly Pythonu pro Azure IoT Edge | Dokumentace Microsoftu
description: Použití Visual Studio Code pro vývoj, vytvářet a ladit modul Pythonu pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c3cf2b703760debb368e26d629ee73f56ce93d39
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441246"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>Použití Visual Studio Code pro vývoj a ladění moduly Pythonu pro Azure IoT Edge

Obchodní logiky můžete proměnit moduly pro Azure IoT Edge. V tomto článku se dozvíte, jak používat Visual Studio Code (VS Code) jako hlavní nástroje k vývoji a ladění moduly Pythonu.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že používáte počítač nebo virtuální počítač s Windows nebo Linuxem jako vývojovém počítači. A simulace zařízení IoT Edge na vývojovém počítači s démonem zabezpečení IoT Edge.

> [!NOTE]
> Tento článek ladění ukazuje, jak se připojit proces v kontejneru modulu a ladit pomocí VS Code. Můžete ladit pouze moduly Pythonu v amd64 kontejnery Linuxu. Pokud nejste obeznámeni s možnosti ladění sady Visual Studio Code, přečtěte si informace o [ladění](https://code.visualstudio.com/Docs/editor/debugging). 

Vzhledem k tomu tento článek používá Visual Studio Code jako hlavní vývojový nástroj, nainstalujte VS Code. Pak přidejte potřebnými rozšířeními:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozšíření docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Rozšíření Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) pro Visual Studio Code. 
* [Python](https://www.python.org/downloads/).
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) pro instalaci balíčků Python (většinou je součástí instalace Pythonu).
* Nainstalujte **Cookiecutter** pomocí následujícího příkazu
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

Jak vytvořit modul, je třeba Dockeru k vytvoření bitové kopie modulu a container registry k uložení bitové kopie modulu:
* [Docker Community Edition](https://docs.docker.com/install/) na vývojovém počítači. 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo [Docker Hubu](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Prototypu a místo registru cloudu pro účely testování můžete použít místní registru Dockeru. 

K otestování modulu do zařízení, potřebujete aktivní služby IoT hub s alespoň jedno zařízení IoT Edge. Pokud chcete použít počítač jako zařízení IoT Edge, postupujte podle kroků v tomto rychlém startu pro [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Vytvořit novou šablonu řešení

Podle následujících kroků vytvořte modul IoT Edge založené na Pythonu sady SDK Azure IoT pomocí Visual Studio Code a rozšíření Azure IoT Edge. Nejprve vytvořte řešení a pak vytvořte první modulu v tomto řešení. Každé řešení může obsahovat více než jeden modul. 

1. Ve Visual Studio Code, vyberte **zobrazení** > **integrovaný terminál**.

2. Vyberte **zobrazení** > **příkaz palety**. 

3. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT Edge**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

4. Přejděte do složky, ve kterém chcete vytvořit nové řešení. Zvolte **vyberte složku**. 

5. Zadejte název pro vaše řešení. 

6. Vyberte **modul Pythonu** jako šablona pro první modul v rámci řešení.

7. Zadejte název modulu. Zvolte název, který je jedinečný v rámci vašeho registru kontejneru. 

8. Zadejte název modulu úložiště imagí. VS Code autopopulates modulu název s **localhost:5000**. Nahraďte ho vlastním registru informace. Pokud používáte místní registru Dockeru pro testování, pak **localhost** je v pořádku. Pokud používáte Azure Container Registry, potom pomocí serveru přihlášení z nastavení svého registru. Přihlašovací server vypadá  **\<název registru\>. azurecr.io**. V řetězci nahraďte pouze část localhost, název vašeho modulu neodstraňujte. Konečný řetězec vypadá jako \<název registru\>.azurecr.io/\<modulename\>.

   ![Zadání úložiště imagí Dockeru](./media/how-to-develop-c-module/repository.png)

VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně.

Existují čtyři položky v rámci řešení: 
* A **.vscode** složka obsahuje konfiguraci ladění.
* A **moduly** složka obsahuje podsložky pro každý modul. V tomto okamžiku máte jenom jeden. Ale můžete přidat více v paletu příkazů pomocí příkazu **Azure IoT Edge: Přidat modul IoT Edge**. 
* **.Env** soubor obsahuje seznam proměnných prostředí. Pokud váš registr Azure Container Registry se v něm budete mít Azure Container Registry uživatelské jméno a heslo. 

   > [!NOTE]
   > Pokud zadáte úložišti imagí pro modul je jenom vytvořen soubor prostředí. Pokud jste přijali výchozí nastavení localhost testovat a ladit v místním prostředí, pak není nutné deklarovat proměnné prostředí. 

* A **deployment.template.json** souborů obsahuje nový modul spolu s ukázku **tempSensor** modulu, která simuluje data, která můžete použít pro testování. Další informace o způsobu práce manifesty nasazení najdete v tématu [zjistěte, jak můžete nasadit moduly a vytvářet manifesty nasazení](module-composition.md). 
* A **deployment.debug.template.json** souboru kontejnery ladicí verze modulu bitové kopie s možností správný kontejner.

## <a name="develop-your-module"></a>Vývoj modulu

Výchozí modul kód Pythonu, který je součástí řešení se nachází v **moduly** > [název vašeho modulu] > **main.py**. V modulu a soubor deployment.template.json nastaveny tak, aby mohli sestavit řešení, ji nasdílet do vašeho registru kontejneru a nasazení do zařízení pro začátek testování bez zásahu do jakéhokoli kódu. Modul je určený pro jednoduše trvat, než vstupní zdroj (v tomto případě tempSensor modulu, která simuluje data) a zřetězit ho do služby IoT Hub. 

Jakmile budete připraveni k přizpůsobení šablony Python s vlastním kódem, použijte [sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) vytvářet moduly, které řeší klíč musí pro řešení IoT, jako je zabezpečení, Správa zařízení a spolehlivost. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Vytvoření a nasazení modulu pro ladění

Ve složce každého modulu existuje několik souborů Docker pro typy jiný kontejner. Použijte některý z těchto souborů, které končí příponou **.debug** vytvořit váš modul pro testování. V současné době podporují moduly Pythonu ladění pouze v amd64 kontejnery Linuxu. 

1. V nástroji VS Code, přejděte `deployment.debug.template.json` souboru. Tento soubor obsahuje ladicí verze modulu možnosti vytvoření imagí pomocí správné. 

3. Přejděte na `main.py`, přidejte následující kódy za část importování
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. Přidejte následující jediný řádek kódu ve zpětné volání, kterou chcete ladit

    ```python
    ptvsd.break_into_debugger()
    ```

   Například, pokud chcete ladit `receive_message_callback` metody. Je to možné hmyz jediný řádek kódu, jako je níže.

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. V nástroji VS Code paletu příkazů, zadejte a spusťte příkaz **Azure IoT Edge: sestavení a Push hraničních zařízeních IoT řešení**.
3. Vyberte `deployment.debug.template.json` soubor pro vaše řešení z palety příkazů. 
4. V Azure IoT Hub Device Explorer klikněte pravým tlačítkem na ID zařízení IoT Edge Potom vyberte **vytvoření nasazení pro jedno zařízení**. 
5. Otevřít vaše řešení **config** složky. Vyberte `deployment.debug.amd64.json` souboru. Zvolte **vyberte Manifest nasazení Edge**. 

Zobrazí se vám nasazení s ID nasazení, v terminálu VS Code integrované se úspěšně vytvořil.

Kontrola stavu kontejneru v Průzkumníku VS Code Dockeru nebo spuštěním `docker ps` příkazu v terminálu.

## <a name="start-debugging-python-module-in-vs-code"></a>Spuštění ladění modul Pythonu v nástroji VS Code
VS Code uchovává informace o konfiguraci v ladění `launch.json` soubor umístěný ve `.vscode` složky v pracovním prostoru. To `launch.json` soubor byl vygenerován při vytvoření nového řešení IoT Edge. Aktualizuje pokaždé, když přidáte nový modul, který podporuje ladění. 

1. Přejděte do zobrazení ladění VS Code. Vyberte konfigurační soubor ladění pro modul. Název možnosti ladění by měl být podobný **ModuleName vzdálené ladění (Python)**

2. Přejděte na adresu `main.c`. Přidejte zarážku v metodě zpětného volání jste `ptvsd.break_into_debugger()` v ní.

3. Vyberte **spustit ladění** nebo vyberte **F5**. Vyberte proces pro připojení.

4. V zobrazení pro ladění pro VS Code zobrazí se vám proměnné na levém panelu. 

Předchozí příklad ukazuje, jak ladit moduly Python IoT Edge v kontejnerech. Přidá vystavené porty ve vaší CreateOptions field kontejner modulu. Po dokončení ladění moduly Pythonu, doporučujeme že odebrat tyto vystavené porty pro moduly IoT Edge připravené pro produkční prostředí.

## <a name="next-steps"></a>Další postup

Po začlenění modulu, zjistěte, jak [nasadit moduly Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md).

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
