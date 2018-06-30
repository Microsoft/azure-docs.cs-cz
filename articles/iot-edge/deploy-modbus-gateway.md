---
title: Nasazení zařízení Modbus se službou Azure IoT Edge | Dokumentace Microsoftu
description: Jak umožnit zařízením s protokolem Modbus TCP komunikaci s Azure IoT Hub vytvořením zařízení s bránou IoT Edge
author: kgremban
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: kgremban
ms.openlocfilehash: 9e69faf1b07af0e60cfd21cd6eb9f00e211ab91e
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031749"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Připojení Modbus TCP zařízení prostřednictvím IoT hraniční zařízení brány

Pokud chcete připojit zařízení IoT využívající protokol Modbus TCP ke službě Azure IoT Hub, můžete jako bránu použít zařízení IoT Edge. Zařízení brány čte data ze zařízení Modbus a předává je do cloudu s použitím podporovaného protokolu. 

![Připojení zařízení Modbus ke službě IoT Hub přes bránu Edge](./media/deploy-modbus-gateway/diagram.png)

Tento článek popisuje vytvoření vlastní bitové kopie kontejneru pro modul Modbus (nebo můžete použít připravený vzor) a jeho nasazení do zařízení IoT Edge, které bude sloužit jako brána. 

V tomto článku se předpokládá, že používáte protokol Modbus TCP. Další informace o tom, jak nakonfigurovat modul pro podporu Modbus RTU najdete v projektu [Modulu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) na Githubu. 

## <a name="prerequisites"></a>Požadavky
* Zařízení Azure IoT Edge. Postup vytvoření tohoto zařízení najdete v článku [Nasazení Azure IoT Edge na simulovaném zařízení ve Windows](quickstart.md) nebo [na Linuxu](quickstart-linux.md). 
* Primární připojovací řetězec klíče pro zařízení IoT Edge.
* Fyzické nebo simulované zařízení Modbus, které podporuje protokol Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Příprava kontejneru Modbus

Chcete-li otestovat funkci brány Modbus, Microsoft nabízí jednoduchý modul, který můžete použít. Chcete-li použít vzorový modul, přejděte k části [Spuštění řešení](#run-the-solution) a zadejte následující jako identifikátor URI bitové kopie: 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

Pokud chcete vytvořit vlastní modul a přizpůsobit ho pro vaše prostředí, použijte open source [modul Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) na Githubu. Podle pokynů uvedených v projektu na GitHubu vytvořte vlastní bitovou kopii kontejneru. Když vytvoříte vlastní bitovou kopii kontejneru, vyhledejte pokyny k jejímu publikování do registru a nasazení vlastního modulu do zařízení v článku [Vývoj a nasazení modulu IoT Edge v jazyce C#](tutorial-csharp-module.md). 


## <a name="run-the-solution"></a>Spuštění řešení
1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT Hub.
2. Přejděte na **IoT Edge** a klikněte na zařízení IoT okraj.
3. Vyberte **Set modules** (Nastavit moduly).
4. Přidejte modul Modbus:
   1. Klikněte na tlačítko **přidat** a vyberte **IoT Edge modulu**.
   2. Do pole **Name** (Název) zadejte „modbus“.
   3. Do pole **Image** (Bitová kopie) zadejte identifikátor URI vzorového kontejneru: `microsoft/azureiotedge-modbus-tcp:1.0-preview`.
   4. Zaškrtnutím políčka **Enable** (Povolit) aktualizujte požadované vlastnosti dvojčete modulu.
   5. Do textového pole zkopírujte následující JSON. Změňte hodnotu **SlaveConnection** na adresu IPv4 vašeho zařízení Modbus.

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{  
                "Op01":{  
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
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
7. V kroku **určení tras** zkopírujte do textového pole následující JSON. Tato trasa odešle všechny zprávy shromážděné modulem Modbus do centra IoT Hub. V této trase znamená „modbusOutput“ koncový bod, který modul Modbus pomocí používá pro výstup dat a „upstream“ je speciální cílové umístění, které bráně Edge říká, aby odesílala zprávy do centra IoT Hub. 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Vyberte **Next** (Další). 
9. V **zkontrolujte nasazení** krok, vyberte **odeslání**. 
10. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Měli byste vidět nové **modbus** modul spuštěn společně s runtime IoT okraj.

## <a name="view-data"></a>Zobrazení dat
Takto si můžete prohlédnout data přicházející z modulu modbus:
```cmd/sh
docker logs -f modbus
```

Můžete také zobrazit telemetrická data odeslaná ze zařízení pomocí [nástroje IoT Hub explorer](https://github.com/azure/iothub-explorer). 

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak IoT hraniční zařízení může fungovat jako brány najdete v tématu [vytvořit IoT hraničním zařízením, která funguje jako brána transparentní][lnk-transparent-gateway-linux]
- Další informace o fungování modulů IoT Edge najdete v tématu [Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md).

<!-- Links -->
[lnk-transparent-gateway-linux]: ./how-to-create-transparent-gateway-linux.md