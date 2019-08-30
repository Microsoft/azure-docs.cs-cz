---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e01bb13b904aaf8671512daa3ef3cbad182f01e3
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161966"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [Azure Portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Zvolte **vytvořit prostředek**a potom do pole **Hledat na Marketplace** zadejte *IoT Hub* .

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole následujícím způsobem:

   - **Předplatné:** Vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: Vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **vytvořit novou** a vyplňte název, který chcete použít. Pokud chcete použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Oblast:** Vyberte oblast, ve které chcete umístit rozbočovač. Vyberte umístění, které je pro vás nejblíže.

   - **Název IoT Hub**: Zadejte název svého rozbočovače. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Vytvoření centra v Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics-vs2019.png)

1. Vyberte **další: Velikost a škálování** pro pokračování v vytváření vašeho centra

   ![Nastavení velikosti a měřítka pro nové centrum pomocí Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Tato obrazovka vám umožní nastavit následující hodnoty:

    - **Úroveň cen a škálování**: Vybraná úroveň. Můžete si vybrat z několika vrstev v závislosti na tom, kolik funkcí potřebujete, a kolik zpráv odesíláte prostřednictvím řešení za den. Úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojit zařízení 500 k rozbočovači a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu IoT Hub na úrovni Free.

    - **IoT Hub jednotky**: Počet zpráv povolených na jednotku za den závisí na cenové úrovni vašeho centra. Pokud třeba chcete, aby centrum podporovalo příchozí zprávy 700 000, zvolíte dvě jednotky úrovně S1.
    Podrobnosti o dalších možnostech vrstev najdete v tématu [Volba správné IoT Hub úrovně](../articles/iot-hub/iot-hub-scaling.md).

    - **Rozšířené nastavení** > **oddíly zařízení-Cloud**: Tato vlastnost spojuje zprávy typu zařízení-Cloud s počtem souběžných čtenářů zpráv. Většina Center potřebuje jenom čtyři oddíly.

1. Pro tento článek přijměte výchozí možnosti a potom vyberte **zkontrolovat + vytvořit** a zkontrolujte si své volby. Na této obrazovce vidíte něco podobného.

   ![Přečtěte si informace o vytvoření nového centra.](./media/iot-hub-include-create-hub/iot-hub-create-review-vs2019.png)

1. Vyberte **vytvořit** a vytvořte nové centrum. Vytvoření centra může trvat několik minut.
