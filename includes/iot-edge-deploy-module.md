---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/31/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: abbde0cb8137650faad563248c8fafe7dc0f469a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75564696"
---
Jednou z klíčových funkcí Azure IoT Edge je možnost nasazovat kód do vašich zařízení IoT Edge z cloudu. **Moduly IoT Edge** jsou spustitelné balíčky implementované jako kontejnery. V této části nasadíte předem sestavený modul z [části Moduly IoT Edge na webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) přímo z centra Azure IoT Hub.

Modul, který nasadíte v této části simuluje senzor a odešle vygenerovaná data. Tento modul je užitečný část kódu, když začínáte s IoT Edge, protože můžete použít simulovaná data pro vývoj a testování. Chcete-li přesně vidět, co tento modul dělá, můžete zobrazit [zdrojový kód simulovaného teplotního senzoru](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Pokud chcete nasadit svůj první modul z Azure Marketplace, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do svého centra IoT hub.

1. V nabídce v levém podokně vyberte v části **Automatická správa zařízení** **položku IoT Edge**.

1. Klikněte na ID zařízení cílového zařízení ze seznamu zařízení.

1. Na horním panelu vyberte **Nastavit moduly**.

1. V části **Moduly okrajů IoT** na stránce klikněte na **Přidat**.

1. V rozevírací nabídce vyberte **modul Marketplace**.

   ![Simulovaný senzor teploty na webu Azure Portal](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. Na **tržišti modulů IoT Edge**vyhledejte "Simulovaný snímač teploty" a vyberte tento modul.

1. Všimněte si, že modul SimulatedTemperatureSensor je automaticky naplněn. V kurzech pomocí této stránky přidáte další moduly do nasazení. Pro tento rychlý start nasadit pouze tento jeden modul. Nejsou vyžadována žádná pověření, protože jsou veřejná.

   ![Nastavení modulů na zařízení](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Vyberte **Další: Trasy,** abyste pokračovali k dalšímu kroku průvodce.

1. Na kartě **Trasy** průvodce definujete, jak jsou zprávy předávány mezi moduly a centrem IoT Hub. Zprávy jsou konstruovány pomocí párů název/hodnota. Pro rychlý start chcete, aby všechny zprávy ze všech modulů`$upstream`přecčovaly do centra IoT Hub ( ). Pokud není automaticky naplněna, přidejte následující kód **pro hodnotu** **názvu** `upstream`:

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Vyberte **Další: Kontrola + vytvoření,** abyste pokračovali k dalšímu kroku průvodce.

1. Na kartě **Revize + vytvoření** průvodce můžete zobrazit náhled souboru JSON, který definuje všechny moduly, které se nasazují do zařízení IoT Edge. Všimněte si, že je součástí **modulu SimulatedTemperatureSensor** a dva další systémové moduly nazývané **edgeAgent** a **edgeHub**. Až skončíte s revizí, vyberte **Vytvořit.**

   Když do zařízení IoT Edge odešlete nové nasazení, do zařízení se nic nevloží. Zařízení místo toho pravidelně odesílá do IoT Hubu dotazy týkající se nových pokynů. Pokud zařízení najde aktualizovaný manifest nasazení, použije informace o novém nasazení k vyžádání imagí modulu z cloudu a potom začne spouštět moduly místně. Tento proces může trvat několik minut.

1. Po vytvoření podrobností o nasazení modulu vás průvodce vrátí na stránku **IoT Edge** v centru IoT. Vyberte zařízení ze seznamu zařízení IoT Edge, abyste zobrazili jeho podrobnosti.

1. Na stránce podrobností o zařízení přejděte dolů na kartu **Moduly.** Měly by být uvedeny tři moduly: $edgeAgent, $edgeHub a SimulatedTemperatureSensor. Pokud jeden nebo více modulů jsou uvedeny jako zadané v nasazení, ale nejsou hlášeny zařízením, vaše zařízení IoT Edge je stále spouští. Chvíli počkejte a v horní části stránky vyberte **Aktualizovat.**

   ![Zobrazit simulatedTemperatureSensor v seznamu nasazených modulů](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
