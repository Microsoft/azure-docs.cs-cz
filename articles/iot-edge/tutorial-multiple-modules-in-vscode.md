---
title: Správa více modulů Azure IoT Edge ve VS Code | Microsoft Docs
description: Visual Studio Code slouží k vývoji řešení Azure IoT Edge, která používají více modulů.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763030"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Vyvíjejte řešení IoT Edge s více moduly v editoru Visual Studio Code (preview)

Visual Studio Code je možné použít k vývoji řešení Azure IoT Edge s více moduly. V tomto článku můžete vidět, jak vytvořit, aktualizovat a nasadit řešení IoT Edge, které přenáší data snímačů kanály na simulované zařízení IoT Edge ve VS Code. 

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle pokynů v tomto článku, budete potřebovat:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Rozšíření Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Rozšíření jazyka C# pro Visual Studio Code (využívající OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- Šablona AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Aktivní centrum IoT alespoň s jedním zařízením IoT Edge

Potřebujete také [Docker pro VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) s integrací Azure IoT Hub Device Explorer ke správě imagí a kontejnerů.

## <a name="prepare-your-first-iot-edge-solution"></a>Příprava prvního řešení IoT Edge

1. Na **paletě příkazů** ve VS Code zadejte a spusťte příkaz **Edge: New IoT Edge solution** (Edge: Nové řešení IoT Edge). Vyberte složku pracovního prostoru a zadejte název řešení (výchozí je EdgeSolution). Vytvořte modul C# (s názvem **SampleModule**) jako první modul uživatele v tomto řešení. Bude také nutné zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště image je založené na místním registru Dockeru (**localhost:5000/<first module name>**). Můžete ho změnit na Azure Container Registry nebo Centrum Dockeru.

   > [!NOTE]
   > Pokud používáte místní registr Dockeru, musí být registr spuštěný. V okně konzoly zadejte následující příkaz:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. V okně nástroje VS Code se načte pracovní prostor řešení IoT Edge. Kořenová složka obsahuje složku s **moduly**, složku **.vscode** a soubor šablony manifestu nasazení. Informace o konfiguracích ladění jsou umístěné ve složce .vscode. Všechny kódy modulu uživatele jsou podsložky složky s moduly. Soubor deployment.template.json je manifest šablony nasazení. Některé parametry v tomto souboru se získávají analýzou ze souboru module.json, který je v každé složce modulu.

3. Přidejte do projektu řešení druhý modul. Zadejte a spusťte příkaz **Edge: Add IoT Edge module** (Edge: Přidat modul IoT Edge). Vyberte soubor šablony nasazení, který chcete aktualizovat. Vyberte modul **Azure Function – C#** s názvem **SampleFunction** a jeho úložiště image Dockeru.

4. Otevřete soubor deployment.template.json. Ověřte, že soubor deklaruje tři moduly a modul runtime. Zpráva se generuje z modulu tempSensor. Zpráva se přímo předá kanálem prostřednictvím modulů SampleModule a SampleFunction a pak se odešle do služby IoT Hub. 

5. Aktualizujte trasy pro tyto moduly s použitím následujícího obsahu:

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Soubor uložte.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Vytvoření a nasazení řešení IoT Edge

1. Na **paletě příkazů** ve VS Code zadejte a spusťte příkaz **Edge: Build IoT Edge solution** (Edge: Vytvořit řešení IoT Edge). Na základě souboru module.json v jednotlivých složkách modulu, zahájí tento příkaz vytváření imagí Dockerů jednotlivých modulů, jejich kontejnerizaci a odeslání. Příkaz pak předá požadovanou hodnotu do souboru deployment.template.json a vygeneruje soubor deployment.json s informacemi ze složky konfigurace. Na integrovaném terminálu ve VS Code se zobrazí průběh sestavování.

2. V **Device Exploreru** v Azure IoT Hub klikněte pravým tlačítkem myši na ID zařízení IoT Edge a pak vyberte příkaz **Create deployment for Edge device** (Vytvořit nasazení pro zařízení Edge). Vyberte soubor deployment.json ve složce konfigurace. Na integrovaném terminálu ve VS Code se zobrazí informace o úspěšném vytvoření nasazení s ID nasazení.

3. Pokud simulujete zařízení IoT Edge na vývojovém počítači, všimněte si, že se všechny kontejnery imagí modulů spouští během několika minut.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

1. Když chcete monitorovat data, která přichází do služby IoT Hub, vyberte **View** (Zobrazit)  >  **Command Palette** (Paleta příkazů). Potom vyberte příkaz **IoT: Start monitoring D2C** (IoT: Začít monitorovat zprávy D2C). 
2. Pokud chcete monitorování dat zastavit, použijte příkaz **IoT: Stop monitoring D2C message** (IoT: Zastavit monitorování zpráv D2C) na **paletě příkazů**. 

## <a name="next-steps"></a>Další kroky

Informace o dalších scénářích pro vývoj s použitím Azure IoT Edge v editoru Visual Studio Code:

* [Ladění modulu C# ve VS Code](how-to-vscode-debug-csharp-module.md)
* [Ladění funkce C# ve VS Code](how-to-vscode-debug-azure-function.md)
