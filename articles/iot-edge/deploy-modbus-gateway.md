---
title: Překlad modbusprotokolů s bránami - Azure IoT Edge | Dokumenty společnosti Microsoft
description: Jak umožnit zařízením s protokolem Modbus TCP komunikaci s Azure IoT Hub vytvořením zařízení s bránou IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511140"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Připojení zařízení TCP Modbus prostřednictvím brány zařízení IoT Edge

Pokud chcete připojit zařízení IoT, která používají protokoly Modbus TCP nebo RTU, k centru Azure IoT hub, můžete jako bránu použít zařízení IoT Edge. Zařízení brány čte data ze zařízení Modbus a předává je do cloudu s použitím podporovaného protokolu.

![Zařízení Modbus se připojují k iot hubu přes bránu IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Tento článek popisuje vytvoření vlastní bitové kopie kontejneru pro modul Modbus (nebo můžete použít připravený vzor) a jeho nasazení do zařízení IoT Edge, které bude sloužit jako brána.

V tomto článku se předpokládá, že používáte protokol Modbus TCP. Další informace o tom, jak nakonfigurovat modul pro podporu Modbus RTU, najdete v tématu [Azure IoT Edge Modbus modul](https://github.com/Azure/iot-edge-modbus) projektu na GitHubu.

## <a name="prerequisites"></a>Požadavky

* Zařízení Azure IoT Edge. Návod, jak ho nastavit, najdete v [tématu Nasazení Azure IoT Edge ve Windows](quickstart.md) nebo [Linuxu](quickstart-linux.md).
* Primární připojovací řetězec klíče pro zařízení IoT Edge.
* Fyzické nebo simulované zařízení Modbus, které podporuje protokol Modbus TCP. Budete potřebovat znát jeho adresu IPv4.

## <a name="prepare-a-modbus-container"></a>Příprava kontejneru Modbus

Chcete-li otestovat funkci brány Modbus, Microsoft nabízí jednoduchý modul, který můžete použít. K modulu se dostanete z Azure Marketplace, [Modbusu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)nebo pomocí identifikátoru URI bit ové bitové kopie `mcr.microsoft.com/azureiotedge/modbus:1.0`.

Pokud chcete vytvořit vlastní modul a přizpůsobit ho pro vaše prostředí, je open source [Azure IoT Edge Modbus modul](https://github.com/Azure/iot-edge-modbus) projektu na GitHubu. Podle pokynů uvedených v projektu na GitHubu vytvořte vlastní bitovou kopii kontejneru. Chcete-li vytvořit image kontejneru, naleznete [vývoj modulů Jazyka C# v sadě Visual Studio](how-to-visual-studio-develop-csharp-module.md) nebo vývoj [modulů v kódu sady Visual Studio](how-to-vs-code-develop-module.md). Tyto články obsahují pokyny k vytváření nových modulů a publikování iimages kontejnerů do registru.

## <a name="try-the-solution"></a>Vyzkoušejte řešení

Tato část vás provede nasazením ukázkového modulu Modbus od Microsoftu do zařízení IoT Edge.

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT Hub.

2. Přejděte na **IoT Edge** a klikněte na zařízení IoT Edge.

3. Vyberte **Set modules** (Nastavit moduly).

4. V části **Moduly IoT Edge** přidejte modul Modbus:

   1. Klikněte na rozevírací plán **Přidat** a vyberte **Modul Marketplace**.
   2. Vyhledejte `Modbus` a vyberte **modul TCP Modbus** od společnosti Microsoft.
   3. Modul se automaticky nakonfiguruje pro váš IoT Hub a zobrazí se v seznamu modulů IoT Edge. Trasy jsou také automaticky konfigurovány. Vyberte **Zkontrolovat a vytvořit**.
   4. Zkontrolujte manifest nasazení a vyberte **Vytvořit**.

5. V seznamu vyberte `ModbusTCPModule`modul Modbus a vyberte kartu **Nastavení dvojčete modulu.** Automaticky se naplní požadovaný montovny JSON pro twin modulu.

6. Vyhledejte vlastnost **SlaveConnection** v JSON a nastavte její hodnotu na adresu IPv4 vašeho zařízení Modbus.

7. Vyberte **Aktualizovat**.

8. Vyberte **Zkontrolovat + vytvořit**, zkontrolujte nasazení a pak vyberte **Vytvořit**.

9. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Měli byste vidět `ModbusTCPModule` nový modul běží spolu s runtime IoT Edge.

## <a name="view-data"></a>Zobrazení dat

Zobrazení dat přicházejících prostřednictvím modulu Modbus:

```cmd/sh
iotedge logs modbus
```

Můžete také zobrazit telemetrická data, kterou zařízení odesílá, pomocí [rozšíření Azure IoT Hub pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak mohou zařízení IoT Edge fungovat jako brány, najdete [v tématu Vytvoření zařízení IoT Edge, které funguje jako transparentní brána](./how-to-create-transparent-gateway.md).
* Další informace o tom, jak moduly IoT Edge fungují, [najdete v tématu Principy modulů Azure IoT Edge](iot-edge-modules.md).
