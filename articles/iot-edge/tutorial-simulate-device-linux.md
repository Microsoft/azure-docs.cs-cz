---
title: Simulace Azure IoT Edge v Linuxu | Microsoft Docs
description: Instalace modulu runtime Azure IoT Edge na simulované zařízení v Linuxu a nasazení prvního modulu
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 0b8b2658af9173cea6a7cdcb0147c7b0dc13a455
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630992"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Nasazení Azure IoT Edge na simulované zařízení v Linuxu nebo MacOS – Preview

Azure IoT Edge umožňuje na zařízení analyzovat a zpracovávat data, abyste je nemuseli všechna odesílat do cloudu. V kurzech o IoT Edge si ukážeme, jak nasadit různé typy modulů vytvořené ze služeb Azure nebo z vlastního kódu, ale napřed potřebujete testovací zařízení. 

V tomto kurzu se naučíte:

1. Vytvořit IoT Hub
2. Zaregistrovat zařízení IoT Edge
3. Spustit modul runtime IoT Edge
4. Nasadit modul

![Architektura kurzu][2]

Simulované zařízení, které v tomto kurzu vytvoříte, je monitor, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy Azure IoT Edge vycházejí z práce v tomto kurzu, ve kterém nasadíte moduly, které analyzují data kvůli získání obchodních informací. 

## <a name="prerequisites"></a>Požadavky

K tomuto kurzu potřebujete počítač nebo virtuální počítač, jako je zařízení IoT. Pokud chcete ze svého počítače vytvořit zařízení IoT Edge, musí na něm být následující služby:

* Balíček Python pip pro instalaci modulu runtime IoT Edge.
   * Linux: `sudo apt-get install python-pip`.
     * _U některých distribucí (např. Raspbian) možná budete muset také upgradovat některé balíčky pip a nainstalovat další závislosti:_
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`.
* Docker ke spouštění modulů IoT Edge.
   * [Nainstalujte Docker pro Linux][lnk-docker-ubuntu] a zkontrolujte, že běží. 
   * [Nainstalujte Docker pro Mac][lnk-docker-mac] a zkontrolujte, že běží. 

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Na začátku kurzu vytvořte IoT Hub.
![Vytvoření IoT Hubu][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného IoT Hubu.
![Registrace zařízení][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na zařízení modul runtime Azure IoT Edge a spusťte ho. 
![Registrace zařízení][5]

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze dvou modulů. Prvním je **agent IoT Edge**, který umožňuje nasadit na zařízení IoT Edge moduly a monitorovat je. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem. Když na novém zařízení nakonfigurujete modul runtime, spustí se napřed jenom agent IoT Edge. IoT Edge Hub se spustí později, až nasadíte modul. 

Do počítače, kde budete spouštět zařízení IoT Edge, stáhněte ovládací skript IoT Edge:
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

Ke konfiguraci modulu runtime použijte připojovací řetězec zařízení IoT Edge z předchozí části:
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

Spusťte modul runtime:
```cmd
sudo iotedgectl start
```

Podívejte se v Dockeru, jestli agent IoT Edge běží jako modul:
```cmd
sudo docker ps
```

![Agent edgeAgent v Dockeru](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>Nasazení modulu

Zařízení Azure IoT Edge spravujte v cloudu, abyste mohli nasadit modul, který bude posílat telemetrická data IoT Hubu.
![Registrace zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto kurzu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k doručení a spuštění modulu IoT Edge na zařízení, aniž byste museli měnit samotné zařízení. V tomto případě doručený modul vytvoří data o prostředí, která použijete pro tyto kurzy. 

Na počítači, na kterém běží simulované zařízení, znovu otevřete příkazový řádek. Zkontrolujte, že modul, který jste nasadili z cloudu, běží na zařízení IoT Edge:

```cmd
sudo docker ps
```

![Zobrazení tří modulů na zařízení](./media/tutorial-simulate-device-linux/docker-ps2.png)

Prohlédněte si zprávy, které posílá modul tempSensor do cloudu:

```cmd
sudo docker logs -f tempSensor
```

![Zobrazení dat z modulu](./media/tutorial-simulate-device-linux/docker-logs.png)

Telemetrická data, která odesílá zařízení, si můžete prohlédnout v [nástroji IoT Hub Explorer][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili nové zařízení IoT Edge a použili jste cloudové rozhraní Azure IoT Edge k nasazení kódu do zařízení. Teď máte simulované zařízení, které generuje nezpracovaná data o prostředí. 

Tento kurz je předpokladem všech dalších kurzů o IoT Edge. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Vývoj a nasazení kódu v jazyce C# jako modulu](tutorial-csharp-module.md)


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
