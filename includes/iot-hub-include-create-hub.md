---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/13/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 775e9fc737798a0e5517f9eb3314c71b14c5be07
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372332"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [Azure Portal](https://portal.azure.com).

1. Přihlaste se k webu [Portál Azure](https://portal.azure.com).

1. Na domovské stránce Azure vyberte tlačítko **+ vytvořit prostředek** a potom do pole **Hledat na Marketplace** zadejte *IoT Hub* .

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole následujícím způsobem:

   - **Předplatné**: vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **vytvořit novou** a vyplňte název, který chcete použít. Pokud chcete použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Oblast**: Vyberte oblast, ve které chcete umístit rozbočovač. Vyberte umístění, které je pro vás nejblíže.

   - **IoT Hub název**: zadejte název vašeho centra. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Vytvoření centra v Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Vyberte **Další: velikost a škálování** pro pokračování v vytváření vašeho centra.

   ![Nastavení velikosti a měřítka pro nové centrum pomocí Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

    Tato obrazovka vám umožní nastavit následující hodnoty:

    - **Úroveň cen a škálování**: vaše vybraná úroveň. Můžete si vybrat z několika vrstev v závislosti na tom, kolik funkcí potřebujete, a kolik zpráv odesíláte prostřednictvím řešení za den. Úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojit zařízení 500 k rozbočovači a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni.

    - **IoT Hub jednotky**: počet zpráv povolených na jednotku za den závisí na cenové úrovni vašeho centra. Pokud třeba chcete, aby centrum podporovalo příchozí zprávy 700 000, zvolíte dvě jednotky úrovně S1.
    Podrobnosti o dalších možnostech vrstev najdete v tématu [Volba správné IoT Hub úrovně](../articles/iot-hub/iot-hub-scaling.md).

    - **Azure Security Center**: Toto zapněte, pokud chcete přidat další vrstvu ochrany před hrozbami do IoT a vašich zařízení. Tato možnost není dostupná pro rozbočovače na úrovni Free. Další informace o této funkci najdete v tématu [Azure Security Center pro IoT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Rozšířená nastavení** > **oddílech typu zařízení-Cloud**: Tato vlastnost má vztah ke zprávám typu zařízení-Cloud na počet souběžných čtenářů zpráv. Většina Center potřebuje jenom čtyři oddíly.

1. Pro tento článek přijměte výchozí možnosti a potom vyberte **Další: značky** , které chcete přejít na další obrazovku.

    Značky jsou páry název-hodnota. Stejnou značku můžete přiřadit více prostředkům a skupinám prostředků pro kategorizaci prostředků a konsolidaci fakturace. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../articles/azure-resource-manager/management/tag-resources.md).

   ![Nastavení velikosti a měřítka pro nové centrum pomocí Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

    Vyberte **Další: zkontrolovat + vytvořit** a zkontrolujte své volby. Na této obrazovce vidíte něco podobného.

   ![Přečtěte si informace o vytvoření nového centra.](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1. Vyberte **vytvořit** a vytvořte nové centrum. Vytvoření centra může trvat několik minut.
