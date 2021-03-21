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
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628925"
---
Jednou z klíčových funkcí Azure IoT Edge je nasazování kódu do vašich IoT Edgech zařízení z cloudu. *Moduly IoT Edge* jsou spustitelné balíčky implementované jako kontejnery. V této části nasadíte předem sestavený modul z [oddílu IoT Edge modulů Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) přímo z Azure IoT Hub.

Modul, který v této části nasadíte, simuluje senzor a odesílá vygenerovaná data. Tento modul je užitečným kódem, když začínáte s IoT Edge, protože můžete použít Simulovaná data pro vývoj a testování. Pokud chcete vidět přesně to, co tento modul dělá, můžete zobrazit [zdrojový kód simulovaného senzoru teploty](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Pomocí těchto kroků nasadíte svůj první modul z Azure Marketplace.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a pokračujte do služby IoT Hub.

1. V nabídce na levé straně v části **Automatická správa zařízení** vyberte **IoT Edge**.

1. V seznamu zařízení vyberte ID zařízení cílového zařízení.

1. Na horním panelu vyberte možnost **nastavit moduly**.

   ![Snímek obrazovky, který ukazuje, že se vyberou moduly set](./media/iot-edge-deploy-module/select-set-modules.png)

1. V části **IoT Edge moduly** otevřete rozevírací nabídku **Přidat** a pak vyberte **modul Marketplace**.

   ![Snímek obrazovky, na kterém se zobrazuje rozevírací nabídka přidat](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. V **tržišti IoT Edge modulu** vyhledejte a vyberte `Simulated Temperature Sensor` modul.

   Modul se přidá do oddílu IoT Edge moduly s požadovaným stavem **spuštění** .

1. Vyberte **Další: trasy** pro pokračování k dalšímu kroku průvodce.

   ![Snímek obrazovky, který ukazuje pokračování dalšího kroku po přidání modulu.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Na kartě **trasy** odeberte výchozí trasu, **Směrování** a potom vyberte **Další: zkontrolovat + vytvořit** a pokračujte dalším krokem průvodce.

   >[!Note]
   >Trasy jsou vytvořené pomocí párů název a hodnota. Na této stránce byste měli vidět dvě trasy. Výchozí trasa, **trasa**, odesílá všechny zprávy do IoT Hub (což se říká `$upstream` ). Druhá trasa **SimulatedTemperatureSensorToIoTHub** byla vytvořena automaticky při přidání modulu z Azure Marketplace. Tato trasa pošle všechny zprávy z modulu simulované teploty do IoT Hub. Výchozí trasu můžete odstranit, protože je v tomto případě redundantní.

   ![Snímek obrazovky, který ukazuje odebrání výchozí trasy, přechod k dalšímu kroku.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Zkontrolujte soubor JSON a pak vyberte **vytvořit**. Soubor JSON definuje všechny moduly, které nasadíte do zařízení IoT Edge. Zobrazí se modul **SimulatedTemperatureSensor** a dva běhové moduly, **edgeAgent** a **edgeHub**.

   >[!Note]
   >Když do zařízení IoT Edge odešlete nové nasazení, do zařízení se nic nevloží. Zařízení místo toho pravidelně odesílá do IoT Hubu dotazy týkající se nových pokynů. Pokud zařízení najde aktualizovaný manifest nasazení, použije informace o novém nasazení k vyžádání imagí modulu z cloudu a potom začne spouštět moduly místně. Tento proces může trvat několik minut.

1. Po vytvoření podrobností nasazení modulu se Průvodce vrátí na stránku s podrobnostmi o zařízení. Zobrazte stav nasazení na kartě **moduly** .

   Měli byste vidět tři moduly: **$edgeAgent**, **$edgeHub** a **SimulatedTemperatureSensor**. Pokud má jeden nebo více modulů **Ano** v rámci **zadání v části nasazení** , ale ne pod položkou **hlášené zařízením**, zařízení IoT Edge je stále spouští. Počkejte několik minut a poté stránku aktualizujte.

   ![Snímek obrazovky, který zobrazuje senzor simulované teploty v seznamu nasazených modulů.](./media/iot-edge-deploy-module/view-deployed-modules.png)
