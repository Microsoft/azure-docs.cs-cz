---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724898"
---
1. Přihlaste se na web [Azure Portal][lnk-portal].
1. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.
   
    ![Snímek obrazovky webu Azure Portal s přechodem do služby IoT Hub][1]

1. V podokně **Centrum IoT** zadejte následující informace o centru IoT:

   * **Předplatné:** Vyberte předplatné, které chcete použít při vytváření tohoto centra IoT Hub.

   * **Skupina prostředků:** Vytvořte skupinu prostředků, která bude hostitelem centra IoT, nebo použijte existující. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure][lnk-resource-groups].

   * **Oblast:** Vyberte umístění, které je vám nejbližší.

   * **Název:** Vytvořte název centra IoT. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Okno Základy centra IoT Hub][2]

2. Pokračujte ve vytváření centra IoT Hub výběrem možnosti **Další: Velikost a škálování**. 

3. Zvolte si **Úroveň ceny a škálování**. Pro potřeby tohoto článku vyberte úroveň **F1 – Free**, pokud je ve vašem předplatném ještě dostupná. Další informace najdete v tématu popisujícím [úrovně cen a škálování][lnk-pricing].

   ![Okno velikosti a škálování centra IoT Hub][3]

4. Vyberte **Zkontrolovat a vytvořit**.

1. Zkontrolujte informace o centru IoT Hub a potom klikněte na **Vytvořit**. Vytvoření centra IoT může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md