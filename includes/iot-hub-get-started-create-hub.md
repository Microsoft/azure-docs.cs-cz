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
ms.openlocfilehash: 08afdcf91499fdb6f2da6e9ccc82313286f5c964
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43111227"
---
## <a name="create-an-iot-hub"></a>Vytvoření centra IoT
Vytvoření služby IoT Hub pro aplikaci simulovaného zařízení, která se ke službě bude připojovat. Následující kroky vám ukážou, jak tuto úlohu dokončit pomocí portálu Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.
   
    ![Panel portálu Azure](./media/iot-hub-get-started-create-hub/create-iot-hub1.png)

3. V podokně **Centrum IoT** zadejte následující informace o centru IoT:

   * **Předplatné:** Vyberte předplatné, které chcete použít při vytváření tohoto centra IoT Hub.

   * **Skupina prostředků:** Vytvořte skupinu prostředků, která bude hostitelem centra IoT, nebo použijte existující. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../articles/azure-resource-manager/resource-group-portal.md).

   * **Oblast:** Vyberte umístění, které je vám nejbližší.

   * **Název:** Vytvořte název centra IoT. Pokud je zadaný název platný, zobrazí se zelený symbol zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Okno Základy centra IoT Hub](./media/iot-hub-get-started-create-hub/create-iot-hub2.png)

4. Pokračujte ve vytváření centra IoT Hub výběrem možnosti **Další: Velikost a škálování**. 

5. Zvolte si **Úroveň ceny a škálování**. Pro potřeby tohoto článku vyberte úroveň **F1 – Free**, pokud je ve vašem předplatném ještě dostupná. Další informace najdete v tématu popisujícím [úrovně cen a škálování](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Okno velikosti a škálování centra IoT Hub](./media/iot-hub-get-started-create-hub/create-iot-hub3.png)

6. Vyberte **Zkontrolovat a vytvořit**.

7. Zkontrolujte informace o centru IoT Hub a potom klikněte na **Vytvořit**. Vytvoření centra IoT může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

8. Až bude nové centrum IoT Hub hotové, kliknutím na jeho dlaždici na webu Azure Portal otevřete okno s jeho vlastnostmi. Teď máte vytvořené centrum IoT Hub a můžete zjistit důležité informace, které umožňují připojit k centru IoT Hub zařízení a aplikace. Klikněte na **Zásady sdíleného přístupu**.
   
9. V okně **Zásady sdíleného přístupu** vyberte zásadu **iothubowner**. Pro pozdější použití si zkopírujte **Připojovací řetězec – primární klíč** pro IoT Hub. Další informace najdete v tématu [Řízení přístupu](../articles/iot-hub/iot-hub-devguide-security.md) v Příručce pro vývojáře pro IoT Hub.
   
    ![Zásady sdíleného přístupu](./media/iot-hub-get-started-create-hub/create-iot-hub5.png)