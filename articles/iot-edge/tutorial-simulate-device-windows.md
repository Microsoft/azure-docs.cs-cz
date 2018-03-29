---
title: Simulovat Azure IoT Edge v systému Windows | Microsoft Docs
description: Instalace modulu runtime Azure IoT Edge v simulovaném zařízení v systému Windows a nasazení první modul
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ae974162a460289a34443879a9e78224684d94ed
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Nasazení v simulovaném zařízení v systému Windows Azure IoT Edge – náhled

Azure IoT Edge umožňuje provádět analýzy a zpracování dat v zařízeních, aniž by museli push všechna data do cloudu. Kurzy IoT Edge ukazují, jak nasadit různé typy modulů, ze služby Azure nebo vlastní kód, ale nejdřív je potřeba zařízení, které chcete otestovat. 

V tomto kurzu se naučíte:

1. Vytvoření služby IoT Hub
2. Zaregistrovat IoT hraniční zařízení
3. Spusťte modul runtime IoT Edge
4. Nasazení modulu

![Kurz architektura][2]

Simulovaná zařízení, které vytvoříte v tomto kurzu je monitorování turbínu větru, který generuje teploty a vlhkosti a přetížení data. Co vás zajímá tato data, protože vaše turbín provést na různých úrovních efektivitu v závislosti na počasí. Ostatní kurzy Azure IoT Edge stavět na práci, kterou se zde provádí nasazení moduly, které analyzovat data podnikových statistik. 

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows k simulaci zařízení s Internet věcí. 

>[!TIP]
>Pokud používáte systém Windows ve virtuálním počítači, povolte [vnořené virtualizace] [ lnk-nested] a přidělit nejméně 2 GB paměti. 

1. Ujistěte se, že používáte podporovanou verzi systému Windows:
   * Windows 10 
   * Windows Server
2. Nainstalujte [Docker pro systém Windows] [ lnk-docker] a ujistěte se, že je spuštěna.
3. Nainstalujte [Python 2.7 v systému Windows] [ lnk-python] a ujistěte se, můžete použít příkaz pip.
4. Spusťte následující příkaz se stáhnout skript řízení IoT okraj.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge můžete spustit buď kontejnery Windows nebo Linux kontejnerů. Pokud používáte jednu z následujících verzí systému Windows, můžete použít Windows kontejnerů:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (Build 16299)
>    * Jádro IoT Windows (sestavení 16299) na zařízení na x64
>
> Jádro IoT Windows, postupujte podle pokynů v [instalaci modulu runtime IoT Edge na jádro IoT Windows][lnk-install-iotcore]. Jinak jednoduše [konfigurace Docker používat Windows kontejnery][lnk-docker-containers]. Použijte následující příkaz k ověření vaší požadavky:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

Tento kurz začněte vytvořením služby IoT Hub.
![Vytvoření centra IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Zaregistrovat IoT hraniční zařízení

Zaregistrujte IoT hraniční zařízení se nově vytvořená IoT Hub.
![Registrovat zařízení][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Instalace a spuštění modulu runtime Azure IoT Edge ve vašem zařízení. 
![Registrovat zařízení][5]

Modul runtime IoT okraj je nasadit na všechna zařízení IoT okraj. Obsahuje dva moduly. **IoT Edge agenta** usnadňuje nasazení a monitorování modulů na IoT hraniční zařízení. **IoT Edge hub** spravuje komunikaci mezi modulů na IoT hraniční zařízení a mezi zařízením a IoT Hub. Pokud nakonfigurujete modul runtime na nové zařízení, pouze IoT Edge agenta se spustí na první. Centra IoT okraj dodává později při nasazení modul. 


Konfigurace modulu runtime připojovacím řetězcem IoT hraniční zařízení z předchozí části.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

Spusťte modul runtime.

```cmd
iotedgectl start
```

Zkontrolujte, zda IoT Edge agenta je spuštěn jako modul Docker.

```cmd
docker ps
```

![V tématu edgeAgent v Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>Nasazení modulu

Spravujte vaše zařízení Azure IoT Edge z cloudu do nasazení modul, který bude posílat telemetrická data do služby IoT Hub.
![Registrovat zařízení][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>Vygeneruje zobrazení dat

V tomto kurzu jste vytvořili nový IoT hraniční zařízení a na něm nainstalován modul runtime IoT okraj. Potom použít portál Azure k modul IoT okraj ke spuštění na zařízení bez nutnosti měnit samotné zařízení. V takovém případě modul, který jste nabídnutých vytvoří prostředí data, která můžete použít pro kurzů k. 

Otevřete příkazový řádek v počítači se systémem simulovaného zařízení znovu. Potvrďte, že modul nasazení z cloudu běží na vašem zařízení IoT hraniční. 

```cmd
docker ps
```

![Zobrazit tři modulů ve vašem zařízení](./media/tutorial-simulate-device-windows/docker-ps2.png)

Zobrazení zpráv odesílány z modulu tempSensor do cloudu. 

```cmd
docker logs -f tempSensor
```

![Zobrazení dat z modulu](./media/tutorial-simulate-device-windows/docker-logs.png)

Můžete také zobrazit telemetrii zařízení odesílá pomocí [nástroji Průzkumník služby IoT Hub][lnk-iothub-explorer]. 

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili nové zařízení IoT okraj a použít rozhraní cloudu Azure IoT Edge k nasazení kódu do zařízení. Nyní máte simulované zařízení generování nezpracovaná data o jeho prostředí. 

V tomto kurzu je předpokladem pro všechny ostatní kurzy IoT okraj. Můžete pokračovat na všechny ostatní kurzy se dozvíte, jak Azure IoT Edge můžete zapnout tato data do podnikových statistik na hranici.

> [!div class="nextstepaction"]
> [Vývoj a nasazení kódu C# jako modul](tutorial-csharp-module.md)

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