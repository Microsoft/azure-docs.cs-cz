---
title: zahrnout soubor (ve verzi Preview datových proudů zařízení)
description: zahrnout soubor (ve verzi Preview datových proudů zařízení)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46f8e59713896cd94b96de62d982072119c32513
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050165"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [Azure Portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Zvolte **vytvořit prostředek**a potom do pole **Hledat na Marketplace** zadejte *IoT Hub* .

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole následujícím způsobem:

   - **Předplatné:** Vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: Vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **vytvořit novou** a vyplňte název, který chcete použít. Pokud chcete použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Oblast:** Vyberte oblast, ve které chcete umístit rozbočovač. Vyberte oblast, která podporuje IoT Hub streamy zařízení ve verzi Preview, buď **střed USA** , nebo **střed USA EUAP**.

   - **Název IoT Hub**: Zadejte název svého rozbočovače. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   ![Vytvoření služby IoT Hub v Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Vyberte **další: Velikost a škálování** pro pokračování v vytváření vašeho centra

   ![Nastavení velikosti a škálování pro nové centrum IoT pomocí Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   V části **velikost a škálování**můžete přijmout výchozí nastavení a dole vybrat možnost **zkontrolovat + vytvořit** . Vezměte v úvahu následující možnosti:

   - **Úroveň cen a škálování**: Vybraná úroveň. Vyberte jednu z úrovní Standard (**S1**, **S2**nebo **S3**) nebo **F1: Úroveň**Free. Tato volba může být také založená na velikosti vašeho loďstva a nestreamované úlohy, které očekáváte v centru, například zprávy telemetrie. Například úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojení zařízení 500 ke službě IoT Hub a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni. 

   - **Počet IoT Hubch jednotek**: Počet zpráv povolených na jednotku za den závisí na cenové úrovni vašeho centra. Tato volba závisí na nestreamované zátěži, kterou očekáváte v centru. Pro nyní můžete vybrat 1.

   - **Rozšířené nastavení** > **oddíly zařízení-Cloud**: Tato vlastnost spojuje zprávy typu zařízení-Cloud s počtem souběžných čtenářů zpráv. Většina Center potřebuje jenom čtyři oddíly.

   Další informace o možnostech vrstev najdete v tématu [Volba správné úrovně služby IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Chcete-li zkontrolovat své volby, klikněte na tlačítko **zkontrolovat + vytvořit**. Výsledky budou vypadat přibližně takto:

   ![Informace o vytvoření nového centra IoT Hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Pokud chcete vytvořit nové centrum IoT, vyberte **vytvořit**. Tento proces trvá několik minut.
