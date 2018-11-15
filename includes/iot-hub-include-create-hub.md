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
ms.openlocfilehash: b1f1f8144ec5b65c21c5ddcecd278a018260047c
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515984"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [webu Azure portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Zvolte +**vytvořit prostředek**, klikněte na tlačítko **Internet of Things**.

3. Klikněte na tlačítko **služby Iot Hub** ze seznamu na pravé straně. Zobrazí se první obrazovka pro vytvoření služby IoT hub.

   ![Snímek obrazovky znázorňující vytvoření Centrum na webu Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

   Vyplňte jednotlivá pole.

   **Předplatné**: Vyberte předplatné, které chcete použít pro službu IoT hub.

   **Skupina prostředků**: můžete vytvořit novou skupinu prostředků nebo použijte již existující. Chcete-li vytvořit nový, klikněte na tlačítko **vytvořit nový** a vyplňte název, který chcete použít. Chcete-li použít existující skupinu prostředků, klikněte na tlačítko **použít existující** a z rozevíracího seznamu vyberte skupinu prostředků. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/resource-group-portal.md).

   **Oblast**: Toto je oblast, ve kterém chcete centrem umístit. Vyberte umístění co nejblíže z rozevíracího seznamu.

   **Název centra IoT**: vložte název služby IoT hub. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

4. Klikněte na tlačítko **Další: dimenzování a škálování** pokračujte vytvoření služby IoT hub.

   ![Snímek obrazovky zobrazující nastavení velikosti a měřítka novou službu IoT hub pomocí webu Azure portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Na této obrazovce můžete přijmout výchozí hodnoty a stačí kliknout na **revize + vytvořit** v dolní části. 

   **Úroveň cen a škálování**: můžete si vybrat z několika vrstev v závislosti na tom, kolik funkce chcete a tom, kolik zpráv, můžete odeslat prostřednictvím řešení za den. Bezplatná úroveň slouží pro testování a vyhodnocení. Umožňuje 500 zařízení k připojení ke službě IoT hub a až 8 000 zpráv denně. Každé předplatné Azure můžete vytvořit v jednom centru IoT v bezplatné úrovni. 

   **Jednotky služby IoT Hub**: počet zpráv povolených na jednotku a den závisí na cenové úrovni vašeho centra. Například pokud chcete pro podporu příchozího přenosu dat 700 000 zpráv služby IoT hub, zvolit dvě jednotky úrovně S1.

   Podrobnosti o další úroveň možnosti najdete v tématu [výběru správné úrovně služby IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

   **Pokročilé / typu zařízení cloud oddíly**: tuto vlastnost souvisí s počtem souběžných čtenářů zpráv zpráv typu zařízení cloud. Většina centra IoT hub stačí jenom čtyři oddíly. 

5. Klikněte na tlačítko **zkontrolujte + vytvořit** zkontrolujte zvolené volby. Vypadá podobně jako tato obrazovka.

   ![Snímek obrazovky zkontrolování informací pro vytvoření nového centra IoT](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

6. Klikněte na tlačítko **vytvořit** vytvořit novou službu IoT hub. Vytváří se Centrum trvá několik minut.
