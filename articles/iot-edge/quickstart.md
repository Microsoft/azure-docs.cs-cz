---
title: Rychlý start pro Azure IoT Edge + Windows | Microsoft Docs
description: Vyzkoušejte Azure IoT Edge spuštěním analýzy na simulovaném zařízení Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 2fd16ab4ade61b1a08f93294051f4246e47839b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631730"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Rychlý start: Nasazení prvního modulu IoT Edge z webu Azure Portal do zařízení s Windows – Preview

V tomto rychlém startu použijeme cloudové rozhraní Azure IoT Edge ke vzdálenému nasazení předem připraveného kódu do zařízení IoT Edge. Abyste mohli tento úkol splnit, napřed použijte zařízení s Windows k simulaci zařízení IoT Edge a potom do něj nasaďte modul.

Pokud nemáte aktivní předplatné Azure, vytvořte si napřed [bezplatný účet][lnk-account].

## <a name="prerequisites"></a>Požadavky

Předpokladem tohoto kurzu je, že k simulaci zařízení IoT (Internet of Things) používáte počítač nebo virtuální počítač s Windows. Pokud na virtuálním počítači běží Windows, povolte [vnořenou virtualizaci][lnk-nested] a přidělte alespoň 2 GB paměti. 

1. Zkontrolujte, že používáte podporovanou verzi Windows:
   * Windows 10 
   * Windows Server
2. Nainstalujte [Docker for Windows][lnk-docker] a zkontrolujte, že běží.
3. Nainstalujte [Python pro Windows][lnk-python] a ověřte, že můžete použít příkaz pip. Tento rychlý start byl testovaný s verzemi Pythonu >=2.7.9 a >=3.5.4.  
4. Pokud si chcete stáhnout ovládací skript IoT Edge, spusťte následující příkaz.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> V Azure IoT Edge můžou běžet kontejnery Windows nebo Linux. Pokud chcete použít kontejnery Windows, musíte mít spuštěný systém:
>    * Windows 10 Fall Creators Update nebo
>    * Windows Server 1709 (build 16299) nebo
>    * Windows IoT Core (build 16299) na zařízení s platformou x64
>
> Pokud používáte Windows IoT Core, postupujte podle pokynů v článku o [instalaci modulu runtime IoT Edge v systému Windows IoT Core][lnk-install-iotcore]. V ostatních případech jednoduše [nakonfigurujte Docker na používání kontejnerů Windows][lnk-docker-containers] a případně také ověřte požadavky následujícím příkazem PowerShellu:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Vytvoření IoT Hubu pomocí Azure CLI

IoT Hub můžete vytvořit ve svém předplatném Azure. Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste už IoT Hub používali a máte vytvořené bezplatné centrum, můžete tuto část přeskočit a přejít k části [Registrace zařízení IoT Edge][anchor-register]. V každém předplatném může být jenom jeden bezplatný IoT Hub. 

1. Přihlaste se na web [Azure Portal][lnk-portal]. 
1. Vyberte tlačítko **Cloud Shell**. 

   ![Tlačítko Cloud Shell][1]

1. Vytvořte skupinu prostředků. Následující kód vytvoří v **západní oblasti USA** skupinu prostředků nazvanou **IoTEdge**:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. V nové skupině prostředků vytvořte IoT Hub. Následující kód vytvoří ve skupině prostředků **IoTEdge** bezplatné centrum **F1** nazvané **MyIotHub**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze dvou modulů. Prvním je agent IoT Edge, který umožňuje nasadit na zařízení IoT Edge moduly a monitorovat je. Druhým je centrum IoT Edge Hub, které na zařízení IoT Edge řídí komunikaci mezi moduly a také mezi zařízením a IoT Hubem. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tomto rychlém startu jste vytvořili nové zařízení IoT Edge a nainstalovali jste na něj modul runtime IoT Edge. Pak jste použili Azure Portal k doručení modulu IoT Edge a jeho spuštění na zařízení, aniž byste museli měnit samotné zařízení. V tomto případě doručený modul vytvoří data prostředí, která můžete použít v těchto kurzech. 

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
## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání vytvořeného simulovaného zařízení spolu s kontejnery Dockeru spuštěnými pro každý modul použijte následující příkaz: 

```cmd
iotedgectl uninstall
```

Pokud vytvořený IoT Hub nepotřebujete, odeberte ho příkazem [az iot hub delete][lnk-delete], kterým odeberete samotný prostředek i všechna přidružená zařízení:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Další kroky

Už umíte nasadit modul IoT Edge na zařízení IoT Edge. Teď zkuste nasadit různé typy služeb Azure jako moduly, abyste mohli analyzovat data hraničního zařízení. 

* [Nasazení funkce Azure Functions jako modulu](tutorial-deploy-function.md)
* [Nasazení služby Azure Stream Analytics jako modulu](tutorial-deploy-stream-analytics.md)
* [Nasazení vlastního kódu jako modulu](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
