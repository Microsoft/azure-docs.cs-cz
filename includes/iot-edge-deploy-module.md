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
ms.openlocfilehash: 536857a5fe3ec3cc037f21835a4152f93197bcb8
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977390"
---
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Modul IoT Edge je spustitelný balíček implementovaný jako kontejner. V této části nasadíte modul, který generuje telemetrická data pro vaše simulované zařízení.

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejděte do služby **IoT Edge** v části **Automatická správa zařízení** a vyberte své zařízení IoT Edge.

3. Vyberte **Nastavit moduly**. Třech krocích průvodce se otevře na portálu, který vás provede přidáním modulů, určení tras a kontrola nasazení. 

4. V **přidat moduly** kroku průvodce, vyhledejte **moduly nasazení** oddílu. Klikněte na tlačítko **přidat** vyberte **modul IoT Edge**.

   ![Přidat nový modul IoT Edge](./media/iot-edge-deploy-module/add-module.png)

5. Do pole **Název** zadejte `tempSensor`.

6. Do pole **Identifikátor URI image** zadejte `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`.

7. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.

   ![Uložení modulu IoT Edge po zadání názvu a URI image](./media/iot-edge-deploy-module/name-image.png)

8. Zpět v prvním kroku v průvodci vyberte **Další**.

9. V **určení tras** kroku průvodce, měli byste mít výchozí trasu, která odešle všechny zprávy ze všech modulů pro službu IoT Hub. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

10. V **nasazení zkontrolujte** kroku v průvodci vyberte **odeslat**.

11. Vraťte se na obrazovku podrobností o zařízení a zvolte **Refresh** (Obnovit). Kromě modulu edgeAgent vytvořeného při prvním spuštění služby by měl být v seznamu ještě jiný modul runtime s názvem **edgeHub** a další modul **tempSensor**.

   Může trvat několik minut, než se nové moduly zobrazení. Zařízení IoT Edge se má načíst nové informace o nasazení z cloudu, spuštění kontejnerů a vytvářet zprávy jeho nového stavu zpět do služby IoT Hub. 

   ![Zobrazení modulu tempSensor v seznamu nasazených modulů](./media/iot-edge-deploy-module/deployed-modules.png)
