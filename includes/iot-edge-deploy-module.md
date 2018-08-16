---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 06e2be14cf7592edf373b6f679ac5d9ff3c87269
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161452"
---
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Modul IoT Edge je spustitelný balíček implementovaný jako kontejner. V této části nasadíte modul, který generuje telemetrická data pro vaše simulované zařízení. 

1. Na webu Azure Portal přejděte do svého centra IoT.
1. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge.
1. Vyberte **Nastavit moduly**.
1. Na stránce v části **Deployment Modules** (Nasazované moduly) klikněte na **Add** (Přidat) a vyberte **IoT Edge Module** (Modul IoT Edge).
1. Do pole **Název** zadejte `tempSensor`. 
1. Do pole **Identifikátor URI image** zadejte `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. 
1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.

   ![Uložení modulu IoT Edge po zadání názvu a URI image](./media/iot-edge-deploy-module/name-image.png)

1. Zpět v kroku **přidání modulů** vyberte **Next** (Další).
1. V kroku **Specify routes** (Zadejte trasy) musíte mít výchozí trasu, která odesílá všechny zprávy ze všech modulů do IoT Hubu. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. V kroku **Kontrola nasazení** vyberte **Odeslat**.
1. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Kromě modulu edgeAgent vytvořeného při prvním spuštění služby by měl být v seznamu ještě jiný modul runtime s názvem **edgeHub** a další modul **tempSensor**. 

   ![Zobrazení modulu tempSensor v seznamu nasazených modulů](./media/iot-edge-deploy-module/deployed-modules.png)
