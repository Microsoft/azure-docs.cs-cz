---
title: Vytvoření centra IoT Azure pomocí Azure IoT Hub Toolkit pro VS Code | Dokumentace Microsoftu
description: Jak používat Azure IoT Hub Toolkit pro VS Code k vytvoření služby IoT hub.
author: formulahendry
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: junhan
ms.openlocfilehash: cee71ddfbf1a20cc7417976d60b04bff6f0deac8
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339021"
---
# <a name="create-an-iot-hub-using-the-azure-iot-hub-toolkit-for-visual-studio-code"></a>Vytvoření IoT hubu pomocí sady Azure IoT Hub Toolkit pro Visual Studio Code

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

V tomto článku se dozvíte, jak používat [Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve Azure IoT Toolkit) k vytvoření služby Azure IoT hub. 

K dokončení tohoto článku, budete potřebovat následující:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- [Visual Studio Code](https://code.visualstudio.com/)

- [Sada Azure IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)

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

Nyní jste nasadili Centrum IoT pomocí Azure IoT Hub Toolkit pro Visual Studio Code. Dále zkoumat, najdete v následujících článcích:

* [Použití rozšíření Azure IoT Hub Toolkit pro Visual Studio Code k odesílání a příjem zpráv mezi zařízením a služby IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

* [Použití rozšíření Azure IoT Hub Toolkit pro Visual Studio Code pro správu zařízení Azure IoT Hub](iot-hub-device-management-iot-toolkit.md)

* [Najdete na stránce wiki Azure IoT Hub Toolkit](https://github.com/microsoft/vscode-azure-iot-toolkit/wiki).
