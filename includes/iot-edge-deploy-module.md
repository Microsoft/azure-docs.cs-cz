---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/04/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 019d6120f8f3769d2d974270d575278da0370ef0
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057368"
---
Jednou z klíčových možností služby Azure IoT Edge je schopnost nasazovat moduly do zařízení IoT Edge z cloudu. Modul IoT Edge je spustitelný balíček implementovaný jako kontejner. V této části jsme nasadit předem sestavené modulu z [moduly IoT Edge části webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Tento modul generuje Simulovaná telemetrická data pro vaše zařízení IoT Edge.

1. V [webu Azure portal](https://portal.azure.com), zadejte **simulované teplotní snímač** do vyhledávání a otevřete výsledek Marketplace.

   ![Simulované teplotní snímač ve službě Azure search na portálu](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Vyberte zařízení IoT Edge pro příjem tento modul. Na **cílová zařízení pro modul IoT Edge** stránky, zadejte následující informace:

   1. **Předplatné**: Vyberte předplatné, které obsahuje IoT hub, kterou používáte.

   2. **IoT Hub**: vyberte název služby IoT hub, kterou používáte.

   3. **Název zařízení IoT Edge**: Pokud dříve v tomto rychlém startu jste použili navrhované zařízení název, zadejte **myEdgeDevice**. Nebo vyberte **najít zařízení** vybírat ze seznamu zařízení IoT Edge ve službě IoT hub. 
   
   4. Vyberte **Vytvořit**.

3. Teď, když jste se rozhodli modul IoT Edge z Azure Marketplace a vybrali zařízení IoT Edge pro příjem modulu, budete přesměrováni na třech krocích průvodce, který vám umožní definovat přesně jak modulu se nasadí. V **přidat moduly** kroku průvodce, Všimněte si, že **SimulatedTemperatureSensor** modulu se vyplní automaticky. V kurzech se použijete na této stránce můžete přidat další moduly vašeho nasazení. Pro účely tohoto rychlého startu jen nasaďte tento jeden modul. Vyberte **Další** pokračujte k dalšímu kroku v průvodci.

4. V **určení tras** kroku v průvodci můžete definovat, jak jsou předány zpráv mezi moduly a pro službu IoT Hub. Pro rychlý start, chcete všechny zprávy ze všech modulů a přejděte do služby IoT Hub (`$upstream`). Pokud není vyplní automaticky, přidejte následující kód, pak vyberte **Další**:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```

5. V **nasazení zkontrolujte** kroku v průvodci můžete zobrazit náhled souboru JSON, který definuje všechny moduly, které se nasadí do zařízení IoT Edge. Všimněte si, že **SimulatedTemperatureSensor** modul je zahrnuta, stejně jako dvou modulů další systém s názvem **edgeAgent** a **edgeHub**. Vyberte **odeslat** až budete mít revizí.

   Když odešlete nové nasazení do zařízení IoT Edge, nic se posune do vašeho zařízení. Místo toho zařízení se dotazuje služby IoT Hub pravidelně žádné nové pokyny. Když se zobrazí informace o nové nasazení, zařízení, která používá k přetahování imagí pro modul z cloudu a spustí moduly místně. Tento proces může trvat několik minut. 

6. Po odeslání podrobnosti o nasazení modulu, průvodce se vrátíte na **IoT Edge** stránka služby IoT hub. Vyberte zařízení ze seznamu zařízení IoT Edge zobrazíte její podrobnosti. 

7. Na stránce s podrobnostmi zařízení, přejděte dolů k položce **moduly** oddílu. By měly být uvedeny tři moduly: $edgeAgent $edgeHub a SimulatedTemperatureSensor. Pokud jeden nebo více následujících moduly jsou uvedené jako zadaný v nasazení ale není hlášeno z zařízení, to znamená, že zařízení IoT Edge se pořád ještě spouští je. Chvíli počkejte a vyberte **aktualizovat** v horní části stránky. 

   ![Zobrazení SimulatedTemperatureSensor v seznamu nasazené moduly](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
