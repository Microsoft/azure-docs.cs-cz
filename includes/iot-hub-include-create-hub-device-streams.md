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
ms.openlocfilehash: 700dd305cf5365a604a5613bd5b48dbd2ac8f840
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021005"
---
Tato část popisuje, jak vytvořit centrum IoT pomocí [Azure Portal](https://portal.azure.com).

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Zvolte **vytvořit prostředek**a potom do pole **Hledat na Marketplace** zadejte *IoT Hub* .

1. Ve výsledcích hledání vyberte **IoT Hub** a pak vyberte **vytvořit**.

1. Na kartě **základy** vyplňte pole následujícím způsobem:

   - **Předplatné**: vyberte předplatné, které chcete použít pro vaše centrum.

   - **Skupina prostředků**: vyberte skupinu prostředků nebo vytvořte novou. Pokud chcete vytvořit nový, vyberte **vytvořit novou** a vyplňte název, který chcete použít. Pokud chcete použít existující skupinu prostředků, vyberte tuto skupinu prostředků. Další informace najdete v tématu [Správa skupin prostředků Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Oblast**: Vyberte oblast, ve které chcete umístit rozbočovač. Vyberte oblast, která podporuje IoT Hub streamy zařízení ve verzi Preview, buď **střed USA** , nebo **střed USA EUAP**.

   - **IoT Hub název**: zadejte název vašeho centra. Tento název musí být globálně jedinečný. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   ![Vytvoření služby IoT Hub v Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Vyberte **Další: velikost a škálování** pro pokračování v vytváření vašeho centra.

   ![Nastavení velikosti a škálování pro nové centrum IoT pomocí Azure Portal](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   V části **velikost a škálování**můžete přijmout výchozí nastavení a dole vybrat možnost **zkontrolovat + vytvořit** . Vezměte v úvahu následující možnosti:

   - **Úroveň cen a škálování**: vaše vybraná úroveň. Vyberte jednu z úrovní Standard (**S1**, **S2**nebo **S3**) nebo **F1: úroveň Free**. Tato volba může být také založená na velikosti vašeho loďstva a nestreamované úlohy, které očekáváte v centru, například zprávy telemetrie. Například úroveň Free je určena pro testování a vyhodnocení. Umožňuje připojení zařízení 500 ke službě IoT Hub a až 8 000 zpráv za den. Každé předplatné Azure může vytvořit jednu službu IoT Hub v bezplatné úrovni. 

   - **Počet jednotek IoT Hub**: počet zpráv povolených na jednotku za den závisí na cenové úrovni vašeho centra. Tato volba závisí na nestreamované zátěži, kterou očekáváte v centru. Pro nyní můžete vybrat 1.

   - **Rozšířená nastavení** > **oddílech typu zařízení-Cloud**: Tato vlastnost má vztah ke zprávám typu zařízení-Cloud na počet souběžných čtenářů zpráv. Většina Center potřebuje jenom čtyři oddíly.

   Další informace o možnostech vrstev najdete v tématu [Volba správné úrovně služby IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

1. Chcete-li zkontrolovat své volby, klikněte na tlačítko **zkontrolovat + vytvořit**. Výsledky budou vypadat přibližně takto:

   ![Informace o vytvoření nového centra IoT Hub](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Pokud chcete vytvořit nové centrum IoT, vyberte **vytvořit**. Tento proces trvá několik minut.
