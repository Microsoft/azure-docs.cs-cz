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
ms.openlocfilehash: fce9e42e24be7f8678292a5d98a683ca4e579cd2
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39357192"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.
   
    ![Snímek obrazovky webu Azure Portal s přechodem do služby IoT Hub](./media/iot-hub-create-hub/create-iot-hub1.png)

3. V podokně **Centrum IoT** zadejte následující informace o centru IoT:

   * **Předplatné:** Vyberte předplatné, které chcete použít při vytváření tohoto centra IoT Hub.

   * **Skupina prostředků:** Vytvořte skupinu prostředků, která bude hostitelem centra IoT, nebo použijte existující. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/resource-group-portal.md).

   * **Oblast:** Vyberte umístění, které je vám nejbližší.

   * **Název:** Vytvořte název centra IoT. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Okno Základy centra IoT Hub](./media/iot-hub-create-hub/create-iot-hub2.png)

4. Pokračujte ve vytváření centra IoT Hub výběrem možnosti **Další: Velikost a škálování**. 

5. Zvolte si **Úroveň ceny a škálování**. Pro potřeby tohoto článku vyberte úroveň **F1 – Free**, pokud je ve vašem předplatném ještě dostupná. Další informace najdete v tématu popisujícím [úrovně cen a škálování](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Okno velikosti a škálování centra IoT Hub](./media/iot-hub-create-hub/create-iot-hub3.png)

6. Vyberte **Zkontrolovat a vytvořit**.

7. Zkontrolujte informace o centru IoT Hub a potom klikněte na **Vytvořit**. Vytvoření centra IoT může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.