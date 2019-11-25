---
title: Quickstart create an Azure IoT Edge device on Windows | Microsoft Docs
description: In this quickstart, learn how to create an IoT Edge device and then deploy prebuilt code remotely from the Azure portal.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6d84c2eed6e68987af3ce932785068191405b942
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452568"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-windows-device"></a>Quickstart: Deploy your first IoT Edge module to a virtual Windows device

Test out Azure IoT Edge in this quickstart by deploying containerized code to a virtual IoT Edge device. IoT Edge allows you to remotely manage code on your devices so that you can send more of your workloads to the edge. For this quickstart, we recommend using an Azure virtual machine for your IoT Edge device, which allows you to quickly create a test machine, install the prerequisites, and then delete it when you're finished. 

V tomto rychlém startu se naučíte:

1. Create an IoT hub.
2. Zaregistrovat zařízení IoT Edge do centra IoT Hub.
3. Install and start the IoT Edge runtime on your virtual device.
4. Vzdáleně nasadit modul na zařízení IoT Edge a odeslat telemetrická data do služby IoT Hub.

![Diagram - Quickstart architecture for device and cloud](./media/quickstart/install-edge-full.png)

This quickstart walks you through creating a Windows virtual machine and configuring it to be IoT Edge device. Potom můžete modul nasadit z webu Azure Portal do svého zařízení. Modul, který v tomto rychlém kurzu nasadíte, je simulovaný snímač, který generuje údaje o teplotě, vlhkosti a atmosferickém tlaku. Další kurzy o Azure IoT Edge vycházejí z tohoto kurzu. V něm nasadíte moduly, které analyzují simulovaná data kvůli získání obchodních informací.

Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

K dokončení řady kroků v tomto rychlém startu použijete Azure CLI. Azure IoT má rozšíření, které nabízí další funkce.

Přidejte rozšíření Azure IoT do instance služby Cloud Shell.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Předpoklady

Cloudové prostředky:

* Skupina prostředků pro správu všech prostředků, které v tomto rychlém startu použijete.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Zařízení IoT Edge:

* A Windows virtual machine to act as your IoT Edge device. You can create this virtual machine using the following command, replacing *{password}* with a secure password:

  ```azurecli-interactive
  az vm create --resource-group IoTEdgeResources --name EdgeVM --image MicrosoftWindowsDesktop:Windows-10:rs5-pro:latest --admin-username azureuser --admin-password {password} --size Standard_DS1_v2
  ```

  It may take a few minutes to create and start the new virtual machine. You can then download an RDP file for use when connecting to your virtual machine:

  1. Navigate to your new Windows virtual machine in the Azure portal.
  1. Vyberte **Connect** (Připojit).
  1. On the **RDP** tab, select **Download RDP File**.

  Open this file with Remote Desktop Connection to connect to your Windows virtual machine using the administrator name and password you specified with the `az vm create` command.


> [!NOTE]
> This quickstart uses a Windows desktop virtual machine for simplicity. For information about which Windows operating systems are generally available for production scenarios, see [Azure IoT Edge supported systems](support.md).
>
> If you're ready to configure your own Windows device for IoT Edge, including devices running IoT Core, follow the steps in [Install the Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

## <a name="create-an-iot-hub"></a>Vytvoření IoT Hubu

Start the quickstart by creating an IoT hub with Azure CLI.

![Diagram - Create an IoT hub in the cloud](./media/quickstart/create-iot-hub.png)

Pro tento rychlý start můžete použít bezplatnou úroveň IoT Hubu. Pokud jste službu IoT Hub někdy používali a máte vytvořené bezplatné centrum IoT, můžete ho použít. V každém předplatném může být jenom jeden bezplatný IoT Hub.

Následující kód vytvoří bezplatné centrum **F1** ve skupině prostředků **IoTEdgeResources**. Nahraďte *{hub_name}* jedinečným názvem vašeho centra IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Pokud dojde k chybě kvůli tomu, že vaše předplatné již jedno bezplatné centrum obsahuje, změňte skladovou položku na **S1**. If you get an error that the IoT Hub name isn't available, it means that someone else already has a hub with that name. Try a new name.

## <a name="register-an-iot-edge-device"></a>Registrace zařízení IoT Edge

Zaregistrujte zařízení IoT Edge do nově vytvořeného centra IoT.
![Diagram - Register a device with an IoT Hub identity](./media/quickstart/register-device.png)

Vytvořte identitu simulovaného zařízení, aby mohla komunikovat s centrem IoT. Identita zařízení se uchovává v cloudu a k přidružení fyzického zařízení k identitě zařízení se používá jedinečný připojovací řetězec zařízení.

Since IoT Edge devices behave and can be managed differently than typical IoT devices, declare this identity to be for an IoT Edge device with the `--edge-enabled` flag.

1. Ve službě Azure Cloud Shell zadejte následující příkaz, kterým v centru vytvoříte zařízení **myEdgeDevice**.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

   If you get an error about iothubowner policy keys, make sure that your cloud shell is running the latest version of the azure-cli-iot-ext extension.

2. Načtěte připojovací řetězec svého zařízení, který propojí vaše fyzické zařízení s jeho identitou ve službě IoT Hub.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Copy the value of the `connectionString` key from the JSON output and save it. This value is the device connection string. You'll use this connection string to configure the IoT Edge runtime in the next section.

   ![Retrieve connection string from CLI output](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalace a spuštění modulu runtime IoT Edge

Nainstalujte na své zařízení IoT Edge modul runtime Azure IoT Edge a nakonfigurujte v něm připojovací řetězec zařízení.
![Diagram - Start the runtime on device](./media/quickstart/start-runtime.png)

Modul runtime IoT Edge se nasadí na všechna zařízení IoT Edge. Skládá se ze tří částí. The **IoT Edge security daemon** starts each time an IoT Edge device boots and bootstraps the device by starting the IoT Edge agent. The **IoT Edge agent** manages deployment and monitoring of modules on the IoT Edge device, including the IoT Edge hub. The **IoT Edge hub** handles communications between modules on the IoT Edge device, and between the device and IoT Hub.

The installation script also includes a container engine called Moby that manages the container images on your IoT Edge device.

Během instalace modulu runtime se zobrazí výzva k zadání připojovacího řetězce zařízení. Použijte řetězec, který jste získali z Azure CLI. Tento řetězec přidruží vaše fyzické zařízení k identitě zařízení IoT Edge v Azure.

### <a name="connect-to-your-iot-edge-device"></a>Connect to your IoT Edge device

The steps in this section all take place on your IoT Edge device, so you want to connect to that virtual machine now via remote desktop.

### <a name="install-and-configure-the-iot-edge-service"></a>Install and configure the IoT Edge service

Pomocí PowerShellu stáhněte a nainstalujte modul runtime IoT Edge. Ke konfiguraci svého zařízení použijte připojovací řetězec zařízení, který jste získali ze služby IoT Hub.

1. If you haven't already, follow the steps in [Register a new Azure IoT Edge device](how-to-register-device.md) to register your device and retrieve the device connection string.

2. In the virtual machine, run PowerShell as an administrator.

   >[!NOTE]
   >Use an AMD64 session of PowerShell to install IoT Edge, not PowerShell (x86). If you're not sure which session type you're using, run the following command:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, downloads the Moby runtime, and then downloads the IoT Edge runtime.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Windows
   ```

4. Your machine may restart automatically. If you are prompted by the Deploy-IoTEdge command to reboot, do so now. 

5. Run PowerShell as an administrator again.

6. The **Initialize-IoTEdge** command configures the IoT Edge runtime on your machine. The command defaults to manual provisioning with Windows containers. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Windows
   ```

7. Po zobrazení výzvy k zadání **připojovacího řetězce** zadejte řetězec, který jste si zkopírovali v předchozí části. Připojovací řetězec zadejte bez uvozovek.

### <a name="view-the-iot-edge-runtime-status"></a>Zobrazení stavu modulu runtime IoT Edge

Ověřte, že se modul runtime úspěšně nainstaloval a nakonfiguroval.

1. Zkontrolujte stav služby IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Pokud potřebujete řešit potíže se službou, načtěte protokoly služby.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

3. Zobrazte všechny moduly spuštěné na vašem zařízení IoT Edge. Vzhledem k tomu, že jde o první spuštění služby, měl by se zobrazit pouze spuštěný modul **edgeAgent**. Modul edgeAgent se spouští ve výchozím nastavení a pomáhá s instalací a spouštěním všech dalších modulů, které do zařízení nasadíte.

   ```powershell
   iotedge list
   ```

   ![Zobrazení jednoho modulu na zařízení](./media/quickstart/iotedge-list-1.png)

It may take a few minutes for the installation to complete and the IoT Edge agent module to start.

Vaše zařízení IoT Edge je teď nakonfigurované. Je připravené na spouštění modulů nasazených v cloudu.

## <a name="deploy-a-module"></a>Nasazení modulu

Manage your Azure IoT Edge device from the cloud to deploy a module that sends telemetry data to IoT Hub.
![Diagram - deploy module from cloud to device](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

In this quickstart, you registered an IoT Edge device and installed the IoT Edge runtime on it. Then, you used the Azure portal to deploy an IoT Edge module to run on the device without having to make changes to the device itself.

In this case, the module that you pushed creates sample data that you can use for testing. The simulated temperature sensor module generates environment data that you can use for testing later. The simulated sensor is monitoring both a machine and the environment around the machine. For example, this sensor might be in a server room, on a factory floor, or on a wind turbine. The message includes ambient temperature and humidity, machine temperature and pressure, and a timestamp. The IoT Edge tutorials use the data created by this module as test data for analytics.

Zkontrolujte, že na zařízení IoT Edge běží modul, který jste nasadili z cloudu.

```powershell
iotedge list
```

   ![Zobrazení tří modulů na zařízení](./media/quickstart/iotedge-list-2.png)

View the messages being sent from the temperature sensor module to the cloud.

```powershell
iotedge logs SimulatedTemperatureSensor -f
```

   >[!TIP]
   >IoT Edge commands are case-sensitive when referring to module names.

   ![Zobrazení dat z modulu](./media/quickstart/iotedge-logs.png)

You can also watch the messages arrive at your IoT hub by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat dalšími kurzy o IoT Edge, použijte zařízení, které jste zaregistrovali a nastavili v tomto rychlém startu. Otherwise, you can delete the Azure resources that you created to avoid charges.

Pokud jste virtuální počítač a centrum IoT vytvořili v nové skupině prostředků, můžete odstranit tuto skupinu a všechny související prostředky. Double check the contents of the resource group to make sure that there's nothing you want to keep. If you don't want to delete the whole group, you can delete individual resources instead.

Odeberte skupinu **IoTEdgeResources**.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Další kroky

In this quickstart, you created an IoT Edge device and used the Azure IoT Edge cloud interface to deploy code onto the device. Teď máte testovací zařízení, které generuje nezpracovaná data o prostředí.

The next step is to set up your local development environment so that you can start creating IoT Edge modules that run your business logic. 

> [!div class="nextstepaction"]
> [Start developing IoT Edge modules for Windows devices](tutorial-develop-for-windows.md)
