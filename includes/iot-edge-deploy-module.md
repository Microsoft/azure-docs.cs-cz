---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 92fb568bb1044df5be8c80372973743e2c0e3dcd
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52900996"
---
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Modul IoT Edge je spustitelný balíček implementovaný jako kontejner. V této části jsme vám nasazení předem sestavených modulu z [moduly IoT Edge části webu Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Tento modul generuje telemetrická data pro vaše simulované zařízení.

1. Na webu Azure Portal, zadejte `Simulated Temperature Sensor` do vyhledávání a otevřete výsledek Marketplace.

   ![Simulované teplotní snímač ve službě Azure search na portálu](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. V **předplatné** pole, vyberte předplatné, prostřednictvím služby IoT Hub, když používáte, pokud ještě není.

3. V **služby IoT Hub** vyberte název centra IoT používáte, pokud ještě není.

4. Klikněte na **najít zařízení**, vyberte zařízení IoT Edge (s názvem `myEdgeDevice`) a pak vyberte **vytvořit**.

5. V **přidat moduly** kroku v průvodci klikněte na **SimulatedTemperatureSensor** modulu, který chcete ověřit nastavení konfigurace, klikněte na tlačítko **Uložit** a vyberte **Další**.

6. V **určení tras** kroku v průvodci zkontrolujte trasy správně nastaveny pomocí výchozí trasy, která odešle všechny zprávy ze všech modulů pro službu IoT Hub (`$upstream`). Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream",
        "upstream": "FROM /messages/* INTO $upstream"
        }
    }
   ```

7. V **nasazení zkontrolujte** kroku v průvodci vyberte **odeslat**.

8. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Kromě edgeAgent modulu, který byl vytvořen při prvním spuštění služby, zobrazí se jiný modul runtime s názvem **edgeHub** a **SimulatedTemperatureSensor** uvedený modul.

   Může trvat několik minut, než se nové moduly zobrazení. Zařízení IoT Edge se má načíst nové informace o nasazení z cloudu, spuštění kontejnerů a vytvářet zprávy jeho nového stavu zpět do služby IoT Hub. 

   ![Zobrazení SimulatedTemperatureSensor v seznamu nasazené moduly](./media/iot-edge-deploy-module/deployed-modules-marketplace-temp.png)
