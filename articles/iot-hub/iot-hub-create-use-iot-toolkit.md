---
title: Vytvoření centra IoT Azure pomocí sady Azure IoT Toolkit pro VS Code | Dokumentace Microsoftu
description: Jak používat Azure IoT Toolkit pro VS Code k vytvoření služby IoT hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: 71b188443ee48d742b14753cd5526edac493d9e3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369132"
---
# <a name="create-an-iot-hub-using-the-azure-iot-toolkit-for-visual-studio-code"></a>Vytvoření IoT hubu pomocí sady Azure IoT Toolkit pro Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Můžete použít [Azure IoT Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) k vytvoření služby Azure IoT hub. V tomto článku se dozvíte, jak vytvořit IoT hub s Azure IoT Toolkit.

K dokončení tohoto článku, budete potřebovat následující:

* Aktivní účet Azure.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

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

Nyní jste nasadili Centrum IoT pomocí Azure IoT Toolkit pro Visual Studio Code, můžete dále zkoumat:

* [Použití rozšíření Azure IoT Toolkit pro Visual Studio Code k odesílání a příjem zpráv mezi zařízením a centrem IoT](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).
* [Stránka wikiwebu](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki) pro Azure IoT Toolkit.
