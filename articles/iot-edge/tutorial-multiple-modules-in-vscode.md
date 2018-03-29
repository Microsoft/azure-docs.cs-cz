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
ms.openlocfilehash: 0ea2dc723c674e7119b6ef38771a73ff4c11e98d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Vývoj IoT řešení s více modulů v aplikaci Visual Studio Code – náhled
Visual Studio Code můžete použít k vývoji řešení IoT Edge s více modulů. Tento kurz vás provede vytváření, aktualizaci a nasazení řešení IoT Edge, jednoduše prostřednictvím kanálu dat snímačů v simulované zařízení IoT Edge v kódu Visual Studio. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použít k vytvoření IoT řešení Visual Studio Code
> * Slouží k přidání nového modulu pracujete VS Code řešení IoT. 
> * Nasazení řešení IoT okraj (více modulů) do zařízení IoT Edge
> * Vygeneruje zobrazení dat

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

## <a name="view-generated-data"></a>Vygeneruje zobrazení dat

1. Chcete-li monitorovat dat odesílaných do služby IoT hub, vyberte **zobrazení** > **příkaz palety...**  a vyhledejte **IoT: spuštění monitorování D2C zpráva**. 
2. Chcete-li zastavit monitorování dat, použijte **IoT: zastavení monitorování D2C zpráva** v příkazu palety. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili řešení IoT pomocí jazyka C# modul, později přidat modul funkce, aktualizovat trasy pro řešení, vytvořené a nasazené simulovaného zařízení IoT okraj. Můžete pokračovat na některý z následujících kurzech Další informace o další scénáře při vývoji Azure IoT Edge v produktu VS Code.

> [!div class="nextstepaction"]
> [Ladění modulu jazyka C# v produktu VS Code](how-to-vscode-debug-csharp-module.md)
> [ladění funkce jazyka C# v produktu VS Code](how-to-vscode-debug-azure-function.md)