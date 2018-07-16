---
title: Práce s několika cloudy Azure IoT Edge v nástroji VS Code | Dokumentace Microsoftu
description: Použití rozšíření IoT pro Visual Studio Code k vývoji více modulů najednou pro Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041243"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Vývoj řešení IoT Edge s několika moduly v aplikaci Visual Studio Code

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

1. Visual Studio Code, otevřít integrovaný terminál výběrem **zobrazení** > **integrovaný terminál**. 

1. V nástroji VS Code **paletu příkazů**, zadejte a spusťte příkaz **Azure IoT Edge: nový IoT Edge řešení**. Vyberte složku pracovního prostoru a zadejte název řešení (výchozí je EdgeSolution). Vytvoření modulu jazyka C# (s názvem **PipeModule**) jako první modul uživatele v tomto řešení. Výchozí šablona modulu jazyka C# je modulu kanálu, který přímo prostřednictvím kanálu předá zpráv přijmout z upstreamu na směru server-klient. Bude také nutné zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště image je založené na místním registru Dockeru (**localhost:5000/<first module name>**). Můžete ho změnit na Azure Container Registry nebo Centrum Dockeru. 

2. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Kořenová složka obsahuje složku s **moduly**, složku **.vscode** a soubor šablony manifestu nasazení. Informace o konfiguracích ladění jsou umístěné ve složce .vscode. Všechny kódy modulu uživatele jsou podsložky složky s moduly. Soubor deployment.template.json je manifest šablony nasazení. Některé parametry v tomto souboru se získávají analýzou ze souboru module.json, který je v každé složce modulu.

3. Přidejte do projektu řešení druhý modul. Existuje několik způsobů, jak přidat nový modul do aktuálního řešení. Zadejte a spusťte příkaz **Azure IoT Edge: modul IoT Edge přidat**. Vyberte soubor šablony nasazení, který chcete aktualizovat. Nebo klikněte pravým tlačítkem na složku moduly nebo deployment.template.json soubor pravým tlačítkem a vyberte **přidat modul IoT Edge**. Pak bude rozevírací seznam a vyberte typ modulu. Vyberte **Azure Functions – C#** modul s názvem **PipeFunction** a jeho úložiště image Dockeru. Výchozí šablona funkce modulu jazyka C# je modulu kanálu, který přímo prostřednictvím kanálu předá zpráv přijmout z upstreamu na směru server-klient.

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

1. V nástroji VS Code **paletu příkazů**, zadejte a spusťte příkaz **Azure IoT Edge: řešení IoT Edge sestavení**. Na základě souboru module.json v jednotlivých složkách modulu, zahájí tento příkaz vytváření imagí Dockerů jednotlivých modulů, jejich kontejnerizaci a odeslání. Příkaz pak předá požadovanou hodnotu do souboru deployment.template.json a vygeneruje soubor deployment.json s informacemi ze složky konfigurace. Na integrovaném terminálu ve VS Code se zobrazí průběh sestavování. 

2. V **Device Exploreru** v Azure IoT Hub klikněte pravým tlačítkem myši na ID zařízení IoT Edge a pak vyberte příkaz **Create deployment for Edge device** (Vytvořit nasazení pro zařízení Edge). Vyberte soubor deployment.json ve složce konfigurace. Na integrovaném terminálu ve VS Code se zobrazí informace o úspěšném vytvoření nasazení s ID nasazení.

3. Pokud jste simulace zařízení IoT Edge na vývojovém počítači, můžete sledovat zobrazíte všechny image kontejnerů modulu začít během několika minut.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte **View** (Zobrazit)  >  **Command Palette** (Paleta příkazů). Potom vyberte příkaz **IoT: Start monitoring D2C** (IoT: Začít monitorovat zprávy D2C). 
2. Pokud chcete monitorování dat zastavit, použijte příkaz **IoT: Stop monitoring D2C message** (IoT: Zastavit monitorování zpráv D2C) na **paletě příkazů**. 

## <a name="next-steps"></a>Další postup

Informace o dalších scénářích pro vývoj s použitím Azure IoT Edge v editoru Visual Studio Code:

* Vývoj modulů ve VS Code s [jazyka C#](how-to-develop-csharp-module.md) nebo [Node.js](how-to-develop-node-module.md).
* Vyvíjet funkce Azure ve VS Code s [jazyka C#](how-to-develop-csharp-function.md).

Vývoj modulů pro zařízení IoT Edge, [principy a použití sady SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).