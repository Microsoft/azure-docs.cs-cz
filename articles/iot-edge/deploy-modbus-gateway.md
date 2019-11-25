---
title: Translate modbus protocols with gateways - Azure IoT Edge | Microsoft Docs
description: Jak umožnit zařízením s protokolem Modbus TCP komunikaci s Azure IoT Hub vytvořením zařízení s bránou IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457213"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connect Modbus TCP devices through an IoT Edge device gateway

Pokud chcete připojit zařízení IoT využívající protokol Modbus TCP ke službě Azure IoT Hub, můžete jako bránu použít zařízení IoT Edge. Zařízení brány čte data ze zařízení Modbus a předává je do cloudu s použitím podporovaného protokolu.

![Modbus devices connect to IoT Hub through IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Tento článek popisuje vytvoření vlastní bitové kopie kontejneru pro modul Modbus (nebo můžete použít připravený vzor) a jeho nasazení do zařízení IoT Edge, které bude sloužit jako brána.

V tomto článku se předpokládá, že používáte protokol Modbus TCP. For more information about how to configure the module to support Modbus RTU, see the [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub.

## <a name="prerequisites"></a>Předpoklady
* Zařízení Azure IoT Edge. For a walkthrough on how to set up one, see [Deploy Azure IoT Edge on Windows](quickstart.md) or [Linux](quickstart-linux.md).
* Primární připojovací řetězec klíče pro zařízení IoT Edge.
* Fyzické nebo simulované zařízení Modbus, které podporuje protokol Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Příprava kontejneru Modbus

Chcete-li otestovat funkci brány Modbus, Microsoft nabízí jednoduchý modul, který můžete použít. You can access the module from the Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), or with the image URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

If you want to create your own module and customize it for your environment, there is an open-source [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) project on GitHub. Podle pokynů uvedených v projektu na GitHubu vytvořte vlastní bitovou kopii kontejneru. To create a container image, refer to [Develop C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md) or [Develop modules in Visual Studio Code](how-to-vs-code-develop-module.md). Those articles provide instructions on creating new modules and publishing container images to a registry.

## <a name="try-the-solution"></a>Try the solution

This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT Hub.

2. Go to **IoT Edge** and click on your IoT Edge device.

3. Vyberte **Nastavit moduly**.

4. Přidejte modul Modbus:

   1. Click **Add** and select **IoT Edge module**.

   2. Do pole **Name** (Název) zadejte „modbus“.

   3. Do pole **Image** (Bitová kopie) zadejte identifikátor URI vzorového kontejneru: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Zaškrtnutím políčka **Enable** (Povolit) aktualizujte požadované vlastnosti dvojčete modulu.

   5. Do textového pole zkopírujte následující JSON. Změňte hodnotu **SlaveConnection** na adresu IPv4 vašeho zařízení Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Vyberte **Save** (Uložit).

5. Zpět v kroku **přidání modulů** vyberte **Další**.

7. V kroku **určení tras** zkopírujte do textového pole následující JSON. Tato trasa odešle všechny zprávy shromážděné modulem Modbus do centra IoT Hub. In this route, **modbusOutput** is the endpoint that Modbus module uses to output data and **$upstream** is a special destination that tells IoT Edge hub to send messages to IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Vyberte **Další**.

9. V kroku **Review Deployment** (Kontrola nasazení) vyberte **Submit** (Odeslat).

10. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). You should see the new **modbus** module running along with the IoT Edge runtime.

## <a name="view-data"></a>Zobrazení dat
Takto si můžete prohlédnout data přicházející z modulu modbus:
```cmd/sh
iotedge logs modbus
```

You can also view the telemetry the device is sending by using the [Azure IoT Hub Toolkit extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (formerly Azure IoT Toolkit extension).

## <a name="next-steps"></a>Další kroky

- To learn more about how IoT Edge devices can act as gateways, see [Create an IoT Edge device that acts as a transparent gateway](./how-to-create-transparent-gateway.md).
- For more information about how IoT Edge modules work, see [Understand Azure IoT Edge modules](iot-edge-modules.md).
