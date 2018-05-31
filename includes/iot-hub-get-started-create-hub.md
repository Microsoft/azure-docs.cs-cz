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
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371231"
---
## <a name="create-an-iot-hub"></a>Vytvoření centra IoT
Vytvoření služby IoT Hub pro aplikaci simulovaného zařízení, která se ke službě bude připojovat. Následující kroky vám ukážou, jak tuto úlohu dokončit pomocí portálu Azure.

1. Přihlaste se na web [Azure Portal][lnk-portal].

1. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.
   
    ![Panel portálu Azure][1]

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

1. Až bude nové centrum IoT Hub hotové, kliknutím na jeho dlaždici na webu Azure Portal otevřete okno s jeho vlastnostmi. Teď máte vytvořené centrum IoT Hub a můžete zjistit důležité informace, které umožňují připojit k centru IoT Hub zařízení a aplikace. Klikněte na **Zásady sdíleného přístupu**.
   
1. V okně **Zásady sdíleného přístupu** vyberte zásadu **iothubowner**. Pro pozdější použití si zkopírujte **Připojovací řetězec – primární klíč** pro IoT Hub. Další informace najdete v tématu [Řízení přístupu][lnk-access-control] v Příručce pro vývojáře pro IoT Hub.
   
    ![Zásady sdíleného přístupu][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
