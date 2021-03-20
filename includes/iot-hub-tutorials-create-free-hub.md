---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99538707"
---
Vytvoření IoT Hubu pomocí webu Azure Portal:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Na domovské stránce Azure vyberte **vytvořit prostředek** a pak zadejte *IoT Hub* v **části Hledat na Marketplace**.

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole následujícím způsobem:

   - **Předplatné**: vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **vytvořit novou** a vyplňte název, který chcete použít. Pokud chcete použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Tento kurz používá název **tutorials-iot-hub-rg**.

   - **Oblast**: Vyberte oblast, ve které chcete umístit rozbočovač. Vyberte umístění, které je pro vás nejblíže. Některé funkce, například [datové proudy zařízení IoT Hub](../articles/iot-hub/iot-hub-device-streams-overview.md), jsou k dispozici pouze v konkrétních oblastech. U těchto omezených funkcí je nutné vybrat jednu z podporovaných oblastí. V tomto kurzu se používá oblast **západní USA** .

   - **IoT Hub název**: zadejte název vašeho centra. Tento název musí být globálně jedinečný. Tento kurz používá **kurzy – IoT-Hub**. Když vytváříte své centrum, musíte si zvolit jedinečný název.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Vytvoření centra v Azure Portal":::

1. Vyberte **Další: sítě** a pokračujte v vytváření vašeho centra.

   Vyberte koncové body, které se můžou připojit k vašemu IoT Hub. Můžete vybrat výchozí nastavení **veřejný koncový bod (všechny sítě)** nebo zvolit **veřejný koncový bod (vybrané rozsahy IP adres)** nebo **soukromý koncový bod**. Přijměte výchozí nastavení tohoto kurzu.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Vyberte koncové body, které se mohou připojit.":::

1. Vyberte **Další: Správa** pro pokračování ve vytváření vašeho centra.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Nastavte velikost a měřítko pro nové centrum pomocí Azure Portal.":::

   Tady můžete přijmout výchozí nastavení. V případě potřeby můžete změnit libovolné z následujících polí:

   - **Úroveň cen a škálování**: vaše vybraná úroveň. Vyberte úroveň Free. Úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojit zařízení 500 k rozbočovači a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni.

   - **IoT Hub jednotky**: počet zpráv povolených na jednotku za den závisí na cenové úrovni vašeho centra. Pokud třeba chcete, aby centrum podporovalo příchozí zprávy 700 000, zvolíte dvě jednotky úrovně S1.
   Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni. Podrobnosti o dalších možnostech vrstev najdete v tématu [Volba správné IoT Hub úrovně](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender pro IoT**: tuto funkci zapněte, pokud chcete přidat další vrstvu ochrany před hrozbami do IoT a vašich zařízení. Tato možnost není dostupná pro rozbočovače na úrovni Free. Další informace o této funkci najdete v tématu [Azure Security Center pro IoT](/azure/asc-for-iot/).

   - **Rozšířená nastavení**  >  **Oddíly zařízení-Cloud**: Tato vlastnost spojuje zprávy typu zařízení-Cloud s počtem souběžných čtenářů zpráv. Většina Center potřebuje jenom čtyři oddíly. Rozbočovač úrovně Free je omezený na dva oddíly.

1. Vyberte **Další: značky** a pokračujte na další obrazovku.

   Značky jsou páry název-hodnota. Stejnou značku můžete přiřadit více prostředkům a skupinám prostředků pro kategorizaci prostředků a konsolidaci fakturace. Další informace najdete v tématu [použití značek k uspořádání prostředků Azure](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Přiřaďte značky pro centrum pomocí Azure Portal.":::

1. Vyberte **Další: zkontrolovat + vytvořit** a zkontrolujte své volby. Na této obrazovce vidíte něco podobného, ale s hodnotami, které jste vybrali při vytváření centra.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Přečtěte si informace o vytvoření nového centra.":::

1. Poznamenejte si zvolený název centra IoT. Tuto hodnotu budete později v kurzu potřebovat.
