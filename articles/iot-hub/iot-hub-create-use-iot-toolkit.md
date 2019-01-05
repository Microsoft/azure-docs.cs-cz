---
title: Vytvoření služby Azure IoT Hub pomocí nástroje Azure IoT pro VS Code | Dokumentace Microsoftu
description: Jak používat nástroje Azure IoT pro VS Code k vytvoření služby IoT hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: junhan
ms.openlocfilehash: 9138a709cf8a166bbb572e04b082c5b8e6c82949
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050230"
---
# <a name="create-an-iot-hub-using-the-azure-iot-tools-for-visual-studio-code"></a>Vytvoření IoT hubu pomocí nástroje Azure IoT pro Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

V tomto článku se dozvíte, jak používat [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) k vytvoření služby Azure IoT hub. 

K dokončení tohoto článku, budete potřebovat následující:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code.

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

1. V sadě Visual Studio Code otevřete **Explorer** zobrazení.

2. V dolní části Průzkumníku, rozbalte **zařízení Azure IoT Hub** oddílu. 

   ![Rozbalte zařízení Azure IoT Hub](./media/iot-hub-create-use-iot-toolkit/azure-iot-hub-devices.png)

3. Klikněte na **...**  v **zařízení Azure IoT Hub** hlavičku oddílu. Pokud nevidíte tři tečky, najeďte myší na záhlaví. 

4. Zvolte **vytvoření služby IoT Hub**.

5. Automaticky otevírané okno se zobrazí v pravém dolním rohu umožňuje při prvním přihlášení k Azure.

6. Vyberte předplatné Azure. 

7. Vyberte skupinu prostředků.

8. Vyberte umístění.

9. Vyberte cenovou úroveň.

10. Zadejte globálně jedinečný název pro službu IoT Hub.

11. Počkejte několik minut, dokud se nevytvoří služby IoT Hub.

## <a name="next-steps"></a>Další postup

Nyní jste nasadili Centrum IoT pomocí IoT nástrojů Azure pro Visual Studio Code. Dále zkoumat, najdete v následujících článcích:

* [Používat nástroje Azure IoT pro Visual Studio Code k odesílání a příjem zpráv mezi zařízením a služby IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Použití nástroje Azure IoT pro Visual Studio Code pro správu zařízení Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Najdete na stránce wiki Azure IoT Hub Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
