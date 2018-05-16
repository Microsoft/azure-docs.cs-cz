---
title: Rychlý start Azure IoT Edge + Windows | Microsoft Docs
description: Vyzkoušení služby Azure IoT Edge spuštěním analýzy na simulované hraniční zařízení
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 888f74d215956f4ad38605ca247f681da700a787
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Rychlý úvod: Nasazení první modul IoT Edge na portálu Azure do zařízení se systémem Windows – náhled

V tento rychlý start použijte k nasazení předem kód vzdáleně na IoT hraniční zařízení rozhraní cloudu Azure IoT okraj. K provedení této úlohy, nejprve použijte k simulaci IoT hraniční zařízení zařízení s Windows, potom můžete nasadit modul do ní.

Pokud nemáte aktivní předplatné Azure, vytvořte [bezplatný účet] [ lnk-account] před zahájením.

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že používáte počítač nebo virtuální počítač se systémem Windows k simulaci zařízení s Internet věcí. Pokud používáte systém Windows ve virtuálním počítači, povolte [vnořené virtualizace] [ lnk-nested] a přidělit nejméně 2 GB paměti. 

1. Ujistěte se, že používáte podporovanou verzi systému Windows:
   * Windows 10 
   * Windows Server
2. Nainstalujte [Docker pro systém Windows] [ lnk-docker] a ujistěte se, že je spuštěna.
3. Nainstalujte [Python v systému Windows] [ lnk-python] a ujistěte se, můžete použít příkaz pip. Tento rychlý start byla testována s verzí Python > = 2.7.9 a > = 3.5.4.  
4. Spusťte následující příkaz se stáhnout skript řízení IoT okraj.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge můžete spustit buď kontejnery Windows nebo Linux kontejnerů. Chcete-li použít Windows kontejnery, budete muset spustit:
>    * Windows 10 patří Creators aktualizovat, nebo
>    * Windows Server. 1709 (sestavení 16299), nebo
>    * Jádro IoT Windows (sestavení 16299) na zařízení na x64
>
> Jádro IoT Windows, postupujte podle pokynů v [instalaci modulu runtime IoT Edge na jádro IoT Windows][lnk-install-iotcore]. Jinak jednoduše [konfigurace Docker používat Windows kontejnery][lnk-docker-containers]a volitelně ověřit vaše požadavky pomocí následujícího příkazu prostředí powershell:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Vytvoření služby IoT hub pomocí rozhraní příkazového řádku Azure

Vytvoření služby IoT hub ve vašem předplatném Azure. Bezplatná úroveň služby IoT Hub funguje pro tento rychlý start. Pokud jste dříve používali IoT Hub a už máte bezplatné centrum vytvořit, můžete tuto část přeskočte a přejděte na [zaregistrovat IoT hraniční zařízení][anchor-register]. Každý odběr může mít pouze jeden bezplatné Centrum IoT. 

1. Přihlaste se na web [Azure Portal][lnk-portal]. 
1. Vyberte **cloudové prostředí** tlačítko. 

   ![Tlačítko prostředí cloudu][1]

1. Vytvořte skupinu prostředků. Následující kód vytvoří skupinu prostředků s názvem **IoTEdge** v **západní USA** oblasti:

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. Vytvoření služby IoT hub v nové skupiny prostředků. Následující kód vytvoří bezplatný **F1** rozbočovače názvem **MyIotHub** ve skupině prostředků **IoTEdge**:

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Zaregistrovat IoT hraniční zařízení

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>Konfigurace modulu runtime IoT Edge

Modul runtime IoT okraj je nasadit na všechna zařízení IoT okraj. Obsahuje dva moduly. Nejprve agenta IoT Edge usnadňuje nasazení a monitorování modulů na IoT hraniční zařízení. Druhý centra IoT Edge spravuje komunikaci mezi modulů na IoT hraniční zařízení a mezi zařízením a IoT Hub. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

V tento rychlý start vytvořit nové zařízení IoT okraj a na něm nainstalován modul runtime IoT okraj. Potom použít portál Azure k modul IoT okraj ke spuštění na zařízení bez nutnosti měnit samotné zařízení. V takovém případě modul, který jste nabídnutých vytvoří prostředí data, která můžete použít pro kurzů k. 

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
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat simulovaného zařízení, kterou jste vytvořili, společně s Docker kontejnerů, které byly spuštěny pro každý modul, použijte následující příkaz: 

```cmd
iotedgectl uninstall
```

Pokud již nepotřebujete službu IoT Hub, který jste vytvořili, můžete použít [az iot hub odstranění] [ lnk-delete] příkaz k odebrání prostředek a veškerá zařízení s ním spojená:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>Další postup

Jste zjistili, jak nasadit modul IoT okraj do IoT hraniční zařízení. Nyní zkuste nasazení různých typů služeb Azure jako modulů, takže je můžete analyzovat data na hranici. 

* [Nasazení funkce Azure jako modul](tutorial-deploy-function.md)
* [Nasazení služby Azure Stream Analytics jako modulu](tutorial-deploy-stream-analytics.md)
* [Nasadit vlastní kód jako modul](tutorial-csharp-module.md)


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
