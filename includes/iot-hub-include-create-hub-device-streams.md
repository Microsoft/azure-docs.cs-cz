---
title: Zahrnout soubor (datové proudy zařízení ve verzi preview)
description: Zahrnout soubor (datové proudy zařízení ve verzi preview)
author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/14/2019
ms.author: rezas
ms.custom: include file
ms.openlocfilehash: ede897054a6cbef254c06bd1d810b933ec09016a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158624"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [webu Azure portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Zvolte +**vytvořit prostředek**, klikněte na tlačítko **Internet of Things**.

3. Klikněte na tlačítko **služby Iot Hub** ze seznamu na pravé straně. Zobrazí se první obrazovka pro vytvoření služby IoT hub.

   ![Snímek obrazovky znázorňující vytvoření Centrum na webu Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-01.png)

   Vyplňte jednotlivá pole:

   **Předplatné**: Vyberte předplatné, které chcete použít pro službu IoT hub.

   **Skupina prostředků**: Můžete vytvořit novou skupinu prostředků nebo použijte již existující. Chcete-li vytvořit nový, klikněte na tlačítko **vytvořit nový** a vyplňte název, který chcete použít. Chcete-li použít existující skupinu prostředků, klikněte na tlačítko **použít existující** a z rozevíracího seznamu vyberte skupinu prostředků. Další informace najdete v tématu [skupiny prostředků spravovat Azure Resource Manageru](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Oblast**: Toto je oblast, ve kterém chcete centrem umístit. Vyberte oblast, která podporuje službu IoT Hub zařízení datové proudy náhled, střed USA a střed USA – EUAP.

   **IoT Hub Name**: Vložte název služby IoT hub. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Klikněte na tlačítko **Další: Dimenzování a škálování** pokračujte vytvoření služby IoT hub.

   ![Snímek obrazovky zobrazující nastavení velikosti a měřítka novou službu IoT hub pomocí webu Azure portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Na této obrazovce můžete přijmout výchozí hodnoty a stačí kliknout na **revize + vytvořit** v dolní části.

   **Úroveň cen a škálování**: Ujistěte se, že vyberete jednu z úrovní Free (F1) nebo standardní (S1, S2, S3). Tato volba může také řídí velikost vašeho fleet a jiných datových proudů úloh, které ve vašem Centru (např. telemetrické zprávy). Například na úrovni free je určená pro účely testování a vyhodnocení. Umožňuje 500 zařízení k připojení ke službě IoT hub a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit v jednom centru IoT v bezplatné úrovni. 

   **Jednotky služby IoT Hub**: Tato volba závisí na jiné datové proudy úlohy, které očekáváte, že ve vašem Centru – teď můžete vybrat 1.

   Podrobnosti o další úroveň možnosti najdete v tématu [výběru správné úrovně služby IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

5. Klikněte na tlačítko **zkontrolujte + vytvořit** zkontrolujte zvolené volby. Vypadá podobně jako tato obrazovka.

   ![Snímek obrazovky zkontrolování informací pro vytvoření nového centra IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

6. Klikněte na tlačítko **vytvořit** vytvořit novou službu IoT hub. Vytváří se Centrum trvá několik minut.
