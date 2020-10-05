---
title: zahrnout soubor
description: zahrnout soubor
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 76c8eb7acf20d8cf68d3573defd947efbc6c3c43
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85801685"
---
Jednou z klíčových funkcí Azure IoT Edge je schopnost nasadit kód do vašich IoT Edgech zařízení z cloudu. *Moduly IoT Edge* jsou spustitelné balíčky implementované jako kontejnery. V této části nasadíte předem sestavený modul z [oddílu IoT Edge moduly Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) přímo z IoT Hub Azure.

Modul, který v této části nasadíte, simuluje senzor a odesílá vygenerovaná data. Tento modul je užitečným kódem, když začínáte s IoT Edge, protože můžete použít Simulovaná data pro vývoj a testování. Pokud chcete vidět přesně to, co tento modul dělá, můžete zobrazit [zdrojový kód simulovaného senzoru teploty](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Pokud chcete nasadit svůj první modul z Azure Marketplace, použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.

1. V nabídce v levém podokně v části **Automatická správa zařízení**vyberte **IoT Edge**.

1. V seznamu zařízení klikněte na ID zařízení cílového zařízení.

1. Na horním panelu vyberte možnost **nastavit moduly**.

   ![Vyberte možnost nastavit moduly na stránce Podrobnosti o zařízení.](./media/iot-edge-deploy-module/select-set-modules.png)

1. V části **IoT Edge moduly** na stránce klikněte na **Přidat** a v rozevírací nabídce vyberte **modul Marketplace** .

   ![Přidat modul Marketplace](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. V **tržišti IoT Edge modulu**vyhledejte "senzor simulované teploty" a vyberte tento modul.

1. Všimněte si, že se modul SimulatedTemperatureSensor přidal do oddílu IoT Edge moduly s požadovaným stavem **spuštěným**.

   Vyberte **Další: trasy** pro pokračování k dalšímu kroku průvodce.

   ![Přejít na další krok, jakmile je v seznamu uveden modul snímače teploty](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Na kartě **trasy** v průvodci můžete určit, jak se mají zprávy předávat mezi moduly a IoT Hub. Trasy jsou vytvořené pomocí párů název/hodnota. Na této stránce byste měli vidět dvě trasy. Výchozí trasa s názvem **Route** odesílá všechny zprávy do IoT Hub (což se říká `$upstream` ). Druhá trasa s názvem **SimulatedTemperatureSensorToIoTHub** byla vytvořena automaticky při přidání modulu z webu Marketplace. Tato trasa odesílá všechny zprávy přímo z modulu simulované teploty do IoT Hub. Výchozí trasu můžete odstranit, protože je v tomto případě redundantní.

   Vyberte **Další: zkontrolovat + vytvořit** a pokračujte k dalšímu kroku průvodce.

   ![Odstraňte výchozí trasu a potom přejděte k dalšímu kroku.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Na kartě **Revize + vytvořit** v průvodci můžete zobrazit náhled souboru JSON, který definuje všechny moduly, které se nasadí do zařízení IoT Edge. Všimněte si, že je zahrnutý modul **SimulatedTemperatureSensor** , a také dva běhové moduly, **edgeAgent** a **edgeHub**. Až budete hotovi, vyberte **vytvořit** .

   Když do zařízení IoT Edge odešlete nové nasazení, do zařízení se nic nevloží. Zařízení místo toho pravidelně odesílá do IoT Hubu dotazy týkající se nových pokynů. Pokud zařízení najde aktualizovaný manifest nasazení, použije informace o novém nasazení k vyžádání imagí modulu z cloudu a potom začne spouštět moduly místně. Tento proces může trvat několik minut.

1. Po vytvoření podrobností nasazení modulu se Průvodce vrátí na stránku s podrobnostmi o zařízení. Na stránce Podrobnosti o zařízení Zobrazte stav nasazení na kartě **moduly** . Měly by být uvedené tři moduly: $edgeAgent, $edgeHub a SimulatedTemperatureSensor. Pokud je jeden nebo více modulů uvedený v části nasazení, ale zařízení není nahlášené, vaše zařízení IoT Edge je pořád spouští. Chvíli počkejte a v horní části stránky vyberte **aktualizovat** .

   ![Zobrazit SimulatedTemperatureSensor v seznamu nasazených modulů](./media/iot-edge-deploy-module/view-deployed-modules.png)
