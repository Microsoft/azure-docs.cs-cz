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
ms.custom: seodec18
ms.openlocfilehash: 325b69eb7b9b069db0ba49b4578541ee801c3444
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476184"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Připojení zařízení Modbus TCP přes zařízení brány IoT Edge

Pokud chcete připojit zařízení IoT využívající protokol Modbus TCP ke službě Azure IoT Hub, můžete jako bránu použít zařízení IoT Edge. Zařízení brány čte data ze zařízení Modbus a předává je do cloudu s použitím podporovaného protokolu.

![Připojení zařízení Modbus ke službě IoT Hub pomocí brány IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Tento článek popisuje vytvoření vlastní bitové kopie kontejneru pro modul Modbus (nebo můžete použít připravený vzor) a jeho nasazení do zařízení IoT Edge, které bude sloužit jako brána.

V tomto článku se předpokládá, že používáte protokol Modbus TCP. Další informace o tom, jak nakonfigurovat modul pro podporu Modbus RTU najdete v článku [modulu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) projektu na Githubu.

## <a name="prerequisites"></a>Požadavky
* Zařízení Azure IoT Edge. Návod, jak nastavit jeden, naleznete v tématu [nasazení Azure IoT Edge na Windows](quickstart.md) nebo [Linux](quickstart-linux.md).
* Primární připojovací řetězec klíče pro zařízení IoT Edge.
* Fyzické nebo simulované zařízení Modbus, které podporuje protokol Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Příprava kontejneru Modbus

Chcete-li otestovat funkci brány Modbus, Microsoft nabízí jednoduchý modul, který můžete použít. V modulu se dá dostat z Azure Marketplace [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), nebo identifikátor URI, image **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Pokud chcete vytvořit vlastní modul a přizpůsobit pro vaše prostředí, je open source [modulu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) projektu na Githubu. Podle pokynů uvedených v projektu na GitHubu vytvořte vlastní bitovou kopii kontejneru. Vytvoření image kontejneru, najdete [vývoj C# moduly v sadě Visual Studio](how-to-visual-studio-develop-csharp-module.md) nebo [vývoj modulů ve Visual Studio Code](how-to-vs-code-develop-module.md). Tyto články poskytují pokyny k vytvoření nové moduly a publikování Image kontejneru do registru.

## <a name="try-the-solution"></a>Vyzkoušejte toto řešení

Tato část vás provede nasazením modul Modbus od Microsoftu do zařízení IoT Edge.

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT Hub.

2. Přejděte na **IoT Edge** a klikněte na zařízení IoT Edge.

3. Vyberte **Nastavit moduly**.

4. Přidejte modul Modbus:

   1. Klikněte na tlačítko **přidat** a vyberte **modul IoT Edge**.

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

7. V kroku **určení tras** zkopírujte do textového pole následující JSON. Tato trasa odešle všechny zprávy shromážděné modulem Modbus do centra IoT Hub. V této trase **modbusOutput** je koncový bod tohoto Modbus modul se používá pro výstup dat a **$upstream** speciální cíl, že hraničních zařízeních IoT hub pro odesílání zpráv do služby IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. Vyberte **Další**.

9. V kroku **Kontrola nasazení** vyberte **Odeslat**.

10. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Měli byste vidět nové **modbus** modulu spuštěn společně s runtime IoT Edge.

## <a name="view-data"></a>Zobrazení dat
Takto si můžete prohlédnout data přicházející z modulu modbus:
```cmd/sh
iotedge logs modbus
```

Můžete také zobrazit telemetrická data odeslaná ze zařízení s použitím [rozšíření Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak zařízení IoT Edge může fungovat jako brána najdete v tématu [vytvoření zařízení IoT Edge, který funguje jako transparentní brána](./how-to-create-transparent-gateway.md).
- Další informace o fungování modulů IoT Edge najdete v tématu [moduly pochopit Azure IoT Edge](iot-edge-modules.md).
