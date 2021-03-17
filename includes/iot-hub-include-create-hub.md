---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cf651a1a09662e3084a8a9bdb6365b69b6ea52b5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580404"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [Azure Portal](https://portal.azure.com).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Na domovské stránce Azure vyberte tlačítko **+ vytvořit prostředek** a potom do pole **Hledat na Marketplace** zadejte *IoT Hub* .

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole následujícím způsobem:

   - **Předplatné**: vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **vytvořit novou** a vyplňte název, který chcete použít. Pokud chcete použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Oblast**: Vyberte oblast, ve které chcete umístit rozbočovač. Vyberte umístění, které je pro vás nejblíže. Některé funkce, například [datové proudy zařízení IoT Hub](../articles/iot-hub/iot-hub-device-streams-overview.md), jsou k dispozici pouze v konkrétních oblastech. U těchto omezených funkcí je nutné vybrat jednu z podporovaných oblastí.

   - **IoT Hub název**: zadejte název vašeho centra. Tento název musí být globálně jedinečný.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png" alt-text="Vytvořte centrum v Azure Portal.":::

1. Vyberte **Další: sítě** a pokračujte v vytváření vašeho centra.

   Vyberte koncové body, které se můžou připojit k vašemu IoT Hub. Můžete vybrat výchozí nastavení **veřejný koncový bod (všechny sítě)** nebo zvolit **veřejný koncový bod (vybrané rozsahy IP adres)** nebo **soukromý koncový bod**. Pro tento příklad přijměte výchozí nastavení.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png" alt-text="Vyberte koncové body, které se mohou připojit.":::

1. Vyberte **Další: Správa** pro pokračování ve vytváření vašeho centra.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-management.png" alt-text="Nastavte velikost a měřítko pro nové centrum pomocí Azure Portal.":::

    Tady můžete přijmout výchozí nastavení. V případě potřeby můžete změnit libovolné z následujících polí:

    - **Úroveň cen a škálování**: vaše vybraná úroveň. Můžete si vybrat z několika vrstev v závislosti na tom, kolik funkcí potřebujete, a kolik zpráv odesíláte prostřednictvím řešení za den. Úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojit zařízení 500 k rozbočovači a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni.

      Pokud pracujete s rychlým startem IoT Hub datových proudů zařízení, vyberte úroveň Free.

    - **IoT Hub jednotky**: počet zpráv povolených na jednotku za den závisí na cenové úrovni vašeho centra. Pokud třeba chcete, aby centrum podporovalo příchozí zprávy 700 000, zvolíte dvě jednotky úrovně S1.
    Podrobnosti o dalších možnostech vrstev najdete v tématu [Volba správné IoT Hub úrovně](../articles/iot-hub/iot-hub-scaling.md).

    - **Defender pro IoT**: tuto funkci zapněte, pokud chcete přidat další vrstvu ochrany před hrozbami do IoT a vašich zařízení. Tato možnost není dostupná pro rozbočovače na úrovni Free. Další informace o této funkci najdete v tématu [Azure Security Center pro IoT](/azure/asc-for-iot/).

    - **Rozšířená nastavení**  >  **Oddíly zařízení-Cloud**: Tato vlastnost spojuje zprávy typu zařízení-Cloud s počtem souběžných čtenářů zpráv. Většina Center potřebuje jenom čtyři oddíly.

1. Vyberte **Další: značky** a pokračujte na další obrazovku.

    Značky jsou páry název-hodnota. Stejnou značku můžete přiřadit více prostředkům a skupinám prostředků pro kategorizaci prostředků a konsolidaci fakturace. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../articles/azure-resource-manager/management/tag-resources.md).

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-tags.png" alt-text="Přiřaďte značky pro centrum pomocí Azure Portal.":::

1. Vyberte **Další: zkontrolovat + vytvořit** a zkontrolujte své volby. Na této obrazovce vidíte něco podobného, ale s hodnotami, které jste vybrali při vytváření centra.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-review-and-create.png" alt-text="Přečtěte si informace o vytvoření nového centra.":::

1. Vyberte **vytvořit** a vytvořte nové centrum. Vytvoření centra může trvat několik minut.