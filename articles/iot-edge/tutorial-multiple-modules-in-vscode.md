---
title: Práce s více modulů IoT Edge v kódu Visual Studio | Microsoft Docs
description: Azure Machine Learning nasadit jako modul pro hraniční zařízení
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Vývoj IoT řešení s více modulů v aplikaci Visual Studio Code – náhled
Visual Studio Code můžete použít k vývoji řešení IoT Edge s více modulů. Tento článek vás provede vytváření, aktualizaci a nasazení řešení IoT Edge dat snímačů kanály na simulované zařízení IoT Edge v kódu Visual Studio. V tomto článku se dozvíte, jak:

* Použít k vytvoření IoT řešení Visual Studio Code
* Slouží k přidání nového modulu pracujete VS Code řešení IoT. 
* Nasazení řešení IoT okraj (více modulů) do zařízení IoT Edge
* Zobrazení vygenerovaných dat

## <a name="prerequisites"></a>Požadavky
* Dokončete následující kurzy
  * [Nasazení modulu jazyka C#](tutorial-csharp-module.md)
  * [Nasazení funkce jazyka C#](tutorial-deploy-function.md)
  * [Nasazení modul Python](tutorial-python-module.md)
* [Docker pro VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) s integrací explorer pro správu bitových kopií a kontejnery.


## <a name="prepare-your-first-iot-edge-solution"></a>Příprava vaše první řešení IoT
1. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: nové IoT řešení**. Pak vyberte pracovní prostor složce, zadejte název řešení (výchozí název je **EdgeSolution**) a vytvořte modul C# (**SampleModule**) jako první modul uživatele v tomto řešení. Budete taky muset zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště bitové kopie je založena na místním registru Docker (`localhost:5000/<first module name>`). Můžete ji také změnit kontejner Azure registru nebo úložiště Docker Hub.

> [!NOTE]
> Pokud používáte místní registru Docker, zkontrolujte, zda je spuštěna registru zadáním příkazu `docker run -d -p 5000:5000 --restart=always --name registry registry:2` v okně konzoly.

2. V okně VS kód načte pracovního prostoru řešení IoT okraj. Je `modules` složku, `.vscode` složku a nasazení manifest souboru šablony v kořenové složce. Zobrazí se konfigurace v ladění `.vscode` složky. Všechny kódy modulu uživatele bude podsložky ve složce `modules`. `deployment.template.json` Je manifestu šablony nasazení. Některé parametry v tomto souboru se získá analýzou z `module.json`, které existuje ve složce každý modul.

3. Druhý modul přidáte do tohoto projektu řešení. Zadejte tento čas a spusťte **Edge: Přidání okraj IoT modulu** a vyberte soubor šablony nasazení aktualizace. Potom vyberte **funkce Azure - C#** s názvem **SampleFunction** a jeho úložiště bitové kopie Docker přidat.

4. Řešení IoT Edge první dva základní moduly je nyní připraven. Výchozí C# modul funguje jako modul kanálu zpráva při Funtion C# funguje jako funkce zpráva kanálu. V `deployment.template.json`, zobrazí se, toto řešení obsahuje tři moduly. Zpráva se budou generovat z `tempSensor` modul a bude přímo přesměruje prostřednictvím `SampleModule` a `SampleFunction`, pak se odešle do služby IoT hub. Aktualizace tras pro tyto moduly se pod obsahu. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Uložte tento soubor.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Vytváření a nasazování řešení IoT Edge
1. V příkazu palety VS Code, zadejte a spusťte příkaz **Edge: sestavení IoT řešení**. Na základě `module.json` zkontroluje a začít vytvářet, containerize a push každé bitové kopie modulu docker v souboru ve složce každý modul, tento příkaz. Pak ji bude analyzovat požadovaná hodnota k `deployment.template.json`, generovat `deployment.json` se skutečnou hodnotou pod `config` složky. Zobrazí se průběh sestavení v terminálu integrované VS Code.

2. V Průzkumníku zařízení Azure IoT Hub, klikněte pravým tlačítkem na ID zařízení IoT okraj a pak vyberte **vytvořit nasazení pro hraniční zařízení**. Vyberte `deployment.json` pod `config` složky. Pak uvidíte, že nasazení je úspěšně vytvořen s nasazením, které ID v produktu VS Code integrované terminálu.

3. Pokud jste [simulaci IoT hraniční zařízení](tutorial-simulate-device-linux.md) na vývojovém počítači. Zobrazí se, že všechny kontejnery image modul bude spuštěn za pár minut.

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

1. Pokud chcete monitorovat data přicházející do centra IoT, vyberte **Zobrazit** > **Paleta příkazů...** a vyhledejte **IoT: Spustit monitorování zpráv typu zařízení-cloud**. 
2. Pokud chcete monitorování dat zastavit, použijte příkaz **IoT: Zastavit monitorování zpráv typu zařízení-cloud** na paletě příkazů. 

## <a name="next-steps"></a>Další postup

Můžete pokračovat na jednom z následujících článků Další informace o další scénáře při vývoji Azure IoT hrany v Visual Studio Code:

* [Ladění modulu jazyka C# v produktu VS Code](how-to-vscode-debug-csharp-module.md)
* [Ladění funkce jazyka C# v produktu VS Code](how-to-vscode-debug-azure-function.md)