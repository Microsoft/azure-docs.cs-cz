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
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
1. Přihlaste se na web [Azure Portal][lnk-portal].
1. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.
   
    ![Snímek obrazovky Azure portálu navigace do služby IoT Hub][1]

1. V podokně **Centrum IoT** zadejte následující informace o centru IoT:

   * **Předplatné**: Zvolte předplatné, který chcete použít k vytvoření toto centrum IoT.

   * **Skupina prostředků:** Vytvořte skupinu prostředků, která bude hostitelem centra IoT, nebo použijte existující. Další informace najdete v tématu [použití skupin prostředků ke správě prostředků Azure][lnk-resource-groups].

   * **Oblast**: Vyberte nejbližší umístění pro vás.

   * **Název:** Vytvořte název centra IoT. Pokud je dostupný název, který zadáte, se zobrazí zelená značka zaškrtnutí.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Okno základy IoT Hub][2]

2. Vyberte **Další: velikosti a měřítka** chcete pokračovat ve vytváření služby IoT hub. 

3. Zvolte vaše **cenovou a škálovací úroveň**. V tomto článku, vyberte **F1 - volné** vrstvy, pokud bude stále k dispozici na vaše předplatné. Další informace najdete v tématu popisujícím [úrovně cen a škálování][lnk-pricing].

   ![Okno velikosti a měřítka IoT Hub][3]

4. Vyberte **zkontrolujte + vytvořit**.

1. Zkontrolujte informace o IoT hub a pak klikněte na **vytvořit**. Vytvoření centra IoT může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md