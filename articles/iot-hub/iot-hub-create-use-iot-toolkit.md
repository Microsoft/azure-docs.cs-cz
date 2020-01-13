---
title: Vytvoření IoT Hub Azure pomocí nástrojů Azure IoT pro VS Code | Microsoft Docs
description: Naučte se používat nástroje Azure IoT pro Visual Studio Code k vytvoření centra IoT Azure ve skupině prostředků.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 668087ae596688e86b7b84a16bc5c0fd0f9fcef6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912257"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Vytvoření centra IoT pomocí nástrojů Azure IoT pro Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

V tomto článku se dozvíte, jak pomocí [nástrojů Azure IoT pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vytvořit službu Azure IoT Hub. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K dokončení tohoto článku potřebujete následující:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code.

## <a name="create-an-iot-hub"></a>Vytvoření IoT Hubu

1. V sadě Visual Studio Code otevřete **Explorer** zobrazení.

2. V dolní části Průzkumníku, rozbalte **zařízení Azure IoT Hub** oddílu. 

   ![Rozšíření zařízení Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klikněte na **...**  v **zařízení Azure IoT Hub** hlavičku oddílu. Pokud nevidíte tři tečky, najeďte myší na záhlaví. 

4. Vyberte **vytvořit IoT Hub**.

5. Automaticky otevírané okno se zobrazí v pravém dolním rohu, abyste se mohli poprvé přihlásit do Azure.

6. Vyberte předplatné Azure. 

7. Vyberte skupinu prostředků.

8. Vyberte umístění.

9. Vyberte cenovou úroveň.

10. Zadejte globálně jedinečný název IoT Hub.

11. Počkejte několik minut, než se vytvoří IoT Hub.

## <a name="next-steps"></a>Další kroky

Nyní jste nasadili službu IoT Hub pomocí nástrojů Azure IoT pro Visual Studio Code. Další podrobnosti najdete v následujících článcích:

* [Pomocí nástrojů Azure IoT pro Visual Studio Code můžete odesílat a přijímat zprávy mezi zařízením a IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Použití nástrojů Azure IoT pro Visual Studio Code pro správu zařízení IoT Hub Azure](iot-hub-device-management-iot-toolkit.md)

* [Přečtěte si stránku wikiwebu IoT Hub Azure pro vs Code](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
