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
ms.openlocfilehash: 12661c77c6a950b482187b09e4465c964e6d6652
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494052"
---
Jednou z klíčových funkcí Azure IoT Edge je schopnost nasadit kód do vašich IoT Edgech zařízení z cloudu. **Moduly IoT Edge** jsou spustitelné balíčky implementované jako kontejnery. V této části nasadíte předem sestavený modul z [oddílu IoT Edge moduly Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). 

Modul, který v této části nasadíte, simuluje senzor a odesílá vygenerovaná data. Tento modul je užitečným kódem, když začínáte s IoT Edge, protože můžete použít Simulovaná data pro vývoj a testování. Pokud chcete vidět přesně to, co tento modul dělá, můžete zobrazit [zdrojový kód simulovaného senzoru teploty](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs). 

Pokud chcete nasadit svůj první modul z Azure Marketplace, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)do hledání zadejte **simulovaný senzor teploty** a otevřete výsledek Marketplace.

   ![Senzor simulované teploty v Azure Portal hledání](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

2. Vyberte zařízení IoT Edge pro příjem tohoto modulu. Na stránce **cílová zařízení pro IoT Edge modul** zadejte následující informace:

   1. **Předplatné**: vyberte předplatné, které obsahuje IoT Hub, které používáte.

   2. **IoT Hub**: vyberte název centra IoT, které používáte.

   3. **IoT Edge název zařízení**: Pokud jste dříve v tomto rychlém startu použili navrhovaný název zařízení, zadejte **myEdgeDevice**. Nebo vyberte **Najít zařízení** a vyberte si ze seznamu IoT Edge zařízení ve službě IoT Hub. 
   
   4. Vyberte **Vytvořit**.

3. Teď, když jste vybrali modul IoT Edge z Azure Marketplace a vybrali IoT Edge zařízení pro příjem modulu, přejdete na tři průvodce, který vám pomůže přesně definovat, jak nasadit modul. V kroku průvodce **přidat moduly** si všimněte, že se automaticky vyplní modul **SimulatedTemperatureSensor** . V kurzech slouží tato stránka k přidání dalších modulů do nasazení. V tomto rychlém startu nasaďte jenom tento modul. Kliknutím na tlačítko **Další** pokračujte dalším krokem průvodce.

4. V kroku **zadat trasy** v průvodci určíte, jak se zprávy předávají mezi moduly a IoT Hub. Pro rychlý Start budete chtít, aby všechny zprávy ze všech modulů přešly na IoT Hub (`$upstream`). Pokud není automaticky vyplněný, přidejte následující kód:

   ```json
    {
    "routes": {
        "route": "FROM /messages/* INTO $upstream"
        }
    }
   ```
   Pak vyberte **Další**.

5. V kroku **Kontrola nasazení** průvodce můžete zobrazit náhled souboru JSON, který definuje všechny moduly, které se nasadí do vašeho zařízení IoT Edge. Všimněte si, že je zahrnutý modul **SimulatedTemperatureSensor** a dva další systémové moduly s názvem **edgeAgent** a **edgeHub**. Vyberte **Odeslat** , až budete hotovi s revizí.

   Když do zařízení IoT Edge odešlete nové nasazení, do zařízení se nic nevloží. Místo toho se zařízení IoT Hub pravidelně dotazuje na jakékoli nové pokyny. Pokud zařízení nalezne aktualizovaný manifest nasazení, použije informace o novém nasazení k vyžádání imagí modulu z cloudu a potom začne spouštět moduly místně. Tento proces může trvat několik minut. 

6. Po odeslání podrobností nasazení modulu se Průvodce vrátí na stránku **IoT Edge** služby IoT Hub. Vyberte zařízení ze seznamu zařízení IoT Edge, abyste viděli jeho podrobnosti. 

7. Na stránce Podrobnosti o zařízení se posuňte dolů k části **moduly** . Měly by být uvedené tři moduly: $edgeAgent, $edgeHub a SimulatedTemperatureSensor. Pokud je jeden nebo více modulů uvedený v části nasazení, ale zařízení není nahlášené, vaše zařízení IoT Edge je pořád spouští. Chvíli počkejte a v horní části stránky vyberte **aktualizovat** . 

   ![Zobrazit SimulatedTemperatureSensor v seznamu nasazených modulů](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
