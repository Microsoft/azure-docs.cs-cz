---
title: Simulovat Azure IoT Edge v systému Linux | Microsoft Docs
description: Instalace modulu runtime Azure IoT Edge v simulovaném zařízení v systému Linux a nasazení první modul
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b7f0aa514cfffb8fb2ca76238a6a65a8e6443e56
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Nasazení Azure IoT Edge v simulovaném zařízení v systému Linux nebo systému MacOS – náhled

Azure IoT Edge umožňuje provádět analýzy a zpracování dat v zařízeních, aniž by museli push všechna data do cloudu. Kurzy IoT Edge ukazují, jak nasadit různé typy modulů, ze služby Azure nebo vlastní kód, ale nejdřív je potřeba zařízení, které chcete otestovat. 

V tomto kurzu se naučíte:

1. Vytvoření služby IoT Hub
2. Zaregistrovat IoT hraniční zařízení
3. Spusťte modul runtime IoT Edge
4. Nasazení modulu

![Kurz architektura][2]

Simulovaná zařízení, které vytvoříte v tomto kurzu je monitorování, které generuje teploty a vlhkosti a přetížení data. Ostatní kurzy Azure IoT Edge stavět na práci, kterou se zde provádí nasazení moduly, které analyzovat data podnikových statistik. 

## <a name="prerequisites"></a>Požadavky

Tento kurz používá svůj počítač nebo virtuální počítač jako zařízení s Internet věcí. Chcete-li váš počítač do IoT hraniční zařízení, jsou požadované následující služby:

* Python pip nainstalovat modul runtime IoT okraj.
   * Linux: `sudo apt-get install python-pip`.
     * _Všimněte si, že se na některých distribuce (například Raspbian), budete pravděpodobně potřebovat určité pip balíčky s upgradem a nainstalovat další závislosti:_
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Docker spouštět hraniční IoT moduly
   * [Instalace Docker pro Linux] [ lnk-docker-ubuntu] a ujistěte se, zda je spuštěna. 
   * [Nainstalovat pro systém Mac Docker] [ lnk-docker-mac] a ujistěte se, zda je spuštěna. 

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Tento kurz začněte vytvořením služby IoT Hub.
![Vytvoření centra IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Zaregistrovat IoT hraniční zařízení

Zaregistrujte IoT hraniční zařízení se nově vytvořená IoT Hub.
![Registrovat zařízení][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Instalace a spuštění modulu runtime Azure IoT Edge ve vašem zařízení. 
![Registrovat zařízení][5]

Modul runtime IoT okraj je nasadit na všechna zařízení IoT okraj. Obsahuje dva moduly. **IoT Edge agenta** usnadňuje nasazení a monitorování modulů na IoT hraniční zařízení. **IoT Edge hub** spravuje komunikaci mezi modulů na IoT hraniční zařízení a mezi zařízením a IoT Hub. Pokud nakonfigurujete modul runtime na nové zařízení, pouze IoT Edge agenta se spustí na první. Centra IoT okraj dodává později při nasazení modul. 

Na počítači, kde je potřeba spustit IoT hraniční zařízení Stáhněte skript řízení hraniční IoT:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Konfigurace modulu runtime připojovacím řetězcem IoT hraniční zařízení z předchozí části:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Spusťte modul runtime:
```cmd
sudo iotedgectl start
```

Zkontrolujte Docker, zda je jako modul spuštěn agent IoT Edge:
```cmd
sudo docker ps
```

![V tématu edgeAgent v Docker](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte vaše zařízení Azure IoT Edge z cloudu do nasazení modul, který bude posílat telemetrická data do služby IoT Hub.
![Registrovat zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto kurzu jste vytvořili nový IoT hraniční zařízení a na něm nainstalován modul runtime IoT okraj. Potom použít portál Azure k modul IoT okraj ke spuštění na zařízení bez nutnosti měnit samotné zařízení. V takovém případě modul, který jste nabídnutých vytvoří prostředí data, která můžete použít pro kurzů k. 

Otevřete příkazový řádek v počítači se systémem simulovaného zařízení znovu. Ověřte, že modul nasazení z cloudu běží na IoT hraniční zařízení:

```cmd
sudo docker ps
```

![Zobrazit tři modulů ve vašem zařízení](./media/tutorial-simulate-device-linux/docker-ps2.png)

Zobrazení zpráv odesílány z modulu tempSensor do cloudu:

```cmd
sudo docker logs -f tempSensor
```

![Zobrazení dat z modulu](./media/tutorial-simulate-device-linux/docker-logs.png)

Můžete také zobrazit telemetrii zařízení odesílá pomocí [nástroji Průzkumník služby IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili nové zařízení IoT okraj a použít rozhraní cloudu Azure IoT Edge k nasazení kódu do zařízení. Nyní máte simulované zařízení generování nezpracovaná data o jeho prostředí. 

V tomto kurzu je předpokladem pro všechny ostatní kurzy IoT okraj. Můžete pokračovat na všechny ostatní kurzy se dozvíte, jak Azure IoT Edge můžete zapnout tato data do podnikových statistik na hranici.

> [!div class="nextstepaction"]
> [Vývoj a nasazení kódu C# jako modul](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
