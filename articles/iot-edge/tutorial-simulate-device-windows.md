---
title: Simulace Azure IoT Edge ve Windows | Microsoft Docs
description: Instalace modulu runtime Azure IoT Edge na simulované zařízení ve Windows a nasazení prvního modulu
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 7ad99a49a578de4997a2d76d48da33aba6847f3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631186"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Nasazení Azure IoT Edge na simulované zařízení ve Windows – Preview

Azure IoT Edge umožňuje na zařízení analyzovat a zpracovávat data, abyste je nemuseli všechna odesílat do cloudu. V kurzech o IoT Edge si ukážeme, jak nasadit různé typy modulů vytvořené ze služeb Azure nebo z vlastního kódu, ale napřed potřebujete testovací zařízení. 

V tomto kurzu se naučíte:

1. Vytvořit IoT Hub
2. Zaregistrovat zařízení IoT Edge
3. Spustit modul runtime IoT Edge
4. Nasadit modul

![Architektura kurzu][2]

Simulované zařízení, které v tomto kurzu vytvoříte, je monitor větrné turbíny, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Tyto údaje jsou pro vás důležité, protože výkon turbín se liší v závislosti na povětrnostních podmínkách. Další kurzy Azure IoT Edge vycházejí z práce v tomto kurzu, ve kterém nasadíte moduly, které analyzují data kvůli získání obchodních informací. 

## <a name="prerequisites"></a>Požadavky

Předpokladem tohoto kurzu je, že k simulaci zařízení IoT (Internet of Things) používáte počítač nebo virtuální počítač s Windows. 

>[!TIP]
>Pokud na virtuálním počítači běží Windows, povolte [vnořenou virtualizaci][lnk-nested] a přidělte alespoň 2 GB paměti. 

1. Zkontrolujte, že používáte podporovanou verzi Windows:
   * Windows 10 
   * Windows Server
2. Nainstalujte [Docker for Windows][lnk-docker] a zkontrolujte, že běží.
3. Nainstalujte [Python pro Windows][lnk-python] a ověřte, že můžete použít příkaz pip. Tento kurz byl testován s verzemi Pythonu >=2.7.9 a >=3.5.4.  
4. Pokud si chcete stáhnout ovládací skript IoT Edge, spusťte následující příkaz.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> V Azure IoT Edge můžou běžet kontejnery Windows nebo Linux. Pokud používáte některou z následujících verzí Windows, můžete použít kontejnery Windows:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (build 16299)
>    * Windows IoT Core (build 16299) na zařízení s platformou x64
>
> Pokud používáte Windows IoT Core, postupujte podle pokynů v článku o [instalaci modulu runtime IoT Edge v systému Windows IoT Core][lnk-install-iotcore]. V ostatních případech jednoduše [nakonfigurujte Docker na použití kontejnerů Windows][lnk-docker-containers]. K ověření požadavků použijte následující příkaz:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Na začátku kurzu vytvořte IoT Hub.
![Vytvoření IoT Hubu][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného IoT Hubu.
![Registrace zařízení][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Nainstalujte na zařízení modul runtime Azure IoT Edge a spusťte ho. 
![Registrace zařízení][5]

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze dvou modulů. Prvním je **agent IoT Edge**, který umožňuje nasadit na zařízení IoT Edge moduly a monitorovat je. Druhým je **IoT Edge Hub**, který na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem. Když na novém zařízení nakonfigurujete modul runtime, spustí se napřed jenom agent IoT Edge. IoT Edge Hub se spustí později, až nasadíte modul. 


Nakonfigurujte v modulu runtime připojovací řetězec zařízení IoT Edge z předchozí části.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Spusťte modul runtime.

```cmd
iotedgectl start
```

Podívejte se v Dockeru, jestli agent IoT Edge běží jako modul.

```cmd
docker ps
```

![Agent edgeAgent v Dockeru](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Nasazení modulu

Zařízení Azure IoT Edge spravujte v cloudu, abyste mohli nasadit modul, který bude posílat telemetrická data IoT Hubu.
![Registrace zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto kurzu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k doručení modulu IoT Edge a jeho spuštění na zařízení, aniž byste museli měnit samotné zařízení. V tomto případě doručený modul vytvoří data prostředí, která můžete použít pro tyto kurzy. 

Na počítači, na kterém běží simulované zařízení, znovu otevřete příkazový řádek. Zkontrolujte, že na zařízení IoT Edge běží modul, který jste nasadili z cloudu. 

```cmd
docker ps
```

![Zobrazení tří modulů na zařízení](./media/tutorial-simulate-device-windows/docker-ps2.png)

Prohlédněte si zprávy, které posílá modul tempSensor do cloudu. 

```cmd
docker logs -f tempSensor
```

![Zobrazení dat z modulu](./media/tutorial-simulate-device-windows/docker-logs.png)

Telemetrická data, která odesílá zařízení, si můžete prohlédnout v [nástroji IoT Hub Explorer][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili nové zařízení IoT Edge a použili jste cloudové rozhraní Azure IoT Edge k nasazení kódu do zařízení. Teď máte simulované zařízení, které generuje nezpracovaná data o svém prostředí. 

Tento kurz je předpokladem všech dalších kurzů o IoT Edge. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Vývoj a nasazení kódu v jazyce C# jako modulu](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md