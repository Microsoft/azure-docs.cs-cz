---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cc83d94acd25914ee57473de53afbc018f310887
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175107"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [webu Azure portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Zvolte +**vytvořit prostředek**, pak *Hledat na Marketplace* pro **služby IoT Hub**.

3. Vyberte **služby IoT Hub** a klikněte na tlačítko **vytvořit** tlačítko. Zobrazí se první obrazovka pro vytvoření služby IoT hub.

   ![Vytvoření centra na webu Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   Vyplňte jednotlivá pole.

   **Předplatné**: Vyberte předplatné, které chcete použít pro službu IoT hub.

   **Skupina prostředků**: Můžete vytvořit novou skupinu prostředků nebo použijte již existující. Chcete-li vytvořit nový, klikněte na tlačítko **vytvořit nový** a vyplňte název, který chcete použít. Chcete-li použít existující skupinu prostředků, klikněte na tlačítko **použít existující** a z rozevíracího seznamu vyberte skupinu prostředků. Další informace najdete v tématu [skupiny prostředků spravovat Azure Resource Manageru](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   **Oblast**: Toto je oblast, ve kterém chcete centrem umístit. Vyberte umístění co nejblíže z rozevíracího seznamu.

   **IoT Hub Name**: Vložte název služby IoT hub. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Klikněte na tlačítko **Další: Dimenzování a škálování** pokračujte vytvoření služby IoT hub.

   ![Nastavení velikosti a škálování novou službu IoT hub pomocí webu Azure portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Na této obrazovce můžete přijmout výchozí hodnoty a stačí kliknout na **revize + vytvořit** v dolní části. 

   **Úroveň cen a škálování**: Můžete si vybrat z několika vrstev v závislosti na tom, kolik funkce chcete a tom, kolik zpráv, můžete odeslat prostřednictvím řešení za den. Bezplatná úroveň slouží pro testování a vyhodnocení. Umožňuje 500 zařízení k připojení ke službě IoT hub a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit v jednom centru IoT v bezplatné úrovni. 

   **Jednotky služby IoT Hub**: Počet zpráv povolených na jednotku a den závisí na cenové úrovni vašeho centra. Například pokud chcete pro podporu příchozího přenosu dat 700 000 zpráv služby IoT hub, zvolit dvě jednotky úrovně S1.

   Podrobnosti o další úroveň možnosti najdete v tématu [výběru správné úrovně služby IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

   **Pokročilé / typu zařízení cloud oddíly**: Tato vlastnost se týká zpráv typu zařízení cloud počtem souběžných čtenářů zpráv. Většina centra IoT hub stačí jenom čtyři oddíly. 

5. Klikněte na tlačítko **zkontrolujte + vytvořit** zkontrolujte zvolené volby. Vypadá podobně jako tato obrazovka.

   ![Projděte si informace o vytvoření nového centra IoT](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. Klikněte na tlačítko **vytvořit** vytvořit novou službu IoT hub. Vytváří se Centrum trvá několik minut.
