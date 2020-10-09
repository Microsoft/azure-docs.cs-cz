---
title: Překlad protokolů Modbus pomocí bran – Azure IoT Edge | Microsoft Docs
description: Jak umožnit zařízením s protokolem Modbus TCP komunikaci s Azure IoT Hub vytvořením zařízení s bránou IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76511140"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Připojení zařízení Modbus TCP přes bránu IoT Edge zařízení

Pokud chcete připojit zařízení IoT, která používají protokoly TCP nebo RTU, do služby Azure IoT Hub, můžete jako bránu použít zařízení IoT Edge. Zařízení brány čte data ze zařízení Modbus a předává je do cloudu s použitím podporovaného protokolu.

![Zařízení Modbus se připojují k IoT Hub prostřednictvím brány IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Tento článek popisuje vytvoření vlastní bitové kopie kontejneru pro modul Modbus (nebo můžete použít připravený vzor) a jeho nasazení do zařízení IoT Edge, které bude sloužit jako brána.

V tomto článku se předpokládá, že používáte protokol Modbus TCP. Další informace o tom, jak nakonfigurovat modul pro podporu Modbus RTU, najdete v projektu [modulu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) na GitHubu.

## <a name="prerequisites"></a>Požadavky

* Zařízení Azure IoT Edge. Návod, jak ho nastavit, najdete v tématu [nasazení Azure IoT Edge v systému Windows](quickstart.md) nebo [Linux](quickstart-linux.md).
* Primární připojovací řetězec klíče pro zařízení IoT Edge.
* Fyzické nebo simulované zařízení Modbus, které podporuje protokol Modbus TCP. Budete potřebovat znát jeho adresu IPv4.

## <a name="prepare-a-modbus-container"></a>Příprava kontejneru Modbus

Chcete-li otestovat funkci brány Modbus, Microsoft nabízí jednoduchý modul, který můžete použít. K modulu můžete přistupovat z Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)nebo s identifikátorem URI image `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Pokud chcete vytvořit vlastní modul a přizpůsobit ho pro své prostředí, na GitHubu je open source projekt [Azure IoT Edgeho modulu Modbus](https://github.com/Azure/iot-edge-modbus) . Podle pokynů uvedených v projektu na GitHubu vytvořte vlastní bitovou kopii kontejneru. Chcete-li vytvořit image kontejneru, přečtěte si téma [vývoj modulů C# v aplikaci Visual Studio](how-to-visual-studio-develop-csharp-module.md) nebo [vývoj modulů v Visual Studio Code](how-to-vs-code-develop-module.md). Tyto články obsahují pokyny k vytváření nových modulů a publikování imagí kontejneru do registru.

## <a name="try-the-solution"></a>Vyzkoušejte řešení

Tato část vás provede nasazením modulu Microsoft Sample Modbus do zařízení IoT Edge.

1. Na webu [Azure Portal](https://portal.azure.com/) otevřete vaše centrum IoT Hub.

2. Přejděte na **IoT Edge** a klikněte na IoT Edge zařízení.

3. Vyberte **Set modules** (Nastavit moduly).

4. V části **IoT Edge moduly** přidejte modul Modbus:

   1. Klikněte na rozevírací seznam **Přidat** a vyberte **modul Marketplace**.
   2. Vyhledejte `Modbus` a vyberte **modul Modbus TCP** od společnosti Microsoft.
   3. Modul se automaticky nakonfiguruje pro váš IoT Hub a zobrazí se v seznamu IoT Edgech modulů. Trasy jsou také automaticky nakonfigurovány. Vyberte **Zkontrolovat a vytvořit**.
   4. Zkontrolujte manifest nasazení a vyberte **vytvořit**.

5. Vyberte modul Modbus, `ModbusTCPModule` v seznamu a vyberte kartu **nastavení s dvojitým modulem** . Požadovaný formát JSON pro nevlákenný požadovaný název modulu je automaticky vyplněn.

6. Vyhledejte vlastnost **SlaveConnection** ve formátu JSON a nastavte její hodnotu na IPv4 adresu vašeho zařízení Modbus.

7. Vyberte **Aktualizovat**.

8. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nasazení a pak vyberte **vytvořit**.

9. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Měl by se zobrazit nový `ModbusTCPModule` modul spuštěný spolu s modulem runtime IoT Edge.

## <a name="view-data"></a>Zobrazení dat

Zobrazení dat přicházejících prostřednictvím modulu Modbus:

```cmd/sh
iotedge logs modbus
```

Můžete také zobrazit telemetrii, kterou zařízení odesílá, pomocí [rozšíření azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak můžou IoT Edge zařízení fungovat jako brány, najdete v tématu [vytvoření IoT Edge zařízení, které funguje jako transparentní brána](./how-to-create-transparent-gateway.md).
* Další informace o tom, jak IoT Edge moduly fungují, najdete v tématu [principy Azure IoT Edgech modulů](iot-edge-modules.md).
