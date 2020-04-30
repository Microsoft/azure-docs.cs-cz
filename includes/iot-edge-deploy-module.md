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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75564696"
---
Jednou z klíčových funkcí Azure IoT Edge je schopnost nasadit kód do vašich IoT Edgech zařízení z cloudu. **Moduly IoT Edge** jsou spustitelné balíčky implementované jako kontejnery. V této části nasadíte předem sestavený modul z [oddílu IoT Edge moduly Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) přímo z IoT Hub Azure.

Modul, který v této části nasadíte, simuluje senzor a odesílá vygenerovaná data. Tento modul je užitečným kódem, když začínáte s IoT Edge, protože můžete použít Simulovaná data pro vývoj a testování. Pokud chcete vidět přesně to, co tento modul dělá, můžete zobrazit [zdrojový kód simulovaného senzoru teploty](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Pokud chcete nasadit svůj první modul z Azure Marketplace, použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení**vyberte **IoT Edge**.

1. V seznamu zařízení klikněte na ID zařízení cílového zařízení.

1. Na horním panelu vyberte možnost **nastavit moduly**.

1. V části **IoT Edge moduly** na stránce klikněte na **Přidat**.

1. V rozevírací nabídce vyberte **modul Marketplace**.

   ![Simulovaný senzor teploty na webu Azure Portal](./media/iot-edge-deploy-module/search-for-temperature-sensor.png)

1. V **tržišti IoT Edge modulu**vyhledejte "senzor simulované teploty" a vyberte tento modul.

1. Všimněte si, že se automaticky vyplní modul SimulatedTemperatureSensor. V kurzech slouží tato stránka k přidání dalších modulů do nasazení. V tomto rychlém startu nasaďte jenom tento modul. Nejsou vyžadovány žádné přihlašovací údaje, protože jsou veřejné.

   ![Nastavit moduly na zařízení](./media/iot-edge-deploy-module/set-modules-on-device.png)

   Vyberte **Další: trasy** pro pokračování k dalšímu kroku průvodce.

1. Na kartě **trasy** v průvodci určíte, jak se mají zprávy předávat mezi moduly a IoT Hub. Zprávy se vytvářejí pomocí párů název/hodnota. Pro rychlý Start budete chtít, aby všechny zprávy ze všech modulů přešly na IoT Hub`$upstream`(). Pokud není automaticky vyplněno, přidejte následující kód pro **hodnotu** pro **název** `upstream`:

   ```sql
    FROM /messages/* INTO $upstream
   ```

   Vyberte **Další: zkontrolovat + vytvořit** a pokračujte k dalšímu kroku průvodce.

1. Na kartě **Revize + vytvořit** v průvodci můžete zobrazit náhled souboru JSON, který definuje všechny moduly, které se nasadí do zařízení IoT Edge. Všimněte si, že je zahrnutý modul **SimulatedTemperatureSensor** a dva další systémové moduly s názvem **edgeAgent** a **edgeHub**. Až budete hotovi, vyberte **vytvořit** .

   Když do zařízení IoT Edge odešlete nové nasazení, do zařízení se nic nevloží. Zařízení místo toho pravidelně odesílá do IoT Hubu dotazy týkající se nových pokynů. Pokud zařízení najde aktualizovaný manifest nasazení, použije informace o novém nasazení k vyžádání imagí modulu z cloudu a potom začne spouštět moduly místně. Tento proces může trvat několik minut.

1. Po vytvoření podrobností nasazení modulu se Průvodce vrátí na stránku **IoT Edge** služby IoT Hub. Vyberte zařízení ze seznamu zařízení IoT Edge, abyste zobrazili jeho podrobnosti.

1. Na stránce Podrobnosti o zařízení se posuňte dolů na kartu **moduly** . Měly by být uvedené tři moduly: $edgeAgent, $edgeHub a SimulatedTemperatureSensor. Pokud je jeden nebo více modulů uvedený v části nasazení, ale zařízení není nahlášené, vaše zařízení IoT Edge je pořád spouští. Chvíli počkejte a v horní části stránky vyberte **aktualizovat** .

   ![Zobrazit SimulatedTemperatureSensor v seznamu nasazených modulů](./media/iot-edge-deploy-module/deployed-modules-marketplace.png)
