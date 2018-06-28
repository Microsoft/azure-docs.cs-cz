---
title: Práce s více modulů Azure IoT Edge v produktu VS Code | Microsoft Docs
description: Použití IoT rozšíření pro Visual Studio Code k vývoji více modulů najednou pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 4e9aac5f19fa75613dee2aba3853a0243d7d966b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048256"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Vývoj IoT řešení s více modulů v aplikaci Visual Studio Code

Visual Studio Code je možné použít k vývoji řešení Azure IoT Edge s více moduly. V tomto článku můžete vidět, jak vytvořit, aktualizovat a nasadit řešení IoT Edge, které přenáší data snímačů kanály na simulované zařízení IoT Edge ve VS Code. 

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle pokynů v tomto článku, budete potřebovat:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- Aktivní centrum IoT alespoň s jedním zařízením IoT Edge

Potřebujete také [Docker pro VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) s integrací Azure IoT Hub Device Explorer ke správě imagí a kontejnerů.

## <a name="create-your-iot-edge-solution"></a>Vytvoření řešení IoT Edge

1. V sadě Visual Studio kódu otevřete integrované terminál výběrem **zobrazení** > **integrované Terminálové**. 

1. V produktu VS Code **příkaz palety**, zadejte a spusťte příkaz **Azure IoT Edge: nové IoT řešení**. Vyberte složku pracovního prostoru a zadejte název řešení (výchozí je EdgeSolution). Vytvořte modul C# (s názvem **PipeModule**) jako první modul uživatele v tomto řešení. Výchozí šablona C# modulu je modulu kanálu, který přímo prostřednictvím kanálu předá zprávy z nadřazeného do proudu. Bude také nutné zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště image je založené na místním registru Dockeru (**localhost:5000/<first module name>**). Můžete ho změnit na Azure Container Registry nebo Centrum Dockeru. 

2. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Kořenová složka obsahuje složku s **moduly**, složku **.vscode** a soubor šablony manifestu nasazení. Informace o konfiguracích ladění jsou umístěné ve složce .vscode. Všechny kódy modulu uživatele jsou podsložky složky s moduly. Soubor deployment.template.json je manifest šablony nasazení. Některé parametry v tomto souboru se získávají analýzou ze souboru module.json, který je v každé složce modulu.

3. Přidejte do projektu řešení druhý modul. Chcete-li přidat nový modul na aktuální řešení několika způsoby. Zadejte a spusťte příkaz **Azure IoT Edge: Přidání okraj IoT modulu**. Vyberte soubor šablony nasazení, který chcete aktualizovat. Nebo klikněte pravým tlačítkem na složku moduly nebo klikněte pravým tlačítkem na soubor deployment.template.json a vyberte **přidat modul Edge IoT**. Potom budou existovat rozevíracího seznamu vyberte typ modulu. Vyberte **Azure Functions - C#** modulu s názvem **PipeFunction** a jeho úložiště Docker bitové kopie. Výchozí šablony funkce modulu jazyka C# je modulu kanálu, který přímo prostřednictvím kanálu předá zprávy z nadřazeného do proudu.

4. Otevřete soubor deployment.template.json. Ověřte, že soubor deklaruje tři moduly a modul runtime. Zpráva se generuje z modulu tempSensor. Zpráva se přímo předá kanálem prostřednictvím modulů SampleModule a SampleFunction a pak se odešle do služby IoT Hub. 

5. Aktualizujte trasy pro tyto moduly s použitím následujícího obsahu:

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Soubor uložte.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Vytvoření a nasazení řešení IoT Edge

1. V produktu VS Code **příkaz palety**, zadejte a spusťte příkaz **Azure IoT Edge: sestavení IoT řešení**. Na základě souboru module.json v jednotlivých složkách modulu, zahájí tento příkaz vytváření imagí Dockerů jednotlivých modulů, jejich kontejnerizaci a odeslání. Příkaz pak předá požadovanou hodnotu do souboru deployment.template.json a vygeneruje soubor deployment.json s informacemi ze složky konfigurace. Na integrovaném terminálu ve VS Code se zobrazí průběh sestavování. 

2. V **Device Exploreru** v Azure IoT Hub klikněte pravým tlačítkem myši na ID zařízení IoT Edge a pak vyberte příkaz **Create deployment for Edge device** (Vytvořit nasazení pro zařízení Edge). Vyberte soubor deployment.json ve složce konfigurace. Na integrovaném terminálu ve VS Code se zobrazí informace o úspěšném vytvoření nasazení s ID nasazení.

3. Pokud jste simulaci IoT hraniční zařízení na vývojovém počítači, můžete sledovat zobrazíte všechny kontejnery image modulu spustit během několika minut.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte **View** (Zobrazit)  >  **Command Palette** (Paleta příkazů). Potom vyberte příkaz **IoT: Start monitoring D2C** (IoT: Začít monitorovat zprávy D2C). 
2. Pokud chcete monitorování dat zastavit, použijte příkaz **IoT: Stop monitoring D2C message** (IoT: Zastavit monitorování zpráv D2C) na **paletě příkazů**. 

## <a name="next-steps"></a>Další postup

Informace o dalších scénářích pro vývoj s použitím Azure IoT Edge v editoru Visual Studio Code:

* [Vývoj modulu jazyka C# v produktu VS Code](how-to-develop-csharp-module.md)
* [Vývoj funkce jazyka C# v produktu VS Code](how-to-develop-csharp-function.md)
