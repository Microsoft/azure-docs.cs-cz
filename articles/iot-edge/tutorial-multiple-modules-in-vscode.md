---
title: Správa více modulů Azure IoT Edge v produktu VS Code | Microsoft Docs
description: Pomocí kódu v jazyce Visual Studio pro vývoj řešení IoT Edge, které používají více modulů.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Vývoj IoT řešení s více modulů v aplikaci Visual Studio Code – náhled
Visual Studio Code můžete použít k vývoji řešení IoT Edge s více modulů. Tento článek vás provede vytváření, aktualizaci a nasazení řešení IoT Edge dat snímačů kanály na simulované zařízení IoT Edge v kódu Visual Studio. 

## <a name="prerequisites"></a>Požadavky

K provedení všech kroků v tomto článku zavedené následující požadavky:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Azure IoT Edge rozšíření pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# pro rozšíření Visual Studio Code (používá technologii OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [Základní rozhraní .NET 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- Šablona AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Aktivní Centrum IoT se alespoň jedno zařízení IoT Edge


* [Docker pro VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) s integrací explorer pro správu bitových kopií a kontejnery.


## <a name="prepare-your-first-iot-edge-solution"></a>Příprava vaše první řešení IoT
1. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: nové IoT řešení**. Pak vyberte pracovní prostor složce, zadejte název řešení (výchozí název je **EdgeSolution**) a vytvořte modul C# (**SampleModule**) jako první modul uživatele v tomto řešení. Budete taky muset zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště bitové kopie je založena na místním registru Docker (`localhost:5000/<first module name>`). Můžete ji také změnit kontejner Azure registru nebo úložiště Docker Hub.

   > [!NOTE]
   > Pokud používáte místní registru Docker, ujistěte se, běží registru zadáním příkazu `docker run -d -p 5000:5000 --restart=always --name registry registry:2` v okně konzoly.

2. V okně VS kód načte pracovního prostoru řešení IoT okraj. Obsahuje kořenové složce `modules` složku, `.vscode` složku a soubor manifestu šablony nasazení. Zobrazí se konfigurace v ladění `.vscode` složky. Všechny kódy modulu uživatele bude podsložky ve složce `modules`. `deployment.template.json` Je manifestu šablony nasazení. Některé parametry v tomto souboru se získá analýzou z `module.json`, které existuje ve složce každý modul.

3. Druhý modul přidáte do tohoto projektu řešení. Zadejte tento čas a spusťte **Edge: Přidání okraj IoT modulu** a vyberte soubor šablony nasazení aktualizace. Potom vyberte **funkce Azure - C#** s názvem **SampleFunction** a jeho úložiště Docker bitové kopie.

4. Otevřete `deployment.template.json` souboru a ověřte, že deklaruje tři moduly kromě modulu runtime. Zpráva se budou generovat z `tempSensor` modul a bude přímo přesměruje prostřednictvím `SampleModule` a `SampleFunction`, pak se odešle do služby IoT hub. 
5. Aktualizace trasy pro tyto moduly s následujícím obsahem:
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Uložte tento soubor.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Vytváření a nasazování řešení IoT Edge
1. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: sestavení IoT řešení**. Na základě `module.json` souboru ve složce každý modul, spusťte příkaz k vytvoření, containerize a push každé bitové kopie modulu docker. Potom předává požadovaná hodnota k `deployment.template.json` a vygeneruje `deployment.json` soubor s informacemi z `config` složky. Zobrazí se průběh sestavení v terminálu integrované VS Code.

2. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro hraniční zařízení**. Vyberte `deployment.json` pod `config` složky. Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

3. Pokud IoT hraniční zařízení jsou simulaci na vývojovém počítači, zobrazí se, že všechny kontejnery image modul bude spuštěn za pár minut.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. Pokud chcete monitorovat data přicházející do centra IoT, vyberte **Zobrazit** > **Paleta příkazů...** a vyhledejte **IoT: Spustit monitorování zpráv typu zařízení-cloud**. 
2. Pokud chcete monitorování dat zastavit, použijte příkaz **IoT: Zastavit monitorování zpráv typu zařízení-cloud** na paletě příkazů. 

## <a name="next-steps"></a>Další postup

Další informace o dalších scénářů pro vývoj Azure IoT hrany v Visual Studio Code:

* [Ladění modulu jazyka C# v produktu VS Code](how-to-vscode-debug-csharp-module.md)
* [Ladění funkce jazyka C# v produktu VS Code](how-to-vscode-debug-azure-function.md)