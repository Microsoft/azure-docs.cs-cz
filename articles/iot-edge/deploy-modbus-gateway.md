---
title: Přeložit modbus protokoly s bránami – Azure IoT Edge | Dokumentace Microsoftu
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
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Připojení zařízení Modbus TCP přes zařízení brány IoT Edge

Pokud chcete připojit zařízení IoT využívající protokol Modbus TCP ke službě Azure IoT Hub, můžete jako bránu použít zařízení IoT Edge. Zařízení brány čte data ze zařízení Modbus a předává je do cloudu s použitím podporovaného protokolu.

![Zařízení Modbus se připojují k IoT Hub prostřednictvím brány IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Tento článek popisuje vytvoření vlastní bitové kopie kontejneru pro modul Modbus (nebo můžete použít připravený vzor) a jeho nasazení do zařízení IoT Edge, které bude sloužit jako brána.

V tomto článku se předpokládá, že používáte protokol Modbus TCP. Další informace o tom, jak nakonfigurovat modul pro podporu Modbus RTU, najdete v projektu [modulu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) na GitHubu.

## <a name="prerequisites"></a>Požadavky
* Zařízení Azure IoT Edge. Návod, jak ho nastavit, najdete v tématu [nasazení Azure IoT Edge v systému Windows](quickstart.md) nebo [Linux](quickstart-linux.md).
* Primární připojovací řetězec klíče pro zařízení IoT Edge.
* Fyzické nebo simulované zařízení Modbus, které podporuje protokol Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Příprava kontejneru Modbus

Chcete-li otestovat funkci brány Modbus, Microsoft nabízí jednoduchý modul, který můžete použít. K modulu můžete přistupovat z Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)nebo s identifikátorem URI image **MCR.Microsoft.com/azureiotedge/Modbus:1.0**.

Pokud chcete vytvořit vlastní modul a přizpůsobit ho pro své prostředí, na GitHubu je open source projekt [Azure IoT Edgeho modulu Modbus](https://github.com/Azure/iot-edge-modbus) . Podle pokynů uvedených v projektu na GitHubu vytvořte vlastní bitovou kopii kontejneru. Chcete-li vytvořit image kontejneru, přečtěte si téma [vývoj C# modulů v aplikaci Visual Studio](how-to-visual-studio-develop-csharp-module.md) nebo [vývoj modulů v Visual Studio Code](how-to-vs-code-develop-module.md). Tyto články obsahují pokyny k vytváření nových modulů a publikování imagí kontejneru do registru.

## <a name="try-the-solution"></a>Vyzkoušejte řešení

Tato část vás provede nasazením modulu Microsoft Sample Modbus do zařízení IoT Edge.

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT Hub.

2. Přejděte na **IoT Edge** a klikněte na IoT Edge zařízení.

3. Vyberte **Set modules** (Nastavit moduly).

4. Přidejte modul Modbus:

   1. Klikněte na **Přidat** a vyberte **IoT Edge modul**.

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

   6. Vyberte **Uložit**.

5. Zpět v kroku **přidání modulů** vyberte **Další**.

7. V kroku **určení tras** zkopírujte do textového pole následující JSON. Tato trasa odešle všechny zprávy shromážděné modulem Modbus do centra IoT Hub. V této trase je **modbusOutput** koncový bod, který modul Modbus používá pro výstup dat a **$upstream** je zvláštní cíl, který dává IoT Edgemu centru pokyn k posílání zpráv do IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Vyberte **Next** (Další).

9. V kroku **Kontrola nasazení** vyberte **Odeslat**.

10. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Měl by se zobrazit nový modul **Modbus** spuštěný spolu s modulem runtime IoT Edge.

## <a name="view-data"></a>Zobrazení dat
Takto si můžete prohlédnout data přicházející z modulu modbus:
```cmd/sh
iotedge logs modbus
```

Můžete také zobrazit telemetrii, kterou zařízení odesílá, pomocí [rozšíření azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak můžou IoT Edge zařízení fungovat jako brány, najdete v tématu [vytvoření IoT Edge zařízení, které funguje jako transparentní brána](./how-to-create-transparent-gateway.md).
- Další informace o tom, jak IoT Edge moduly fungují, najdete v tématu [principy Azure IoT Edgech modulů](iot-edge-modules.md).
